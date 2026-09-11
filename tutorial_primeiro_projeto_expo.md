# Tutorial — Construindo seu primeiro aplicativo Expo passo a passo

Neste tutorial, você vai criar um aplicativo com **React Native e Expo** aos poucos. Em vez de começar com uma tela completa, vamos adicionar um elemento de cada vez.

Ao terminar cada etapa:

1. salve o arquivo `App.js`;
2. observe a mudança no aplicativo;
3. teste o elemento novo;
4. só então avance para a próxima etapa.

Essa sequência permite entender o papel de cada componente antes de combiná-los em uma interface completa.

## O que você vai aprender

- criar e executar um projeto Expo básico;
- mostrar textos com `Text`;
- organizar elementos com `View`;
- criar estilos usando `StyleSheet`;
- receber dados com `TextInput`;
- atualizar a tela com `useState`;
- responder a toques com `Pressable`;
- exibir conteúdo condicional;
- mostrar imagens com `Image`;
- criar listas dinâmicas com `map`;
- permitir rolagem com `ScrollView`.

## 1. Preparando o projeto

Você precisa ter o **Node.js LTS** instalado. Abra o terminal e execute:

```bash
# Mostra a versão instalada do Node.js.
node -v

# Mostra a versão instalada do gerenciador de pacotes npm.
npm -v
```

Se os comandos exibirem números de versão, crie o projeto:

```bash
# Cria a pasta MeuPrimeiroApp usando o template básico em JavaScript.
npx create-expo-app@latest MeuPrimeiroApp --template blank
```

Entenda o comando:

- `npx` executa uma ferramenta sem exigir instalação global;
- `create-expo-app@latest` utiliza a versão mais recente do criador de projetos;
- `MeuPrimeiroApp` será o nome da pasta;
- `--template blank` cria a estrutura mais simples, sem navegação pronta.

Agora entre na pasta e abra o projeto:

```bash
# Entra na pasta criada pelo comando anterior.
cd MeuPrimeiroApp

# Abre a pasta atual no VS Code.
code .
```

Se `code .` não funcionar, abra o VS Code manualmente e selecione **Arquivo > Abrir Pasta**.

## 2. Iniciando o aplicativo

No terminal, dentro da pasta `MeuPrimeiroApp`, execute:

```bash
# Inicia o servidor de desenvolvimento do Expo.
npx expo start
```

Você pode abrir o aplicativo de uma destas formas:

- leia o QR Code com o Expo Go no Android ou com a câmera do iPhone;
- pressione `a` para abrir um emulador Android configurado;
- pressione `i` para abrir o simulador iOS no macOS;
- pressione `w` para abrir no navegador.

Mantenha o terminal aberto durante os testes. Para encerrá-lo, pressione `Ctrl+C`.

## 3. Etapa 1 — Exibindo o primeiro texto

Abra o arquivo `App.js`, apague o conteúdo existente e digite:

```jsx
// Importa os dois componentes básicos usados nesta etapa.
import { Text, View } from 'react-native';

// App é o componente principal do aplicativo.
export default function App() {
  // return informa o que será mostrado na tela.
  return (
    // View é um contêiner que agrupa outros componentes.
    <View>
      {/* Text é o componente usado para exibir qualquer texto. */}
      <Text>Olá, mundo!</Text>
    </View>
  );
}
```

Salve o arquivo com `Ctrl+S` no Windows/Linux ou `Cmd+S` no macOS. O aplicativo deve atualizar automaticamente e mostrar **Olá, mundo!**.

### Teste antes de continuar

Altere a mensagem para seu nome, salve novamente e confirme que o texto muda.

### O que aprendemos

- `View` organiza e agrupa elementos;
- `Text` mostra textos;
- um componente React é uma função que retorna uma interface em JSX;
- o **Fast Refresh** atualiza o aplicativo quando salvamos o arquivo.

## 4. Etapa 2 — Adicionando mais elementos

Uma `View` pode conter vários elementos. Substitua o código anterior por:

```jsx
import { Text, View } from 'react-native';

export default function App() {
  return (
    <View>
      {/* Cada Text representa um elemento independente. */}
      <Text>Meu primeiro aplicativo</Text>
      <Text>Estou aprendendo React Native.</Text>
      <Text>Esta é a minha primeira tela.</Text>
    </View>
  );
}
```

Salve e observe os três textos. No React Native, textos sempre devem estar dentro de um componente `Text`.

### Teste antes de continuar

Adicione um quarto `Text` com o nome da sua disciplina.

## 5. Etapa 3 — Criando os primeiros estilos

Agora vamos usar `StyleSheet` para alterar a aparência e a posição dos elementos:

```jsx
// StyleSheet permite organizar os estilos do aplicativo.
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    // A propriedade style liga o componente a um estilo criado abaixo.
    <View style={styles.container}>
      <Text style={styles.titulo}>Meu primeiro aplicativo</Text>
      <Text style={styles.texto}>Estou aprendendo React Native.</Text>
    </View>
  );
}

// StyleSheet.create cria um conjunto de estilos reutilizáveis.
const styles = StyleSheet.create({
  container: {
    flex: 1, // Faz a View ocupar toda a tela.
    backgroundColor: '#EEF2FF', // Define a cor de fundo.
    alignItems: 'center', // Centraliza os elementos horizontalmente.
    justifyContent: 'center', // Centraliza os elementos verticalmente.
    padding: 24, // Cria espaço interno nas bordas.
  },
  titulo: {
    color: '#1E1B4B', // Define a cor do texto.
    fontSize: 28, // Define o tamanho da fonte.
    fontWeight: 'bold', // Deixa o texto em negrito.
    textAlign: 'center', // Centraliza o texto.
  },
  texto: {
    color: '#475569',
    fontSize: 16,
    marginTop: 8, // Cria espaço acima do componente.
  },
});
```

Salve e compare a tela com a etapa anterior.

> Os estilos lembram CSS, mas são escritos como objetos JavaScript. Propriedades com hífen no CSS usam camelCase: `background-color` se torna `backgroundColor`.

### Teste antes de continuar

Troque `#EEF2FF` por `#FEF3C7`, altere o `fontSize` e observe o resultado.

## 6. Etapa 4 — Recebendo texto do usuário

Vamos adicionar um `TextInput`. Ele mostra um campo em que o usuário pode digitar:

```jsx
import { StyleSheet, Text, TextInput, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Identificação</Text>

      {/* TextInput cria um campo de entrada de texto. */}
      <TextInput
        style={styles.campo}
        placeholder="Digite seu nome"
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#EEF2FF',
    padding: 24,
  },
  titulo: {
    color: '#1E1B4B',
    fontSize: 28,
    fontWeight: 'bold',
    marginBottom: 16,
  },
  campo: {
    backgroundColor: '#FFFFFF',
    borderWidth: 1, // Define a espessura da borda.
    borderColor: '#A5B4FC',
    borderRadius: 10, // Arredonda os cantos.
    fontSize: 16,
    paddingHorizontal: 14,
    paddingVertical: 12,
  },
});
```

Salve, toque no campo e digite. O aplicativo ainda não utiliza o texto; faremos isso na próxima etapa.

### Teste antes de continuar

Troque o `placeholder` e a cor da borda.

## 7. Etapa 5 — Mostrando o nome dinamicamente

O Hook `useState` guarda um valor que pode mudar. Quando esse valor é atualizado, o React atualiza a tela.

```jsx
// Importa o Hook que guarda valores dinâmicos.
import { useState } from 'react';
import { StyleSheet, Text, TextInput, View } from 'react-native';

export default function App() {
  // nome contém o valor atual; setNome é a função que o atualiza.
  const [nome, setNome] = useState('');

  return (
    <View style={styles.container}>
      {/* As chaves inserem uma expressão JavaScript dentro do JSX. */}
      <Text style={styles.titulo}>Olá, {nome || 'estudante'}!</Text>

      {/* value mostra o estado; onChangeText atualiza o estado ao digitar. */}
      <TextInput
        style={styles.campo}
        placeholder="Digite seu nome"
        value={nome}
        onChangeText={setNome}
      />

      <Text style={styles.explicacao}>
        Texto digitado: {nome || 'nenhum texto ainda'}
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#EEF2FF',
    padding: 24,
  },
  titulo: {
    color: '#1E1B4B',
    fontSize: 28,
    fontWeight: 'bold',
    marginBottom: 16,
  },
  campo: {
    backgroundColor: '#FFFFFF',
    borderWidth: 1,
    borderColor: '#A5B4FC',
    borderRadius: 10,
    fontSize: 16,
    padding: 12,
  },
  explicacao: {
    color: '#475569',
    fontSize: 16,
    marginTop: 12,
  },
});
```

Salve e digite lentamente. A saudação muda a cada letra. O trecho `nome || 'estudante'` usa **estudante** quando o nome está vazio.

### Teste antes de continuar

Apague todo o nome e veja os textos padrão aparecerem novamente.

## 8. Etapa 6 — Criando um botão e um contador

Agora vamos usar `Pressable` para responder a toques e outro estado para guardar um número:

```jsx
import { useState } from 'react';
import { Pressable, StyleSheet, Text, View } from 'react-native';

export default function App() {
  // O contador começa com zero.
  const [minutos, setMinutos] = useState(0);

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Tempo de estudo</Text>
      <Text style={styles.contador}>{minutos} minutos</Text>

      {/* onPress executa a função quando o usuário toca no botão. */}
      <Pressable
        style={styles.botao}
        onPress={() => setMinutos(minutos + 5)}
      >
        <Text style={styles.textoBotao}>Adicionar 5 minutos</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#EEF2FF',
    padding: 24,
  },
  titulo: {
    color: '#1E1B4B',
    fontSize: 28,
    fontWeight: 'bold',
  },
  contador: {
    color: '#334155',
    fontSize: 22,
    marginVertical: 20,
  },
  botao: {
    backgroundColor: '#4F46E5',
    borderRadius: 10,
    paddingHorizontal: 20,
    paddingVertical: 14,
  },
  textoBotao: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```

Salve e toque várias vezes no botão. `minutos + 5` calcula o novo valor e `setMinutos` atualiza a tela.

### Teste antes de continuar

Mude o botão para adicionar 10 minutos. Depois crie outro botão que use `setMinutos(0)`.

## 9. Etapa 7 — Usando conteúdo e estilos condicionais

Podemos mudar texto e estilo de acordo com uma condição:

```jsx
import { useState } from 'react';
import { Pressable, StyleSheet, Text, View } from 'react-native';

export default function App() {
  const [estudando, setEstudando] = useState(true);

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Status</Text>

      {/* A condição escolhe a mensagem e também o estilo. */}
      <Text style={[styles.status, estudando ? styles.ativo : styles.pausa]}>
        {estudando ? 'Estudando agora' : 'Em uma pausa'}
      </Text>

      <Pressable
        style={styles.botao}
        onPress={() => setEstudando(!estudando)}
      >
        <Text style={styles.textoBotao}>Alternar status</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#EEF2FF',
  },
  titulo: {
    color: '#1E1B4B',
    fontSize: 28,
    fontWeight: 'bold',
  },
  status: {
    fontSize: 20,
    fontWeight: 'bold',
    marginVertical: 20,
  },
  ativo: { color: '#15803D' }, // Verde quando estudando é true.
  pausa: { color: '#B45309' }, // Laranja quando estudando é false.
  botao: {
    backgroundColor: '#4F46E5',
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

O operador `condição ? valorSeVerdadeiro : valorSeFalso` é chamado de operador ternário. Já `!estudando` inverte o valor atual.

### Teste antes de continuar

Troque as mensagens e as cores dos dois estados.

## 10. Etapa 8 — Adicionando uma imagem

O template `blank` cria a pasta `assets`. Para mostrar `assets/icon.png`, acrescente `Image` ao código da etapa anterior.

Altere a importação:

```jsx
// Image foi acrescentado à lista de componentes importados.
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
```

Coloque este componente logo após a abertura da `View` principal:

```jsx
{/* require carrega uma imagem que está dentro do projeto. */}
<Image source={require('./assets/icon.png')} style={styles.logo} />
```

Acrescente o estilo `logo` dentro do `StyleSheet.create`:

```jsx
logo: {
  width: 90, // Define a largura da imagem.
  height: 90, // Define a altura da imagem.
  marginBottom: 16,
},
```

Salve e confirme que a imagem aparece acima do título.

### Teste antes de continuar

Altere `width` e `height`. Use valores iguais para evitar distorção.

## 11. Etapa 9 — Criando uma lista dinâmica

Uma lista pode ser gerada a partir de um array. Coloque estes dados antes da função `App`:

```jsx
// Este array contém os dados que serão exibidos.
const topicos = ['Componentes', 'Estilos', 'Conteúdo dinâmico'];
```

Dentro da `View`, adicione:

```jsx
<Text style={styles.titulo}>Conteúdos da aula</Text>

{/* map percorre o array e cria um Text para cada item. */}
{topicos.map((topico, indice) => (
  <Text key={topico} style={styles.itemLista}>
    {indice + 1}. {topico}
  </Text>
))}
```

Acrescente este estilo:

```jsx
itemLista: {
  color: '#334155',
  fontSize: 17,
  paddingVertical: 6,
},
```

O método `map` cria um componente para cada item. A propriedade `key` fornece uma identificação única ao elemento.

### Teste antes de continuar

Acrescente dois textos ao array `topicos`. A interface deve criar os itens automaticamente.

## 12. Etapa 10 — Juntando tudo no aplicativo final

Agora que cada recurso foi testado, substitua o `App.js` pelo código completo:

```jsx
import { useState } from 'react';
import {
  Image,
  Pressable,
  ScrollView,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';
import { StatusBar } from 'expo-status-bar';

const topicos = ['Componentes', 'Estilos', 'Estado', 'Eventos'];

export default function App() {
  // Estados que guardam os valores modificados pelo usuário.
  const [nome, setNome] = useState('');
  const [minutos, setMinutos] = useState(0);
  const [estudando, setEstudando] = useState(true);

  // Define um valor padrão quando o nome estiver vazio.
  const nomeExibido = nome.trim() || 'estudante';

  return (
    // ScrollView permite rolar a tela em aparelhos menores.
    <ScrollView contentContainerStyle={styles.container}>
      <StatusBar style="dark" />

      <View style={styles.card}>
        <Image source={require('./assets/icon.png')} style={styles.logo} />

        <Text style={styles.titulo}>Olá, {nomeExibido}!</Text>
        <Text style={styles.subtitulo}>Meu primeiro projeto React Native</Text>

        <TextInput
          style={styles.campo}
          placeholder="Digite seu nome"
          value={nome}
          onChangeText={setNome}
        />

        <Text style={[styles.status, estudando ? styles.ativo : styles.pausa]}>
          {estudando ? 'Estudando agora' : 'Em uma pausa'}
        </Text>

        <Pressable
          style={styles.botaoSecundario}
          onPress={() => setEstudando(!estudando)}
        >
          <Text style={styles.textoBotaoSecundario}>Alternar status</Text>
        </Pressable>

        <Text style={styles.contador}>Tempo de estudo: {minutos} min</Text>

        <Pressable
          style={styles.botao}
          onPress={() => setMinutos(minutos + 5)}
        >
          <Text style={styles.textoBotao}>Adicionar 5 minutos</Text>
        </Pressable>

        {/* Só mostra a mensagem quando a condição é verdadeira. */}
        {minutos >= 30 && (
          <Text style={styles.meta}>Parabéns! Meta atingida!</Text>
        )}
      </View>

      <View style={styles.card}>
        <Text style={styles.secaoTitulo}>Conteúdos praticados</Text>

        {topicos.map((topico, indice) => (
          <Text key={topico} style={styles.itemLista}>
            {indice + 1}. {topico}
          </Text>
        ))}
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flexGrow: 1,
    justifyContent: 'center',
    backgroundColor: '#EEF2FF',
    padding: 24,
  },
  card: {
    width: '100%',
    maxWidth: 520,
    alignSelf: 'center',
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 20,
    marginBottom: 16,
    elevation: 3,
    shadowColor: '#000000',
    shadowOpacity: 0.12,
    shadowRadius: 8,
    shadowOffset: { width: 0, height: 3 },
  },
  logo: {
    width: 80,
    height: 80,
    alignSelf: 'center',
    marginBottom: 12,
  },
  titulo: {
    color: '#1E1B4B',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  subtitulo: {
    color: '#475569',
    fontSize: 16,
    textAlign: 'center',
    marginTop: 6,
    marginBottom: 20,
  },
  campo: {
    borderWidth: 1,
    borderColor: '#A5B4FC',
    borderRadius: 10,
    padding: 12,
    fontSize: 16,
    marginBottom: 16,
  },
  status: {
    fontSize: 18,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 12,
  },
  ativo: { color: '#15803D' },
  pausa: { color: '#B45309' },
  botaoSecundario: {
    alignItems: 'center',
    borderWidth: 1,
    borderColor: '#4F46E5',
    borderRadius: 10,
    padding: 12,
  },
  textoBotaoSecundario: {
    color: '#4F46E5',
    fontSize: 16,
    fontWeight: 'bold',
  },
  contador: {
    color: '#1E293B',
    fontSize: 18,
    textAlign: 'center',
    marginTop: 22,
    marginBottom: 10,
  },
  botao: {
    alignItems: 'center',
    backgroundColor: '#4F46E5',
    borderRadius: 10,
    padding: 14,
  },
  textoBotao: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
  meta: {
    color: '#15803D',
    fontSize: 16,
    fontWeight: 'bold',
    textAlign: 'center',
    marginTop: 16,
  },
  secaoTitulo: {
    color: '#1E1B4B',
    fontSize: 20,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  itemLista: {
    color: '#334155',
    fontSize: 16,
    paddingVertical: 6,
  },
});
```

## 13. Teste final

1. Digite seu nome e observe a saudação.
2. Apague o nome e confirme que **estudante** aparece.
3. Alterne entre estudo e pausa e observe texto e cor.
4. Adicione minutos até a mensagem de meta aparecer.
5. Confira os itens criados a partir do array.
6. Reduza a janela ou gire o celular para testar a rolagem.

O fluxo observado é a ideia central do React Native:

```text
Ação do usuário → atualização do estado → nova renderização da interface
```

## 14. Desafios

1. Crie um botão para zerar o tempo.
2. Permita diminuir cinco minutos sem chegar a valores negativos.
3. Adicione um campo para o nome do curso.
4. Mude a meta de 30 para 60 minutos.
5. Adicione novos itens ao array `topicos`.

## 15. Problemas comuns

| Problema | Como resolver |
|---|---|
| `node` ou `npm` não é reconhecido | Instale o Node.js LTS e reabra o terminal. |
| O comando `code .` não funciona | Abra a pasta manualmente pelo VS Code. |
| O QR Code não conecta | Confirme a mesma rede Wi-Fi ou execute `npx expo start --tunnel`. |
| A alteração não aparece | Salve o `App.js` ou pressione `r` no terminal. |
| O aplicativo mostra uma tela vermelha | Confira chaves, parênteses, vírgulas e nomes próximos à linha indicada. |
| O cache mantém um erro antigo | Encerre com `Ctrl+C` e execute `npx expo start --clear`. |

## Checklist

- [ ] Testei `View` e `Text` antes de adicionar estilos.
- [ ] Alterei propriedades do `StyleSheet` e observei o resultado.
- [ ] Digitei no `TextInput`.
- [ ] Mostrei o nome dinamicamente usando `useState`.
- [ ] Atualizei um contador com `Pressable`.
- [ ] Testei texto e estilos condicionais.
- [ ] Exibi uma imagem local.
- [ ] Criei elementos a partir de uma lista.
- [ ] Reuni os elementos no aplicativo final.

Parabéns! Você construiu o aplicativo progressivamente e testou cada conceito antes de avançar.

## Referências oficiais

- [Criar um projeto Expo](https://docs.expo.dev/get-started/create-a-project/)
- [Templates do create-expo-app](https://docs.expo.dev/more/create-expo/)
- [Componentes principais do React Native](https://reactnative.dev/docs/components-and-apis)
- [Estilos no React Native](https://reactnative.dev/docs/style)
