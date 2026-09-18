# Tutorial — Projeto Expo incremental com SQLite para cadastrar e consultar produtos

Neste tutorial, você vai construir um aplicativo de produtos com **React Native, Expo e SQLite**. O projeto começa com uma tela simples e cresce aos poucos. Em cada etapa, adicionaremos somente alguns conceitos novos.

Ao final, o aplicativo permitirá:

- digitar o nome, o preço e a quantidade de um produto;
- salvar produtos em um banco de dados SQLite;
- manter os dados mesmo depois de fechar e abrir o aplicativo;
- consultar todos os produtos cadastrados;
- pesquisar produtos pelo nome;
- excluir um produto da lista.

O SQLite é um banco de dados armazenado no próprio dispositivo. Portanto, este projeto não precisa de servidor nem de conexão com a internet para cadastrar e consultar os produtos.

> O banco criado neste tutorial é local. Os produtos cadastrados em um aparelho não aparecem automaticamente em outro aparelho.

## Como estudar com este tutorial

Em cada etapa:

1. substitua o conteúdo do arquivo `App.js` pelo código apresentado;
2. salve o arquivo;
3. observe a atualização automática;
4. faça o pequeno experimento sugerido;
5. avance somente depois de entender a mudança.

Não é necessário memorizar o código. O objetivo é relacionar cada trecho ao resultado mostrado na tela e compreender o caminho percorrido pelos dados.

## 1. Criando o projeto

Confirme que o Node.js e o npm estão instalados:

```bash
node -v
npm -v
```

Crie um projeto Expo básico em JavaScript e entre na pasta criada:

```bash
npx create-expo-app MeuCadastroDeProdutos --template blank
cd MeuCadastroDeProdutos
```

Instale a biblioteca do SQLite usando o comando do Expo:

```bash
npx expo install expo-sqlite
```

O comando `expo install` escolhe uma versão compatível com a versão do Expo utilizada pelo projeto.

Por fim, abra a pasta no VS Code:

```bash
code .
```

Se `code .` não funcionar, abra o VS Code e use **Arquivo > Abrir Pasta**.

## 2. Executando o aplicativo

Dentro da pasta do projeto, execute:

```bash
npx expo start
```

Espere o terminal mostrar o QR Code e os atalhos do Expo. Depois, você pode:

- ler o QR Code com o Expo Go;
- pressionar `a` para abrir um emulador Android;
- pressionar `i` para abrir o simulador iOS no macOS.

Para este tutorial, dê preferência ao **Expo Go, emulador Android ou simulador iOS**. O SQLite faz parte do Expo Go e os dados permanecem armazenados no aplicativo entre as execuções.

> O `expo-sqlite` também possui suporte para web, mas essa configuração exige suporte a arquivos WebAssembly e cabeçalhos específicos. Há uma seção opcional sobre isso no fim do tutorial.

Mantenha o terminal aberto. Para interromper o servidor, pressione `Ctrl+C`.

## 3. Etapa 1 — Criando a primeira tela

Abra `App.js`, apague o conteúdo e escreva:

```jsx
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Cadastro de produtos</Text>
      <Text style={styles.subtitulo}>
        Registre e consulte os produtos do estoque.
      </Text>
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
  titulo: {
    color: '#0F172A',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  subtitulo: {
    color: '#475569',
    fontSize: 16,
    textAlign: 'center',
    marginTop: 8,
  },
});
```

`App` é um componente: uma função que retorna a interface. A `View` agrupa os elementos, e cada `Text` exibe um texto.

### Experimente

Troque o título e altere a cor de fundo definida em `backgroundColor`.

## 4. Etapa 2 — Montando o formulário

Agora adicione três campos de texto e um botão. Ainda não salvaremos informações no banco.

Substitua `App.js` por:

```jsx
import { Pressable, StyleSheet, Text, TextInput, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Cadastro de produtos</Text>

      <View style={styles.formulario}>
        <Text style={styles.rotulo}>Nome</Text>
        <TextInput
          style={styles.campo}
          placeholder="Ex.: Caderno"
        />

        <Text style={styles.rotulo}>Preço</Text>
        <TextInput
          style={styles.campo}
          placeholder="Ex.: 19,90"
          keyboardType="decimal-pad"
        />

        <Text style={styles.rotulo}>Quantidade</Text>
        <TextInput
          style={styles.campo}
          placeholder="Ex.: 10"
          keyboardType="number-pad"
        />

        <Pressable style={styles.botao}>
          <Text style={styles.textoBotao}>Cadastrar produto</Text>
        </Pressable>
      </View>
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
  titulo: {
    color: '#0F172A',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 24,
  },
  formulario: {
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 20,
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
    paddingVertical: 10,
    marginBottom: 16,
  },
  botao: {
    alignItems: 'center',
    backgroundColor: '#2563EB',
    borderRadius: 10,
    padding: 14,
  },
  textoBotao: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```

O `TextInput` recebe o texto digitado. A propriedade `keyboardType` solicita um teclado mais apropriado para números, mas não substitui a validação dos valores.

### Experimente

Digite informações nos três campos. Observe que, por enquanto, o botão não executa nenhuma ação.

## 5. Etapa 3 — Controlando os campos com `useState`

Para utilizar os valores digitados, cada campo será ligado a um estado. Importe `useState` e altere o início do componente:

```jsx
import { useState } from 'react';
import { Pressable, StyleSheet, Text, TextInput, View } from 'react-native';

export default function App() {
  const [nome, setNome] = useState('');
  const [preco, setPreco] = useState('');
  const [quantidade, setQuantidade] = useState('');

  function mostrarProduto() {
    alert(`${nome} — R$ ${preco} — ${quantidade} unidade(s)`);
  }

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Cadastro de produtos</Text>

      <View style={styles.formulario}>
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

        <Pressable style={styles.botao} onPress={mostrarProduto}>
          <Text style={styles.textoBotao}>Cadastrar produto</Text>
        </Pressable>
      </View>
    </View>
  );
}
```

Mantenha o mesmo `StyleSheet.create` da etapa anterior depois do componente.

A ligação de cada campo tem duas partes:

- `value={nome}` mostra no campo o valor guardado no estado;
- `onChangeText={setNome}` atualiza o estado sempre que o texto muda.

Esse padrão é chamado de **campo controlado**. Ao pressionar o botão, `mostrarProduto` lê os três estados.

### Experimente

Preencha os campos e pressione **Cadastrar produto**. Depois, troque a mensagem exibida pela função `mostrarProduto`.

## 6. Etapa 4 — Abrindo o banco e criando a tabela

Um banco SQLite contém tabelas. Nossa tabela se chamará `produtos` e terá quatro colunas:

| Coluna | Tipo | Finalidade |
|---|---|---|
| `id` | `INTEGER` | Identificador único gerado automaticamente. |
| `nome` | `TEXT` | Nome obrigatório do produto. |
| `preco` | `REAL` | Preço do produto. |
| `quantidade` | `INTEGER` | Quantidade disponível. |

Substitua `App.js` por:

```jsx
import { StyleSheet, Text, View } from 'react-native';
import { SQLiteProvider, useSQLiteContext } from 'expo-sqlite';

// Esta função é executada quando o banco é aberto pelo SQLiteProvider.
async function criarBanco(db) {
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

function TelaProdutos() {
  // Acesso ao banco fornecido pelo SQLiteProvider.
  const db = useSQLiteContext();

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Cadastro de produtos</Text>
      <Text style={styles.mensagem}>Banco de dados pronto para uso.</Text>
    </View>
  );
}

export default function App() {
  return (
    <SQLiteProvider databaseName="produtos.db" onInit={criarBanco}>
      <TelaProdutos />
    </SQLiteProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#F1F5F9',
    padding: 24,
  },
  titulo: {
    color: '#0F172A',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  mensagem: {
    color: '#166534',
    fontSize: 16,
    textAlign: 'center',
    marginTop: 12,
  },
});
```

O fluxo agora é:

```text
App
└── SQLiteProvider abre produtos.db
    ├── criarBanco cria a tabela, caso ela ainda não exista
    └── TelaProdutos acessa o banco com useSQLiteContext
```

Algumas partes importantes:

- `databaseName="produtos.db"` define o nome do arquivo do banco;
- `onInit={criarBanco}` executa a preparação do banco;
- `CREATE TABLE IF NOT EXISTS` cria a tabela somente quando necessário;
- `PRIMARY KEY AUTOINCREMENT` gera um identificador diferente para cada produto;
- `useSQLiteContext()` fornece o banco aos componentes que estão dentro do `SQLiteProvider`;
- `execAsync()` executa um ou mais comandos SQL sem bloquear a interface.

> Declarar `const db = useSQLiteContext()` nesta etapa confirma onde o banco será acessado. Ele será efetivamente usado na próxima etapa.

### Experimente

Feche e abra o aplicativo. A instrução `IF NOT EXISTS` permite executar a inicialização novamente sem recriar ou apagar a tabela existente.

## 7. Etapa 5 — Cadastrando produtos com `INSERT`

Agora reuniremos o formulário, a validação e o banco. Substitua `App.js` pelo código a seguir:

```jsx
import { useState } from 'react';
import {
  Alert,
  Pressable,
  SafeAreaView,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';
import { SQLiteProvider, useSQLiteContext } from 'expo-sqlite';

async function criarBanco(db) {
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

function TelaProdutos() {
  const db = useSQLiteContext();
  const [nome, setNome] = useState('');
  const [preco, setPreco] = useState('');
  const [quantidade, setQuantidade] = useState('');

  async function cadastrarProduto() {
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
      await db.runAsync(
        'INSERT INTO produtos (nome, preco, quantidade) VALUES (?, ?, ?)',
        nomeLimpo,
        precoNumerico,
        quantidadeNumerica
      );

      setNome('');
      setPreco('');
      setQuantidade('');
      Alert.alert('Sucesso', 'Produto cadastrado.');
    } catch (erro) {
      console.error(erro);
      Alert.alert('Erro', 'Não foi possível cadastrar o produto.');
    }
  }

  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.titulo}>Cadastro de produtos</Text>

      <View style={styles.formulario}>
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

        <Pressable style={styles.botao} onPress={cadastrarProduto}>
          <Text style={styles.textoBotao}>Cadastrar produto</Text>
        </Pressable>
      </View>
    </SafeAreaView>
  );
}

export default function App() {
  return (
    <SQLiteProvider databaseName="produtos.db" onInit={criarBanco}>
      <TelaProdutos />
    </SQLiteProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F1F5F9',
    padding: 24,
  },
  titulo: {
    color: '#0F172A',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 24,
  },
  formulario: {
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 20,
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
    fontSize: 16,
    paddingHorizontal: 12,
    paddingVertical: 10,
    marginBottom: 16,
  },
  botao: {
    alignItems: 'center',
    backgroundColor: '#2563EB',
    borderRadius: 10,
    padding: 14,
  },
  textoBotao: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```

A instrução principal desta etapa é:

```sql
INSERT INTO produtos (nome, preco, quantidade) VALUES (?, ?, ?)
```

Os símbolos `?` são substituídos, na mesma ordem, por `nomeLimpo`, `precoNumerico` e `quantidadeNumerica`.

Não monte comandos SQL concatenando os textos digitados pelo usuário. Os parâmetros `?` mantêm os valores separados do comando e ajudam a evitar injeção de SQL.

A função também:

- remove espaços extras do nome com `trim()`;
- aceita preço com vírgula ou ponto;
- converte os textos numéricos com `Number()`;
- rejeita preços negativos e quantidades que não sejam inteiras;
- limpa os campos depois de um cadastro bem-sucedido;
- usa `try/catch` para tratar possíveis erros do banco.

### Experimente

Cadastre dois produtos. Em seguida, tente cadastrar um produto sem nome e outro com quantidade negativa para conferir a validação.

## 8. Etapa 6 — Consultando e exibindo os produtos

Os dados já estão no banco, mas ainda não aparecem na tela. Para consultá-los, usaremos:

```sql
SELECT id, nome, preco, quantidade FROM produtos ORDER BY nome
```

Faça estas alterações no código da etapa anterior.

Primeiro, importe também `useEffect`:

```jsx
import { useEffect, useState } from 'react';
```

Inclua `FlatList` entre os componentes importados de `react-native`:

```jsx
import {
  Alert,
  FlatList,
  Pressable,
  SafeAreaView,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';
```

Dentro de `TelaProdutos`, logo depois dos três estados do formulário, crie o estado da lista e a função de consulta:

```jsx
const [produtos, setProdutos] = useState([]);

async function consultarProdutos() {
  try {
    const resultado = await db.getAllAsync(
      'SELECT id, nome, preco, quantidade FROM produtos ORDER BY nome'
    );
    setProdutos(resultado);
  } catch (erro) {
    console.error(erro);
    Alert.alert('Erro', 'Não foi possível consultar os produtos.');
  }
}

useEffect(() => {
  consultarProdutos();
}, []);
```

Depois do `INSERT`, atualize a lista antes de mostrar a mensagem:

```jsx
await consultarProdutos();

setNome('');
setPreco('');
setQuantidade('');
Alert.alert('Sucesso', 'Produto cadastrado.');
```

Por fim, depois do fechamento da `View` do formulário e antes do fechamento de `SafeAreaView`, adicione:

```jsx
<Text style={styles.tituloLista}>Produtos cadastrados</Text>

<FlatList
  data={produtos}
  keyExtractor={(item) => String(item.id)}
  contentContainerStyle={styles.lista}
  ListEmptyComponent={
    <Text style={styles.listaVazia}>Nenhum produto cadastrado.</Text>
  }
  renderItem={({ item }) => (
    <View style={styles.produto}>
      <Text style={styles.nomeProduto}>{item.nome}</Text>
      <Text style={styles.detalhesProduto}>
        R$ {Number(item.preco).toFixed(2).replace('.', ',')} · Estoque: {item.quantidade}
      </Text>
    </View>
  )}
/>
```

Acrescente os novos estilos dentro de `StyleSheet.create`:

```jsx
tituloLista: {
  color: '#0F172A',
  fontSize: 21,
  fontWeight: 'bold',
  marginTop: 24,
  marginBottom: 10,
},
lista: {
  paddingBottom: 24,
},
listaVazia: {
  color: '#64748B',
  textAlign: 'center',
  padding: 24,
},
produto: {
  backgroundColor: '#FFFFFF',
  borderRadius: 12,
  padding: 16,
  marginBottom: 10,
},
nomeProduto: {
  color: '#0F172A',
  fontSize: 17,
  fontWeight: 'bold',
},
detalhesProduto: {
  color: '#475569',
  fontSize: 15,
  marginTop: 5,
},
```

O `useEffect` executa a consulta quando `TelaProdutos` aparece pela primeira vez. O método `getAllAsync()` devolve todas as linhas encontradas como um array de objetos. Esse array é guardado em `produtos` e exibido pela `FlatList`.

O fluxo do cadastro completo agora é:

```text
Usuário preenche o formulário
        ↓
cadastrarProduto valida os valores
        ↓
runAsync executa o INSERT
        ↓
getAllAsync executa o SELECT
        ↓
setProdutos atualiza o estado
        ↓
FlatList redesenha a lista
```

### Experimente

Cadastre três produtos. Feche completamente o aplicativo, abra-o novamente e confirme que a lista foi recuperada do banco.

## 9. Etapa 7 — Pesquisando produtos pelo nome

Crie um novo estado junto aos demais estados:

```jsx
const [busca, setBusca] = useState('');
```

Altere `consultarProdutos` para receber um texto opcional:

```jsx
async function consultarProdutos(texto = '') {
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
}
```

Antes do título **Produtos cadastrados**, adicione o campo de busca:

```jsx
<Text style={styles.tituloLista}>Consultar produtos</Text>

<TextInput
  style={styles.campoBusca}
  placeholder="Digite parte do nome"
  value={busca}
  onChangeText={(texto) => {
    setBusca(texto);
    consultarProdutos(texto);
  }}
/>
```

Remova o antigo `Text` com `styles.tituloLista` que dizia **Produtos cadastrados**, para não deixar dois títulos seguidos.

Acrescente o estilo:

```jsx
campoBusca: {
  borderWidth: 1,
  borderColor: '#CBD5E1',
  borderRadius: 10,
  backgroundColor: '#FFFFFF',
  fontSize: 16,
  paddingHorizontal: 12,
  paddingVertical: 10,
  marginBottom: 12,
},
```

No SQL, o operador `LIKE` procura um padrão. Os sinais `%` significam que pode existir qualquer texto antes ou depois do trecho digitado. Assim, a busca `cad` pode encontrar `Caderno`.

O valor pesquisado continua sendo passado por um parâmetro `?`, sem concatená-lo diretamente ao comando SQL.

### Experimente

Cadastre `Caderno`, `Caneta azul` e `Mochila`. Pesquise por `ca`, apague o texto e observe a lista completa reaparecer.

## 10. Etapa 8 — Excluindo um produto

Dentro de `TelaProdutos`, crie esta função:

```jsx
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
```

No `renderItem` da `FlatList`, coloque as informações e o botão lado a lado:

```jsx
renderItem={({ item }) => (
  <View style={styles.produto}>
    <View style={styles.informacoesProduto}>
      <Text style={styles.nomeProduto}>{item.nome}</Text>
      <Text style={styles.detalhesProduto}>
        R$ {Number(item.preco).toFixed(2).replace('.', ',')} · Estoque: {item.quantidade}
      </Text>
    </View>

    <Pressable
      style={styles.botaoExcluir}
      onPress={() => confirmarExclusao(item)}
    >
      <Text style={styles.textoExcluir}>Excluir</Text>
    </Pressable>
  </View>
)}
```

Atualize `produto` e acrescente os novos estilos:

```jsx
produto: {
  flexDirection: 'row',
  alignItems: 'center',
  backgroundColor: '#FFFFFF',
  borderRadius: 12,
  padding: 16,
  marginBottom: 10,
},
informacoesProduto: {
  flex: 1,
},
botaoExcluir: {
  backgroundColor: '#FEE2E2',
  borderRadius: 8,
  paddingHorizontal: 12,
  paddingVertical: 9,
  marginLeft: 12,
},
textoExcluir: {
  color: '#B91C1C',
  fontWeight: 'bold',
},
```

A exclusão usa o identificador do produto:

```sql
DELETE FROM produtos WHERE id = ?
```

Usar o `id` evita excluir por engano outros produtos que tenham o mesmo nome.

### Experimente

Cadastre dois produtos com o mesmo nome e exclua somente um deles. Confirme que cada linha possui um `id` independente.

## 11. Código final completo

Depois de fazer as etapas incrementais, use este código para comparar e revisar seu `App.js`:

```jsx
import { useEffect, useState } from 'react';
import {
  Alert,
  FlatList,
  Pressable,
  SafeAreaView,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';
import { SQLiteProvider, useSQLiteContext } from 'expo-sqlite';

async function criarBanco(db) {
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

function TelaProdutos() {
  const db = useSQLiteContext();
  const [nome, setNome] = useState('');
  const [preco, setPreco] = useState('');
  const [quantidade, setQuantidade] = useState('');
  const [busca, setBusca] = useState('');
  const [produtos, setProdutos] = useState([]);

  async function consultarProdutos(texto = '') {
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
  }

  useEffect(() => {
    consultarProdutos();
  }, []);

  async function cadastrarProduto() {
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
      await db.runAsync(
        'INSERT INTO produtos (nome, preco, quantidade) VALUES (?, ?, ?)',
        nomeLimpo,
        precoNumerico,
        quantidadeNumerica
      );

      setNome('');
      setPreco('');
      setQuantidade('');
      setBusca('');
      await consultarProdutos();
      Alert.alert('Sucesso', 'Produto cadastrado.');
    } catch (erro) {
      console.error(erro);
      Alert.alert('Erro', 'Não foi possível cadastrar o produto.');
    }
  }

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

  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.titulo}>Cadastro de produtos</Text>

      <View style={styles.formulario}>
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
          onPress={cadastrarProduto}
        >
          <Text style={styles.textoBotao}>Cadastrar produto</Text>
        </Pressable>
      </View>

      <Text style={styles.tituloLista}>Consultar produtos</Text>

      <TextInput
        style={styles.campoBusca}
        placeholder="Digite parte do nome"
        value={busca}
        onChangeText={(texto) => {
          setBusca(texto);
          consultarProdutos(texto);
        }}
      />

      <FlatList
        data={produtos}
        keyExtractor={(item) => String(item.id)}
        contentContainerStyle={styles.lista}
        ListEmptyComponent={
          <Text style={styles.listaVazia}>Nenhum produto encontrado.</Text>
        }
        renderItem={({ item }) => (
          <View style={styles.produto}>
            <View style={styles.informacoesProduto}>
              <Text style={styles.nomeProduto}>{item.nome}</Text>
              <Text style={styles.detalhesProduto}>
                R$ {Number(item.preco).toFixed(2).replace('.', ',')} · Estoque: {item.quantidade}
              </Text>
            </View>

            <Pressable
              style={styles.botaoExcluir}
              onPress={() => confirmarExclusao(item)}
            >
              <Text style={styles.textoExcluir}>Excluir</Text>
            </Pressable>
          </View>
        )}
      />
    </SafeAreaView>
  );
}

export default function App() {
  return (
    <SQLiteProvider databaseName="produtos.db" onInit={criarBanco}>
      <TelaProdutos />
    </SQLiteProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F1F5F9',
    paddingHorizontal: 20,
    paddingTop: 20,
  },
  titulo: {
    color: '#0F172A',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 18,
  },
  formulario: {
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 18,
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
    fontSize: 16,
    paddingHorizontal: 12,
    paddingVertical: 10,
    marginBottom: 14,
  },
  botao: {
    alignItems: 'center',
    backgroundColor: '#2563EB',
    borderRadius: 10,
    padding: 14,
  },
  botaoPressionado: {
    opacity: 0.75,
  },
  textoBotao: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
  tituloLista: {
    color: '#0F172A',
    fontSize: 21,
    fontWeight: 'bold',
    marginTop: 22,
    marginBottom: 10,
  },
  campoBusca: {
    borderWidth: 1,
    borderColor: '#CBD5E1',
    borderRadius: 10,
    backgroundColor: '#FFFFFF',
    fontSize: 16,
    paddingHorizontal: 12,
    paddingVertical: 10,
    marginBottom: 12,
  },
  lista: {
    paddingBottom: 24,
  },
  listaVazia: {
    color: '#64748B',
    textAlign: 'center',
    padding: 24,
  },
  produto: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#FFFFFF',
    borderRadius: 12,
    padding: 16,
    marginBottom: 10,
  },
  informacoesProduto: {
    flex: 1,
  },
  nomeProduto: {
    color: '#0F172A',
    fontSize: 17,
    fontWeight: 'bold',
  },
  detalhesProduto: {
    color: '#475569',
    fontSize: 15,
    marginTop: 5,
  },
  botaoExcluir: {
    backgroundColor: '#FEE2E2',
    borderRadius: 8,
    paddingHorizontal: 12,
    paddingVertical: 9,
    marginLeft: 12,
  },
  textoExcluir: {
    color: '#B91C1C',
    fontWeight: 'bold',
  },
});
```

## 12. Teste final

1. Abra o aplicativo e confirme que a mensagem de lista vazia aparece.
2. Tente cadastrar um produto com um campo vazio e confira a validação.
3. Cadastre pelo menos três produtos com preços e quantidades diferentes.
4. Confira se os produtos aparecem em ordem alfabética.
5. Pesquise usando somente uma parte do nome.
6. Apague a pesquisa e confirme que todos os produtos reaparecem.
7. Exclua um produto e confirme a operação.
8. Feche completamente o aplicativo e abra-o novamente.
9. Confira se os produtos restantes continuam cadastrados.

## 13. Entendendo as operações CRUD

CRUD é uma sigla para as quatro operações básicas realizadas com dados:

| Operação | Significado | SQL | Situação no projeto |
|---|---|---|---|
| Create | Criar | `INSERT` | Cadastrar produto. |
| Read | Ler | `SELECT` | Listar e pesquisar produtos. |
| Update | Atualizar | `UPDATE` | Proposto nos desafios. |
| Delete | Excluir | `DELETE` | Excluir produto. |

O aplicativo já implementa três dessas quatro operações.

## 14. Desafios graduais

Faça um desafio por vez:

1. Mostre o `id` de cada produto na lista.
2. Destaque com outra cor os produtos cuja quantidade seja zero.
3. Adicione um campo `categoria` à tabela e ao formulário.
4. Adicione um botão para aumentar o estoque em uma unidade usando `UPDATE`.
5. Crie uma tela ou formulário para editar nome, preço e quantidade.
6. Mostre a quantidade total de itens em estoque usando `SUM(quantidade)`.
7. Mostre o número de produtos cadastrados usando `COUNT(*)`.
8. Adicione uma ordenação por menor preço e por maior preço.

> Depois que uma tabela já existe, mudar somente o comando `CREATE TABLE IF NOT EXISTS` não altera sua estrutura. Para adicionar colunas em um aplicativo que já possui dados, estude migrações e o comando `ALTER TABLE`.

## 15. Problemas comuns

| Problema | Possível solução |
|---|---|
| `Unable to resolve expo-sqlite` | Interrompa o Expo, execute `npx expo install expo-sqlite` e inicie novamente. |
| `useSQLiteContext must be used within a SQLiteProvider` | Confira se `TelaProdutos` está dentro de `SQLiteProvider`. |
| `no such table: produtos` | Confira o nome da tabela e se `onInit={criarBanco}` está presente. |
| O produto é salvo, mas não aparece | Execute `await consultarProdutos()` depois do `INSERT`. |
| A lista não aparece ao abrir | Confira o `useEffect` e o `import { useEffect } from 'react'`. |
| O preço com vírgula é rejeitado | Confira `preco.replace(',', '.')` antes da conversão. |
| A quantidade decimal é rejeitada | Isso é intencional: a validação usa `Number.isInteger`. |
| Os dados desapareceram | Reinstalar o aplicativo, limpar os dados do app ou limpar o armazenamento do navegador pode remover o banco local. |
| Uma alteração antiga continua aparecendo | Execute `npx expo start --clear`. |
| O aplicativo exibe uma tela vermelha | Leia a primeira mensagem do erro e confira chaves, parênteses, vírgulas e importações. |

## 16. Teste opcional no navegador

O suporte web do `expo-sqlite` utiliza WebAssembly e exige configuração adicional do Metro e cabeçalhos que permitam `SharedArrayBuffer`. Esse suporte pode ter diferenças em relação ao Android e ao iOS.

Se seu objetivo é aprender SQLite pela primeira vez, conclua o tutorial em um dispositivo ou emulador. Se também precisar da versão web, siga a seção **Web setup** da documentação oficial correspondente à versão do Expo instalada no projeto.

Confira sua versão com:

```bash
npx expo --version
npm list expo expo-sqlite
```

Não copie configurações de outra versão sem conferir a documentação, pois os requisitos do suporte web podem mudar.

## Checklist de aprendizagem

- [ ] Criei e executei um projeto Expo com o template `blank`.
- [ ] Instalei `expo-sqlite` com `npx expo install`.
- [ ] Criei campos controlados com `useState`.
- [ ] Converti e validei os dados digitados.
- [ ] Abri o banco com `SQLiteProvider`.
- [ ] Acessei o banco com `useSQLiteContext`.
- [ ] Criei a tabela com `CREATE TABLE IF NOT EXISTS`.
- [ ] Cadastrei produtos com `runAsync()` e `INSERT`.
- [ ] Passei os valores SQL usando parâmetros `?`.
- [ ] Consultei produtos com `getAllAsync()` e `SELECT`.
- [ ] Exibi os resultados com `FlatList`.
- [ ] Pesquisei pelo nome usando `LIKE`.
- [ ] Excluí um produto usando seu `id`.
- [ ] Confirmei que os dados permanecem depois de reiniciar o aplicativo.

## Referências oficiais

- [Criar um projeto Expo](https://docs.expo.dev/get-started/create-a-project/)
- [SQLite no Expo](https://docs.expo.dev/versions/latest/sdk/sqlite/)
- [Estado de um componente com `useState`](https://react.dev/reference/react/useState)
- [Sincronização com `useEffect`](https://react.dev/reference/react/useEffect)
- [TextInput no React Native](https://reactnative.dev/docs/textinput)
- [FlatList no React Native](https://reactnative.dev/docs/flatlist)
- [Linguagem SQL do SQLite](https://www.sqlite.org/lang.html)
