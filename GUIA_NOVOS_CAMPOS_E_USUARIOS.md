# Tutorial: novos campos de produtos e cadastro de usuários

Este guia mostra como evoluir este projeto em duas etapas:

1. acrescentar `categoria` e `descricao` ao cadastro de produtos;
2. criar um CRUD básico de usuários com `nome`, `email` e `telefone`.

Ao final, será possível cadastrar, consultar, alterar e excluir produtos e usuários. Os exemplos seguem a estrutura atual do projeto, que usa Expo SDK 57, React Native, React Navigation e `expo-sqlite`.

## 1. Antes de começar

Instale as dependências e execute o projeto:

```bash
npm install
npm start
```

O `expo-sqlite` já está instalado. Para projetos em que ele ainda não exista, no Expo SDK 57 a instalação recomendada é:

```bash
npx expo install expo-sqlite
```

Arquivos que serão alterados ou criados:

```text
src/
├── database/
│   └── database.js                 # alterar
└── screens/
    ├── HomeScreen.js               # alterar
    ├── ProductFormScreen.js        # alterar
    ├── ProductsListScreen.js       # alterar
    ├── UserFormScreen.js           # criar
    └── UsersListScreen.js          # criar
App.js                              # alterar
```

> Importante: `CREATE TABLE IF NOT EXISTS` não modifica uma tabela que já existe. Como o aplicativo pode já ter um arquivo `produtos.db` no aparelho, os novos campos precisam ser adicionados por uma migração com `ALTER TABLE`.

## 2. Atualizar o banco de dados

Substitua o conteúdo de `src/database/database.js` pelo código abaixo:

```js
const DATABASE_VERSION = 1;

async function colunaExiste(db, tabela, coluna) {
  const colunas = await db.getAllAsync(`PRAGMA table_info(${tabela})`);
  return colunas.some((item) => item.name === coluna);
}

export async function initializeDatabase(db) {
  await db.execAsync(`
    PRAGMA journal_mode = WAL;

    CREATE TABLE IF NOT EXISTS produtos (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      nome TEXT NOT NULL,
      preco REAL NOT NULL,
      quantidade INTEGER NOT NULL
    );
  `);

  const resultado = await db.getFirstAsync('PRAGMA user_version');
  let versaoAtual = resultado?.user_version ?? 0;

  if (versaoAtual < 1) {
    if (!(await colunaExiste(db, 'produtos', 'categoria'))) {
      await db.execAsync(`
        ALTER TABLE produtos
        ADD COLUMN categoria TEXT NOT NULL DEFAULT 'Sem categoria';
      `);
    }

    if (!(await colunaExiste(db, 'produtos', 'descricao'))) {
      await db.execAsync(`
        ALTER TABLE produtos
        ADD COLUMN descricao TEXT NOT NULL DEFAULT '';
      `);
    }

    await db.execAsync(`
      CREATE TABLE IF NOT EXISTS usuarios (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        nome TEXT NOT NULL,
        email TEXT NOT NULL UNIQUE,
        telefone TEXT NOT NULL DEFAULT ''
      );
    `);

    versaoAtual = 1;
  }

  await db.execAsync(`PRAGMA user_version = ${DATABASE_VERSION}`);
}
```

### O que a migração faz

- mantém os produtos que já estavam cadastrados;
- consulta as colunas existentes com `PRAGMA table_info`;
- adiciona apenas as colunas que ainda não existem;
- usa valores padrão para que registros antigos continuem válidos;
- cria a tabela `usuarios` com e-mail único;
- grava a versão do esquema em `PRAGMA user_version`.

Quando outra mudança de banco for necessária, aumente `DATABASE_VERSION` para `2` e adicione um novo bloco `if (versaoAtual < 2)`. Não edite retroativamente uma migração já distribuída aos usuários.

## 3. Incluir os novos campos no formulário de produtos

Abra `src/screens/ProductFormScreen.js`.

### 3.1 Criar os estados

Logo depois dos estados já existentes, adicione:

```js
const [categoria, setCategoria] = useState('');
const [descricao, setDescricao] = useState('');
```

### 3.2 Preencher os campos durante uma alteração

Dentro do `if (produto)` do `useEffect`, adicione:

```js
setCategoria(produto.categoria ?? '');
setDescricao(produto.descricao ?? '');
```

O operador `??` evita que um produto antigo preencha o campo com `null` ou `undefined`.

### 3.3 Tratar e validar os valores

No início de `salvarProduto`, junto às outras conversões, adicione:

```js
const categoriaLimpa = categoria.trim();
const descricaoLimpa = descricao.trim();
```

Inclua `categoriaLimpa === ''` na condição que apresenta o alerta de dados inválidos. A descrição pode permanecer opcional. Uma mensagem possível é:

```js
Alert.alert(
  'Dados inválidos',
  'Informe nome, categoria, preço válido e quantidade inteira.'
);
```

### 3.4 Atualizar os comandos SQL

Substitua o `UPDATE` pelo seguinte:

```js
await db.runAsync(
  `UPDATE produtos
   SET nome = ?, preco = ?, quantidade = ?, categoria = ?, descricao = ?
   WHERE id = ?`,
  nomeLimpo,
  precoNumerico,
  quantidadeNumerica,
  categoriaLimpa,
  descricaoLimpa,
  produto.id
);
```

Substitua o `INSERT` por:

```js
await db.runAsync(
  `INSERT INTO produtos
    (nome, preco, quantidade, categoria, descricao)
   VALUES (?, ?, ?, ?, ?)`,
  nomeLimpo,
  precoNumerico,
  quantidadeNumerica,
  categoriaLimpa,
  descricaoLimpa
);
```

Os marcadores `?` vinculam os valores separadamente do SQL e evitam concatenar diretamente textos informados pelo usuário.

### 3.5 Adicionar os componentes visuais

Antes do botão de salvar, inclua:

```jsx
<Text style={styles.rotulo}>Categoria</Text>
<TextInput
  style={styles.campo}
  placeholder="Ex.: Papelaria"
  value={categoria}
  onChangeText={setCategoria}
/>

<Text style={styles.rotulo}>Descrição</Text>
<TextInput
  style={[styles.campo, styles.campoMultilinha]}
  placeholder="Detalhes do produto (opcional)"
  value={descricao}
  onChangeText={setDescricao}
  multiline
  numberOfLines={4}
  textAlignVertical="top"
/>
```

Acrescente ao `StyleSheet.create`:

```js
campoMultilinha: {
  minHeight: 100,
},
```

## 4. Mostrar e pesquisar os novos dados de produtos

Em `src/screens/ProductsListScreen.js`, altere a consulta para selecionar os novos campos e pesquisar também por categoria:

```js
const termo = `%${texto.trim()}%`;

const resultado = await db.getAllAsync(
  `SELECT id, nome, preco, quantidade, categoria, descricao
     FROM produtos
    WHERE nome LIKE ? OR categoria LIKE ?
    ORDER BY nome`,
  termo,
  termo
);
```

Altere o placeholder da busca:

```jsx
placeholder="Pesquisar por nome ou categoria"
```

Dentro do cartão, depois do nome do produto, mostre os novos valores:

```jsx
<Text style={styles.detalhesProduto}>
  Categoria: {item.categoria}
</Text>
{item.descricao !== '' && (
  <Text style={styles.detalhesProduto}>{item.descricao}</Text>
)}
```

É essencial incluir os campos no `SELECT`: o objeto `produto` enviado à tela de alteração é o próprio item retornado pela consulta.

## 5. Criar o formulário de usuários

Crie `src/screens/UserFormScreen.js`:

```js
import { useEffect, useState } from 'react';
import {
  Alert,
  Pressable,
  ScrollView,
  StyleSheet,
  Text,
  TextInput,
} from 'react-native';
import { useSQLiteContext } from 'expo-sqlite';

export default function UserFormScreen({ navigation, route }) {
  const db = useSQLiteContext();
  const usuario = route.params?.usuario;
  const editando = Boolean(usuario);

  const [nome, setNome] = useState('');
  const [email, setEmail] = useState('');
  const [telefone, setTelefone] = useState('');

  useEffect(() => {
    if (usuario) {
      setNome(usuario.nome);
      setEmail(usuario.email);
      setTelefone(usuario.telefone ?? '');
    }
  }, [usuario]);

  async function salvarUsuario() {
    const nomeLimpo = nome.trim();
    const emailLimpo = email.trim().toLowerCase();
    const telefoneLimpo = telefone.trim();
    const emailValido = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(emailLimpo);

    if (nomeLimpo === '' || !emailValido) {
      Alert.alert('Dados inválidos', 'Informe um nome e um e-mail válido.');
      return;
    }

    try {
      if (editando) {
        await db.runAsync(
          `UPDATE usuarios
           SET nome = ?, email = ?, telefone = ?
           WHERE id = ?`,
          nomeLimpo,
          emailLimpo,
          telefoneLimpo,
          usuario.id
        );
      } else {
        await db.runAsync(
          'INSERT INTO usuarios (nome, email, telefone) VALUES (?, ?, ?)',
          nomeLimpo,
          emailLimpo,
          telefoneLimpo
        );
      }

      Alert.alert(
        'Sucesso',
        editando ? 'Usuário alterado.' : 'Usuário cadastrado.',
        [{ text: 'OK', onPress: () => navigation.goBack() }]
      );
    } catch (erro) {
      console.error(erro);

      if (String(erro).includes('UNIQUE constraint failed')) {
        Alert.alert('E-mail já cadastrado', 'Informe outro endereço de e-mail.');
        return;
      }

      Alert.alert('Erro', 'Não foi possível salvar o usuário.');
    }
  }

  return (
    <ScrollView
      style={styles.container}
      contentContainerStyle={styles.conteudo}
      keyboardShouldPersistTaps="handled"
    >
      <Text style={styles.titulo}>
        {editando ? 'Alterar usuário' : 'Novo usuário'}
      </Text>
      <Text style={styles.descricao}>Preencha os dados do usuário.</Text>

      <Text style={styles.rotulo}>Nome</Text>
      <TextInput
        style={styles.campo}
        placeholder="Ex.: Maria da Silva"
        value={nome}
        onChangeText={setNome}
        autoCapitalize="words"
      />

      <Text style={styles.rotulo}>E-mail</Text>
      <TextInput
        style={styles.campo}
        placeholder="Ex.: maria@email.com"
        value={email}
        onChangeText={setEmail}
        keyboardType="email-address"
        autoCapitalize="none"
        autoCorrect={false}
      />

      <Text style={styles.rotulo}>Telefone</Text>
      <TextInput
        style={styles.campo}
        placeholder="Ex.: (11) 99999-9999"
        value={telefone}
        onChangeText={setTelefone}
        keyboardType="phone-pad"
      />

      <Pressable
        style={({ pressed }) => [
          styles.botao,
          pressed && styles.botaoPressionado,
        ]}
        onPress={salvarUsuario}
      >
        <Text style={styles.textoBotao}>
          {editando ? 'Salvar alterações' : 'Cadastrar usuário'}
        </Text>
      </Pressable>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#F1F5F9' },
  conteudo: { padding: 24 },
  titulo: { color: '#0F172A', fontSize: 27, fontWeight: 'bold' },
  descricao: {
    color: '#64748B',
    fontSize: 15,
    marginTop: 7,
    marginBottom: 24,
  },
  rotulo: {
    color: '#334155',
    fontSize: 15,
    fontWeight: 'bold',
    marginBottom: 6,
  },
  campo: {
    borderWidth: 1,
    borderColor: '#CBD5E1',
    borderRadius: 10,
    backgroundColor: '#FFFFFF',
    fontSize: 16,
    paddingHorizontal: 12,
    paddingVertical: 11,
    marginBottom: 17,
  },
  botao: {
    alignItems: 'center',
    backgroundColor: '#2563EB',
    borderRadius: 10,
    padding: 15,
    marginTop: 5,
  },
  botaoPressionado: { opacity: 0.75 },
  textoBotao: { color: '#FFFFFF', fontSize: 16, fontWeight: 'bold' },
});
```

Este é um cadastro local de pessoas, não um sistema de autenticação. Caso futuramente exista login, não armazene senhas em texto puro no SQLite; use um serviço de autenticação ou uma solução segura com hash e salt no servidor.

## 6. Criar a consulta de usuários

Crie `src/screens/UsersListScreen.js`:

```js
import { useCallback, useState } from 'react';
import {
  Alert,
  FlatList,
  Pressable,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';
import { useFocusEffect } from '@react-navigation/native';
import { useSQLiteContext } from 'expo-sqlite';

export default function UsersListScreen({ navigation }) {
  const db = useSQLiteContext();
  const [usuarios, setUsuarios] = useState([]);
  const [busca, setBusca] = useState('');

  const consultarUsuarios = useCallback(
    async (texto = '') => {
      try {
        const termo = `%${texto.trim()}%`;
        const resultado = await db.getAllAsync(
          `SELECT id, nome, email, telefone
             FROM usuarios
            WHERE nome LIKE ? OR email LIKE ?
            ORDER BY nome`,
          termo,
          termo
        );
        setUsuarios(resultado);
      } catch (erro) {
        console.error(erro);
        Alert.alert('Erro', 'Não foi possível consultar os usuários.');
      }
    },
    [db]
  );

  useFocusEffect(
    useCallback(() => {
      consultarUsuarios(busca);
    }, [busca, consultarUsuarios])
  );

  function confirmarExclusao(usuario) {
    Alert.alert('Excluir usuário', `Deseja excluir ${usuario.nome}?`, [
      { text: 'Cancelar', style: 'cancel' },
      {
        text: 'Excluir',
        style: 'destructive',
        onPress: async () => {
          try {
            await db.runAsync('DELETE FROM usuarios WHERE id = ?', usuario.id);
            await consultarUsuarios(busca);
          } catch (erro) {
            console.error(erro);
            Alert.alert('Erro', 'Não foi possível excluir o usuário.');
          }
        },
      },
    ]);
  }

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Usuários cadastrados</Text>

      <TextInput
        style={styles.campoBusca}
        placeholder="Pesquisar por nome ou e-mail"
        value={busca}
        onChangeText={setBusca}
        autoCapitalize="none"
      />

      <Pressable
        style={styles.botaoNovo}
        onPress={() => navigation.navigate('CadastroUsuario')}
      >
        <Text style={styles.textoBotaoNovo}>+ Novo usuário</Text>
      </Pressable>

      <FlatList
        data={usuarios}
        keyExtractor={(item) => String(item.id)}
        contentContainerStyle={styles.lista}
        ListEmptyComponent={
          <Text style={styles.listaVazia}>Nenhum usuário encontrado.</Text>
        }
        renderItem={({ item }) => (
          <View style={styles.cartao}>
            <Text style={styles.nome}>{item.nome}</Text>
            <Text style={styles.detalhes}>{item.email}</Text>
            {item.telefone !== '' && (
              <Text style={styles.detalhes}>{item.telefone}</Text>
            )}

            <View style={styles.acoes}>
              <Pressable
                style={styles.botaoEditar}
                onPress={() =>
                  navigation.navigate('CadastroUsuario', { usuario: item })
                }
              >
                <Text style={styles.textoEditar}>Alterar</Text>
              </Pressable>
              <Pressable
                style={styles.botaoExcluir}
                onPress={() => confirmarExclusao(item)}
              >
                <Text style={styles.textoExcluir}>Excluir</Text>
              </Pressable>
            </View>
          </View>
        )}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#F1F5F9', padding: 20 },
  titulo: {
    color: '#0F172A',
    fontSize: 25,
    fontWeight: 'bold',
    marginBottom: 14,
  },
  campoBusca: {
    borderWidth: 1,
    borderColor: '#CBD5E1',
    borderRadius: 10,
    backgroundColor: '#FFFFFF',
    fontSize: 16,
    paddingHorizontal: 12,
    paddingVertical: 11,
  },
  botaoNovo: {
    alignSelf: 'flex-start',
    backgroundColor: '#2563EB',
    borderRadius: 9,
    paddingHorizontal: 15,
    paddingVertical: 11,
    marginTop: 12,
    marginBottom: 14,
  },
  textoBotaoNovo: { color: '#FFFFFF', fontWeight: 'bold' },
  lista: { paddingBottom: 24 },
  listaVazia: { color: '#64748B', textAlign: 'center', padding: 28 },
  cartao: {
    backgroundColor: '#FFFFFF',
    borderRadius: 13,
    padding: 16,
    marginBottom: 11,
  },
  nome: {
    color: '#0F172A',
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 5,
  },
  detalhes: { color: '#475569', fontSize: 15, marginTop: 2 },
  acoes: { flexDirection: 'row', marginTop: 13 },
  botaoEditar: {
    backgroundColor: '#DBEAFE',
    borderRadius: 8,
    paddingHorizontal: 14,
    paddingVertical: 9,
    marginRight: 9,
  },
  textoEditar: { color: '#1D4ED8', fontWeight: 'bold' },
  botaoExcluir: {
    backgroundColor: '#FEE2E2',
    borderRadius: 8,
    paddingHorizontal: 14,
    paddingVertical: 9,
  },
  textoExcluir: { color: '#B91C1C', fontWeight: 'bold' },
});
```

## 7. Registrar as telas na navegação

Em `App.js`, importe as duas telas:

```js
import UserFormScreen from './src/screens/UserFormScreen';
import UsersListScreen from './src/screens/UsersListScreen';
```

Dentro de `<Stack.Navigator>`, acrescente:

```jsx
<Stack.Screen
  name="CadastroUsuario"
  component={UserFormScreen}
  options={({ route }) => ({
    title: route.params?.usuario ? 'Alterar usuário' : 'Cadastrar usuário',
  })}
/>
<Stack.Screen
  name="ConsultaUsuarios"
  component={UsersListScreen}
  options={{ title: 'Consultar usuários' }}
/>
```

Os nomes usados em `navigation.navigate(...)` devem ser exatamente iguais aos nomes definidos no `Stack.Screen`.

## 8. Adicionar os acessos na tela inicial

Em `src/screens/HomeScreen.js`, adicione dois botões depois dos botões de produtos:

```jsx
<Pressable
  style={styles.botaoPrincipal}
  onPress={() => navigation.navigate('CadastroUsuario')}
>
  <Text style={styles.textoBotaoPrincipal}>Cadastrar usuário</Text>
</Pressable>

<Pressable
  style={styles.botaoSecundario}
  onPress={() => navigation.navigate('ConsultaUsuarios')}
>
  <Text style={styles.textoBotaoSecundario}>Consultar usuários</Text>
</Pressable>
```

Como `botaoPrincipal` não tem margem superior, inclua nela:

```js
marginTop: 12,
```

Se o conteúdo não couber em aparelhos menores, troque a `View` externa da tela inicial por um `ScrollView` e importe `ScrollView` de `react-native`.

## 9. Executar e testar

Inicie o projeto:

```bash
npm start
```

Faça este roteiro de teste:

1. Abra um banco que já possua produtos e confirme que os registros continuam presentes.
2. Cadastre um produto com categoria e descrição.
3. Altere o produto e verifique se todos os campos são preenchidos.
4. Pesquise o produto pelo nome e pela categoria.
5. Cadastre um usuário com nome, e-mail e telefone.
6. Tente repetir o e-mail e confirme a mensagem de duplicidade.
7. Altere e exclua um usuário.
8. Feche e abra o aplicativo para confirmar a persistência.

No desenvolvimento, o Expo SDK 57 também oferece o inspetor do `expo-sqlite`. Com o projeto em execução, pressione `Shift + M` no terminal do Expo e selecione **Open expo-sqlite** para visualizar tabelas, colunas e registros.

## 10. Como acrescentar outros campos depois

Para adicionar, por exemplo, o campo `codigoBarras`, repita o fluxo completo:

1. aumente `DATABASE_VERSION`;
2. crie uma migração com `ALTER TABLE produtos ADD COLUMN codigo_barras TEXT`;
3. crie o estado com `useState`;
4. carregue o valor no `useEffect` ao editar;
5. adicione o `TextInput`;
6. valide ou normalize o valor;
7. inclua a coluna e o valor no `INSERT` e no `UPDATE`;
8. inclua a coluna no `SELECT`;
9. mostre o valor na lista e, se necessário, inclua-o na busca;
10. teste cadastro novo, alteração e registros criados antes da migração.

Esse checklist evita o erro comum de atualizar apenas a interface e esquecer o banco ou os comandos SQL.

## Referências

- [Expo SDK 57](https://docs.expo.dev/versions/v57.0.0/)
- [Expo SQLite no SDK 57](https://docs.expo.dev/versions/v57.0.0/sdk/sqlite/)

