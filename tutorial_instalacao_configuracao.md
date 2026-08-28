# Tutorial — Instalação e Configuração do Ambiente de Desenvolvimento React Native
### Aula 1 | ARA0089 — Programação para Dispositivos Móveis em Android

Neste tutorial você vai instalar todas as ferramentas necessárias para desenvolver aplicativos com **React Native** usando o **Expo**, entender a estrutura de um projeto, e rodar seu primeiro app "Hello World" diretamente no seu celular.

**Ao final deste tutorial você terá:**
- Node.js instalado e funcionando.
- VS Code instalado e configurado com extensões úteis.
- Um projeto Expo criado e rodando.
- O app **Expo Go** instalado no seu celular, exibindo seu primeiro app.

---

## 1. Instalando o Node.js

O React Native e o Expo dependem do Node.js para rodar as ferramentas de linha de comando (CLI) e gerenciar pacotes.

> ⚠️ **Importante — compatibilidade de versões:** o Expo exige uma versão **mínima** de Node.js que muda a cada nova versão do SDK. Antes de instalar, confira a versão mínima exigida na documentação oficial: [https://docs.expo.dev/get-started/installation/](https://docs.expo.dev/get-started/installation/). Todo o tutorial parte do princípio de que você sempre vai usar as **versões mais atuais e compatíveis entre si** de Node.js, Expo Go e Expo SDK — nunca fixe uma versão antiga "porque funcionou uma vez", pois isso é a causa mais comum de erros de incompatibilidade neste tipo de projeto.

### 1.1. Baixar e instalar

1. Acesse [https://nodejs.org](https://nodejs.org).
2. Baixe a versão **LTS** (Long Term Support) — é a versão estável recomendada e, na prática, é a que mantém compatibilidade com as versões mais recentes do Expo.
3. Execute o instalador:
   - **Windows/Mac:** siga o assistente de instalação, mantendo as opções padrão.
   - **Linux (Ubuntu/Debian):** você também pode instalar via terminal:
     ```bash
     sudo apt update
     sudo apt install nodejs npm
     ```

### 1.2. Verificar a instalação

Abra o terminal (Prompt de Comando, PowerShell, Terminal do Mac/Linux) e digite:

```bash
node -v
npm -v
```

Você deve ver a versão instalada, por exemplo:

```
v22.14.0
10.9.2
```

> **Se o comando não for reconhecido:** feche e reabra o terminal (às vezes é necessário reiniciar o computador para que o PATH seja atualizado).

> **Se você já tinha uma versão antiga do Node.js instalada:** verifique se ela atende ao mínimo exigido pela versão atual do Expo SDK (passo acima). Se estiver desatualizada, desinstale a versão antiga e instale a LTS mais recente antes de continuar — isso evita erros de compatibilidade lá na frente.

---

## 2. Instalando o VS Code

O **Visual Studio Code (VS Code)** é o editor de código mais usado para desenvolvimento com React Native.

### 2.1. Baixar e instalar

1. Acesse [https://code.visualstudio.com](https://code.visualstudio.com).
2. Baixe a versão correspondente ao seu sistema operacional (Windows, Mac ou Linux).
3. Instale seguindo o assistente padrão.

### 2.2. Extensões recomendadas

Abra o VS Code, vá até o ícone de **Extensões** (barra lateral esquerda, ícone de quadrados) e instale:

| Extensão | Para que serve |
|---|---|
| **ES7+ React/Redux/React-Native snippets** | Atalhos para criar componentes React mais rápido |
| **Prettier - Code formatter** | Formatação automática de código |
| **React Native Tools** | Suporte a depuração de projetos React Native |

> **Dica:** para instalar, clique no ícone de extensões, pesquise pelo nome e clique em **Install**.

---

## 3. Instalando o Expo

O **Expo** é um conjunto de ferramentas que simplifica o desenvolvimento em React Native, eliminando a necessidade de configurar Android Studio ou Xcode para começar a programar.

### 3.1. Não é necessário instalar globalmente

Diferente de versões antigas do tutorial, hoje **não é preciso instalar o Expo CLI globalmente**. Ele será baixado automaticamente quando você criar um novo projeto (próximo passo), usando o `npx` (que já vem com o Node.js).

### 3.2. Instalar o app Expo Go no celular

O **Expo Go** é o aplicativo que permite rodar seu projeto diretamente no celular, sem precisar gerar um instalador (`.apk`) a cada teste.

1. No seu celular Android, abra a **Google Play Store**.
2. Pesquise por **Expo Go**.
3. Instale (ou, se já tiver instalado, **atualize**) o aplicativo para a versão mais recente.

> Se você usa iPhone, o processo é o mesmo, mas pela **App Store**.

> ⚠️ **Regra de compatibilidade Expo Go x Expo SDK:** o app Expo Go baixado da loja só roda projetos criados com a **mesma versão (ou versão muito próxima) do Expo SDK** que ele suporta no momento. Como o Expo Go é atualizado periodicamente para acompanhar as versões mais novas do SDK, a combinação mais segura é sempre:
> - **Expo Go atualizado** (mais recente da loja) **+**
> - **Projeto criado com `npx create-expo-app@latest`** (mais recente do SDK)
>
> Se você (ou um colega) usar um projeto mais antigo, criado há muitos meses, o Expo Go atual pode recusar abri-lo com um erro do tipo *"Project is incompatible with this version of Expo Go"*. Veja a solução na seção 7 (Resolução de problemas).

---

## 4. Criando seu primeiro projeto

### 4.1. Criar o projeto

No terminal, navegue até a pasta onde deseja criar seus projetos e execute:

```bash
npx create-expo-app --template
```

> **Por que o template `blank`?** O comando `create-expo-app` sem template usa, por padrão, um modelo com **Expo Router** e navegação por abas já configurada, o que adiciona pastas e arquivos extras (`app/`, telas de exemplo, etc.). Para este primeiro contato com o ambiente, vamos usar o template **blank** (em branco), que gera a estrutura mais simples possível — um único arquivo `App.js` como ponto de partida — ideal para entender o básico antes de avançar para navegação entre telas (assunto de uma aula futura).

Esse comando vai:
- Baixar as dependências necessárias, sempre na versão mais recente do Expo SDK.
- Criar uma pasta chamada `MeuPrimeiroApp` com toda a estrutura inicial do projeto, já com um `package.json` cujas versões de `expo`, `react` e `react-native` são compatíveis entre si e com o Expo Go atual.

Aguarde a instalação terminar (pode levar alguns minutos, dependendo da internet).

> ⚠️ **Dica para todo o semestre:** sempre que precisar instalar uma nova biblioteca no projeto (câmera, mapas, ícones, etc.), use o comando:
> ```bash
> npx expo install nome-da-biblioteca
> ```
> em vez do `npm install nome-da-biblioteca` tradicional. O `npx expo install` consulta a versão do Expo SDK do seu projeto e instala automaticamente a versão da biblioteca que é **compatível** com ela, evitando erros difíceis de depurar por incompatibilidade de versões.

### 4.2. Entrar na pasta do projeto

```bash
cd MeuPrimeiroApp
```

### 4.3. Abrir o projeto no VS Code

Ainda no terminal, dentro da pasta do projeto:

```bash
code .
```

> O comando `code .` abre o VS Code na pasta atual. Se não funcionar, abra o VS Code manualmente e use **File > Open Folder**.

---

## 5. Entendendo a estrutura do projeto

Ao abrir a pasta `MeuPrimeiroApp` no VS Code, você verá uma estrutura parecida com esta (template `blank`):

```
MeuPrimeiroApp/
├── assets/               # imagens, ícones e fontes usados no app
├── node_modules/         # bibliotecas instaladas (não mexer diretamente aqui)
├── .gitignore            # arquivos que o Git deve ignorar
├── App.js                # ponto de entrada do app — a tela inicial
├── app.json              # configurações do projeto Expo (nome, ícone, versão...)
├── babel.config.js       # configuração do compilador Babel
├── package.json          # lista de dependências e scripts do projeto
└── package-lock.json     # trava as versões exatas das dependências
```

### O que você precisa saber, por enquanto:

| Arquivo/Pasta | Função |
|---|---|
| `App.js` | Onde fica o código principal (e, por enquanto, único) da tela do seu app |
| `package.json` | "RG" do projeto: nome, versão, e a lista de bibliotecas usadas |
| `assets/` | Onde você deve colocar imagens e ícones que o app vai usar |
| `node_modules/` | Pasta gerada automaticamente — nunca edite nem apague arquivos aqui manualmente |

> **Não se preocupe em entender 100% da estrutura agora.** Com o template `blank`, tudo começa em `App.js`. Conforme o semestre avançar (e chegarmos ao tema de navegação entre telas), vamos criar novas pastas e arquivos para organizar múltiplas telas.

---

## 6. Rodando o app "Hello World" no celular

### 6.1. Editar a tela inicial (opcional, para praticar)

Abra o arquivo `App.js`, na raiz do projeto, e localize o texto de exemplo. Altere para algo como:

```jsx
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text>Olá, mundo! Meu primeiro app React Native 🚀</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

Salve o arquivo (`Ctrl+S` ou `Cmd+S`).

### 6.2. Iniciar o servidor de desenvolvimento

No terminal, dentro da pasta do projeto, execute:

```bash
npx expo start
```

Isso vai:
- Iniciar o **Metro Bundler** (o servidor que empacota seu código).
- Abrir uma página no navegador com um **QR Code**.

### 6.3. Conectar o celular

1. Certifique-se de que o **celular e o computador estão na mesma rede Wi-Fi**.
2. Abra o app **Expo Go** no celular.
3. Toque em **Scan QR code** (Android) ou use a câmera nativa do iPhone.
4. Aponte para o QR Code exibido no terminal ou navegador.

Em poucos segundos, o app deve carregar no celular, exibindo a mensagem que você escreveu! 🎉

### 6.4. Testando o "Fast Refresh"

Com o app aberto no celular, volte ao VS Code e altere o texto novamente (ex.: mude a mensagem). Salve o arquivo — o app no celular deve atualizar automaticamente, sem precisar reiniciar. Essa funcionalidade se chama **Fast Refresh** e vai acelerar muito o desenvolvimento ao longo do semestre.

---

## 7. Resolução de problemas comuns

| Problema | Possível solução |
|---|---|
| `node` ou `npm` não reconhecido | Reinicie o terminal/computador após instalar o Node.js |
| QR Code não conecta | Verifique se celular e computador estão na mesma rede Wi-Fi |
| Erro de permissão ao instalar pacotes (Mac/Linux) | Evite usar `sudo npm install`; prefira configurar o npm para não exigir permissões de administrador |
| App trava em "Loading..." no celular | Feche o Expo Go, pare o servidor (`Ctrl+C` no terminal) e rode `npx expo start -c` (limpa o cache) |
| Firewall bloqueando conexão | Em redes corporativas/acadêmicas, o Wi-Fi pode bloquear a conexão entre celular e PC; nesse caso, use `npx expo start --tunnel` |
| "Project is incompatible with this version of Expo Go" | O projeto foi criado com uma versão de Expo SDK diferente da suportada pelo Expo Go instalado. Solução: crie o projeto novamente com `npx create-expo-app@latest` (versão mais recente) e mantenha o Expo Go do celular sempre atualizado pela loja de aplicativos |
| Erro ao instalar/usar uma biblioteca (ex.: câmera, mapas) | Sempre instale bibliotecas com `npx expo install nome-da-biblioteca` em vez de `npm install`, para garantir a versão compatível com o SDK do projeto |
| Dúvida se as versões do projeto estão consistentes | Rode `npx expo-doctor` na pasta do projeto — ele verifica automaticamente se há incompatibilidades entre as dependências instaladas |

---

## 8. Tarefa final: salvando o projeto no GitHub

Agora que seu app está rodando, vamos garantir que ele fique salvo com segurança e versionado — isso vai ser essencial ao longo do semestre, especialmente quando você começar a trabalhar em grupo no projeto final.

### 8.1. Criar uma conta no GitHub (se ainda não tiver)

1. Acesse [https://github.com](https://github.com) e crie uma conta gratuita, caso ainda não tenha uma.

### 8.2. Instalar o Git (se ainda não tiver)

1. Acesse [https://git-scm.com/downloads](https://git-scm.com/downloads) e baixe o Git para o seu sistema operacional.
2. Instale seguindo o assistente padrão.
3. Verifique a instalação no terminal:

```bash
git --version
```

### 8.3. Configurar seu usuário no Git (apenas na primeira vez)

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seuemail@exemplo.com"
```

### 8.4. Criar o repositório no GitHub

1. No GitHub, clique no botão **New** (ou no ícone **+** > **New repository**).
2. Dê um nome ao repositório, por exemplo: `meu-primeiro-app`.
3. Deixe como **Public** (ou **Private**, se preferir, mas lembre-se de adicionar o professor como colaborador nesse caso).
4. **Não marque** a opção de criar `README`, `.gitignore` ou licença — vamos enviar esses arquivos direto da nossa máquina.
5. Clique em **Create repository**.

O GitHub vai te mostrar uma URL parecida com:

```
https://github.com/seu-usuario/meu-primeiro-app.git
```

### 8.5. Verificar o `.gitignore`

Os projetos criados com `create-expo-app` já vêm com um arquivo `.gitignore` configurado, que evita subir a pasta `node_modules` (que é pesada e pode ser reinstalada a qualquer momento) e outros arquivos temporários.

Abra o arquivo `.gitignore` na raiz do projeto e confirme que ele contém pelo menos a linha:

```
node_modules/
```

> **Nunca envie a pasta `node_modules` para o GitHub.** Ela é recriada automaticamente com o comando `npm install`.

### 8.6. Inicializar o repositório local e enviar o projeto

No terminal, dentro da pasta do projeto (`MeuPrimeiroApp`), execute os comandos abaixo, um de cada vez:

```bash
git init
git add .
git commit -m "Primeiro commit: projeto Hello World com Expo"
git branch -M main
git remote add origin https://github.com/seu-usuario/meu-primeiro-app.git
git push -u origin main
```

> Substitua a URL do `git remote add origin` pela URL do **seu** repositório, copiada no passo 8.4.

### 8.7. Conferir no GitHub

1. Atualize a página do seu repositório no navegador.
2. Confirme que os arquivos do projeto (como `App.js`, `package.json`, `app.json`) aparecem lá — e que a pasta `node_modules` **não** aparece.

### 8.8. Entrega

Envie o link do seu repositório (ex.: `https://github.com/seu-usuario/meu-primeiro-app`) para o professor, pelo canal indicado em sala de aula.

> **Dica para o restante do semestre:** a cada avanço relevante no projeto do grupo, façam um novo commit (`git add .`, `git commit -m "mensagem descrevendo o que mudou"`, `git push`). Isso cria um histórico do desenvolvimento e facilita muito caso algo dê errado e seja preciso voltar a uma versão anterior.

---

## 9. Checklist final da Aula 1

Antes de encerrar, confirme que você conseguiu:

- [ ] Instalar o Node.js (versão LTS mais recente, compatível com o Expo SDK atual) e verificar com `node -v` e `npm -v`.
- [ ] Instalar o VS Code e as extensões recomendadas.
- [ ] Instalar (ou atualizar) o app Expo Go no celular para a versão mais recente da loja.
- [ ] Criar um projeto com `npx create-expo-app@latest`.
- [ ] Rodar `npx expo start` e visualizar o app no celular via QR Code.
- [ ] Editar o texto da tela e ver a atualização automática (Fast Refresh).
- [ ] Criar uma conta no GitHub e instalar o Git.
- [ ] Criar um repositório e enviar (`push`) o projeto para o GitHub.
- [ ] Enviar o link do repositório para o professor.

Com o ambiente configurado, você já está pronto para começar a explorar a sintaxe JSX e os componentes do React Native na próxima aula!