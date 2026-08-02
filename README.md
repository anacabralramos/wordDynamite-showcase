<p align="center">
  <img src="assets/bootsplash/logo.png" alt="Word Dynamite" width="160" />
</p>

<h1 align="center">Word Dynamite</h1>

<p align="center">
  <strong>Encontre as palavras. Acenda o pavio. Não deixe o tempo explodir.</strong>
</p>

<p align="center">
  Puzzle de palavras com dinamite, gelo e bônus — offline-first, sincronizado na nuvem e animado com Skia.
</p>

<p align="center">
  <img alt="React Native" src="https://img.shields.io/badge/React%20Native-0.78-61DAFB?style=for-the-badge&logo=react&logoColor=white" />
  <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-5.0-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img alt="Firebase" src="https://img.shields.io/badge/Firebase-Auth-FFCA28?style=for-the-badge&logo=firebase&logoColor=black" />
  <img alt="SQLite" src="https://img.shields.io/badge/SQLite-SQLCipher-003B57?style=for-the-badge&logo=sqlite&logoColor=white" />
</p>

<p align="center">
  <img alt="Platform" src="https://img.shields.io/badge/iOS%20%7C%20Android-native-111111?style=flat-square" />
  <img alt="i18n" src="https://img.shields.io/badge/i18n-pt--BR%20·%20en--US%20·%20es-88b0c4?style=flat-square" />
  <img alt="License" src="https://img.shields.io/badge/license-private-lightgrey?style=flat-square" />
</p>

---

## O jogo

**Word Dynamite** é um word puzzle em que cada nível é uma grade de letras esperando para ser desvendada. Monte as palavras certas, acumule **dinamites**, quebre o **gelo** e dispute o melhor tempo — com efeitos visuais de pavio, fumaça e explosão feitos em **Shopify Skia**.

|                     |                                                                                               |
| ------------------- | --------------------------------------------------------------------------------------------- |
| **Jogue offline**   | Progresso e saldo ficam no SQLite criptografado; a fila de sync sobe tudo quando a rede volta |
| **Salve na nuvem**  | Convidado, e-mail ou Google — login sincroniza identidade e histórico de níveis               |
| **Evolua no Deck**  | Blocos de níveis, histórico e retomada do ponto em que parou                                  |
| **Ganhe dinamites** | Bônus de partida, anúncios recompensados e economia validada no backend                       |

---

## Stack

| Camada       | Tecnologia                                                |
| ------------ | --------------------------------------------------------- |
| App          | React Native **0.78** · React **19** · TypeScript         |
| UI & motion  | Reanimated · Gesture Handler · Linear Gradient · **Skia** |
| Estado       | Zustand · Contexts (Auth, Sound, Language, Error)         |
| Persistência | **op-sqlite** + **SQLCipher**                             |
| Auth         | Firebase Auth (anônimo, e-mail, Google)                   |
| Backend      | API REST (Axios) + fila de sync local                     |
| Ads          | Google Mobile Ads (banner, interstitial, rewarded)        |
| Analytics    | Firebase Analytics                                        |
| i18n         | i18next · react-i18next · react-native-localize           |

---

## Arquitetura (visão rápida)

```text
┌─────────────────────────────────────────────────────────────┐
│  Screens  ·  Components  ·  Hooks  ·  Stores (Zustand)      │
├─────────────────────────────────────────────────────────────┤
│  Services                                                   │
│    Auth · Levels · GameLifecycle · SyncWorker · i18n        │
├──────────────────┬──────────────────┬───────────────────────┤
│  @gt/api         │  @gt/firebase    │  @gt/database         │
│  REST (Axios)    │  Auth / Analytics│  SQLite + SQLCipher   │
│                  │                  │  + sync queue          │
└──────────────────┴──────────────────┴───────────────────────┘
```

**Offline-first:** partidas e gastos de dinamite entram numa fila local. O `SyncWorker` processa por prioridade (ex.: `SYNC_LEVEL_PROGRESS`), com retry e tratamento de erros fatais de rede/servidor.

**Path aliases** (`src/`): `@screens`, `@components`, `@services`, `@stores`, `@gt/api`, `@gt/database`, `@gt/firebase`, `@animations`, …

---

## Pré-requisitos

- **Node.js** ≥ 18
- **Yarn** (recomendado) ou npm
- Ambiente React Native conforme o guia oficial: [Set Up Your Environment](https://reactnative.dev/docs/set-up-your-environment)
- **Android:** Android Studio, SDK, emulador/dispositivo, **JDK 17**
- **iOS** (macOS): Xcode, CocoaPods (`bundle install` + `pod install`)

---

## Começando

### 1. Clone e instale

```bash
git clone https://github.com/anacabralramos/word-dynamite-react-native-app.git
cd word-dynamite-react-native-app
yarn install
```

### 2. Variáveis de ambiente

```bash
cp .env.example .env
```

Preencha o `.env` (não versionado):

```env
# SQLite
DB_FILE_NAME=game_database.db

# Firebase (Console → Configurações do projeto)
FIREBASE_API_KEY=...
FIREBASE_AUTH_DOMAIN=...
FIREBASE_PROJECT_ID=...
FIREBASE_STORAGE_BUCKET=...
FIREBASE_MESSAGING_SENDER_ID=...
FIREBASE_APP_ID=...
FIREBASE_MEASUREMENT_ID=...

# Google Sign-In (opcional)
WEB_CLIENT_ID=....apps.googleusercontent.com
```

### 3. Firebase nativo

Baixe do Firebase Console e coloque:

| Plataforma | Arquivo                            |
| ---------- | ---------------------------------- |
| Android    | `android/app/google-services.json` |
| iOS        | `ios/GoogleService-Info.plist`     |

Ative Authentication (Anonymous, Email/Password e, se quiser, Google).

### 4. API local

Por padrão o client aponta para:

```text
http://localhost:3000/api
```

Ajuste em `src/gateways/api/api.ts` se o backend estiver em outro host (no Android emulator use `10.0.2.2` em vez de `localhost`).

### 5. iOS (primeira vez / deps nativas)

```bash
cd ios
bundle install
bundle exec pod install
cd ..
```

### 6. Rodar

```bash
# Terminal 1 — Metro
yarn start

# Terminal 2 — device / emulator
yarn android
# ou
yarn ios
```

Também dá para abrir `android/` no Android Studio ou o `.xcworkspace` no Xcode.

---

## Scripts

| Comando        | O que faz                        |
| -------------- | -------------------------------- |
| `yarn start`   | Metro bundler                    |
| `yarn android` | Sobe no Android                  |
| `yarn ios`     | Sobe no iOS                      |
| `yarn lint`    | ESLint                           |
| `yarn test`    | Jest                             |
| `postinstall`  | Aplica patches (`patch-package`) |

---

## Estrutura do `src/`

```text
src/
├── screens/          # Home, Game, Deck, Login, Resume, …
├── components/       # UI compartilhada
├── services/         # Auth, Levels, GameLifecycle, sync/
├── gateways/
│   ├── api/          # HTTP
│   ├── database/     # SQLite + repositórios
│   └── firebase/     # Auth / Analytics
├── stores/           # Zustand (sessão, nível, ads)
├── contexts/         # Auth, Sound, Language, Error
├── navigation/       # Root stack
├── i18n/             # pt-BR · en-US · es
├── animations/       # helpers Reanimated
├── theme/            # tokens visuais
└── assets/           # ícones, sons, backgrounds
```

---

## Destaques de produto

- **Guest → conta:** jogue como convidado e vincule progresso depois (e-mail ou Google)
- **Economia de dinamites:** ledger local + validação no `POST /levels/progress`
- **Anúncios:** banner, interstitial e rewarded (AdMob; IDs de teste no `app.json` em dev)
- **Som e trilha:** mute independente de SFX e trilha
- **Splash:** `react-native-bootsplash` — para regenerar a partir do logo:

```bash
npx react-native-bootsplash generate assets/bootsplash/logo.png \
  --background "#88b0c4" \
  --platforms android,ios
```

---

## Troubleshooting

| Problema                | Tente                                                              |
| ----------------------- | ------------------------------------------------------------------ |
| Metro / cache estranho  | `yarn start --reset-cache`                                         |
| Build Android           | `cd android && ./gradlew clean`                                    |
| Build iOS               | `cd ios && bundle exec pod install` + clean no Xcode               |
| Env não carrega         | Confirme `.env` na raiz e reinicie o Metro (`react-native-dotenv`) |
| API no emulador Android | Troque `localhost` por `10.0.2.2` no `baseURL`                     |

---

## Documentação interna

- [`docs/user-sync-vs-levels-progress.md`](docs/user-sync-vs-levels-progress.md) — quando usar `/user/sync` vs `/levels/progress`

---

## Learn more

- [React Native — Getting Started](https://reactnative.dev/docs/getting-started)
- [React Native — Environment Setup](https://reactnative.dev/docs/set-up-your-environment)
- [Firebase Auth](https://rnfirebase.io/auth/usage)
- [op-sqlite](https://ospfranco.github.io/op-sqlite/)
- [Shopify React Native Skia](https://shopify.github.io/react-native-skia/)

---

<p align="center">
  <sub>Feito com pavio curto e café longo.</sub>
</p>
