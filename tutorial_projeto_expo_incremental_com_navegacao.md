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

Crie um projeto Expo básico em JavaScript:

```bash
npx create-expo-app --template blank
cd MeuAppDeHabitos
code .
```

O parâmetro `--template blank` cria um projeto pequeno, apropriado para acompanhar a inclusão de cada recurso.

Se `code .` não funcionar, abra o VS Code e use **Arquivo > Abrir Pasta**.

## 2. Executando o aplicativo

No terminal, dentro da pasta do projeto, execute:

```bash
npx expo start
```

Depois, você pode:

- ler o QR Code com o Expo Go;
- pressionar `a` para abrir um emulador Android;
- pressionar `i` para abrir o simulador iOS no macOS;
- pressionar `w` para abrir no navegador.

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

## 9. Etapa 7 — Preparando duas páginas

Antes de criar o menu, separe a interface em duas funções. Cada função será uma página:

```jsx
function InicioScreen() {
  return (
    <View>
      <Text>Página inicial</Text>
    </View>
  );
}

function SobreScreen() {
  return (
    <View>
      <Text>Sobre o aplicativo</Text>
    </View>
  );
}
```

Usamos o sufixo `Screen`, que significa “tela”, apenas como convenção para reconhecer os componentes que serão páginas.

Na próxima etapa, `InicioScreen` receberá o contador construído anteriormente e `SobreScreen` terá as informações do projeto.

## 10. Etapa 8 — Instalando o navegador

Interrompa o Expo com `Ctrl+C`. No terminal, dentro da pasta `MeuAppDeHabitos`, instale a biblioteca principal, as dependências compatíveis com o Expo e o navegador de abas:

```bash
npm install @react-navigation/native @react-navigation/bottom-tabs
npx expo install react-native-screens react-native-safe-area-context
```

Depois, inicie novamente:

```bash
npx expo start
```

O `NavigationContainer` controlará a navegação. O `createBottomTabNavigator` criará o menu inferior.

## 11. Etapa 9 — Aplicativo completo com menu de navegação

Substitua todo o conteúdo de `App.js` pelo código final:

```jsx
import { useState } from 'react';
import { Pressable, StyleSheet, Text, View } from 'react-native';
import { StatusBar } from 'expo-status-bar';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

// Cria o navegador que exibirá as opções no menu inferior.
const Tab = createBottomTabNavigator();

function InicioScreen() {
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

function SobreScreen() {
  return (
    <View style={styles.container}>
      <View style={styles.cartao}>
        <Text style={styles.tituloSobre}>Sobre o aplicativo</Text>
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
  tituloSobre: {
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
  cabecalho: {
    backgroundColor: '#F0F9FF',
  },
  rotuloMenu: {
    fontSize: 13,
    fontWeight: 'bold',
  },
});
```

### Entendendo a navegação

- `NavigationContainer` envolve e gerencia toda a navegação;
- `Tab.Navigator` cria o menu de abas;
- cada `Tab.Screen` registra uma opção do menu;
- `name` identifica a rota;
- `component` informa qual componente será exibido;
- `options` personaliza o título e o texto da aba.

Neste exemplo, o menu apresenta **Início** e **Sobre**. Ao trocar de aba, o navegador escolhe qual componente de página deve aparecer.

O botão principal também possui um estilo dinâmico: `Pressable` fornece o valor `pressed`, e `styles.botaoPressionado` reduz temporariamente a opacidade durante o toque.

Usamos `setCopos((valorAtual) => valorAtual + 1)` no código final. Essa forma calcula o próximo valor a partir do estado mais recente e é recomendada quando a atualização depende do valor anterior.

## 12. Teste final

1. Confirme que o menu apresenta as opções **Início** e **Sobre**.
2. Na página **Início**, adicione alguns copos.
3. Abra **Sobre** e retorne para **Início**.
4. Confira se o contador conserva o valor ao alternar as abas.
5. Alcance oito copos e observe a mudança da mensagem e da cor.
6. Pressione **Reiniciar** e confirme que o estado volta a zero.

## 13. Desafios graduais

Faça um desafio por vez:

1. Troque o hábito de beber água por outro hábito diário.
2. Altere a meta de 8 para 10.
3. Mostre quantos registros faltam para alcançar a meta.
4. Impeça que o contador ultrapasse a meta.
5. Adicione um botão para diminuir o valor sem permitir números negativos.
6. Personalize as cores do cabeçalho e do menu.
7. Crie uma terceira página chamada **Dicas** e registre-a em outro `Tab.Screen`.

## 14. Problemas comuns

| Problema | Possível solução |
|---|---|
| `useState is not defined` | Confira se existe `import { useState } from 'react';`. |
| `setCopos is not a function` | Confira a ordem: `const [copos, setCopos] = useState(0);`. |
| O estilo não aparece | Verifique `style={styles.nomeDoEstilo}` e o mesmo nome no `StyleSheet`. |
| `Unable to resolve @react-navigation...` | Interrompa o Expo, execute os comandos de instalação e inicie novamente. |
| O menu não aparece | Confira se `Tab.Navigator` contém os dois componentes `Tab.Screen`. |
| O aplicativo exibe uma tela vermelha | Leia a primeira mensagem do erro e confira chaves, parênteses, vírgulas e importações. |
| Uma alteração antiga continua aparecendo | Execute `npx expo start --clear`. |

## Checklist de aprendizagem

- [ ] Criei e executei um projeto com o template `blank`.
- [ ] Exibi elementos com `Text` e os agrupei com `View`.
- [ ] Apliquei estilos por meio da propriedade `style`.
- [ ] Organizei estilos reutilizáveis com `StyleSheet.create`.
- [ ] Criei um valor dinâmico com `useState`.
- [ ] Atualizei o estado a partir do evento `onPress`.
- [ ] Alterei texto e estilo de acordo com uma condição.
- [ ] Separei a interface em dois componentes de página.
- [ ] Instalei e configurei o React Navigation.
- [ ] Naveguei entre duas páginas usando o menu inferior.

## Referências oficiais

- [Criar um projeto Expo](https://docs.expo.dev/get-started/create-a-project/)
- [Estado de um componente com `useState`](https://react.dev/reference/react/useState)
- [Estilos no React Native](https://reactnative.dev/docs/style)
- [Primeiros passos com React Navigation](https://reactnavigation.org/docs/getting-started/)
- [Navegador de abas inferiores](https://reactnavigation.org/docs/bottom-tab-navigator/)
