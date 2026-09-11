# Tutorial — Projeto Expo incremental com estado, estilos e navegação

Neste tutorial, você vai construir um aplicativo de hábitos com **React Native e Expo**. O projeto começa com um único texto e cresce aos poucos. Em cada etapa, adicionaremos somente um conceito novo.

Ao final, o aplicativo terá:

- textos e contêineres;
- estilos criados com `StyleSheet` e aplicados pela propriedade `style`;
- um contador modificado dinamicamente com `useState`;
- aparência e mensagens que mudam de acordo com o estado;
- duas páginas acessadas por um menu de navegação inferior.

> Em React, o nome correto do Hook é `useState`, com **S maiúsculo**. JavaScript diferencia letras maiúsculas de minúsculas.

## Como estudar com este tutorial

Em cada etapa:

1. substitua o conteúdo do arquivo `App.js` pelo código apresentado;
2. salve o arquivo;
3. observe a atualização automática;
4. faça o pequeno experimento sugerido;
5. avance somente depois de entender a mudança.

Não é necessário memorizar o código. O objetivo é relacionar cada trecho ao resultado mostrado na tela.

## 1. Criando o projeto

Confirme que o Node.js e o npm estão instalados:

```bash
node -v
npm -v
```

Crie um projeto Expo básico em JavaScript e entre na pasta criada:

```bash
npx create-expo-app MeuAppDeHabitos --template blank
cd MeuAppDeHabitos
```

Instale ou confira as dependências básicas do projeto:

```bash
npm install
```

Instale também as bibliotecas necessárias para executar e testar o aplicativo no navegador:

```bash
npx expo install react-dom react-native-web @expo/metro-runtime
```

- `react-dom` renderiza a aplicação no navegador;
- `react-native-web` adapta os componentes do React Native para a web;
- `@expo/metro-runtime` fornece o suporte do Metro para a execução web.

Agora instale as bibliotecas que serão usadas no menu de navegação das etapas finais:

```bash
npm install @react-navigation/native @react-navigation/bottom-tabs
npx expo install react-native-screens react-native-safe-area-context
```

O comando `expo install` escolhe versões compatíveis com a versão do Expo utilizada pelo projeto.

Por fim, abra a pasta no VS Code:

```bash
code .
```

O parâmetro `--template blank` cria um projeto pequeno, apropriado para acompanhar a inclusão de cada recurso.

> O `create-expo-app` normalmente já executa a instalação inicial. Mesmo assim, `npm install` é útil para garantir que todas as dependências registradas em `package.json` estejam disponíveis, principalmente quando o projeto foi copiado ou baixado da internet.

Se `code .` não funcionar, abra o VS Code e use **Arquivo > Abrir Pasta**.

## 2. Executando o aplicativo

No terminal, dentro da pasta do projeto, execute:

```bash
npx expo start
```

Espere o terminal mostrar o QR Code e os atalhos do Expo. Depois, você pode:

- ler o QR Code com o Expo Go;
- pressionar `a` para abrir um emulador Android;
- pressionar `i` para abrir o simulador iOS no macOS;
- **pressionar a tecla `w` para abrir e testar o projeto no navegador**.

Para testar pela web, não encerre o comando `npx expo start`. Com o terminal do Expo selecionado, pressione apenas:

```text
w
```

O Expo compilará a versão web e abrirá o endereço local do projeto no navegador. Sempre que você salvar `App.js` ou um arquivo dentro de `src`, a página deverá ser atualizada automaticamente.

Mantenha o terminal aberto. Para interromper o servidor, pressione `Ctrl+C`.

## 3. Etapa 1 — Primeiro elemento da tela

Abra `App.js`, apague o conteúdo e escreva:

```jsx
import { Text } from 'react-native';

export default function App() {
  return <Text>Meu aplicativo de hábitos</Text>;
}
```

`App` é um componente: uma função que retorna a interface. O componente `Text` exibe texto no React Native.

### Experimente

Troque o texto pelo nome de um hábito que você deseja praticar.

## 4. Etapa 2 — Agrupando novos elementos

Uma tela geralmente possui vários elementos. Para agrupá-los, use uma `View`:

```jsx
import { Text, View } from 'react-native';

export default function App() {
  return (
    <View>
      <Text>Meu aplicativo de hábitos</Text>
      <Text>Hábito: beber água</Text>
      <Text>Meta diária: 8 copos</Text>
    </View>
  );
}
```

Observe a relação:

```text
View
├── Text: título
├── Text: hábito
└── Text: meta
```

### Experimente

Adicione um quarto `Text` com uma mensagem de incentivo.

## 5. Etapa 3 — Aplicando estilos com `style`

A propriedade `style` conecta um componente a uma regra visual. O `StyleSheet` ajuda a reunir regras reutilizáveis:

```jsx
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Meu aplicativo de hábitos</Text>

      <View style={styles.cartao}>
        <Text style={styles.habito}>Beber água</Text>
        <Text style={styles.meta}>Meta diária: 8 copos</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#E0F2FE',
    padding: 24,
  },
  titulo: {
    color: '#0C4A6E',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 20,
  },
  cartao: {
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 20,
  },
  habito: {
    color: '#075985',
    fontSize: 22,
    fontWeight: 'bold',
  },
  meta: {
    color: '#475569',
    fontSize: 16,
    marginTop: 8,
  },
});
```

Algumas propriedades importantes:

- `flex: 1` faz o contêiner ocupar o espaço disponível;
- `padding` cria espaço interno;
- `marginBottom` cria espaço externo abaixo do elemento;
- `backgroundColor` altera o fundo;
- `borderRadius` arredonda os cantos.

Os estilos lembram CSS, mas usam objetos JavaScript. Por isso, `background-color` é escrito como `backgroundColor`.

### Experimente

Altere a cor de fundo, o tamanho do título e o arredondamento do cartão, uma propriedade por vez.

## 6. Etapa 4 — Criando uma informação dinâmica com `useState`

Até agora, todos os valores eram fixos. O Hook `useState` permite guardar um valor que pode mudar enquanto o aplicativo está aberto.

Substitua `App.js` por:

```jsx
import { useState } from 'react';
import { Pressable, StyleSheet, Text, View } from 'react-native';

export default function App() {
  // copos é o valor atual; setCopos é a função que o modifica.
  const [copos, setCopos] = useState(0);

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Controle de hidratação</Text>

      <View style={styles.cartao}>
        <Text style={styles.rotulo}>Copos consumidos hoje</Text>
        <Text style={styles.numero}>{copos}</Text>

        <Pressable
          style={styles.botao}
          onPress={() => setCopos(copos + 1)}
        >
          <Text style={styles.textoBotao}>Adicionar um copo</Text>
        </Pressable>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#E0F2FE',
    padding: 24,
  },
  titulo: {
    color: '#0C4A6E',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 20,
  },
  cartao: {
    alignItems: 'center',
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 24,
  },
  rotulo: {
    color: '#475569',
    fontSize: 17,
  },
  numero: {
    color: '#0369A1',
    fontSize: 64,
    fontWeight: 'bold',
    marginVertical: 16,
  },
  botao: {
    backgroundColor: '#0284C7',
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

O fluxo é:

```text
Toque no botão → setCopos atualiza o estado → React redesenha a tela
```

`useState(0)` define o valor inicial como zero. O evento `onPress` executa uma função que soma um ao valor atual.

### Experimente

Faça o botão adicionar dois copos por toque. Depois retorne para um.

## 7. Etapa 5 — Adicionando uma segunda ação

Inclua um botão para reiniciar o contador. Coloque este trecho logo depois do primeiro `Pressable`:

```jsx
<Pressable
  style={styles.botaoSecundario}
  onPress={() => setCopos(0)}
>
  <Text style={styles.textoBotaoSecundario}>Reiniciar</Text>
</Pressable>
```

Acrescente estas regras dentro de `StyleSheet.create`:

```jsx
botaoSecundario: {
  borderColor: '#0284C7',
  borderRadius: 10,
  borderWidth: 1,
  marginTop: 12,
  paddingHorizontal: 20,
  paddingVertical: 12,
},
textoBotaoSecundario: {
  color: '#0284C7',
  fontSize: 16,
  fontWeight: 'bold',
},
```

Agora duas ações modificam o mesmo estado: uma incrementa `copos`; a outra atribui zero.

### Experimente

Adicione alguns copos, toque em **Reiniciar** e confira se o número volta a zero.

## 8. Etapa 6 — Mudando texto e estilo dinamicamente

Vamos mostrar se a meta de oito copos foi alcançada. Adicione estas constantes logo após o `useState`:

```jsx
const metaAtingida = copos >= 8;
const mensagem = metaAtingida ? 'Meta atingida! Muito bem!' : 'Continue bebendo água.';
```

Depois do número, antes dos botões, adicione:

```jsx
<Text
  style={[
    styles.mensagem,
    metaAtingida ? styles.sucesso : styles.pendente,
  ]}
>
  {mensagem}
</Text>
```

Por fim, acrescente os estilos:

```jsx
mensagem: {
  fontSize: 17,
  fontWeight: 'bold',
  marginBottom: 18,
},
sucesso: {
  color: '#15803D',
},
pendente: {
  color: '#B45309',
},
```

O array passado a `style` combina estilos. `styles.mensagem` é sempre aplicado; o segundo estilo depende da condição.

```text
copos menor que 8  → mensagem laranja
copos igual ou maior que 8 → mensagem verde
```

O operador `condição ? valorA : valorB` é chamado de operador ternário.

### Experimente

Toque até chegar a oito copos. Observe que o texto e a cor mudam sem recarregar o aplicativo.

## 9. Etapa 7 — Organizando os arquivos das páginas

Até agora, toda a interface está em `App.js`. A partir desta etapa, cada página será criada em um arquivo separado dentro da pasta `src/screens`.

Adotaremos este padrão:

- nomes de componentes começam com letra maiúscula;
- arquivos de páginas terminam com `Screen.js`;
- o nome do arquivo é igual ao nome do componente;
- todas as páginas ficam dentro de `src/screens`;
- `App.js` fica responsável somente pela configuração geral e pela navegação.

Ao terminar as próximas etapas, a estrutura será:

```text
MeuAppDeHabitos/
├── App.js
└── src/
    └── screens/
        ├── InicioScreen.js
        └── SobreScreen.js
```

No explorador de arquivos do VS Code:

1. crie a pasta `src` na raiz do projeto;
2. dentro de `src`, crie a pasta `screens`;
3. não apague `App.js`.

> O caminho `src/screens` ajuda a separar as páginas dos arquivos de configuração. Em projetos maiores, outras pastas poderão ser acrescentadas dentro de `src`.

## 10. Etapa 8 — Criando a página inicial em arquivo separado

Crie o primeiro arquivo de página:

```text
src/screens/InicioScreen.js
```

Mova para esse arquivo a interface do contador construída nas etapas anteriores. Digite o seguinte conteúdo em **`src/screens/InicioScreen.js`**:

```jsx
import { useState } from 'react';
import { Pressable, StyleSheet, Text, View } from 'react-native';

// Esta função representa somente a página inicial.
export default function InicioScreen() {
  const [copos, setCopos] = useState(0);
  const metaAtingida = copos >= 8;
  const mensagem = metaAtingida
    ? 'Meta atingida! Muito bem!'
    : 'Continue bebendo água.';

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>Controle de hidratação</Text>

      <View style={styles.cartao}>
        <Text style={styles.rotulo}>Copos consumidos hoje</Text>
        <Text style={styles.numero}>{copos}</Text>

        <Text
          style={[
            styles.mensagem,
            metaAtingida ? styles.sucesso : styles.pendente,
          ]}
        >
          {mensagem}
        </Text>

        <Pressable
          style={({ pressed }) => [
            styles.botao,
            pressed && styles.botaoPressionado,
          ]}
          onPress={() => setCopos((valorAtual) => valorAtual + 1)}
        >
          <Text style={styles.textoBotao}>Adicionar um copo</Text>
        </Pressable>

        <Pressable
          style={styles.botaoSecundario}
          onPress={() => setCopos(0)}
        >
          <Text style={styles.textoBotaoSecundario}>Reiniciar</Text>
        </Pressable>
      </View>
    </View>
  );
}

// Estes estilos pertencem somente à página InicioScreen.
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#E0F2FE',
    padding: 24,
  },
  titulo: {
    color: '#0C4A6E',
    fontSize: 28,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 20,
  },
  cartao: {
    width: '100%',
    maxWidth: 520,
    alignItems: 'center',
    alignSelf: 'center',
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 24,
    elevation: 3,
    shadowColor: '#000000',
    shadowOffset: { width: 0, height: 3 },
    shadowOpacity: 0.12,
    shadowRadius: 8,
  },
  rotulo: {
    color: '#475569',
    fontSize: 17,
  },
  numero: {
    color: '#0369A1',
    fontSize: 64,
    fontWeight: 'bold',
    marginVertical: 16,
  },
  mensagem: {
    fontSize: 17,
    fontWeight: 'bold',
    marginBottom: 18,
  },
  sucesso: {
    color: '#15803D',
  },
  pendente: {
    color: '#B45309',
  },
  botao: {
    width: '100%',
    alignItems: 'center',
    backgroundColor: '#0284C7',
    borderRadius: 10,
    paddingHorizontal: 20,
    paddingVertical: 14,
  },
  botaoPressionado: {
    opacity: 0.7,
  },
  textoBotao: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
  botaoSecundario: {
    width: '100%',
    alignItems: 'center',
    borderColor: '#0284C7',
    borderRadius: 10,
    borderWidth: 1,
    marginTop: 12,
    paddingHorizontal: 20,
    paddingVertical: 12,
  },
  textoBotaoSecundario: {
    color: '#0284C7',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```

Observe o `export default` antes da função. Ele permite que `InicioScreen` seja importado por outro arquivo.

Neste momento, a página já existe, mas ainda não está ligada ao `App.js`. Essa ligação será feita depois que as duas páginas estiverem prontas.

## 11. Etapa 9 — Criando a segunda página em arquivo separado

Agora crie o arquivo:

```text
src/screens/SobreScreen.js
```

Digite o seguinte conteúdo em **`src/screens/SobreScreen.js`**:

```jsx
import { StyleSheet, Text, View } from 'react-native';

// Esta função representa somente a página Sobre.
export default function SobreScreen() {
  return (
    <View style={styles.container}>
      <View style={styles.cartao}>
        <Text style={styles.titulo}>Sobre o aplicativo</Text>
        <Text style={styles.paragrafo}>
          Este projeto foi criado para praticar componentes, estilos, estado e
          navegação com React Native e Expo.
        </Text>
        <Text style={styles.paragrafo}>
          A meta sugerida no exemplo é registrar oito copos de água por dia.
        </Text>
      </View>
    </View>
  );
}

// Estes estilos pertencem somente à página SobreScreen.
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#E0F2FE',
    padding: 24,
  },
  cartao: {
    width: '100%',
    maxWidth: 520,
    alignItems: 'center',
    alignSelf: 'center',
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 24,
    elevation: 3,
    shadowColor: '#000000',
    shadowOffset: { width: 0, height: 3 },
    shadowOpacity: 0.12,
    shadowRadius: 8,
  },
  titulo: {
    color: '#0C4A6E',
    fontSize: 26,
    fontWeight: 'bold',
    marginBottom: 16,
    textAlign: 'center',
  },
  paragrafo: {
    color: '#334155',
    fontSize: 17,
    lineHeight: 25,
    marginBottom: 12,
    textAlign: 'center',
  },
});
```

Agora existem duas páginas independentes:

| Arquivo | Componente exportado | Responsabilidade |
|---|---|---|
| `src/screens/InicioScreen.js` | `InicioScreen` | Exibir e atualizar o contador de copos. |
| `src/screens/SobreScreen.js` | `SobreScreen` | Apresentar informações sobre o projeto. |

## 12. Etapa 10 — Incluindo uma logomarca na página Sobre

Vamos acrescentar uma imagem à página **Sobre** para simular a logomarca do aplicativo. Você pode salvar a imagem dentro do projeto ou carregá-la diretamente de um endereço da internet.

### Opção A — Baixar e usar uma imagem local

Esta é a opção recomendada, pois a imagem continuará aparecendo mesmo quando o celular estiver sem internet.

O template do Expo normalmente já possui a pasta `assets`. Se ela não existir, crie-a na raiz do projeto:

```text
MeuAppDeHabitos/
├── App.js
├── assets/
│   └── logo.png
└── src/
    └── screens/
        ├── InicioScreen.js
        └── SobreScreen.js
```

Para obter uma imagem:

1. procure na internet uma imagem que represente o aplicativo, por exemplo, uma gota de água;
2. escolha uma imagem que você tenha permissão para utilizar;
3. baixe preferencialmente um arquivo PNG ou JPG;
4. renomeie o arquivo para `logo.png`;
5. mova o arquivo para a pasta `assets` do projeto.

Para praticar sem procurar uma imagem, você também pode baixar a pequena imagem de exemplo da documentação do React Native. Execute o comando na raiz do projeto:

```bash
curl -L https://reactnative.dev/img/tiny_logo.png -o assets/logo.png
```

Depois, substitua todo o conteúdo de **`src/screens/SobreScreen.js`** por:

```jsx
import { Image, StyleSheet, Text, View } from 'react-native';

export default function SobreScreen() {
  return (
    <View style={styles.container}>
      <View style={styles.cartao}>
        {/* ../../ volta de src/screens até a raiz do projeto. */}
        <Image
          source={require('../../assets/logo.png')}
          style={styles.logo}
        />

        <Text style={styles.titulo}>Sobre o aplicativo</Text>
        <Text style={styles.paragrafo}>
          Este projeto foi criado para praticar componentes, estilos, estado e
          navegação com React Native e Expo.
        </Text>
        <Text style={styles.paragrafo}>
          A meta sugerida no exemplo é registrar oito copos de água por dia.
        </Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#E0F2FE',
    padding: 24,
  },
  cartao: {
    width: '100%',
    maxWidth: 520,
    alignItems: 'center',
    alignSelf: 'center',
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 24,
    elevation: 3,
    shadowColor: '#000000',
    shadowOffset: { width: 0, height: 3 },
    shadowOpacity: 0.12,
    shadowRadius: 8,
  },
  logo: {
    width: 120,
    height: 120,
    resizeMode: 'contain',
    marginBottom: 18,
  },
  titulo: {
    color: '#0C4A6E',
    fontSize: 26,
    fontWeight: 'bold',
    marginBottom: 16,
    textAlign: 'center',
  },
  paragrafo: {
    color: '#334155',
    fontSize: 17,
    lineHeight: 25,
    marginBottom: 12,
    textAlign: 'center',
  },
});
```

O caminho usado em `require` parte do arquivo `SobreScreen.js`:

```text
src/screens/SobreScreen.js → ../../ → raiz → assets/logo.png
```

Se você mudar o nome ou o local da imagem, deverá atualizar esse caminho no código.

### Opção B — Usar uma imagem diretamente da internet

Nesta opção, nenhum arquivo de imagem precisa ser salvo no projeto. O aplicativo precisará de internet para carregar a logomarca.

Em **`src/screens/SobreScreen.js`**, mantenha a importação de `Image`:

```jsx
import { Image, StyleSheet, Text, View } from 'react-native';
```

Troque o componente `Image` da opção anterior por:

```jsx
<Image
  source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
  style={styles.logo}
/>
```

Mantenha também o estilo com largura e altura definidas:

```jsx
logo: {
  width: 120,
  height: 120,
  resizeMode: 'contain',
  marginBottom: 18,
},
```

Para usar outra imagem, substitua somente o endereço entre aspas:

```jsx
<Image
  source={{ uri: 'https://endereco-do-site.com/minha-logo.png' }}
  style={styles.logo}
/>
```

Use um endereço que comece com `https://` e termine diretamente em uma imagem, como `.png`, `.jpg` ou `.jpeg`. O endereço de uma página de pesquisa não funciona como fonte da imagem.

> Imagens remotas precisam ter `width` e `height` definidas no estilo. Caso o endereço deixe de funcionar ou o aparelho fique sem internet, a imagem não será exibida.

### Experimente

Altere `width` e `height` para `80` e depois para `160`. Mantenha os dois valores iguais para evitar que uma logomarca quadrada fique distorcida.

## 13. Etapa 11 — Conferindo a instalação do navegador

As bibliotecas de navegação já foram instaladas no início do tutorial. Antes de continuar, interrompa o Expo com `Ctrl+C` e confira a instalação:

```bash
npm list @react-navigation/native @react-navigation/bottom-tabs
```

Se algum pacote aparecer como ausente ou se você não executou a instalação inicial, use:

```bash
npm install @react-navigation/native @react-navigation/bottom-tabs
npx expo install react-native-screens react-native-safe-area-context
```

Depois, inicie novamente:

```bash
npx expo start
```

Para continuar testando no navegador, aguarde o Expo iniciar e pressione `w` novamente.

O `NavigationContainer` controlará a navegação. O `createBottomTabNavigator` criará o menu inferior.

## 14. Etapa 12 — Ligando as páginas ao menu de navegação

Agora altere somente o arquivo da raiz:

```text
App.js
```

Substitua todo o conteúdo de **`App.js`** pelo código abaixo. As páginas não serão declaradas novamente nesse arquivo; elas serão importadas de `src/screens`:

```jsx
import { StyleSheet } from 'react-native';
import { StatusBar } from 'expo-status-bar';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

// Importa cada página de seu próprio arquivo.
import InicioScreen from './src/screens/InicioScreen';
import SobreScreen from './src/screens/SobreScreen';

// Cria o navegador que exibirá as opções no menu inferior.
const Tab = createBottomTabNavigator();

export default function App() {
  return (
    <>
      <StatusBar style="dark" />

      <NavigationContainer>
        <Tab.Navigator
          screenOptions={{
            headerStyle: styles.cabecalho,
            headerTintColor: '#0C4A6E',
            tabBarActiveTintColor: '#0284C7',
            tabBarInactiveTintColor: '#64748B',
            tabBarLabelStyle: styles.rotuloMenu,
          }}
        >
          <Tab.Screen
            name="Inicio"
            component={InicioScreen}
            options={{
              title: 'Meu hábito',
              tabBarLabel: 'Início',
            }}
          />
          <Tab.Screen
            name="Sobre"
            component={SobreScreen}
            options={{ tabBarLabel: 'Sobre' }}
          />
        </Tab.Navigator>
      </NavigationContainer>
    </>
  );
}

const styles = StyleSheet.create({
  cabecalho: {
    backgroundColor: '#F0F9FF',
  },
  rotuloMenu: {
    fontSize: 13,
    fontWeight: 'bold',
  },
});
```

Revise a estrutura final antes de testar:

```text
MeuAppDeHabitos/
├── App.js                         # Configura o navegador e registra as páginas
└── src/
    └── screens/
        ├── InicioScreen.js        # Página com o contador e o useState
        └── SobreScreen.js         # Página com informações do aplicativo
```

### Entendendo a navegação

- `NavigationContainer` envolve e gerencia toda a navegação;
- `Tab.Navigator` cria o menu de abas;
- cada `Tab.Screen` registra uma opção do menu;
- `name` identifica a rota;
- `component` informa qual componente será exibido;
- `options` personaliza o título e o texto da aba;
- os comandos `import` de `App.js` carregam as páginas de seus respectivos arquivos.

Neste exemplo, o menu apresenta **Início** e **Sobre**. Ao trocar de aba, o navegador escolhe qual componente de página deve aparecer.

O botão principal também possui um estilo dinâmico: `Pressable` fornece o valor `pressed`, e `styles.botaoPressionado` reduz temporariamente a opacidade durante o toque.

Usamos `setCopos((valorAtual) => valorAtual + 1)` no código final. Essa forma calcula o próximo valor a partir do estado mais recente e é recomendada quando a atualização depende do valor anterior.

## 15. Teste final

1. Confirme que o menu apresenta as opções **Início** e **Sobre**.
2. Na página **Início**, adicione alguns copos.
3. Abra **Sobre** e retorne para **Início**.
4. Confira se o contador conserva o valor ao alternar as abas.
5. Alcance oito copos e observe a mudança da mensagem e da cor.
6. Pressione **Reiniciar** e confirme que o estado volta a zero.
7. Abra **Sobre** e confirme que a logomarca aparece acima do título.

## 16. Desafios graduais

Faça um desafio por vez:

1. Troque o hábito de beber água por outro hábito diário.
2. Altere a meta de 8 para 10.
3. Mostre quantos registros faltam para alcançar a meta.
4. Impeça que o contador ultrapasse a meta.
5. Adicione um botão para diminuir o valor sem permitir números negativos.
6. Personalize as cores do cabeçalho e do menu.
7. Crie `src/screens/DicasScreen.js`, exporte o componente `DicasScreen`, importe-o em `App.js` e registre-o em outro `Tab.Screen`.

## 17. Problemas comuns

| Problema | Possível solução |
|---|---|
| `useState is not defined` | Confira se existe `import { useState } from 'react';`. |
| `setCopos is not a function` | Confira a ordem: `const [copos, setCopos] = useState(0);`. |
| O estilo não aparece | Verifique `style={styles.nomeDoEstilo}` e o mesmo nome no `StyleSheet`. |
| Uma página não é encontrada | Confira o nome do arquivo, o `export default` e o caminho usado no `import` de `App.js`. |
| A imagem local não aparece | Confira se o arquivo está em `assets/logo.png` e se o caminho usado é `../../assets/logo.png`. |
| A imagem remota não aparece | Confira a internet, use um endereço `https://` direto e mantenha `width` e `height` no estilo. |
| `Unable to resolve @react-navigation...` | Interrompa o Expo, execute os comandos de instalação e inicie novamente. |
| O menu não aparece | Confira se `Tab.Navigator` contém os dois componentes `Tab.Screen`. |
| O Expo solicita dependências para web | Execute `npx expo install react-dom react-native-web @expo/metro-runtime` e inicie novamente. |
| O aplicativo exibe uma tela vermelha | Leia a primeira mensagem do erro e confira chaves, parênteses, vírgulas e importações. |
| Pressionar `w` não abre o navegador | Clique no terminal em que `npx expo start` está rodando e pressione `w` novamente. |
| Uma alteração antiga continua aparecendo | Execute `npx expo start --clear`. |

## Checklist de aprendizagem

- [ ] Criei e executei um projeto com o template `blank`.
- [ ] Instalei as dependências usando os comandos apresentados no início.
- [ ] Instalei `react-dom`, `react-native-web` e `@expo/metro-runtime`.
- [ ] Pressionei `w` no terminal do Expo e testei o aplicativo no navegador.
- [ ] Exibi elementos com `Text` e os agrupei com `View`.
- [ ] Apliquei estilos por meio da propriedade `style`.
- [ ] Organizei estilos reutilizáveis com `StyleSheet.create`.
- [ ] Criei um valor dinâmico com `useState`.
- [ ] Atualizei o estado a partir do evento `onPress`.
- [ ] Alterei texto e estilo de acordo com uma condição.
- [ ] Criei a pasta padronizada `src/screens`.
- [ ] Criei `InicioScreen.js` e `SobreScreen.js` como arquivos separados.
- [ ] Exportei cada página e importei ambas em `App.js`.
- [ ] Exibi uma logomarca local ou remota na página `SobreScreen`.
- [ ] Instalei e configurei o React Navigation.
- [ ] Naveguei entre duas páginas usando o menu inferior.

## Referências oficiais

- [Criar um projeto Expo](https://docs.expo.dev/get-started/create-a-project/)
- [Estado de um componente com `useState`](https://react.dev/reference/react/useState)
- [Estilos no React Native](https://reactnative.dev/docs/style)
- [Imagens locais e remotas no React Native](https://reactnative.dev/docs/images)
- [Primeiros passos com React Navigation](https://reactnavigation.org/docs/getting-started/)
- [Navegador de abas inferiores](https://reactnavigation.org/docs/bottom-tab-navigator/)
