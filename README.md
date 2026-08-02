<p align="center">
  <img src="assets/bootsplash/logo.png" alt="Word Dynamite" width="160" />
</p>

<h1 align="center">Word Dynamite</h1>

<p align="center">
  <strong>Find the words. Light the fuse. Don't let time explode.</strong>
</p>

<p align="center">
  A word puzzle with dynamite, ice, and bonuses — offline-first, cloud-synced, and animated with Skia.
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

## The game

**Word Dynamite** is a word puzzle where each level is a letter grid waiting to be solved. Build the right words, collect **dynamites**, break the **ice**, and chase the best time — with fuse, smoke, and explosion effects built in **Shopify Skia**.

|                     |                                                                                                    |
| ------------------- | -------------------------------------------------------------------------------------------------- |
| **Play offline**    | Progress and balance live in encrypted SQLite; the sync queue uploads everything when the network returns |
| **Save to the cloud** | Guest, email, or Google — sign-in syncs identity and level history                                |
| **Progress in Deck** | Level blocks, history, and resume from where you left off                                         |
| **Earn dynamites**  | Match bonuses, rewarded ads, and economy validated on the backend                                  |

---

## Stack

| Layer        | Technology                                                |
| ------------ | --------------------------------------------------------- |
| App          | React Native **0.78** · React **19** · TypeScript         |
| UI & motion  | Reanimated · Gesture Handler · Linear Gradient · **Skia** |
| State        | Zustand · Contexts (Auth, Sound, Language, Error)         |
| Persistence  | **op-sqlite** + **SQLCipher**                             |
| Auth         | Firebase Auth (anonymous, email, Google)                  |
| Backend      | REST API (Axios) + local sync queue                       |
| Ads          | Google Mobile Ads (banner, interstitial, rewarded)        |
| Analytics    | Firebase Analytics                                        |
| i18n         | i18next · react-i18next · react-native-localize           |

---

## Architecture (quick look)

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

**Offline-first:** matches and dynamite spends go into a local queue. `SyncWorker` processes by priority (e.g. `SYNC_LEVEL_PROGRESS`), with retry and handling for fatal network/server errors.

**Path aliases** (`src/`): `@screens`, `@components`, `@services`, `@stores`, `@gt/api`, `@gt/database`, `@gt/firebase`, `@animations`, …

---

## Prerequisites

- **Node.js** ≥ 18
- **Yarn** (recommended) or npm
- React Native environment per the official guide: [Set Up Your Environment](https://reactnative.dev/docs/set-up-your-environment)
- **Android:** Android Studio, SDK, emulator/device, **JDK 17**
- **iOS** (macOS): Xcode, CocoaPods (`bundle install` + `pod install`)

---

## Getting started

### 1. Clone and install

```bash
git clone https://github.com/anacabralramos/word-dynamite-react-native-app.git
cd word-dynamite-react-native-app
yarn install
```

### 2. Environment variables

```bash
cp .env.example .env
```

Fill in `.env` (not versioned):

```env
# SQLite
DB_FILE_NAME=game_database.db

# Firebase (Console → Project settings)
FIREBASE_API_KEY=...
FIREBASE_AUTH_DOMAIN=...
FIREBASE_PROJECT_ID=...
FIREBASE_STORAGE_BUCKET=...
FIREBASE_MESSAGING_SENDER_ID=...
FIREBASE_APP_ID=...
FIREBASE_MEASUREMENT_ID=...

# Google Sign-In (optional)
WEB_CLIENT_ID=....apps.googleusercontent.com
```

### 3. Native Firebase

Download from the Firebase Console and place:

| Platform | File                               |
| -------- | ---------------------------------- |
| Android  | `android/app/google-services.json` |
| iOS      | `ios/GoogleService-Info.plist`     |

Enable Authentication (Anonymous, Email/Password, and Google if you want).

### 4. Local API

By default the client points to:

```text
http://localhost:3000/api
```

Adjust in `src/gateways/api/api.ts` if the backend is on another host (on the Android emulator use `10.0.2.2` instead of `localhost`).

### 5. iOS (first time / native deps)

```bash
cd ios
bundle install
bundle exec pod install
cd ..
```

### 6. Run

```bash
# Terminal 1 — Metro
yarn start

# Terminal 2 — device / emulator
yarn android
# or
yarn ios
```

You can also open `android/` in Android Studio or the `.xcworkspace` in Xcode.

---

## Scripts

| Command        | What it does                     |
| -------------- | -------------------------------- |
| `yarn start`   | Metro bundler                    |
| `yarn android` | Run on Android                   |
| `yarn ios`     | Run on iOS                       |
| `yarn lint`    | ESLint                           |
| `yarn test`    | Jest                             |
| `postinstall`  | Apply patches (`patch-package`)  |

---

## `src/` structure

```text
src/
├── screens/          # Home, Game, Deck, Login, Resume, …
├── components/       # Shared UI
├── services/         # Auth, Levels, GameLifecycle, sync/
├── gateways/
│   ├── api/          # HTTP
│   ├── database/     # SQLite + repositories
│   └── firebase/     # Auth / Analytics
├── stores/           # Zustand (session, level, ads)
├── contexts/         # Auth, Sound, Language, Error
├── navigation/       # Root stack
├── i18n/             # pt-BR · en-US · es
├── animations/       # Reanimated helpers
├── theme/            # Visual tokens
└── assets/           # Icons, sounds, backgrounds
```

---

## Product highlights

- **Guest → account:** play as a guest and link progress later (email or Google)
- **Dynamite economy:** local ledger + validation on `POST /levels/progress`
- **Ads:** banner, interstitial, and rewarded (AdMob; test IDs in `app.json` in dev)
- **Sound and music:** independent mute for SFX and soundtrack
- **Splash:** `react-native-bootsplash` — to regenerate from the logo:

```bash
npx react-native-bootsplash generate assets/bootsplash/logo.png \
  --background "#88b0c4" \
  --platforms android,ios
```

---

## Troubleshooting

| Issue                   | Try                                                                |
| ----------------------- | ------------------------------------------------------------------ |
| Metro / weird cache     | `yarn start --reset-cache`                                         |
| Android build           | `cd android && ./gradlew clean`                                    |
| iOS build               | `cd ios && bundle exec pod install` + clean in Xcode               |
| Env not loading         | Confirm `.env` at the root and restart Metro (`react-native-dotenv`) |
| API on Android emulator | Replace `localhost` with `10.0.2.2` in `baseURL`                   |

---

## Internal docs

- [`docs/user-sync-vs-levels-progress.md`](docs/user-sync-vs-levels-progress.md) — when to use `/user/sync` vs `/levels/progress`

---

## Learn more

- [React Native — Getting Started](https://reactnative.dev/docs/getting-started)
- [React Native — Environment Setup](https://reactnative.dev/docs/set-up-your-environment)
- [Firebase Auth](https://rnfirebase.io/auth/usage)
- [op-sqlite](https://ospfranco.github.io/op-sqlite/)
- [Shopify React Native Skia](https://shopify.github.io/react-native-skia/)

---

<p align="center">
  <sub>Built with a short fuse and a long coffee.</sub>
</p>
