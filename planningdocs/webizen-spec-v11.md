# Webizen v0.25: Software Development Specification (Version 11)

## 1. Project Overview

**Date**: July 11, 2025  
**Purpose**: Webizen v0.25 is a decentralized social-web browser, refactored from Beaker Browser (Electron-based, Chromium renderer), delivered as a WebExtension (Chrome, Firefox) and Electron desktop app. It enables social interactions via an address book, chat with Context Markup Language (CML) annotations, semantic bookmarks with media processing, RDF-based agreements, media sharing, work tracking, a semantic library, web usage tracking, local Solid pod, local apps, an app store, AI assistants, parental controls, accessibility support, and native eCash wallet functionality with multi-signature (multi-sig) support. It integrates with WebCard Viewer’s Agent Discovery Protocol (ADP) for identity discovery, uses a refactored Cashtab library for eCash-based access and transactions, Chronik for blockchain queries, GUN.eco for chat, WebTorrent/WebRTC/WebSockets for media and communication, Quadstore for local RDF storage, and supports secure backups to IPFS, Google Drive, local HDD, or Solid pods. Access requires a small eCash payment or token until development costs are recouped, then becomes obligation-free. The system is modular, extensible, uses Turtle/N3 for permissions/network/WAC, JSON-LD for marketing/Web 2.0, and RDF/JSON for AI, accessibility, and app store configs, with independent module updates to avoid breaking changes. A post-installation guide assists users in setting up the browser.

### 1.1 Goals
- **Access Control**: Require eCash payment (e.g., 100 XEC) or token until development costs (e.g., 1,000,000 XEC) are met; then obligation-free.
- **Service Activation**: Verify eCash address ownership via native Cashtab wallet.
- **Identity Discovery**: Fetch user details (eCash address, optional WebID) via ADP.
- **Address Book**: Manage contacts from web history or ADP, with agreement-based relationships.
- **Chat and Annotations**: Support 1:1, group chats, and URL annotations with CML.
- **Semantic Bookmarks**: Store browser history or manual bookmarks with media metadata (PDFs, etc.) and WebTorrent magnet links in RDF Schema.
- **Connection Requests**: Negotiate RDF-based agreements (UDHR, CRC terms).
- **Media Sharing**: Share videos/PDFs via WebTorrent, with free previews and paid full access.
- **Content Creation**: Tool for uploading media and associating with ADP/eCash.
- **Work Tracking**: Track active tab time, project associations, Git integration, contribution metadata, multi-sig payments.
- **Semantic Library**: Share resources by topics/groups with faceted search and permissioned access.
- **Web Usage Tracking**: WebExtension tracks usage (toggleable) and syncs with Electron app.
- **Solid Pod**: Host local Solid pod in Electron for storage/sync.
- **Local Apps**: Host Solid, Webizen v0.25, and third-party apps with JSON-to-RDF translator.
- **App Store**: Download and integrate apps with metadata stored in RDF/JSON.
- **AI Assistants**: Support local (LM Studio, Ollama, AnythingLLM) and online (Grok, Gemini, OpenAI, Claude) LLMs with RDF/JSON configs and translation.
- **Parental Controls**: Guardians manage ward accounts (subdomains) with rules (e.g., block adult content).
- **Accessibility**: Support screen readers (NVDA, VoiceOver, JAWS) and TTS AI options (local/online) with RDF/JSON configs.
- **Secure Backups**: Encrypt data with native Cashtab key; store in IPFS, Google Drive, local HDD, or Solid pods.
- **Device Synchronization**: Sync data across devices using encrypted backups or Solid pods.
- **Deployment**: Deliver as WebExtension, Electron app, and demo site on GitHub Pages.
- **Post-Installation Guide**: Step-by-step setup for native Cashtab, app store, ADP, AI/TTS, and accessibility.

### 1.2 Key Features
- **Refactored Beaker Browser**: Leverage Beaker’s Electron/Chromium core, replacing Dat with Webizen’s P2P stack (WebTorrent, GUN.eco, WebRTC, WebSockets).
- **Native Cashtab Wallet**: Integrated eCash wallet with multi-sig support for project payments.
- **App Store**: Discover, download, and integrate apps with RDF/JSON metadata.
- **Modular Design**: Independent modules with versioned interfaces for updates without breaking changes.
- **Extensibility**: Plugin system and dynamic RDF ontology support (e.g., `cashtab#v1`, `appstore#v1`).
- **Data Formats**: Turtle/N3 for permissions/network/WAC/accessibility; JSON-LD for marketing/Web 2.0; RDF/JSON for AI, TTS, and app store configs.
- **Faceted Search**: SPARQL-based search for library resources.
- **Device Sync**: Cross-device consistency via encrypted backups or Solid pods.
- **Access Model**: eCash payment or token until obligation-free.
- **Dual Implementation**: WebExtension (sidebar) and Electron app (main window with Solid pod, local apps).
- **Decentralized**: No server dependencies; uses GUN, WebTorrent, WebRTC, IPFS, Solid.
- **User Setup**: Post-installation guide for native Cashtab, app store, and other features.

### 1.3 Client Requirements
- **Native Cashtab Wallet**: Integrated in Webizen for payments/tokens.
- **Browser/OS**: Chrome (v120+), Firefox (v115+) for WebExtension; Windows, macOS, Linux for Electron.
- **Storage**: IndexedDB (50MB) for Quadstore; 100MB for Solid pod (Electron).
- **Internet**: Required for ADP, Chronik, GUN, WebTorrent, IPFS, Google Drive, online LLMs/TTS.
- **Optional**: LM Studio, Ollama, AnythingLLM for local AI; Google Cloud TTS, AWS Polly for online TTS; Solid pod for sync.
- **Hardware**: Minimum 4GB RAM, 1GHz CPU for Electron (Solid pod, local apps, media processing).
- **Accessibility**: Screen reader support (NVDA, VoiceOver, JAWS); TTS-compatible browser/OS.
- **Dependencies**: Node.js 20 (LTS), npm, libtool, m4, make, g++ (Linux), autoconf, automake (Linux/macOS).

## 2. Architecture

### 2.1 Components
- **Access Control**: Verifies eCash payment/token via native Cashtab/Chronik; tracks obligation status.
- **Identity (ADP)**: Fetches eCash address (`adp:hasEcashAccount`) from DNS TXT + IPFS.
- **Service Activation (Cashtab)**: Signs message to verify eCash address; Chronik validates.
- **Address Book**: Stores contacts from web history or ADP; relationships via agreements.
- **Chat (GUN.eco, WebRTC, WebSockets)**: Real-time 1:1, group chats, and URL annotations with CML.
- **Bookmarks (SemBookmarks)**: JSON-LD bookmarks with media metadata and magnet links.
- **Agreements**: RDF-based connection requests shared via GUN.
- **Media (WebTorrent)**: P2P sharing of videos/PDFs; eCash transactions for full access.
- **Content Creation**: Tool for uploading media and associating with ADP/eCash.
- **Work Tracking**: Tracks active tab time, project associations, Git integration, contribution metadata, multi-sig payments.
- **Semantic Library**: Permissioned resource sharing with faceted search.
- **Web Usage Tracking**: WebExtension tracks usage; syncs with Electron app.
- **Solid Pod**: Local Solid server in Electron for storage/sync.
- **Local Apps**: Host Solid, Webizen v0.25, and third-party apps with JSON-to-RDF translator.
- **App Store**: Discover and install apps with RDF/JSON metadata.
- **AI Assistants**: Local/online LLMs with RDF/JSON configs and translation.
- **Parental Controls**: Guardian-managed ward accounts with rule-based restrictions.
- **Accessibility**: Screen reader compatibility and TTS AI options (local/online) with RDF/JSON configs.
- **Backups**: Encrypted storage in Quadstore, IPFS, Google Drive, local HDD, or Solid pods.
- **Device Sync**: Sync via IPFS/Google Drive/Solid; future mobile vault.
- **UI**: React-based sidebar (WebExtension) or main window (Electron) with Tailwind CSS and ARIA.

### 2.2 Modularity
- **Modules**: Separate directories (`src/modules/`) with versioned interfaces.
- **Interfaces**: Standard module interface (`init`, `handleEvent`, `getData`).
- **Dependency Injection**: `src/services/` provides shared services.
- **Config**: `src/config.js` toggles modules and access settings:
  ```javascript
  export const modules = {
    access: { version: '1.0.0' },
    adp: { version: '1.0.0' },
    cashtab: { version: '1.0.0' },
    chronik: { version: '1.0.0' },
    addressbook: { version: '1.0.0' },
    chat: { version: '1.0.0', gun: true, webrtc: true, websockets: true },
    bookmarks: { version: '1.0.0' },
    agreements: { version: '1.0.0' },
    media: { version: '1.0.0' },
    work: { version: '1.0.0' },
    library: { version: '1.0.0' },
    solid: { version: '1.0.0' },
    apps: { version: '1.0.0' },
    appstore: { version: '1.0.0' },
    translator: { version: '1.0.0' },
    ai: { version: '1.0.0' },
    parental: { version: '1.0.0' },
    accessibility: { version: '1.0.0' },
    backups: { version: '1.0.0', ipfs: true, googleDrive: true, local: true, solid: true },
  };
  export const accessConfig = {
    paymentAmount: '100 XEC',
    tokenId: 'token:abc123...',
    fundAddress: 'ecash:qp...socialwebfund',
    developmentCost: '1000000 XEC',
    obligationFree: false,
  };
  ```
- **Event Bus**: `src/services/eventBus.js` for module communication:
  ```javascript
  export const eventBus = {
    on(event, callback) { /* ... */ },
    emit(event, data) { /* ... */ },
  };
  ```
- **Platform-Specific Logic**: `src/platforms/` isolates WebExtension and Electron code.

### 2.3 File Structure
```
socialweb/
├── public/
│   ├── index.html          # Demo site entry
│   ├── sidebar.html        # WebExtension sidebar
│   ├── icon.png            # Extension/app icon
│   └── manifest.json       # WebExtension manifest
├── src/
│   ├── components/         # React components
│   │   ├── Sidebar.js      # WebExtension sidebar UI
│   │   ├── MainWindow.js   # Electron main window UI
│   │   ├── Access.js       # Access control UI with native Cashtab
│   │   ├── AddressBook.js  # Address book UI
│   │   ├── Chat.js         # Chat and annotations
│   │   ├── Bookmarks.js    # Semantic bookmarks
│   │   ├── Agreements.js   # Connection requests
│   │   ├── Media.js        # Media sharing/creation
│   │   ├── Work.js         # Work tracking with multi-sig
│   │   ├── Library.js      # Semantic library
│   │   ├── Tracking.js     # Web usage tracking
│   │   ├── Apps.js         # Local app hosting
│   │   ├── AppStore.js     # App store UI
│   │   ├── AI.js           # AI assistant UI
│   │   ├── ParentalControls.js # Parental controls UI
│   │   └── Accessibility.js # Accessibility controls UI
│   ├── modules/
│   │   ├── access/         # Payment/token access control
│   │   │   └── index.js
│   │   ├── adp/            # ADP fetching
│   │   │   └── index.js
│   │   ├── cashtab/        # Native eCash wallet with multi-sig
│   │   │   └── index.js
│   │   ├── chronik/        # Chronik queries
│   │   │   └── index.js
│   │   ├── addressbook/    # Address book management
│   │   │   └── index.js
│   │   ├── chat/           # GUN, WebRTC, WebSockets, CML
│   │   │   ├── gun.js
│   │   │   ├── webrtc.js
│   │   │   ├── websockets.js
│   │   │   └── index.js
│   │   ├── bookmarks/      # Semantic bookmarks with media processing
│   │   │   └── index.js
│   │   ├── agreements/     # Connection requests
│   │   │   └── index.js
│   │   ├── media/          # WebTorrent media sharing
│   │   │   └── index.js
│   │   ├── work/           # Work tracking and Git integration
│   │   │   └── index.js
│   │   ├── library/        # Semantic library with faceted search
│   │   │   └── index.js
│   │   ├── solid/          # Local Solid pod server
│   │   │   └── index.js
│   │   ├── apps/           # Local app hosting
│   │   │   └── index.js
│   │   ├── appstore/       # App store discovery and installation
│   │   │   └── index.js
│   │   ├── translator/     # JSON-to-RDF translator
│   │   │   └── index.js
│   │   ├── ai/             # AI assistant integration
│   │   │   └── index.js
│   │   ├── parental/       # Parental controls
│   │   │   └── index.js
│   │   ├── accessibility/  # Screen reader and TTS support
│   │   │   └── index.js
│   │   ├── backups/        # IPFS, Google Drive, local, Solid
│   │   │   └── index.js
│   ├── services/
│   │   ├── quadstore.js    # Quadstore setup
│   │   ├── jsonld.js       # JSON-LD parsing
│   │   ├── turtle.js       # Turtle/N3 parsing
│   │   ├── ipfs.js         # IPFS client
│   │   ├── webtorrent.js   # WebTorrent client
│   │   ├── webrtc.js       # WebRTC signaling
│   │   ├── websockets.js   # WebSocket client
│   │   ├── permissions.js  # Turtle/N3 permission system (WAC)
│   │   └── eventBus.js     # Event bus
│   ├── platforms/
│   │   ├── webextension/   # WebExtension-specific logic
│   │   │   └── index.js
│   │   └── electron/       # Electron-specific logic
│   │       ├── main.js     # Electron main process
│   │       └── index.js
│   ├── apps/
│   │   ├── configs/        # Third-party app config files
│   │   │   └── themeforest-app1.json
│   │   ├── store/          # App store metadata
│   │   │   └── example-app.json
│   │   ├── installed/      # Installed apps
│   │   │   └── example-app/
│   ├── ai/
│   │   ├── configs/        # AI assistant config files
│   │   │   ├── grok.json
│   │   │   ├── gemini.json
│   │   │   ├── openai.json
│   │   │   ├── claude.json
│   │   │   ├── lmstudio.json
│   │   │   ├── ollama.json
│   │   │   └── anythingllm.json
│   ├── accessibility/
│   │   ├── configs/        # TTS config files
│   │   │   ├── web-speech.json
│   │   │   ├── google-tts.json
│   │   │   └── aws-polly.json
│   ├── ontologies/
│   │   ├── SemBookmarks-v1.ttl # Versioned bookmark ontology
│   │   ├── cml-v1.ttl         # Versioned CML ontology
│   │   ├── ai-v1.ttl          # Versioned AI ontology
│   │   ├── parental-v1.ttl     # Versioned parental ontology
│   │   ├── accessibility-v1.ttl # Versioned accessibility ontology
│   │   ├── cashtab-v1.ttl      # Versioned Cashtab ontology
│   │   ├── appstore-v1.ttl     # Versioned app store ontology
│   ├── config.js           # Module and access configuration
│   ├── App.js              # Main React app
│   └── background.js       # WebExtension background script
├── docs/
│   ├── post-installation.md # Post-installation guide
├── tests/
│   ├── unit/               # Unit tests
│   ├── integration/        # Integration tests
│   ├── e2e/                # End-to-end tests
├── package.json            # Dependencies and scripts
├── tailwind.config.js      # Tailwind config
├── postcss.config.js       # PostCSS config
└── README.md               # Documentation
```

## 3. Technology Stack

- **WebExtension**: WebExtensions API, React 18.3.0, Tailwind CSS 3.4.0.
- **Electron**: Electron 32.1.0, React 18.3.0, Tailwind CSS 3.4.0.
- **Identity/Data**: N3.js 1.27.0, Quadstore 11.0.0, quadstore-comunica 4.0.0, @comunica/query-sparql 3.0.0, jsonld.js 8.3.2, ipfs-http-client 62.0.0, @inrupt/solid-client 2.0.0, @inrupt/solid-client-authn-browser 2.0.0, node-solid-server 6.0.0, pdf.js 4.0.0.
- **Communication**: GUN.eco 0.2020.1236, WebTorrent 2.1.0, WebRTC (native), WebSockets (ws 8.18.0).
- **eCash**: Native Cashtab (refactored from `@cashtab/wallet-lib`), Chronik (chronik-client 0.11.0).
- **Backups**: CryptoJS 4.1.1, googleapis 144.0.0.
- **AI**: LM Studio (`http://localhost:1234/v1/chat/completions`), Ollama (`http://localhost:11434`), AnythingLLM (local), Grok (`https://x.ai/api`), Gemini, OpenAI, Claude.
- **Accessibility**: Web Speech API (native), Google Cloud TTS, AWS Polly.
- **Apps**: `cimba` (updated fork), ThemeForest JSON-based apps, app store.
- **Build Tools**: Vite 5.0.0, electron-builder 24.0.0.
- **Node.js**: 20 (LTS).
- **RDF Vocabularies**:
  - SemBookmarks (`https://mediaprophet.github.io/init-draft-standards-wip/SemBookmarks#v1`).
  - ActivityStreams (`https://www.w3.org/ns/activitystreams#`).
  - ADP (`https://webcivics.github.io/adp/ontdev/adp#`).
  - Agreements (`https://mediaprophet.github.io/init-draft-standards-wip/agreements#v1`).
  - Media (`https://mediaprophet.github.io/init-draft-standards-wip/media#v1`).
  - Work (`https://mediaprophet.github.io/init-draft-standards-wip/work#v1`).
  - Library (`https://mediaprophet.github.io/init-draft-standards-wip/library#v1`).
  - Access (`https://mediaprophet.github.io/init-draft-standards-wip/access#v1`).
  - AI (`https://mediaprophet.github.io/init-draft-standards-wip/ai#v1`).
  - Parental (`https://mediaprophet.github.io/init-draft-standards-wip/parental#v1`).
  - CML (`https://mediaprophet.github.io/init-draft-standards-wip/cml#v1`).
  - Accessibility (`https://mediaprophet.github.io/init-draft-standards-wip/accessibility#v1`).
  - Cashtab (`https://mediaprophet.github.io/init-draft-standards-wip/cashtab#v1`).
  - AppStore (`https://mediaprophet.github.io/init-draft-standards-wip/appstore#v1`).

## 4. Refactoring Beaker Browser and Cashtab

### 4.1 Beaker Baseline
- **Source**: Fork `github.com/beakerbrowser/beaker` (MIT license, Electron-based, Chromium renderer, Dat protocol).
- **Key Features Retained**:
  - Electron/Chromium core for cross-platform rendering.
  - P2P capabilities (adapted to WebTorrent/GUN.eco).
  - Plugin system for extensibility (aligned with Webizen’s modules).
- **Features Removed**:
  - Dat-specific APIs (`dat://` protocol, `bkr` CLI).
  - Beaker’s built-in editor and site creation tools (replaced by Webizen’s media/content creation).
  - Legacy UI (`app/shell-window`, `app/builtin-pages`).
- **Challenges**:
  - Electron’s process-level sandbox issues (restore via `webPreferences: { sandbox: true }`).
  - Module errors from outdated dependencies (use `npm run rebuild`).
  - Limited mobile support (plan for future vault integration).

### 4.2 Cashtab Refactor
- **Source**: Refactor `@cashtab/wallet-lib` into `modules/cashtab`.
- **Key Features**:
  - Native eCash wallet for transactions and message signing.
  - Multi-sig support using OP_CHECKMULTISIG:
    ```javascript
    import { ChronikClient } from 'chronik-client';
    const chronik = new ChronikClient('https://chronik.be.cash');
    async function createMultiSigWallet(signers, required, purpose) {
      const script = `OP_${required}_OP_${signers.length}_OP_CHECKMULTISIG`;
      const wallet = await chronik.script(script).create();
      return { address: wallet.address, script, purpose };
    }
    async function signMultiSigTx(wallet, txData, signatures) {
      // Validate signatures against wallet.script
      return chronik.tx(txData).sign(signatures);
    }
    ```
  - Store wallet data in Quadstore (Turtle/N3).
- **Integration**: Use with `modules/work` and `modules/apps` for project payments.
- **Challenges**: Ensure secure key management; test multi-sig with Chronik.

### 4.3 App Store Specification
- **Discovery**: Fetch app metadata from IPFS or GitHub via `modules/appstore`.
- **Metadata**: JSON-LD with validation:
  ```json
  {
    "@context": "https://mediaprophet.github.io/init-draft-standards-wip/appstore#v1",
    "@id": "https://ipfs.io/ipfs/Qm.../app1",
    "@type": "appstore:App",
    "appstore:name": "Example App",
    "appstore:version": "1.0.0",
    "appstore:downloadUrl": "https://ipfs.io/ipfs/Qm...",
    "appstore:permissions": ["storage", "network"],
    "appstore:category": "Social"
  }
  ```
- **Installation**: Download to `apps/installed/`; integrate via `modules/apps`.
- **Security**: Hash verification, sandboxed execution (iframe/Web Worker).
- **Social Features**: Share app recommendations via GUN.eco.

### 4.4 Refactoring Plan
- **Fork Beaker**:
  - Fork `github.com/beakerbrowser/beaker` to `github.com/mediaprophet/socialweb`.
  - Update `package.json`:
    ```json
    {
      "name": "webizen",
      "version": "0.25.0",
      "dependencies": {
        "n3": "^1.27.0",
        "quadstore": "^11.0.0",
        "quadstore-comunica": "^4.0.0",
        "@comunica/query-sparql": "^3.0.0",
        "jsonld": "^8.3.2",
        "ipfs-http-client": "^62.0.0",
        "@inrupt/solid-client": "^2.0.0",
        "@inrupt/solid-client-authn-browser": "^2.0.0",
        "node-solid-server": "^6.0.0",
        "pdf.js": "^4.0.0",
        "crypto-js": "^4.1.1",
        "googleapis": "^144.0.0",
        "gun": "^0.2020.1236",
        "webtorrent": "^2.1.0",
        "ws": "^8.18.0",
        "chronik-client": "^0.11.0",
        "react": "^18.3.0",
        "react-dom": "^18.3.0",
        "tailwindcss": "^3.4.0",
        "vite": "^5.0.0",
        "electron": "^32.1.0",
        "electron-builder": "^24.0.0"
      },
      "scripts": {
        "start": "electron src/platforms/electron/main.js",
        "build": "vite build",
        "watch": "vite watch",
        "rebuild": "electron-rebuild",
        "test": "jest"
      },
      "engines": {
        "node": ">=20.0.0"
      }
    }
    ```
- **Remove Dat Dependencies**:
  - Delete `app/dat`, `scripts/dat`, and `bkr` CLI.
  - Replace with `modules/webtorrent`, `modules/gun`, `modules/webrtc`, `modules/websockets`.
- **Refactor Cashtab**:
  - Integrate `@cashtab/wallet-lib` into `modules/cashtab`.
  - Add multi-sig support for project payments.
- **Update UI**:
  - Replace Beaker’s `app/shell-window` and `app/builtin-pages` with Webizen’s `src/components/` (React 18, Tailwind CSS, ARIA).
  - Adapt `app/background-process` to `src/background.js` for WebExtension.
- **Security**:
  - Restore Electron sandboxing (`webPreferences: { sandbox: true }`).
  - Sanitize inputs and limit permissions (`webRequest`, `tabs`, `history`).

## 5. Development Phases

### Phase 1: Core Infrastructure and Beaker/Cashtab Refactor (Weeks 1-4)
- **Objective**: Fork Beaker, refactor Cashtab, set up Webizen’s modular structure, and integrate Electron core.
- **Tasks**:
  1. Fork `github.com/beakerbrowser/beaker` to `github.com/mediaprophet/socialweb`.
  2. Update `package.json` with Node.js 20 and dependencies.
  3. Remove Dat-specific code (`app/dat`, `scripts/dat`, `bkr` CLI).
  4. Refactor `@cashtab/wallet-lib` into `modules/cashtab` with multi-sig support.
  5. Configure `manifest.json` for WebExtension:
     ```json
     {
       "name": "Webizen v0.25",
       "version": "0.25.0",
       "permissions": ["storage", "activeTab", "sidebarAction", "tabs", "history", "webRequest", "webRequestBlocking", "https://*/*"],
       "sidebar_action": { "default_panel": "sidebar.html", "default_icon": "icon.png" },
       "background": { "scripts": ["background.js"], "persistent": true },
       "browser_action": { "default_icon": "icon.png" }
     }
     ```
  6. Configure Electron in `platforms/electron/main.js`:
     ```javascript
     const { app, BrowserWindow } = require('electron');
     const { startServer } = require('node-solid-server');
     app.whenReady().then(async () => {
       await startServer({ port: 3000, root: './solid-data' });
       const win = new BrowserWindow({ 
         webPreferences: { sandbox: true, preload: path.join(__dirname, '../preload/preload.js') },
         width: 400, 
         height: 600 
       });
       win.loadFile('public/index.html');
     });
     ```
  7. Set up `services/quadstore.js`, `services/jsonld.js`, `services/turtle.js`, `services/ipfs.js`, `services/webtorrent.js`, `services/webrtc.js`, `services/websockets.js`, `services/permissions.js`.
  8. Create `services/eventBus.js`.
  9. Define versioned ontologies in `ontologies/`.
- **Deliverables**:
  - Forked and refactored Beaker repository.
  - Native Cashtab module with multi-sig.
  - WebExtension and Electron skeleton.
  - Modular ADP, Cashtab, Chronik, Solid, Quadstore, JSON-LD, Turtle/N3, WebTorrent, WebRTC, WebSockets.

### Phase 2: Access Control and Native Cashtab (Weeks 5-6)
- **Objective**: Implement payment/token access model with native Cashtab.
- **Tasks**:
  1. Implement `modules/access` and `modules/cashtab`:
     - Verify payment or token; support multi-sig wallets:
       ```javascript
       import { ChronikClient } from 'chronik-client';
       const chronik = new ChronikClient('https://chronik.be.cash');
       async function verifyAccess(userAddress) {
         const paid = await chronik.address(userAddress).history(0, 10).then(txs => 
           txs.txs.some(tx => tx.outputs.some(out => out.value === '100' && out.address === accessConfig.fundAddress)));
         const hasToken = await chronik.address(userAddress).utxos().then(utxos => 
           utxos.some(utxo => utxo.token?.tokenId === accessConfig.tokenId));
         return paid || hasToken || accessConfig.obligationFree;
       }
       ```
     - Store wallet data in Quadstore (Turtle/N3).
  2. Create `components/Access.js` with ARIA attributes for native wallet UI.
  3. Handle errors (e.g., wallet not initialized).
- **Deliverables**:
  - Access module (v1.0.0).
  - Native Cashtab module (v1.0.0).
  - Accessible access UI.

### Phase 3: Service Activation (Week 7)
- **Objective**: Enable native Cashtab-based activation.
- **Tasks**:
  1. Implement `modules/cashtab/activateService.js`:
     - Sign message with native Cashtab; verify with Chronik; store in Quadstore.
  2. Update `components/Sidebar.js` and `components/MainWindow.js` with ARIA attributes.
  3. Handle errors.
- **Deliverables**:
  - Activation module (v1.0.0).
  - Accessible platform-specific UI.

### Phase 4: App Store (Weeks 8-9)
- **Objective**: Implement app store for discovery and installation.
- **Tasks**:
  1. Implement `modules/appstore`:
     - Fetch app metadata from IPFS/GitHub; validate and install:
       ```javascript
       import { create } from 'ipfs-http-client';
       const ipfs = create({ url: 'http://localhost:5001' });
       async function installApp(appId) {
         const metadata = await ipfs.cat(appId).toJson();
         if (verifyHash(metadata)) {
           await downloadApp(metadata.downloadUrl, 'apps/installed/');
           eventBus.emit('app:installed', metadata);
         }
       }
       ```
     - Store metadata in Quadstore (RDF/JSON).
  2. Create `components/AppStore.js` with ARIA attributes.
- **Deliverables**:
  - App store module (v1.0.0).
  - Accessible app store UI.

### Phase 5: Parental Controls (Weeks 10-11)
- **Objective**: Implement guardian-managed ward accounts.
- **Tasks**:
  1. Implement `modules/parental`:
     - Link ward subdomain; enforce rules via `webRequest`.
     - Store rules in Quadstore (Turtle/N3).
  2. Create `components/ParentalControls.js` with ARIA attributes.
- **Deliverables**:
  - Parental controls module (v1.0.0).
  - Accessible parental controls UI.

### Phase 6: Web Usage Tracking (Weeks 12-13)
- **Objective**: Implement toggleable web usage tracking in WebExtension.
- **Tasks**:
  1. Update `modules/work`:
     - Track active tab time; sync with Electron via WebSockets/IPFS.
  2. Create `components/Tracking.js` with ARIA attributes.
- **Deliverables**:
  - Tracking module (v1.0.0).
  - Accessible tracking UI.

### Phase 7: Semantic Bookmarks (Weeks 14-15)
- **Objective**: Implement SemBookmarks with media processing and magnet links.
- **Tasks**:
  1. Update `modules/bookmarks`:
     - Process PDFs/media with `pdf.js`; generate magnet links; store in Quadstore (RDF Schema).
  2. Create `components/Bookmarks.js` with ARIA attributes.
- **Deliverables**:
  - Bookmarks module (v1.0.0).
  - JSON-LD/RDF Schema serialization.
  - Accessible bookmarks UI.

### Phase 8: Chat and URL Annotations (Weeks 16-17)
- **Objective**: Implement chat and annotations with CML support.
- **Tasks**:
  1. Update `modules/chat`:
     - Support CML for annotations; share via GUN.eco.
  2. Create `components/Chat.js` with ARIA attributes.
- **Deliverables**:
  - Chat/annotation module (v1.0.0).
  - CML support.
  - Accessible chat UI.

### Phase 9: Connection Requests (Weeks 18-19)
- **Objective**: Implement agreements without Solid.
- **Tasks**:
  1. Implement `modules/agreements`:
     - Store in Quadstore; share via GUN.
  2. Create `components/Agreements.js` with ARIA attributes.
- **Deliverables**:
  - Agreements module (v1.0.0).
  - Turtle/N3 permissions.
  - Accessible agreements UI.

### Phase 10: Work Tracking (Weeks 20-21)
- **Objective**: Implement work tracking with multi-sig payments.
- **Tasks**:
  1. Update `modules/work`:
     - Track tab time; associate with projects; integrate multi-sig payments; publish to IPFS/WebTorrent.
  2. Create `components/Work.js` with ARIA attributes.
- **Deliverables**:
  - Work tracking module (v1.0.0).
  - Accessible work UI.

### Phase 11: Semantic Library (Weeks 22-23)
- **Objective**: Implement library with faceted search.
- **Tasks**:
  1. Implement `modules/library`:
     - Store resources; faceted search via SPARQL.
  2. Create `components/Library.js` with ARIA attributes.
- **Deliverables**:
  - Library module (v1.0.0).
  - Accessible faceted search UI.

### Phase 12: Media Sharing and Creation (Weeks 24-25)
- **Objective**: Implement WebTorrent-based media sharing and creation.
- **Tasks**:
  1. Implement `modules/media`:
     - Share via WebTorrent; verify payment with Chronik.
  2. Create `components/Media.js` with ARIA attributes.
- **Deliverables**:
  - Media module (v1.0.0).
  - Accessible content creation UI.

### Phase 13: Solid Pod and Local Apps (Weeks 26-27)
- **Objective**: Implement local Solid pod and app hosting.
- **Tasks**:
  1. Implement `modules/solid`, `modules/apps`, `modules/translator`.
  2. Create `components/Apps.js` with ARIA attributes.
  3. Store app configs in `apps/configs/`.
- **Deliverables**:
  - Solid pod module (v1.0.0).
  - App hosting module (v1.0.0).
  - Translator module (v1.0.0).
  - Accessible apps UI.

### Phase 14: AI Assistants (Weeks 28-29)
- **Objective**: Implement local/online AI assistants with translation.
- **Tasks**:
  1. Update `modules/ai`:
     - Support multiple LLMs; store configs in `ai/configs/`.
     - Translate content with collapsed original display.
  2. Create `components/AI.js` with ARIA attributes.
- **Deliverables**:
  - AI assistant module (v1.0.0).
  - Accessible translation UI.

### Phase 15: Accessibility (Weeks 30-31)
- **Objective**: Implement screen reader and TTS support.
- **Tasks**:
  1. Implement `modules/accessibility`:
     - Add ARIA attributes to all components.
     - Support TTS via Web Speech API and online providers (Google Cloud TTS, AWS Polly).
     - Store configs in Quadstore (RDF/JSON).
  2. Create `components/Accessibility.js` for TTS controls.
- **Deliverables**:
  - Accessibility module (v1.0.0).
  - Accessible TTS UI.

### Phase 16: Secure Backups and Device Sync (Weeks 32-33)
- **Objective**: Implement encrypted backups and synchronization.
- **Tasks**:
  1. Implement `modules/backups`:
     - Encrypt with native Cashtab key; sync via IPFS, Google Drive, local HDD, Solid pods.
  2. Create `components/Backups.js` with ARIA attributes.
- **Deliverables**:
  - Backup/sync module (v1.0.0).
  - Accessible backup UI.

### Phase 17: Post-Installation Guide (Week 34)
- **Objective**: Create user setup guide.
- **Tasks**:
  1. Write `docs/post-installation.md`:
     ```markdown
     # Webizen v0.25 Post-Installation Guide

     ## Step 1: Install Webizen
     - **WebExtension**: Download from Chrome Web Store or Firefox Add-ons.
     - **Electron App**: Download from GitHub Releases (`github.com/mediaprophet/socialweb`).
     - Install Node.js 20 (LTS) and run `npm install` and `npm run rebuild`.

     ## Step 2: Set Up Native Cashtab Wallet
     - Open Webizen; navigate to Access panel.
     - Create or import an eCash wallet (no external Cashtab extension required).
     - Fund with 100 XEC for access (send to `ecash:qp...socialwebfund`).
     - For projects, configure multi-sig wallets in Work panel (e.g., 2-of-3 signers).

     ## Step 3: Activate Service
     - In Access panel, sign a message with native Cashtab wallet to verify eCash address.
     - Confirm activation in UI.

     ## Step 4: Configure ADP
     - Set up DNS TXT record with eCash address (`adp:hasEcashAccount`).
     - Host WebID on IPFS (optional).
     - Test ADP in Address Book panel.

     ## Step 5: Set Up App Store
     - Go to App Store panel; browse apps from IPFS or GitHub.
     - Install apps (e.g., `Example App`) to `apps/installed/`.
     - Verify app integrity before running.

     ## Step 6: Set Up AI Assistants
     - In AI panel, select local (LM Studio, Ollama, AnythingLLM) or online (Grok, Gemini, OpenAI, Claude) providers.
     - Configure translation language (e.g., `en-US`).

     ## Step 7: Configure Accessibility
     - In Accessibility panel, enable screen reader support (NVDA, VoiceOver, JAWS).
     - Select TTS provider (Web Speech API, Google Cloud TTS, AWS Polly).
     - Customize language and voice settings.

     ## Step 8: Explore Features
     - **Address Book**: Add contacts via web history or ADP.
     - **Chat**: Start 1:1 or group chats; add CML annotations.
     - **Bookmarks**: Save URLs or media with AI summaries and magnet links.
     - **Media**: Share videos/PDFs via WebTorrent.
     - **Library**: Organize resources with faceted search.
     - **Work**: Track time and payments (multi-sig) for projects.
     - **Parental Controls**: Set rules for ward accounts (subdomains).
     - **Backups**: Enable IPFS, Google Drive, or local storage.

     ## Troubleshooting
     - **Cashtab Errors**: Ensure wallet is funded; check `https://chronik.be.cash`.
     - **Module Errors**: Run `npm run rebuild`.
     - **App Store**: Verify app metadata; contact support for issues.
     - **Accessibility**: Test with screen readers; contact support via `github.com/mediaprophet/socialweb/issues`.
     ```
  2. Integrate guide into `README.md` and GitHub Pages demo.
- **Deliverables**:
  - Post-installation guide.
  - Updated documentation.

### Phase 18: Testing and Refinement (Weeks 35-36)
- **Objective**: Ensure reliability, security, and accessibility.
- **Tasks**:
  1. Unit tests: All modules, including native Cashtab and app store.
  2. Integration tests: Workflows (e.g., multi-sig payments, app installation).
  3. E2E tests: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
  4. Accessibility tests: WCAG 2.1 compliance; screen reader tests (NVDA, VoiceOver, JAWS).
  5. Security audit: Encryption, E2EE, OAuth, WAC, sandboxing, multi-sig wallets.
- **Deliverables**:
  - Test suite (>80% coverage).
  - Bug fixes.
  - Accessibility report.
  - Security report.

### Phase 19: Deployment (Week 37)
- **Objective**: Deploy Webizen v0.25.
- **Tasks**:
  1. Build: `npm run build`.
  2. Package WebExtension: `.crx`, `.xpi`.
  3. Package Electron app: Windows, macOS, Linux (using `electron-builder`).
  4. Publish to Chrome Web Store, Firefox Add-ons, GitHub Releases.
  5. Deploy demo to `https://mediaprophet.github.io/socialweb`.
  6. Publish alpha to `github.com/WebCivics/SocialWeb`.
  7. Document usage, development, accessibility, and app store features.
- **Deliverables**:
  - Deployed WebExtension and Electron app.
  - Demo site.
  - Documentation with accessibility and app store guides.

## 6. Workflows

- **Access Control**: Verify payment/token via native Cashtab → Store status → Enable app.
- **Activation**: ADP → Native Cashtab sign → Chronik verify → Quadstore store.
- **Parental Controls**: Guardian sets rules → Enforce via `webRequest` → Store in Quadstore.
- **Web Usage Tracking**: Toggle tracking → Sync with Electron via WebSockets/IPFS.
- **Address Book**: Web history/ADP → Quadstore → Agreement → Chat/Annotations.
- **Chat/Annotations**: GUN/WebRTC/WebSockets → CML annotations → Quadstore → JSON-LD.
- **Bookmarks**: History API or manual → Process media → Store with magnet links → Quadstore (RDF Schema).
- **Agreements**: GUN share → Quadstore → Turtle/N3.
- **Work**: Track tab time → Project association → Multi-sig payments → Git/IPFS → Contribution metadata.
- **Library**: Resource storage → Faceted search → Turtle/N3 permissions, JSON-LD sharing.
- **Media**: WebTorrent share → Chronik payment → Quadstore metadata.
- **Solid Pod**: Store/sync data at `http://localhost:3000`.
- **Local Apps**: Host Solid/Webizen v0.25/third-party apps; translate JSON to RDF/JSON.
- **App Store**: Discover apps → Validate and install → Integrate with `modules/apps`.
- **AI Assistants**: Define agents in RDF/JSON → Query/translate content → UI display.
- **Accessibility**: Apply ARIA attributes; enable TTS for content (bookmarks, chat, etc.).
- **Backups/Sync**: Encrypt with native Cashtab key → IPFS/Google Drive/local/Solid.

## 7. Security

- **Native Cashtab**: Secure key storage in Quadstore; multi-sig validation.
- **Encryption**: CryptoJS AES-256; GUN SEA; WebTorrent encryption.
- **Permissions**: Turtle/N3 for private/public/permissive access; WAC for future VPN.
- **Chronik**: Cache results.
- **Google Drive/Solid**: Minimal OAuth scopes (`drive.file`).
- **IPFS**: Encrypted data only.
- **App Store**: Hash verification; sandboxed execution.
- **WebExtension/Electron**: Sanitize inputs; minimal permissions; Electron sandboxing.

## 8. Testing

- **Unit**: Modules (access, cashtab, chronik, GUN, WebTorrent, WebRTC, WebSockets, addressbook, chat, bookmarks, agreements, media, work, library, solid, apps, appstore, translator, ai, parental, accessibility, backups).
- **Integration**: Workflows (multi-sig payments, app installation).
- **E2E**: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
- **Accessibility**: WCAG 2.1 compliance; screen reader tests (NVDA, VoiceOver, JAWS).
- **Security**: Encryption, E2EE, OAuth, WAC, sandboxing, multi-sig wallets.

## 9. Deployment

- **Repository**: `github.com/mediaprophet/socialweb` (development); `github.com/WebCivics/SocialWeb` (alpha).
- **WebExtension**: Chrome Web Store, Firefox Add-ons, GitHub Releases.
- **Electron App**: GitHub Releases (Windows, macOS, Linux).
- **Demo Site**: GitHub Pages (`https://mediaprophet.github.io/socialweb`).
- **Documentation**: README, user/developer guides, accessibility guide, app store guide, post-installation guide.

## 10. Timeline

- **Duration**: 37 weeks (1-2 developers).
- **Milestones**:
  - Weeks 1-4: Core infrastructure and Beaker/Cashtab refactor.
  - Weeks 5-6: Access control and native Cashtab.
  - Week 7: Service activation.
  - Weeks 8-9: App store.
  - Weeks 10-11: Parental controls.
  - Weeks 12-13: Web usage tracking.
  - Weeks 14-15: Semantic bookmarks.
  - Weeks 16-17: Chat and annotations.
  - Weeks 18-19: Connection requests.
  - Weeks 20-21: Work tracking.
  - Weeks 22-23: Semantic library.
  - Weeks 24-25: Media sharing/creation.
  - Weeks 26-27: Solid pod and local apps.
  - Weeks 28-29: AI assistants.
  - Weeks 30-31: Accessibility.
  - Weeks 32-33: Backups and sync.
  - Week 34: Post-installation guide.
  - Weeks 35-36: Testing.
  - Week 37: Deployment.

## 11. Risks and Mitigations

- **Beaker Refactor**: Outdated dependencies; run `npm run rebuild` and update to Node.js 20.
- **Cashtab Refactor**: Ensure secure key management; test multi-sig with Chronik.
- **App Store**: Validate app metadata; sandbox execution to prevent malicious code.
- **Chronik**: Cache results; fallback to manual.
- **GUN/WebTorrent**: Multiple relays; IPFS fallback.
- **Google Drive/Solid**: Clear OAuth/setup guide; fallback to IPFS/local.
- **LM Studio/Ollama/AnythingLLM**: Disable if unavailable.
- **Electron Compatibility**: Test across OSes; debug logs (avoid tmux on macOS).
- **Accessibility**: Test with screen readers; provide TTS fallback (Web Speech API).
- **Adoption**: In-app tutorials and post-installation guide.

## 12. Future Enhancements

- Social VPN using Tailscale and WAC (pending study).
- Mobile vault integration (Cashtab-compatible).
- Advanced media formats.
- Social analytics dashboard.
- WebPush notifications.
- Enhanced TTS voices and languages.
- Dat protocol support (optional, for legacy Beaker compatibility).

## 13. Recommendations

- **Hybrid P2P**: WebTorrent for media, WebRTC for low-latency chat, GUN for messaging.
- **Ontologies**: Versioned `SemBookmarks#v1`, `cml#v1`, `ai#v1`, `parental#v1`, `accessibility#v1`, `cashtab#v1`, `appstore#v1` for updates.
- **Sync Scheduler**: Periodic checks for updates.
- **Mobile Vault**: Plan for mobile key store integration.
- **Tutorials**: In-app guides for ADP, native Cashtab, app store, media, work, library, access, Solid, AI, parental controls, bookmarks, annotations, accessibility.
- **Beaker Lessons**: Avoid feature bloat; focus on user-driven social-web features.

## 14. Next Steps

1. Review specification with stakeholders.
2. Fork `github.com/beakerbrowser/beaker` to `github.com/mediaprophet/socialweb`.
3. Begin Phase 1 development.
4. Iterate through phases, testing each milestone.
5. Publish alpha to `github.com/WebCivics/SocialWeb`.