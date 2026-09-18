# Tutorial — Projeto Expo com navegação e SQLite para gerenciar produtos

Neste tutorial, você construirá um aplicativo com **React Native, Expo e SQLite**. O projeto será organizado em arquivos separados desde o início para que cada parte tenha uma responsabilidade clara.

Ao final, o aplicativo terá:

- uma tela inicial com informações básicas;
- botões para abrir o cadastro e a consulta de produtos;
- um banco SQLite armazenado no dispositivo;
- cadastro de nome, preço e quantidade;
- listagem e pesquisa de produtos;
- alteração de produtos já cadastrados;
- exclusão com confirmação;
- navegação entre as telas.

O aplicativo implementará as quatro operações CRUD:

| Operação | SQL | Recurso do aplicativo |
|---|---|---|
| Create | `INSERT` | Cadastrar produto |
| Read | `SELECT` | Consultar produtos |
| Update | `UPDATE` | Alterar produto |
| Delete | `DELETE` | Excluir produto |

> O SQLite é local. Os dados permanecem no aparelho, mas não são sincronizados automaticamente entre dispositivos.

## Como estudar com este tutorial

Em cada etapa:

1. crie ou altere apenas os arquivos indicados;
2. salve os arquivos;
3. teste a mudança no Expo Go ou em um emulador;
4. confira se não há mensagens de erro no terminal;
5. avance somente depois de compreender a etapa.

## 1. Criando o projeto

Confirme que Node.js e npm estão instalados:

```bash
node -v
npm -v
```

Crie um projeto Expo básico em JavaScript:

```bash
npx create-expo-app MeuCadastroDeProdutos --template blank
cd MeuCadastroDeProdutos
```

Instale o SQLite e as bibliotecas de navegação:

```bash
npx expo install expo-sqlite
npm install @react-navigation/native @react-navigation/native-stack
npx expo install react-native-screens react-native-safe-area-context
```

O comando `expo install` escolhe versões compatíveis com o SDK do projeto.

Abra a pasta no VS Code:

```bash
code .
```

## 2. Executando o aplicativo

Execute:

```bash
npx expo start
```

Se o celular não conseguir acessar o projeto pela rede local, inicie o Expo no modo túnel:

```bash
npx expo start --tunnel
```

O túnel cria um endereço acessível pela internet. Depois que o novo QR Code aparecer no terminal, leia-o com o Expo Go. Mantenha o terminal aberto e deixe o computador e o celular conectados à internet durante o teste.

> Na primeira execução, o Expo pode solicitar a instalação do pacote necessário para criar o túnel. Confirme a instalação e aguarde a geração do novo QR Code. O modo túnel costuma ser mais lento que a conexão pela rede local, mas ajuda quando os dispositivos estão em redes diferentes ou quando o roteador bloqueia a comunicação direta.

Depois, você pode:

- ler o QR Code com o Expo Go;
- pressionar `a` para abrir o emulador Android;
- pressionar `i` para abrir o simulador iOS no macOS.

Para este tutorial, dê preferência ao Expo Go ou a um emulador. O suporte web do `expo-sqlite` exige configuração adicional de WebAssembly e cabeçalhos específicos.

## 3. Planejando a organização dos arquivos

Ao final do tutorial, o projeto terá esta estrutura:

```text
MeuCadastroDeProdutos/
├── App.js
└── src/
    ├── database/
    │   └── database.js
    └── screens/
        ├── HomeScreen.js
        ├── ProductFormScreen.js
        └── ProductsListScreen.js
```

Cada arquivo terá uma responsabilidade:

| Arquivo | Responsabilidade |
|---|---|
| `App.js` | Abrir o banco e configurar a navegação |
| `database.js` | Criar a tabela de produtos |
| `HomeScreen.js` | Exibir informações e os acessos principais |
| `ProductFormScreen.js` | Cadastrar ou alterar um produto |
| `ProductsListScreen.js` | Consultar, pesquisar, alterar e excluir |

Crie as pastas pelo explorador do VS Code ou pelo terminal:

```bash
mkdir -p src/database src/screens
```

## 4. Etapa 1 — Criando a tela inicial

Crie o arquivo:

```text
src/screens/HomeScreen.js
```

Adicione:

```jsx
import { Pressable, StyleSheet, Text, View } from 'react-native';

export default function HomeScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <View style={styles.apresentacao}>
        <Text style={styles.icone}>📦</Text>
        <Text style={styles.titulo}>Controle de produtos</Text>
        <Text style={styles.descricao}>
          Cadastre produtos e acompanhe preços e quantidades do seu estoque.
        </Text>
      </View>

      <View style={styles.resumo}>
        <Text style={styles.tituloResumo}>O que você pode fazer</Text>
        <Text style={styles.itemResumo}>• Cadastrar novos produtos</Text>
        <Text style={styles.itemResumo}>• Consultar e pesquisar o estoque</Text>
        <Text style={styles.itemResumo}>• Alterar ou excluir registros</Text>
      </View>

      <Pressable
        style={styles.botaoPrincipal}
        onPress={() => navigation.navigate('CadastroProduto')}
      >
        <Text style={styles.textoBotaoPrincipal}>Cadastrar produto</Text>
      </Pressable>

      <Pressable
        style={styles.botaoSecundario}
        onPress={() => navigation.navigate('ConsultaProdutos')}
      >
        <Text style={styles.textoBotaoSecundario}>Consultar produtos</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#F1F5F9',
    padding: 24,
  },
  apresentacao: {
    alignItems: 'center',
    marginBottom: 28,
  },
  icone: {
    fontSize: 54,
    marginBottom: 12,
  },
  titulo: {
    color: '#0F172A',
    fontSize: 29,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  descricao: {
    color: '#475569',
    fontSize: 16,
    lineHeight: 23,
    textAlign: 'center',
    marginTop: 10,
  },
  resumo: {
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 20,
    marginBottom: 22,
  },
  tituloResumo: {
    color: '#1E293B',
    fontSize: 17,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  itemResumo: {
    color: '#475569',
    fontSize: 15,
    marginTop: 6,
  },
  botaoPrincipal: {
    alignItems: 'center',
    backgroundColor: '#2563EB',
    borderRadius: 10,
    padding: 15,
  },
  textoBotaoPrincipal: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
  botaoSecundario: {
    alignItems: 'center',
    borderWidth: 2,
    borderColor: '#2563EB',
    borderRadius: 10,
    padding: 13,
    marginTop: 12,
  },
  textoBotaoSecundario: {
    color: '#1D4ED8',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```

`navigation.navigate()` solicita a abertura de uma rota. As rotas serão registradas em `App.js` na próxima etapa.

### Experimente

Altere o título, a descrição ou os itens do cartão. Os botões ainda não funcionarão enquanto as rotas não forem configuradas.

## 5. Etapa 2 — Configurando a navegação

Substitua o conteúdo de `App.js` por:

```jsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

import HomeScreen from './src/screens/HomeScreen';

const Stack = createNativeStackNavigator();

function TelaTemporaria() {
  return null;
}

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        initialRouteName="Inicio"
        screenOptions={{
          headerStyle: { backgroundColor: '#EFF6FF' },
          headerTintColor: '#1E3A8A',
          headerTitleStyle: { fontWeight: 'bold' },
        }}
      >
        <Stack.Screen
          name="Inicio"
          component={HomeScreen}
          options={{ title: 'Início' }}
        />
        <Stack.Screen
          name="CadastroProduto"
          component={TelaTemporaria}
          options={{ title: 'Cadastrar produto' }}
        />
        <Stack.Screen
          name="ConsultaProdutos"
          component={TelaTemporaria}
          options={{ title: 'Consultar produtos' }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

Agora os botões abrem telas temporárias. O cabeçalho apresenta automaticamente um botão para retornar.

O `NavigationContainer` controla a navegação, enquanto `Stack.Navigator` organiza as telas em uma pilha. Cada `Stack.Screen` associa o nome de uma rota a um componente.

### Experimente

Abra as duas opções e use a seta do cabeçalho para retornar à tela inicial.

## 6. Etapa 3 — Criando o banco de dados

Crie:

```text
src/database/database.js
```

Adicione:

```jsx
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
}
```

A tabela possui:

| Coluna | Tipo | Finalidade |
|---|---|---|
| `id` | `INTEGER` | Identificador único gerado automaticamente |
| `nome` | `TEXT` | Nome obrigatório |
| `preco` | `REAL` | Preço do produto |
| `quantidade` | `INTEGER` | Quantidade em estoque |

Agora altere `App.js`. Importe o provedor e a função de inicialização:

```jsx
import { SQLiteProvider } from 'expo-sqlite';
import { initializeDatabase } from './src/database/database';
```

Envolva o `NavigationContainer` com `SQLiteProvider`:

```jsx
export default function App() {
  return (
    <SQLiteProvider databaseName="produtos.db" onInit={initializeDatabase}>
      <NavigationContainer>
        {/* mantenha o Stack.Navigator desta etapa aqui */}
      </NavigationContainer>
    </SQLiteProvider>
  );
}
```

- `databaseName` define o nome do arquivo local;
- `onInit` prepara o banco antes de liberar os componentes filhos;
- `CREATE TABLE IF NOT EXISTS` não apaga uma tabela existente;
- as telas poderão acessar o mesmo banco com `useSQLiteContext()`.

## 7. Etapa 4 — Criando a tela de cadastro

Crie:

```text
src/screens/ProductFormScreen.js
```

Adicione:

```jsx
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

export default function ProductFormScreen({ navigation, route }) {
  const db = useSQLiteContext();
  const produto = route.params?.produto;
  const editando = Boolean(produto);

  const [nome, setNome] = useState('');
  const [preco, setPreco] = useState('');
  const [quantidade, setQuantidade] = useState('');

  useEffect(() => {
    if (produto) {
      setNome(produto.nome);
      setPreco(String(produto.preco).replace('.', ','));
      setQuantidade(String(produto.quantidade));
    }
  }, [produto]);

  async function salvarProduto() {
    const nomeLimpo = nome.trim();
    const precoNumerico = Number(preco.replace(',', '.'));
    const quantidadeNumerica = Number(quantidade);

    if (
      nomeLimpo === '' ||
      preco.trim() === '' ||
      !Number.isFinite(precoNumerico) ||
      precoNumerico < 0 ||
      quantidade.trim() === '' ||
      !Number.isInteger(quantidadeNumerica) ||
      quantidadeNumerica < 0
    ) {
      Alert.alert(
        'Dados inválidos',
        'Informe um nome, um preço válido e uma quantidade inteira.'
      );
      return;
    }

    try {
      if (editando) {
        await db.runAsync(
          `UPDATE produtos
           SET nome = ?, preco = ?, quantidade = ?
           WHERE id = ?`,
          nomeLimpo,
          precoNumerico,
          quantidadeNumerica,
          produto.id
        );
      } else {
        await db.runAsync(
          'INSERT INTO produtos (nome, preco, quantidade) VALUES (?, ?, ?)',
          nomeLimpo,
          precoNumerico,
          quantidadeNumerica
        );
      }

      Alert.alert(
        'Sucesso',
        editando ? 'Produto alterado.' : 'Produto cadastrado.',
        [{ text: 'OK', onPress: () => navigation.goBack() }]
      );
    } catch (erro) {
      console.error(erro);
      Alert.alert('Erro', 'Não foi possível salvar o produto.');
    }
  }

  return (
    <ScrollView
      style={styles.container}
      contentContainerStyle={styles.conteudo}
      keyboardShouldPersistTaps="handled"
    >
      <Text style={styles.titulo}>
        {editando ? 'Alterar produto' : 'Novo produto'}
      </Text>
      <Text style={styles.descricao}>
        Preencha os dados e pressione o botão para salvar.
      </Text>

      <Text style={styles.rotulo}>Nome</Text>
      <TextInput
        style={styles.campo}
        placeholder="Ex.: Caderno"
        value={nome}
        onChangeText={setNome}
      />

      <Text style={styles.rotulo}>Preço</Text>
      <TextInput
        style={styles.campo}
        placeholder="Ex.: 19,90"
        keyboardType="decimal-pad"
        value={preco}
        onChangeText={setPreco}
      />

      <Text style={styles.rotulo}>Quantidade</Text>
      <TextInput
        style={styles.campo}
        placeholder="Ex.: 10"
        keyboardType="number-pad"
        value={quantidade}
        onChangeText={setQuantidade}
      />

      <Pressable
        style={({ pressed }) => [
          styles.botao,
          pressed && styles.botaoPressionado,
        ]}
        onPress={salvarProduto}
      >
        <Text style={styles.textoBotao}>
          {editando ? 'Salvar alterações' : 'Cadastrar produto'}
        </Text>
      </Pressable>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F1F5F9',
  },
  conteudo: {
    padding: 24,
  },
  titulo: {
    color: '#0F172A',
    fontSize: 27,
    fontWeight: 'bold',
  },
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
  botaoPressionado: {
    opacity: 0.75,
  },
  textoBotao: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```

Essa tela possui dois modos:

- sem produto nos parâmetros, executa `INSERT`;
- com um produto nos parâmetros, preenche os campos e executa `UPDATE`.

Os valores são enviados ao SQL por parâmetros `?`. Isso separa os dados digitados do comando e evita concatenar entradas do usuário diretamente no SQL.

### Ligando a tela ao navegador

Em `App.js`, importe:

```jsx
import ProductFormScreen from './src/screens/ProductFormScreen';
```

No `Stack.Screen` chamado `CadastroProduto`, substitua `TelaTemporaria`:

```jsx
<Stack.Screen
  name="CadastroProduto"
  component={ProductFormScreen}
  options={({ route }) => ({
    title: route.params?.produto ? 'Alterar produto' : 'Cadastrar produto',
  })}
/>
```

Agora o botão **Cadastrar produto** da tela inicial abre o formulário e salva no banco.

### Experimente

Tente salvar com campos vazios, preço negativo e quantidade decimal. Depois, cadastre dois produtos válidos.

## 8. Etapa 5 — Criando a consulta de produtos

Crie:

```text
src/screens/ProductsListScreen.js
```

Adicione:

```jsx
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

export default function ProductsListScreen({ navigation }) {
  const db = useSQLiteContext();
  const [produtos, setProdutos] = useState([]);
  const [busca, setBusca] = useState('');

  const consultarProdutos = useCallback(
    async (texto = '') => {
      try {
        const resultado = await db.getAllAsync(
          `SELECT id, nome, preco, quantidade
           FROM produtos
           WHERE nome LIKE ?
           ORDER BY nome`,
          `%${texto.trim()}%`
        );
        setProdutos(resultado);
      } catch (erro) {
        console.error(erro);
        Alert.alert('Erro', 'Não foi possível consultar os produtos.');
      }
    },
    [db]
  );

  useFocusEffect(
    useCallback(() => {
      consultarProdutos(busca);
    }, [busca, consultarProdutos])
  );

  function confirmarExclusao(produto) {
    Alert.alert(
      'Excluir produto',
      `Deseja excluir ${produto.nome}?`,
      [
        { text: 'Cancelar', style: 'cancel' },
        {
          text: 'Excluir',
          style: 'destructive',
          onPress: async () => {
            try {
              await db.runAsync('DELETE FROM produtos WHERE id = ?', produto.id);
              await consultarProdutos(busca);
            } catch (erro) {
              console.error(erro);
              Alert.alert('Erro', 'Não foi possível excluir o produto.');
            }
          },
        },
      ]
    );
  }

  function abrirEdicao(produto) {
    navigation.navigate('CadastroProduto', { produto });
  }

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Produtos cadastrados</Text>

      <TextInput
        style={styles.campoBusca}
        placeholder="Pesquisar pelo nome"
        value={busca}
        onChangeText={setBusca}
      />

      <Pressable
        style={styles.botaoNovo}
        onPress={() => navigation.navigate('CadastroProduto')}
      >
        <Text style={styles.textoBotaoNovo}>+ Novo produto</Text>
      </Pressable>

      <FlatList
        data={produtos}
        keyExtractor={(item) => String(item.id)}
        contentContainerStyle={styles.lista}
        ListEmptyComponent={
          <Text style={styles.listaVazia}>Nenhum produto encontrado.</Text>
        }
        renderItem={({ item }) => (
          <View style={styles.cartao}>
            <View style={styles.informacoes}>
              <Text style={styles.nomeProduto}>{item.nome}</Text>
              <Text style={styles.detalhesProduto}>
                R$ {Number(item.preco).toFixed(2).replace('.', ',')}
              </Text>
              <Text style={styles.detalhesProduto}>
                Quantidade: {item.quantidade}
              </Text>
            </View>

            <View style={styles.acoes}>
              <Pressable
                style={styles.botaoEditar}
                onPress={() => abrirEdicao(item)}
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
  container: {
    flex: 1,
    backgroundColor: '#F1F5F9',
    padding: 20,
  },
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
  textoBotaoNovo: {
    color: '#FFFFFF',
    fontWeight: 'bold',
  },
  lista: {
    paddingBottom: 24,
  },
  listaVazia: {
    color: '#64748B',
    textAlign: 'center',
    padding: 28,
  },
  cartao: {
    backgroundColor: '#FFFFFF',
    borderRadius: 13,
    padding: 16,
    marginBottom: 11,
  },
  informacoes: {
    marginBottom: 13,
  },
  nomeProduto: {
    color: '#0F172A',
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 5,
  },
  detalhesProduto: {
    color: '#475569',
    fontSize: 15,
    marginTop: 2,
  },
  acoes: {
    flexDirection: 'row',
  },
  botaoEditar: {
    backgroundColor: '#DBEAFE',
    borderRadius: 8,
    paddingHorizontal: 14,
    paddingVertical: 9,
    marginRight: 9,
  },
  textoEditar: {
    color: '#1D4ED8',
    fontWeight: 'bold',
  },
  botaoExcluir: {
    backgroundColor: '#FEE2E2',
    borderRadius: 8,
    paddingHorizontal: 14,
    paddingVertical: 9,
  },
  textoExcluir: {
    color: '#B91C1C',
    fontWeight: 'bold',
  },
});
```

Essa tela executa três operações:

```sql
SELECT id, nome, preco, quantidade FROM produtos
WHERE nome LIKE ? ORDER BY nome
```

```sql
DELETE FROM produtos WHERE id = ?
```

E envia o produto selecionado para a tela responsável pelo `UPDATE`:

```jsx
navigation.navigate('CadastroProduto', { produto });
```

O `useFocusEffect` atualiza a lista sempre que a tela volta a ficar ativa. Isso é importante depois de cadastrar ou alterar um produto.

### Ligando a consulta ao navegador

Em `App.js`, importe:

```jsx
import ProductsListScreen from './src/screens/ProductsListScreen';
```

No `Stack.Screen` chamado `ConsultaProdutos`, substitua `TelaTemporaria`:

```jsx
<Stack.Screen
  name="ConsultaProdutos"
  component={ProductsListScreen}
  options={{ title: 'Consultar produtos' }}
/>
```

Remova também a função `TelaTemporaria`, pois ela não é mais necessária.

### Experimente

Abra a consulta, pesquise parte de um nome e apague a pesquisa. Depois, altere e exclua produtos diferentes.

## 9. Código final de `App.js`

Depois de concluir as etapas, compare seu arquivo com este:

```jsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { SQLiteProvider } from 'expo-sqlite';

import { initializeDatabase } from './src/database/database';
import HomeScreen from './src/screens/HomeScreen';
import ProductFormScreen from './src/screens/ProductFormScreen';
import ProductsListScreen from './src/screens/ProductsListScreen';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <SQLiteProvider databaseName="produtos.db" onInit={initializeDatabase}>
      <NavigationContainer>
        <Stack.Navigator
          initialRouteName="Inicio"
          screenOptions={{
            headerStyle: { backgroundColor: '#EFF6FF' },
            headerTintColor: '#1E3A8A',
            headerTitleStyle: { fontWeight: 'bold' },
          }}
        >
          <Stack.Screen
            name="Inicio"
            component={HomeScreen}
            options={{ title: 'Início' }}
          />
          <Stack.Screen
            name="CadastroProduto"
            component={ProductFormScreen}
            options={({ route }) => ({
              title: route.params?.produto
                ? 'Alterar produto'
                : 'Cadastrar produto',
            })}
          />
          <Stack.Screen
            name="ConsultaProdutos"
            component={ProductsListScreen}
            options={{ title: 'Consultar produtos' }}
          />
        </Stack.Navigator>
      </NavigationContainer>
    </SQLiteProvider>
  );
}
```

Revise a estrutura final:

```text
MeuCadastroDeProdutos/
├── App.js
└── src/
    ├── database/
    │   └── database.js
    └── screens/
        ├── HomeScreen.js
        ├── ProductFormScreen.js
        └── ProductsListScreen.js
```

O fluxo entre as telas é:

```text
Início
├── Cadastrar produto ──> Formulário ──> INSERT
└── Consultar produtos ─> Lista
                            ├── Novo ───> Formulário ──> INSERT
                            ├── Alterar > Formulário ──> UPDATE
                            └── Excluir ───────────────> DELETE
```

## 10. Entendendo o cadastro e a alteração na mesma tela

A tela `ProductFormScreen` verifica se recebeu um produto:

```jsx
const produto = route.params?.produto;
const editando = Boolean(produto);
```

Ao cadastrar pela tela inicial, nenhum parâmetro é enviado. Portanto, `editando` é `false` e a tela executa `INSERT`.

Ao pressionar **Alterar** na lista, o produto é enviado como parâmetro. O `useEffect` copia seus dados para os campos, `editando` é `true` e a tela executa `UPDATE` usando o `id`.

Reutilizar o formulário evita manter duas telas quase iguais.

## 11. Teste final

1. Abra o aplicativo e confira as informações da tela inicial.
2. Pressione **Cadastrar produto**.
3. Tente salvar o formulário vazio e confira a validação.
4. Cadastre pelo menos três produtos.
5. Retorne ao início e abra **Consultar produtos**.
6. Confira se a lista está em ordem alfabética.
7. Pesquise por parte de um nome.
8. Pressione **Alterar**, modifique os dados e salve.
9. Confira a alteração na lista.
10. Exclua um produto e confirme a operação.
11. Feche completamente o aplicativo e abra-o novamente.
12. Confira se os produtos restantes continuam armazenados.

## 12. Desafios graduais

1. Mostre o `id` de cada produto na lista.
2. Mostre uma mensagem diferente quando a quantidade for zero.
3. Adicione o campo `categoria` ao cadastro e à consulta.
4. Mostre o total de unidades com `SUM(quantidade)`.
5. Mostre a quantidade de produtos com `COUNT(*)`.
6. Adicione opções para ordenar por nome, preço ou quantidade.
7. Crie uma tela com produtos sem estoque.
8. Desabilite o botão enquanto uma operação estiver sendo executada.

> Depois que uma tabela existe, mudar apenas `CREATE TABLE IF NOT EXISTS` não altera sua estrutura. Para adicionar colunas sem perder dados, estude migrações e `ALTER TABLE`.

## 13. Problemas comuns

| Problema | Possível solução |
|---|---|
| `Unable to resolve expo-sqlite` | Execute `npx expo install expo-sqlite` e reinicie o Expo. |
| `Unable to resolve @react-navigation...` | Refaça os comandos de instalação da navegação. |
| `useSQLiteContext must be used within a SQLiteProvider` | Confira se o navegador está dentro de `SQLiteProvider`. |
| `no such table: produtos` | Confira `onInit={initializeDatabase}` e o nome da tabela. |
| Uma tela não é encontrada | Confira nome, caminho, `export default` e `import`. |
| O botão não abre a tela | O nome usado em `navigate` deve ser idêntico ao do `Stack.Screen`. |
| A lista não atualiza após editar | Confira o `useFocusEffect` em `ProductsListScreen.js`. |
| O preço com vírgula é rejeitado | Confira `preco.replace(',', '.')`. |
| Campos numéricos vazios viram zero | Confira os testes `preco.trim() === ''` e `quantidade.trim() === ''`. |
| Os dados desapareceram | Reinstalar ou limpar os dados do aplicativo remove o banco local. |
| Uma alteração antiga continua aparecendo | Execute `npx expo start --clear`. |

## Checklist de aprendizagem

- [ ] Criei as pastas `src/database` e `src/screens`.
- [ ] Separei banco, navegação e telas em arquivos diferentes.
- [ ] Criei uma tela inicial com informações e dois botões.
- [ ] Configurei a navegação em pilha.
- [ ] Instalei e configurei `expo-sqlite`.
- [ ] Criei a tabela com `CREATE TABLE IF NOT EXISTS`.
- [ ] Cadastrei produtos com `INSERT`.
- [ ] Consultei produtos com `SELECT`.
- [ ] Pesquisei pelo nome com `LIKE`.
- [ ] Reaproveitei o formulário para executar `UPDATE`.
- [ ] Excluí pelo `id` com `DELETE`.
- [ ] Usei parâmetros `?` nos comandos SQL.
- [ ] Atualizei a lista com `useFocusEffect`.
- [ ] Confirmei a persistência depois de reiniciar o aplicativo.

## Referências oficiais

- [Criar um projeto Expo](https://docs.expo.dev/get-started/create-a-project/)
- [SQLite no Expo](https://docs.expo.dev/versions/latest/sdk/sqlite/)
- [Primeiros passos com React Navigation](https://reactnavigation.org/docs/getting-started/)
- [Navegação Native Stack](https://reactnavigation.org/docs/native-stack-navigator/)
- [Hook `useFocusEffect`](https://reactnavigation.org/docs/use-focus-effect/)
- [TextInput no React Native](https://reactnative.dev/docs/textinput)
- [FlatList no React Native](https://reactnative.dev/docs/flatlist)
- [Linguagem SQL do SQLite](https://www.sqlite.org/lang.html)
