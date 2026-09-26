# Tutorial — Login com e-mail e senha usando Firebase Authentication

Neste tutorial, você vai proteger o aplicativo de produtos e usuários criado nas aulas anteriores com um sistema de autenticação. Antes de acessar a tela inicial, a pessoa deverá criar uma conta ou entrar com e-mail e senha.

Ao final, o aplicativo terá:

- criação de conta no Firebase Authentication;
- login com e-mail e senha;
- sessão mantida ao fechar e abrir o aplicativo;
- rotas públicas para entrar e criar uma conta;
- rotas internas exibidas somente para usuários autenticados;
- botão para encerrar a sessão.

Este tutorial continua o projeto concluído no [tutorial 5](5-tutorial_incluir_novos_campos_e_cadastro_usuario.md).

> Neste material, **conta do Firebase** significa a conta Google usada pelo desenvolvedor para acessar o console. **Conta do aplicativo** significa o e-mail e a senha cadastrados pelo usuário no Firebase Authentication. Elas são coisas diferentes.

## 1. O que é Firebase?

O **Firebase** é uma plataforma do Google que oferece serviços de backend prontos para aplicativos web e mobile. Entre eles estão autenticação, bancos de dados, armazenamento de arquivos, hospedagem, notificações e monitoramento.

Usaremos o **Firebase Authentication**, serviço responsável por criar contas, validar credenciais e informar qual usuário está autenticado. Ele evita que o aplicativo precise criar seu próprio servidor para armazenar e comparar senhas.

O Firebase oferece dois planos principais:

| Plano | Cobrança | Indicação |
|---|---|---|
| **Spark** | Gratuito e sem necessidade de cadastrar forma de pagamento | Aulas, testes, protótipos e aplicações dentro das cotas gratuitas |
| **Blaze** | Pagamento conforme o uso, com uma conta de faturamento vinculada | Aplicações que precisam ultrapassar cotas ou usar determinados serviços pagos |

Para este tutorial, o plano **Spark** é suficiente. A autenticação por e-mail e senha faz parte das opções de autenticação disponíveis sem custo, sujeita aos limites de uso do serviço. Não ative o plano Blaze para realizar a aula.

> Planos, cotas e preços podem mudar. Antes de publicar uma aplicação real, consulte a [página oficial de preços](https://firebase.google.com/pricing) e configure alertas de orçamento caso escolha o plano Blaze. Alertas ajudam a acompanhar gastos, mas não funcionam como um bloqueio automático de consumo.

## 2. Entendendo a solução

O fluxo que construiremos será:

```text
Aplicativo aberto
       │
       ▼
Firebase verifica a sessão
       │
       ├── sem usuário ──> Login ──> Criar conta
       │                      │            │
       │                      └──────┬─────┘
       │                             │ sucesso
       └── com usuário ──────────────┘
                                     ▼
                             Telas internas do app
                                     │
                                     └── Sair ──> Login
```

O aplicativo não armazenará senhas no SQLite. O Firebase receberá as credenciais por conexão segura e cuidará do processo de autenticação.

> O CRUD local de usuários criado no tutorial 5 não representa contas de acesso. Aqueles registros são dados locais do aplicativo. As contas deste tutorial ficam no Firebase Authentication e possuem um identificador próprio chamado `uid`.

## 3. Criar um projeto no Firebase

1. Acesse o [console do Firebase](https://console.firebase.google.com/).
2. Entre com uma conta Google.
3. Selecione **Criar um projeto**.
4. Informe um nome, por exemplo, `aula-react-native`.
5. Aceite os termos solicitados e avance.
6. O Google Analytics é opcional para esta aula. Você pode desativá-lo.
7. Selecione **Criar projeto** e aguarde a preparação.

O nome exibido e algumas posições dos botões podem mudar com atualizações do console, mas o processo continuará envolvendo a criação de um projeto e o registro de um aplicativo.

## 4. Registrar o aplicativo

Usaremos o Firebase JavaScript SDK, compatível com Expo Go. Por isso, registre um aplicativo **Web**, mesmo que o projeto também seja executado em Android e iOS.

1. Na visão geral do projeto, selecione o ícone **Web** (`</>`).
2. Informe um apelido, como `App Produtos Expo`.
3. Não é necessário ativar o Firebase Hosting.
4. Selecione **Registrar app**.
5. O console exibirá um objeto chamado `firebaseConfig`. Mantenha essa página aberta ou copie os valores para uso nas próximas etapas.

O objeto terá formato semelhante a este:

```js
const firebaseConfig = {
  apiKey: 'valor-fornecido-pelo-firebase',
  authDomain: 'seu-projeto.firebaseapp.com',
  projectId: 'seu-projeto',
  storageBucket: 'seu-projeto.firebasestorage.app',
  messagingSenderId: '000000000000',
  appId: '1:000000000000:web:0000000000000000000000',
};
```

Esses valores também podem ser consultados depois em **Configurações do projeto > Geral > Seus aplicativos**.

## 5. Ativar login por e-mail e senha

Registrar o aplicativo não ativa automaticamente os métodos de login.

1. No menu do projeto, abra **Authentication**.
2. Selecione **Começar**.
3. Abra a área **Sign-in method** ou **Método de login**.
4. Escolha o provedor **E-mail/senha**.
5. Ative a primeira opção de e-mail e senha.
6. Mantenha desativado o login por link de e-mail, pois ele não será usado nesta aula.
7. Salve a alteração.

Se essa etapa for esquecida, o aplicativo apresentará o erro `auth/operation-not-allowed` ao tentar criar uma conta.

## 6. Instalar as dependências

Na pasta do projeto Expo, execute:

```bash
npx expo install firebase @react-native-async-storage/async-storage
```

O pacote `firebase` conecta o aplicativo ao Firebase Authentication. O `AsyncStorage` será usado pelo SDK para manter a sessão no dispositivo entre as execuções.

O Firebase JavaScript SDK funciona no Expo Go e não exige alterações nos projetos nativos para este exemplo.

## 7. Configurar as variáveis do projeto

Crie o arquivo `.env.local` na raiz do projeto:

```env
EXPO_PUBLIC_FIREBASE_API_KEY=cole-a-api-key
EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN=seu-projeto.firebaseapp.com
EXPO_PUBLIC_FIREBASE_PROJECT_ID=seu-projeto
EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET=seu-projeto.firebasestorage.app
EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=cole-o-sender-id
EXPO_PUBLIC_FIREBASE_APP_ID=cole-o-app-id
```

Substitua cada exemplo pelo valor correspondente do seu `firebaseConfig`. Não use aspas nem espaços ao redor do sinal de igual.

O prefixo `EXPO_PUBLIC_` permite que o Expo disponibilize a variável no código executado pelo aplicativo. Isso também significa que os valores ficam visíveis no pacote final.

> A configuração de um aplicativo Firebase identifica o projeto, mas não funciona como uma senha administrativa. Mesmo assim, nunca coloque chaves privadas, senhas ou credenciais de servidor em variáveis `EXPO_PUBLIC_`. A proteção dos dados Firebase deve ser feita com Authentication, Security Rules e App Check, quando aplicável.

Acrescente `.env.local` ao `.gitignore`:

```gitignore
.env.local
```

Crie também `.env.example`, que pode ser enviado ao GitHub sem os valores da sua turma:

```env
EXPO_PUBLIC_FIREBASE_API_KEY=
EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN=
EXPO_PUBLIC_FIREBASE_PROJECT_ID=
EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET=
EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=
EXPO_PUBLIC_FIREBASE_APP_ID=
```

Depois de criar ou alterar as variáveis, reinicie o Expo:

```bash
npx expo start --clear
```

## 8. Inicializar o Firebase

Crie a pasta `src/services` e o arquivo `src/services/firebase.js`:

```js
import AsyncStorage from '@react-native-async-storage/async-storage';
import { getApp, getApps, initializeApp } from 'firebase/app';
import {
  getAuth,
  getReactNativePersistence,
  initializeAuth,
} from 'firebase/auth';

const firebaseConfig = {
  apiKey: process.env.EXPO_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.EXPO_PUBLIC_FIREBASE_PROJECT_ID,
  storageBucket: process.env.EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.EXPO_PUBLIC_FIREBASE_APP_ID,
};

const app = getApps().length === 0
  ? initializeApp(firebaseConfig)
  : getApp();

let auth;

try {
  auth = initializeAuth(app, {
    persistence: getReactNativePersistence(AsyncStorage),
  });
} catch (error) {
  if (error.code === 'auth/already-initialized') {
    auth = getAuth(app);
  } else {
    throw error;
  }
}

export { auth };
```

O teste com `getApps()` evita inicializar o aplicativo Firebase mais de uma vez durante as atualizações automáticas do Expo. O bloco `try/catch` faz o mesmo para o serviço de autenticação.

`getReactNativePersistence(AsyncStorage)` informa onde o Firebase deve guardar a sessão. A senha não é armazenada por esse código.

## 9. Criar o contexto de autenticação

O contexto permitirá que diferentes telas descubram qual usuário está conectado e usem as ações de entrar, cadastrar e sair.

Crie a pasta `src/contexts` e o arquivo `src/contexts/AuthContext.js`:

```jsx
import { createContext, useContext, useEffect, useState } from 'react';
import {
  createUserWithEmailAndPassword,
  onAuthStateChanged,
  signInWithEmailAndPassword,
  signOut,
} from 'firebase/auth';

import { auth } from '../services/firebase';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [usuario, setUsuario] = useState(null);
  const [carregando, setCarregando] = useState(true);

  useEffect(() => {
    const cancelarObservacao = onAuthStateChanged(auth, (usuarioFirebase) => {
      setUsuario(usuarioFirebase);
      setCarregando(false);
    });

    return cancelarObservacao;
  }, []);

  async function entrar(email, senha) {
    return signInWithEmailAndPassword(auth, email.trim(), senha);
  }

  async function criarConta(email, senha) {
    return createUserWithEmailAndPassword(auth, email.trim(), senha);
  }

  async function sair() {
    return signOut(auth);
  }

  return (
    <AuthContext.Provider
      value={{ usuario, carregando, entrar, criarConta, sair }}
    >
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const contexto = useContext(AuthContext);

  if (!contexto) {
    throw new Error('useAuth deve ser usado dentro de AuthProvider.');
  }

  return contexto;
}
```

O observador `onAuthStateChanged` é executado quando a verificação inicial termina e sempre que alguém entra ou sai. Por isso, não precisamos navegar manualmente para a tela inicial depois do login.

## 10. Traduzir os erros mais comuns

Crie `src/utils/authErrors.js`:

```js
export function obterMensagemDeAutenticacao(codigo) {
  const mensagens = {
    'auth/email-already-in-use': 'Este e-mail já possui uma conta.',
    'auth/invalid-email': 'Informe um e-mail válido.',
    'auth/invalid-credential': 'E-mail ou senha incorretos.',
    'auth/user-disabled': 'Esta conta foi desativada.',
    'auth/weak-password': 'A senha não atende aos requisitos de segurança.',
    'auth/too-many-requests': 'Muitas tentativas. Aguarde e tente novamente.',
    'auth/network-request-failed': 'Não foi possível acessar a internet.',
    'auth/operation-not-allowed': 'Ative o login por e-mail e senha no Firebase.',
  };

  return mensagens[codigo] ?? 'Não foi possível concluir a autenticação.';
}
```

Mensagens genéricas no login evitam revelar se determinado e-mail possui uma conta. Essa prática reduz a possibilidade de enumeração de usuários.

## 11. Criar a tela de login

Crie `src/screens/LoginScreen.js`:

```jsx
import { useState } from 'react';
import {
  ActivityIndicator,
  Alert,
  KeyboardAvoidingView,
  Platform,
  Pressable,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';

import { useAuth } from '../contexts/AuthContext';
import { obterMensagemDeAutenticacao } from '../utils/authErrors';

export default function LoginScreen({ navigation }) {
  const { entrar } = useAuth();
  const [email, setEmail] = useState('');
  const [senha, setSenha] = useState('');
  const [enviando, setEnviando] = useState(false);

  async function fazerLogin() {
    if (!email.trim() || !senha) {
      Alert.alert('Campos obrigatórios', 'Informe o e-mail e a senha.');
      return;
    }

    try {
      setEnviando(true);
      await entrar(email, senha);
    } catch (error) {
      Alert.alert('Não foi possível entrar', obterMensagemDeAutenticacao(error.code));
    } finally {
      setEnviando(false);
    }
  }

  return (
    <KeyboardAvoidingView
      style={styles.container}
      behavior={Platform.OS === 'ios' ? 'padding' : undefined}
    >
      <View style={styles.cartao}>
        <Text style={styles.icone}>🔐</Text>
        <Text style={styles.titulo}>Acessar o aplicativo</Text>
        <Text style={styles.descricao}>
          Entre com a conta cadastrada no Firebase.
        </Text>

        <Text style={styles.rotulo}>E-mail</Text>
        <TextInput
          style={styles.campo}
          value={email}
          onChangeText={setEmail}
          placeholder="aluno@exemplo.com"
          autoCapitalize="none"
          autoCorrect={false}
          keyboardType="email-address"
          textContentType="username"
        />

        <Text style={styles.rotulo}>Senha</Text>
        <TextInput
          style={styles.campo}
          value={senha}
          onChangeText={setSenha}
          placeholder="Digite sua senha"
          autoCapitalize="none"
          secureTextEntry
          textContentType="password"
          onSubmitEditing={fazerLogin}
        />

        <Pressable
          style={[styles.botao, enviando && styles.botaoDesativado]}
          onPress={fazerLogin}
          disabled={enviando}
        >
          {enviando
            ? <ActivityIndicator color="#FFFFFF" />
            : <Text style={styles.textoBotao}>Entrar</Text>}
        </Pressable>

        <Pressable
          style={styles.link}
          onPress={() => navigation.navigate('CriarConta')}
          disabled={enviando}
        >
          <Text style={styles.textoLink}>Ainda não tenho uma conta</Text>
        </Pressable>
      </View>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#F1F5F9',
    padding: 24,
  },
  cartao: {
    backgroundColor: '#FFFFFF',
    borderRadius: 16,
    padding: 24,
  },
  icone: { fontSize: 42, textAlign: 'center' },
  titulo: {
    color: '#1E3A8A',
    fontSize: 24,
    fontWeight: 'bold',
    marginTop: 8,
    textAlign: 'center',
  },
  descricao: {
    color: '#475569',
    marginBottom: 24,
    marginTop: 8,
    textAlign: 'center',
  },
  rotulo: { color: '#334155', fontWeight: 'bold', marginBottom: 6 },
  campo: {
    borderColor: '#CBD5E1',
    borderRadius: 8,
    borderWidth: 1,
    marginBottom: 16,
    padding: 12,
  },
  botao: {
    alignItems: 'center',
    backgroundColor: '#2563EB',
    borderRadius: 8,
    minHeight: 48,
    justifyContent: 'center',
    marginTop: 4,
  },
  botaoDesativado: { opacity: 0.6 },
  textoBotao: { color: '#FFFFFF', fontSize: 16, fontWeight: 'bold' },
  link: { alignItems: 'center', padding: 14 },
  textoLink: { color: '#1D4ED8', fontWeight: 'bold' },
});
```

## 12. Criar a tela de cadastro da conta

Crie `src/screens/SignUpScreen.js`:

```jsx
import { useState } from 'react';
import {
  ActivityIndicator,
  Alert,
  KeyboardAvoidingView,
  Platform,
  Pressable,
  ScrollView,
  StyleSheet,
  Text,
  TextInput,
} from 'react-native';

import { useAuth } from '../contexts/AuthContext';
import { obterMensagemDeAutenticacao } from '../utils/authErrors';

export default function SignUpScreen() {
  const { criarConta } = useAuth();
  const [email, setEmail] = useState('');
  const [senha, setSenha] = useState('');
  const [confirmacao, setConfirmacao] = useState('');
  const [enviando, setEnviando] = useState(false);

  async function cadastrar() {
    if (!email.trim() || !senha || !confirmacao) {
      Alert.alert('Campos obrigatórios', 'Preencha todos os campos.');
      return;
    }

    if (senha !== confirmacao) {
      Alert.alert('Senhas diferentes', 'Digite a mesma senha nos dois campos.');
      return;
    }

    try {
      setEnviando(true);
      await criarConta(email, senha);
    } catch (error) {
      Alert.alert('Não foi possível criar a conta', obterMensagemDeAutenticacao(error.code));
    } finally {
      setEnviando(false);
    }
  }

  return (
    <KeyboardAvoidingView
      style={styles.container}
      behavior={Platform.OS === 'ios' ? 'padding' : undefined}
    >
      <ScrollView
        contentContainerStyle={styles.conteudo}
        keyboardShouldPersistTaps="handled"
      >
        <Text style={styles.titulo}>Criar conta</Text>
        <Text style={styles.descricao}>
          Use um e-mail válido e não compartilhe sua senha.
        </Text>

        <Text style={styles.rotulo}>E-mail</Text>
        <TextInput
          style={styles.campo}
          value={email}
          onChangeText={setEmail}
          placeholder="aluno@exemplo.com"
          autoCapitalize="none"
          autoCorrect={false}
          keyboardType="email-address"
          textContentType="username"
        />

        <Text style={styles.rotulo}>Senha</Text>
        <TextInput
          style={styles.campo}
          value={senha}
          onChangeText={setSenha}
          placeholder="Crie uma senha"
          autoCapitalize="none"
          secureTextEntry
          textContentType="newPassword"
        />

        <Text style={styles.rotulo}>Confirmar senha</Text>
        <TextInput
          style={styles.campo}
          value={confirmacao}
          onChangeText={setConfirmacao}
          placeholder="Repita a senha"
          autoCapitalize="none"
          secureTextEntry
          textContentType="newPassword"
          onSubmitEditing={cadastrar}
        />

        <Pressable
          style={[styles.botao, enviando && styles.botaoDesativado]}
          onPress={cadastrar}
          disabled={enviando}
        >
          {enviando
            ? <ActivityIndicator color="#FFFFFF" />
            : <Text style={styles.textoBotao}>Cadastrar</Text>}
        </Pressable>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#F1F5F9' },
  conteudo: { flexGrow: 1, justifyContent: 'center', padding: 24 },
  titulo: { color: '#1E3A8A', fontSize: 26, fontWeight: 'bold' },
  descricao: { color: '#475569', marginBottom: 24, marginTop: 8 },
  rotulo: { color: '#334155', fontWeight: 'bold', marginBottom: 6 },
  campo: {
    backgroundColor: '#FFFFFF',
    borderColor: '#CBD5E1',
    borderRadius: 8,
    borderWidth: 1,
    marginBottom: 16,
    padding: 12,
  },
  botao: {
    alignItems: 'center',
    backgroundColor: '#2563EB',
    borderRadius: 8,
    minHeight: 48,
    justifyContent: 'center',
    marginTop: 4,
  },
  botaoDesativado: { opacity: 0.6 },
  textoBotao: { color: '#FFFFFF', fontSize: 16, fontWeight: 'bold' },
});
```

Quando `createUserWithEmailAndPassword` conclui o cadastro, o Firebase também autentica a nova conta. O observador criado no contexto perceberá a mudança e mostrará as telas internas.

## 13. Separar as rotas públicas e internas

Substitua `App.js` pelo código abaixo. Ele inclui as telas de produtos e usuários registradas nas aulas anteriores:

```jsx
import { ActivityIndicator, Pressable, StyleSheet, Text, View } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { SQLiteProvider } from 'expo-sqlite';

import { AuthProvider, useAuth } from './src/contexts/AuthContext';
import { initializeDatabase } from './src/database/database';
import HomeScreen from './src/screens/HomeScreen';
import LoginScreen from './src/screens/LoginScreen';
import ProductFormScreen from './src/screens/ProductFormScreen';
import ProductsListScreen from './src/screens/ProductsListScreen';
import SignUpScreen from './src/screens/SignUpScreen';
import UserFormScreen from './src/screens/UserFormScreen';
import UsersListScreen from './src/screens/UsersListScreen';

const Stack = createNativeStackNavigator();

const opcoesPadrao = {
  headerStyle: { backgroundColor: '#EFF6FF' },
  headerTintColor: '#1E3A8A',
  headerTitleStyle: { fontWeight: 'bold' },
};

function RotasPublicas() {
  return (
    <Stack.Navigator screenOptions={opcoesPadrao}>
      <Stack.Screen
        name="Login"
        component={LoginScreen}
        options={{ headerShown: false }}
      />
      <Stack.Screen
        name="CriarConta"
        component={SignUpScreen}
        options={{ title: 'Criar conta' }}
      />
    </Stack.Navigator>
  );
}

function RotasInternas() {
  const { sair } = useAuth();

  return (
    <SQLiteProvider databaseName="produtos.db" onInit={initializeDatabase}>
      <Stack.Navigator screenOptions={opcoesPadrao}>
        <Stack.Screen
          name="Inicio"
          component={HomeScreen}
          options={{
            title: 'Início',
            headerRight: () => (
              <Pressable onPress={sair} hitSlop={12}>
                <Text style={styles.textoSair}>Sair</Text>
              </Pressable>
            ),
          }}
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
        <Stack.Screen
          name="CadastroUsuario"
          component={UserFormScreen}
          options={({ route }) => ({
            title: route.params?.usuario
              ? 'Alterar usuário'
              : 'Cadastrar usuário',
          })}
        />
        <Stack.Screen
          name="ConsultaUsuarios"
          component={UsersListScreen}
          options={{ title: 'Consultar usuários' }}
        />
      </Stack.Navigator>
    </SQLiteProvider>
  );
}

function Navegacao() {
  const { usuario, carregando } = useAuth();

  if (carregando) {
    return (
      <View style={styles.carregamento}>
        <ActivityIndicator size="large" color="#2563EB" />
        <Text style={styles.textoCarregamento}>Verificando sessão...</Text>
      </View>
    );
  }

  return (
    <NavigationContainer>
      {usuario ? <RotasInternas /> : <RotasPublicas />}
    </NavigationContainer>
  );
}

export default function App() {
  return (
    <AuthProvider>
      <Navegacao />
    </AuthProvider>
  );
}

const styles = StyleSheet.create({
  carregamento: {
    alignItems: 'center',
    backgroundColor: '#F1F5F9',
    flex: 1,
    justifyContent: 'center',
  },
  textoCarregamento: { color: '#475569', marginTop: 12 },
  textoSair: { color: '#B91C1C', fontWeight: 'bold' },
});
```

Enquanto o Firebase consulta o armazenamento local, o aplicativo mostra um indicador de carregamento. Isso evita exibir a tela de login rapidamente antes de restaurar uma sessão existente.

Ao sair, `usuario` volta a ser `null`. As rotas internas são removidas e as rotas públicas passam a ser exibidas. Assim, o botão voltar do aparelho não reabre uma tela protegida.

## 14. Conferir a estrutura do projeto

Os novos arquivos estarão organizados assim:

```text
MeuCadastroDeProdutos/
├── .env.example
├── .env.local
├── App.js
└── src/
    ├── contexts/
    │   └── AuthContext.js
    ├── database/
    │   └── database.js
    ├── screens/
    │   ├── HomeScreen.js
    │   ├── LoginScreen.js
    │   ├── ProductFormScreen.js
    │   ├── ProductsListScreen.js
    │   ├── SignUpScreen.js
    │   ├── UserFormScreen.js
    │   └── UsersListScreen.js
    ├── services/
    │   └── firebase.js
    └── utils/
        └── authErrors.js
```

## 15. Executar e testar

Inicie o aplicativo:

```bash
npx expo start --clear
```

Faça o seguinte roteiro de teste:

1. Confirme que a tela de login aparece antes da tela inicial.
2. Tente enviar campos vazios.
3. Abra a tela de cadastro e informe senhas diferentes.
4. Crie uma conta com um e-mail válido.
5. Confirme que a tela inicial aparece depois do cadastro.
6. No console do Firebase, abra **Authentication > Users** e localize a nova conta.
7. Selecione **Sair** e tente entrar com uma senha incorreta.
8. Entre com a senha correta.
9. Feche completamente o aplicativo e abra-o novamente.
10. Confirme que a sessão foi restaurada e que a tela inicial apareceu.
11. Saia e confirme que não é possível voltar às telas internas pelo botão do aparelho.

Cada aluno deve usar um e-mail diferente. Contas de aula podem ser removidas depois em **Authentication > Users** no console do Firebase.

## 16. Problemas comuns

### `auth/operation-not-allowed`

O provedor de e-mail e senha não foi ativado. Repita a seção 5.

### `auth/invalid-api-key` ou erro de configuração

Confira os valores em `.env.local`, verifique o prefixo `EXPO_PUBLIC_` e reinicie com `npx expo start --clear`.

### A sessão desaparece ao recarregar

Confira se `@react-native-async-storage/async-storage` está instalado e se `initializeAuth` recebeu `getReactNativePersistence(AsyncStorage)`.

### `auth/email-already-in-use`

Já existe uma conta com esse e-mail no projeto Firebase. Entre com ela, use outro e-mail ou remova a conta de teste no console.

### `auth/weak-password`

A senha não atende à política configurada no Firebase. Use uma senha mais forte ou consulte **Authentication > Settings > Password policy**.

### O aplicativo fica em “Verificando sessão...”

Confira se o dispositivo tem internet, se as variáveis pertencem ao mesmo projeto e se não há erro no terminal do Expo.

## 17. Segurança e limites desta aula

- não armazene senhas no SQLite, no código ou em arquivos enviados ao GitHub;
- não use uma conta ou senha real importante durante exercícios compartilhados;
- não trate apenas a troca de telas como autorização de dados;
- os dados de produtos e usuários continuam locais no aparelho;
- se os dados forem levados para Firestore ou Realtime Database, crie Security Rules que verifiquem `request.auth`;
- em um aplicativo real, implemente recuperação de senha, verificação de e-mail e política de privacidade;
- acompanhe as cotas e o faturamento antes de ativar serviços pagos.

O login identifica o usuário, mas não transforma automaticamente o banco SQLite em um banco individual e seguro. Todos que utilizarem o aplicativo no mesmo aparelho continuarão acessando o mesmo arquivo local. Separar dados por conta exigiria relacionar registros ao `uid` ou armazená-los em um backend protegido.

## 18. Desafios

1. Mostre `usuario.email` na tela inicial.
2. Peça confirmação antes de encerrar a sessão.
3. Adicione um botão de mostrar ou ocultar a senha.
4. Implemente recuperação de senha com `sendPasswordResetEmail`.
5. Envie a verificação de endereço com `sendEmailVerification`.
6. Relacione cada produto ao `uid` da conta e filtre a consulta por esse identificador.

## Checklist de aprendizagem

- [ ] Criei um projeto no console do Firebase.
- [ ] Registrei um aplicativo Web para usar o JavaScript SDK.
- [ ] Ativei o provedor de e-mail e senha.
- [ ] Instalei Firebase e AsyncStorage.
- [ ] Configurei as variáveis do Expo.
- [ ] Criei o contexto e o observador de autenticação.
- [ ] Implementei cadastro, login e logout.
- [ ] Separei rotas públicas e internas.
- [ ] Testei a persistência da sessão.
- [ ] Sei diferenciar autenticação de autorização de dados.

## Referências oficiais

- [Firebase: adicionar o SDK JavaScript a um projeto](https://firebase.google.com/docs/web/setup)
- [Firebase Authentication com e-mail e senha](https://firebase.google.com/docs/auth/web/password-auth)
- [Referência da API Firebase Authentication](https://firebase.google.com/docs/reference/js/auth)
- [Planos e faturamento do Firebase](https://firebase.google.com/docs/projects/billing/firebase-pricing-plans)
- [Preços e cotas do Firebase](https://firebase.google.com/pricing)
- [Usando Firebase com Expo](https://docs.expo.dev/guides/using-firebase/)
- [Variáveis de ambiente no Expo](https://docs.expo.dev/guides/environment-variables/)
