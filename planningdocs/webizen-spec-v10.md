# Webizen v0.25: Software Development Specification (Version 10)

## 1. Project Overview

**Date**: July 11, 2025  
**Purpose**: Webizen v0.25 is a decentralized social-web browser, refactored from Beaker Browser (an abandoned Electron-based P2P browser using Dat protocol), delivered as a WebExtension (Chrome, Firefox) and Electron desktop app. It enables social interactions via an address book, chat with Context Markup Language (CML) annotations, semantic bookmarks with media processing, RDF-based agreements, media sharing, work tracking, a semantic library, web usage tracking, local Solid pod, local apps, AI assistants, parental controls, and accessibility support. It integrates with WebCard Viewer’s Agent Discovery Protocol (ADP) for identity discovery, uses Cashtab for eCash-based access and transactions, Chronik for blockchain queries, GUN.eco for chat, WebTorrent/WebRTC/WebSockets for media and communication, Quadstore for local RDF storage, and supports secure backups to IPFS, Google Drive, local HDD, or Solid pods. Access requires a small eCash payment or token until development costs are recouped, then becomes obligation-free. The system is modular, extensible, uses Turtle/N3 for permissions/network/WAC, JSON-LD for marketing/Web 2.0, and RDF/JSON for AI and accessibility configs, with independent module updates to avoid breaking changes. A post-installation guide assists users in setting up the browser.

### 1.1 Goals
- **Access Control**: Require eCash payment (e.g., 100 XEC) or token until development costs (e.g., 1,000,000 XEC) are met; then obligation-free.
- **Service Activation**: Verify eCash address ownership via Cashtab.
- **Identity Discovery**: Fetch user details (eCash address, optional WebID) via ADP.
- **Address Book**: Manage contacts from web history or ADP, with agreement-based relationships.
- **Chat and Annotations**: Support 1:1, group chats, and URL annotations with CML.
- **Semantic Bookmarks**: Store browser history or manual bookmarks with media metadata (PDFs, etc.) and WebTorrent magnet links in RDF Schema.
- **Connection Requests**: Negotiate RDF-based agreements (UDHR, CRC terms).
- **Media Sharing**: Share videos/PDFs via WebTorrent, with free previews and paid full access.
- **Content Creation**: Tool for uploading media and associating with ADP/eCash.
- **Work Tracking**: Track active tab time, project associations, Git integration, contribution metadata.
- **Semantic Library**: Share resources by topics/groups with faceted search and permissioned access.
- **Web Usage Tracking**: WebExtension tracks usage (toggleable) and syncs with Electron app.
- **Solid Pod**: Host local Solid pod in Electron for storage/sync.
- **Local Apps**: Host Solid, Webizen v0.25, and third-party apps with JSON-to-RDF translator.
- **AI Assistants**: Support local (LM Studio, Ollama, AnythingLLM) and online (Grok, Gemini, OpenAI, Claude) LLMs with RDF/JSON configs and translation.
- **Parental Controls**: Guardians manage ward accounts (subdomains) with rules (e.g., block adult content).
- **Accessibility**: Support screen readers (NVDA, VoiceOver, JAWS) and TTS AI options (local/online) with RDF/JSON configs.
- **Secure Backups**: Encrypt data with Cashtab key; store in IPFS, Google Drive, local HDD, or Solid pods.
- **Device Synchronization**: Sync data across devices using encrypted backups or Solid pods.
- **Deployment**: Deliver as WebExtension, Electron app, and demo site on GitHub Pages.
- **Post-Installation Guide**: Step-by-step setup for users.

### 1.2 Key Features
- **Refactored Beaker Browser**: Leverage Beaker’s Electron/Chromium core, replacing Dat with Webizen’s P2P stack (WebTorrent, GUN.eco, WebRTC, WebSockets).
- **Modular Design**: Independent modules with versioned interfaces for updates without breaking changes.
- **Extensibility**: Plugin system and dynamic RDF ontology support (e.g., `accessibility#v1`).
- **Data Formats**: Turtle/N3 for permissions/network/WAC/accessibility; JSON-LD for marketing/Web 2.0; RDF/JSON for AI and TTS configs.
- **Faceted Search**: SPARQL-based search for library resources.
- **Device Sync**: Cross-device consistency via encrypted backups or Solid pods.
- **Access Model**: eCash payment or token until obligation-free.
- **Dual Implementation**: WebExtension (sidebar) and Electron app (main window with Solid pod, local apps).
- **Decentralized**: No server dependencies; uses GUN, WebTorrent, WebRTC, IPFS, Solid.
- **User Setup**: Post-installation guide for Cashtab, ADP, AI/TTS, and accessibility.

### 1.3 Client Requirements
- **Cashtab Wallet**: Browser extension (WebExtension) or integrated (Electron) for payments/tokens.
- **Browser/OS**: Chrome (v120+), Firefox (v115+) for WebExtension; Windows, macOS, Linux for Electron.
- **Storage**: IndexedDB (50MB) for Quadstore; 100MB for Solid pod (Electron).
- **Internet**: Required for ADP, Chronik, GUN, WebTorrent, IPFS, Google Drive, online LLMs/TTS.
- **Optional**: LM Studio, Ollama, AnythingLLM for local AI; Google Cloud TTS, AWS Polly for online TTS; Solid pod for sync.
- **Hardware**: Minimum 4GB RAM, 1GHz CPU for Electron (Solid pod, local apps, media processing).
- **Accessibility**: Screen reader support (NVDA, VoiceOver, JAWS); TTS-compatible browser/OS.
- **Dependencies**: Node.js (>=12.0), npm, libtool, m4, make, g++ (Linux), autoconf, automake (Linux/macOS).

## 2. Architecture

### 2.1 Components
- **Access Control**: Verifies eCash payment/token via Cashtab/Chronik; tracks obligation status.
- **Identity (ADP)**: Fetches eCash address (`adp:hasEcashAccount`) from DNS TXT + IPFS.
- **Service Activation (Cashtab)**: Signs message to verify eCash address; Chronik validates.
- **Address Book**: Stores contacts from web history or ADP; relationships via agreements.
- **Chat (GUN.eco, WebRTC, WebSockets)**: Real-time 1:1, group chats, and URL annotations with CML.
- **Bookmarks (SemBookmarks)**: JSON-LD bookmarks with media metadata and magnet links.
- **Agreements**: RDF-based connection requests shared via GUN.
- **Media (WebTorrent)**: P2P sharing of videos/PDFs; eCash transactions for full access.
- **Content Creation**: Tool for uploading media and associating with ADP/eCash.
- **Work Tracking**: Tracks active tab time, project associations, Git integration, contribution metadata.
- **Semantic Library**: Permissioned resource sharing with faceted search.
- **Web Usage Tracking**: WebExtension tracks usage; syncs with Electron app.
- **Solid Pod**: Local Solid server in Electron for storage/sync.
- **Local Apps**: Host Solid, Webizen v0.25, and third-party apps with JSON-to-RDF translator.
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
│   │   ├── Access.js       # Access control UI
│   │   ├── AddressBook.js  # Address book UI
│   │   ├── Chat.js         # Chat and annotations
│   │   ├── Bookmarks.js    # Semantic bookmarks
│   │   ├── Agreements.js   # Connection requests
│   │   ├── Media.js        # Media sharing/creation
│   │   ├── Work.js         # Work tracking
│   │   ├── Library.js      # Semantic library
│   │   ├── Tracking.js     # Web usage tracking
│   │   ├── Apps.js         # Local app hosting
│   │   ├── AI.js           # AI assistant UI
│   │   ├── ParentalControls.js # Parental controls UI
│   │   └── Accessibility.js # Accessibility controls UI
│   ├── modules/
│   │   ├── access/         # Payment/token access control
│   │   │   └── index.js
│   │   ├── adp/            # ADP fetching
│   │   │   └── index.js
│   │   ├── cashtab/        # Cashtab integration
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

- **WebExtension**: WebExtensions API, React 17, Tailwind CSS.
- **Electron**: Electron (`^32.0.0`), React 17, Tailwind CSS (replacing Beaker’s styling).
- **Identity/Data**: N3.js (`^1.26.0`), Quadstore (`^10.0.0`), quadstore-comunica (`^4.0.0`), @comunica/query-sparql (`^3.0.0`), jsonld.js (`^8.3.2`), ipfs-http-client (`^60.0.0`), @inrupt/solid-client (`^2.0.0`), @inrupt/solid-client-authn-browser (`^2.0.0`), node-solid-server (`^6.0.0`), pdf.js (`^4.0.0`).
- **Communication**: GUN.eco (`^0.2020.1236`), WebTorrent (`^2.0.0`), WebRTC (native), WebSockets (`ws@^8.18.0`).
- **eCash**: Cashtab (`@cashtab/wallet-lib`), Chronik (`chronik-client@^0.11.0`).
- **Backups**: CryptoJS (`^4.1.1`), googleapis (`^144.0.0`).
- **AI**: LM Studio (`http://localhost:1234/v1/chat/completions`), Ollama (`http://localhost:11434`), AnythingLLM (local), Grok (`https://x.ai/api`), Gemini, OpenAI, Claude.
- **Accessibility**: Web Speech API (native), Google Cloud TTS, AWS Polly.
- **Apps**: `cimba` (updated fork), ThemeForest JSON-based apps.
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

## 4. Refactoring Beaker Browser

### 4.1 Beaker Baseline
- **Source**: Fork `github.com/beakerbrowser/beaker` (MIT license, Electron-based, Chromium renderer, Dat protocol).[](https://en.wikipedia.org/wiki/Beaker_%28web_browser%29)
- **Key Features Retained**:
  - Electron/Chromium core for cross-platform rendering.
  - P2P capabilities (adapted to WebTorrent/GUN.eco).
  - Plugin system for extensibility (aligned with Webizen’s modules).
- **Features Removed**:
  - Dat-specific APIs (`dat://` protocol, `bkr` CLI).
  - Beaker’s built-in editor and site creation tools (replaced by Webizen’s media/content creation).
  - Legacy UI (`app/shell-window`, `app/builtin-pages`).
- **Challenges**:
  - Electron’s process-level sandbox issues (restore via Webizen’s security measures).[](https://pfrazee.github.io/blog/beaker-browser-0.1)
  - Module errors from outdated dependencies (use `npm run rebuild`).[](https://github.com/beakerbrowser/beaker)
  - Limited mobile support (plan for future vault integration).

### 4.2 Refactoring Plan
- **Fork and Setup**:
  - Fork `github.com/beakerbrowser/beaker` to `github.com/mediaprophet/socialweb`.
  - Update `package.json` with Webizen dependencies and scripts:
    ```json
    {
      "name": "webizen",
      "version": "0.25.0",
      "dependencies": {
        "@cashtab/wallet-lib": "^1.0.0",
        "chronik-client": "^0.11.0",
        "gun": "^0.2020.1236",
        "webtorrent": "^2.0.0",
        "ws": "^8.18.0",
        "n3": "^1.26.0",
        "quadstore": "^10.0.0",
        "quadstore-comunica": "^4.0.0",
        "@comunica/query-sparql": "^3.0.0",
        "jsonld": "^8.3.2",
        "ipfs-http-client": "^60.0.0",
        "@inrupt/solid-client": "^2.0.0",
        "@inrupt/solid-client-authn-browser": "^2.0.0",
        "node-solid-server": "^6.0.0",
        "pdf.js": "^4.0.0",
        "crypto-js": "^4.1.1",
        "googleapis": "^144.0.0",
        "react": "^17.0.2",
        "react-dom": "^17.0.2",
        "tailwindcss": "^3.0.0"
      },
      "scripts": {
        "start": "electron src/platforms/electron/main.js",
        "build": "vite build",
        "watch": "vite watch",
        "rebuild": "electron-rebuild",
        "test": "jest"
      }
    }
    ```
- **Remove Dat Dependencies**:
  - Delete `app/dat`, `scripts/dat`, and related `bkr` CLI tools.
  - Replace with `modules/webtorrent`, `modules/gun`, `modules/webrtc`, `modules/websockets`.
- **Update UI**:
  - Replace Beaker’s `app/shell-window` and `app/builtin-pages` with Webizen’s `src/components/` (React, Tailwind CSS, ARIA).
  - Adapt `app/background-process` to `src/background.js` for WebExtension.
- **Integrate Cashtab**:
  - Add `modules/cashtab` for eCash payment verification:
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
- **Security**:
  - Restore Electron’s sandboxing (disabled in Beaker) using `webPreferences: { sandbox: true }`.
  - Sanitize inputs and limit permissions (`webRequest`, `tabs`, `history`).

### 4.3 Post-Installation Guide
- **File**: `docs/post-installation.md`
- **Content**: Step-by-step setup for Webizen v0.25, including Cashtab, ADP, AI/TTS, and accessibility.

## 5. Development Phases

### Phase 1: Core Infrastructure and Beaker Refactor (Weeks 1-3)
- **Objective**: Fork Beaker, set up Webizen’s modular structure, and integrate Electron core.
- **Tasks**:
  1. Fork `github.com/beakerbrowser/beaker` to `github.com/mediaprophet/socialweb`.
  2. Update `package.json` with Webizen dependencies and scripts.
  3. Remove Dat-specific code (`app/dat`, `scripts/dat`, `bkr` CLI).
  4. Configure `manifest.json` for WebExtension:
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
  5. Configure Electron in `platforms/electron/main.js`:
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
  6. Reuse Beaker’s Electron setup; add Webizen modules (`adp`, `cashtab`, `chronik`, `solid`).
  7. Set up `services/quadstore.js`, `services/jsonld.js`, `services/turtle.js`, `services/ipfs.js`, `services/webtorrent.js`, `services/webrtc.js`, `services/websockets.js`, `services/permissions.js`.
  8. Create `services/eventBus.js`.
  9. Define versioned ontologies in `ontologies/`.
- **Deliverables**:
  - Forked and refactored Beaker repository.
  - WebExtension and Electron skeleton.
  - Modular ADP, Cashtab, Chronik, Solid, Quadstore, JSON-LD, Turtle/N3, WebTorrent, WebRTC, WebSockets.

### Phase 2: Access Control and Cashtab Integration (Weeks 4-5)
- **Objective**: Implement payment/token access model with Cashtab.
- **Tasks**:
  1. Implement `modules/access` and `modules/cashtab`:
     - Verify payment or token; track obligation status in Quadstore.
  2. Create `components/Access.js` with ARIA attributes.
  3. Handle errors (e.g., no Cashtab).
- **Deliverables**:
  - Access module (v1.0.0).
  - Cashtab module (v1.0.0).
  - Accessible access UI.

### Phase 3: Service Activation (Week 6)
- **Objective**: Enable Cashtab-based activation.
- **Tasks**:
  1. Implement `modules/cashtab/activateService.js`:
     - Sign message with Cashtab; verify with Chronik; store in Quadstore.
  2. Update `components/Sidebar.js` and `components/MainWindow.js` with ARIA attributes.
  3. Handle errors.
- **Deliverables**:
  - Activation module (v1.0.0).
  - Accessible platform-specific UI.

### Phase 4: Parental Controls (Weeks 7-8)
- **Objective**: Implement guardian-managed ward accounts.
- **Tasks**:
  1. Implement `modules/parental`:
     - Link ward subdomain; enforce rules via `webRequest`.
     - Store rules in Quadstore (Turtle/N3).
  2. Create `components/ParentalControls.js` with ARIA attributes.
- **Deliverables**:
  - Parental controls module (v1.0.0).
  - Accessible parental controls UI.

### Phase 5: Web Usage Tracking (Weeks 9-10)
- **Objective**: Implement toggleable web usage tracking in WebExtension.
- **Tasks**:
  1. Update `modules/work`:
     - Track active tab time; sync with Electron via WebSockets/IPFS.
  2. Create `components/Tracking.js` with ARIA attributes.
- **Deliverables**:
  - Tracking module (v1.0.0).
  - Accessible tracking UI.

### Phase 6: Semantic Bookmarks (Weeks 11-12)
- **Objective**: Implement SemBookmarks with media processing and magnet links.
- **Tasks**:
  1. Update `modules/bookmarks`:
     - Process PDFs/media with `pdf.js`; generate magnet links; store in Quadstore (RDF Schema).
  2. Create `components/Bookmarks.js` with ARIA attributes.
- **Deliverables**:
  - Bookmarks module (v1.0.0).
  - JSON-LD/RDF Schema serialization.
  - Accessible bookmarks UI.

### Phase 7: Chat and URL Annotations (Weeks 13-14)
- **Objective**: Implement chat and annotations with CML support.
- **Tasks**:
  1. Update `modules/chat`:
     - Support CML for annotations; share via GUN.eco.
  2. Create `components/Chat.js` with ARIA attributes.
- **Deliverables**:
  - Chat/annotation module (v1.0.0).
  - CML support.
  - Accessible chat UI.

### Phase 8: Connection Requests (Weeks 15-16)
- **Objective**: Implement agreements without Solid.
- **Tasks**:
  1. Implement `modules/agreements`:
     - Store in Quadstore; share via GUN.
  2. Create `components/Agreements.js` with ARIA attributes.
- **Deliverables**:
  - Agreements module (v1.0.0).
  - Turtle/N3 permissions.
  - Accessible agreements UI.

### Phase 9: Work Tracking (Weeks 17-18)
- **Objective**: Implement work tracking and contribution metadata.
- **Tasks**:
  1. Update `modules/work`:
     - Track tab time; associate with projects; publish to IPFS/WebTorrent.
  2. Create `components/Work.js` with ARIA attributes.
- **Deliverables**:
  - Work tracking module (v1.0.0).
  - Accessible work UI.

### Phase 10: Semantic Library (Weeks 19-20)
- **Objective**: Implement library with faceted search.
- **Tasks**:
  1. Implement `modules/library`:
     - Store resources; faceted search via SPARQL.
  2. Create `components/Library.js` with ARIA attributes.
- **Deliverables**:
  - Library module (v1.0.0).
  - Accessible faceted search UI.

### Phase 11: Media Sharing and Creation (Weeks 21-22)
- **Objective**: Implement WebTorrent-based media sharing and creation.
- **Tasks**:
  1. Implement `modules/media`:
     - Share via WebTorrent; verify payment with Chronik.
  2. Create `components/Media.js` with ARIA attributes.
- **Deliverables**:
  - Media module (v1.0.0).
  - Accessible content creation UI.

### Phase 12: Solid Pod and Local Apps (Weeks 23-24)
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

### Phase 13: AI Assistants (Weeks 25-26)
- **Objective**: Implement local/online AI assistants with translation.
- **Tasks**:
  1. Update `modules/ai`:
     - Support multiple LLMs; store configs in `ai/configs/`.
     - Translate content with collapsed original display.
  2. Create `components/AI.js` with ARIA attributes.
- **Deliverables**:
  - AI assistant module (v1.0.0).
  - Accessible translation UI.

### Phase 14: Accessibility (Weeks 27-28)
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

### Phase 15: Secure Backups and Device Sync (Weeks 29-30)
- **Objective**: Implement encrypted backups and synchronization.
- **Tasks**:
  1. Implement `modules/backups`:
     - Encrypt with Cashtab key; sync via IPFS, Google Drive, local HDD, Solid pods.
  2. Create `components/Backups.js` with ARIA attributes.
- **Deliverables**:
  - Backup/sync module (v1.0.0).
  - Accessible backup UI.

### Phase 16: Post-Installation Guide (Week 31)
- **Objective**: Create user setup guide.
- **Tasks**:
  1. Write `docs/post-installation.md`:
     ```markdown
     # Webizen v0.25 Post-Installation Guide

     ## Step 1: Install Webizen
     - **WebExtension**: Download from Chrome Web Store or Firefox Add-ons.
     - **Electron App**: Download from GitHub Releases (`github.com/mediaprophet/socialweb`).
     - Run `npm install` and `npm run rebuild` for Electron app (requires Node.js >=12.0).

     ## Step 2: Install Cashtab Wallet
     - Download from `https://chromewebstore.google.com/detail/cashtab/obldfcmebhllhjlhjbnghaipekcppeag`.
     - Create or import an eCash wallet.
     - Fund with 100 XEC for access (send to `ecash:qp...socialwebfund`).

     ## Step 3: Activate Service
     - Open Webizen; navigate to Access panel.
     - Sign a message with Cashtab to verify eCash address.
     - Confirm activation in UI.

     ## Step 4: Configure ADP
     - Set up DNS TXT record with eCash address (`adp:hasEcashAccount`).
     - Host WebID on IPFS (optional).
     - Test ADP in Address Book panel.

     ## Step 5: Set Up AI Assistants
     - Go to AI panel; select local (LM Studio, Ollama, AnythingLLM) or online (Grok, Gemini, OpenAI, Claude) providers.
     - Configure translation language (e.g., `en-US`).

     ## Step 6: Configure Accessibility
     - In Accessibility panel, enable screen reader support (NVDA, VoiceOver, JAWS).
     - Select TTS provider (Web Speech API, Google Cloud TTS, AWS Polly).
     - Customize language and voice settings.

     ## Step 7: Explore Features
     - **Address Book**: Add contacts via web history or ADP.
     - **Chat**: Start 1:1 or group chats; add CML annotations.
     - **Bookmarks**: Save URLs or media with AI summaries and magnet links.
     - **Media**: Share videos/PDFs via WebTorrent.
     - **Library**: Organize resources with faceted search.
     - **Parental Controls**: Set rules for ward accounts (subdomains).
     - **Backups**: Enable IPFS, Google Drive, or local storage.

     ## Troubleshooting
     - **Cashtab Errors**: Ensure wallet is funded; check `https://chronik.be.cash`.
     - **Module Errors**: Run `npm run rebuild`.
     - **Accessibility**: Test with screen readers; contact support via `github.com/mediaprophet/socialweb/issues`.
     ```
  2. Integrate guide into `README.md` and GitHub Pages demo.
- **Deliverables**:
  - Post-installation guide.
  - Updated documentation.

### Phase 17: Testing and Refinement (Weeks 32-33)
- **Objective**: Ensure reliability, security, and accessibility.
- **Tasks**:
  1. Unit tests: All modules.
  2. Integration tests: Workflows.
  3. E2E tests: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
  4. Accessibility tests: WCAG 2.1 compliance; screen reader tests (NVDA, VoiceOver, JAWS).
  5. Security audit: Encryption, E2EE, OAuth, WAC, sandboxing.
- **Deliverables**:
  - Test suite (>80% coverage).
  - Bug fixes.
  - Accessibility report.
  - Security report.

### Phase 18: Deployment (Week 34)
- **Objective**: Deploy Webizen v0.25.
- **Tasks**:
  1. Build: `npm run build`.
  2. Package WebExtension: `.crx`, `.xpi`.
  3. Package Electron app: Windows, macOS, Linux (using `electron-builder`).[](https://www.electron.build/index.html)
  4. Publish to Chrome Web Store, Firefox Add-ons, GitHub Releases.
  5. Deploy demo to `https://mediaprophet.github.io/socialweb`.
  6. Publish alpha to `github.com/WebCivics/SocialWeb`.
  7. Document usage, development, and accessibility features.
- **Deliverables**:
  - Deployed WebExtension and Electron app.
  - Demo site.
  - Documentation with accessibility guide.

## 6. Workflows

- **Access Control**: Verify payment/token via Cashtab → Store status → Enable app.
- **Activation**: ADP → Cashtab sign → Chronik verify → Quadstore store.
- **Parental Controls**: Guardian sets rules → Enforce via `webRequest` → Store in Quadstore.
- **Web Usage Tracking**: Toggle tracking → Sync with Electron via WebSockets/IPFS.
- **Address Book**: Web history/ADP → Quadstore → Agreement → Chat/Annotations.
- **Chat/Annotations**: GUN/WebRTC/WebSockets → CML annotations → Quadstore → JSON-LD.
- **Bookmarks**: History API or manual → Process media → Store with magnet links → Quadstore (RDF Schema).
- **Agreements**: GUN share → Quadstore → Turtle/N3.
- **Work**: Track tab time → Project association → Git/IPFS → Contribution metadata.
- **Library**: Resource storage → Faceted search → Turtle/N3 permissions, JSON-LD sharing.
- **Media**: WebTorrent share → Chronik payment → Quadstore metadata.
- **Solid Pod**: Store/sync data at `http://localhost:3000`.
- **Local Apps**: Host Solid/Webizen v0.25/third-party apps; translate JSON to RDF/JSON.
- **AI Assistants**: Define agents in RDF/JSON → Query/translate content → UI display.
- **Accessibility**: Apply ARIA attributes; enable TTS for content (bookmarks, chat, etc.).
- **Backups/Sync**: Encrypt with Cashtab key → IPFS/Google Drive/local/Solid.

## 7. Security

- **Cashtab**: Secure key handling.
- **Encryption**: CryptoJS AES-256; GUN SEA; WebTorrent encryption.
- **Permissions**: Turtle/N3 for private/public/permissive access; WAC for future VPN.
- **Chronik**: Cache results.
- **Google Drive/Solid**: Minimal OAuth scopes (`drive.file`).
- **IPFS**: Encrypted data only.
- **WebExtension/Electron**: Sanitize inputs; minimal permissions; restore Electron sandboxing.[](https://pfrazee.github.io/blog/beaker-browser-0.1)

## 8. Testing

- **Unit**: Modules (access, cashtab, chronik, GUN, WebTorrent, WebRTC, WebSockets, addressbook, chat, bookmarks, agreements, media, work, library, solid, apps, translator, ai, parental, accessibility, backups).
- **Integration**: Workflows.
- **E2E**: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
- **Accessibility**: WCAG 2.1 compliance; screen reader tests (NVDA, VoiceOver, JAWS).
- **Security**: Encryption, E2EE, OAuth, WAC, sandboxing.

## 9. Deployment

- **Repository**: `github.com/mediaprophet/socialweb` (development); `github.com/WebCivics/SocialWeb` (alpha).
- **WebExtension**: Chrome Web Store, Firefox Add-ons, GitHub Releases.
- **Electron App**: GitHub Releases (Windows, macOS, Linux).
- **Demo Site**: GitHub Pages (`https://mediaprophet.github.io/socialweb`).
- **Documentation**: README, user/developer guides, accessibility guide, post-installation guide.

## 10. Timeline

- **Duration**: 34 weeks (1-2 developers).
- **Milestones**:
  - Weeks 1-3: Core infrastructure and Beaker refactor.
  - Weeks 4-5: Access control and Cashtab integration.
  - Week 6: Service activation.
  - Weeks 7-8: Parental controls.
  - Weeks 9-10: Web usage tracking.
  - Weeks 11-12: Semantic bookmarks.
  - Weeks 13-14: Chat and annotations.
  - Weeks 15-16: Connection requests.
  - Weeks 17-18: Work tracking.
  - Weeks 19-20: Semantic library.
  - Weeks 21-22: Media sharing/creation.
  - Weeks 23-24: Solid pod and local apps.
  - Weeks 25-26: AI assistants.
  - Weeks 27-28: Accessibility.
  - Weeks 29-30: Backups and sync.
  - Week 31: Post-installation guide.
  - Weeks 32-33: Testing.
  - Week 34: Deployment.

## 11. Risks and Mitigations

- **Beaker Refactor**: Outdated dependencies; run `npm run rebuild` and update to latest Electron (`^32.0.0`).[](https://github.com/beakerbrowser/beaker)
- **Cashtab**: Error if not installed; provide link (`https://chromewebstore.google.com/detail/cashtab/obldfcmebhllhjlhjbnghaipekcppeag`).
- **Chronik**: Cache results; fallback to manual.
- **GUN/WebTorrent**: Multiple relays; IPFS fallback.
- **Google Drive/Solid**: Clear OAuth/setup guide; fallback to IPFS/local.
- **LM Studio/Ollama/AnythingLLM**: Disable if unavailable.
- **Electron Compatibility**: Test across OSes; debug logs (avoid tmux on macOS).[](https://github.com/beakerbrowser/beaker)
- **Local Apps**: Validate third-party app configs; sandbox iframes.
- **Parental Controls**: Ensure robust rule enforcement; provide guardian tutorials.
- **Media Processing**: Handle large files efficiently; fallback to manual metadata.
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

- **Hybrid P2P**: WebTorrent for media, WebRTC for low-latency chat, GUN for messaging (replace Beaker’s Dat).
- **Ontologies**: Versioned `SemBookmarks#v1`, `cml#v1`, `ai#v1`, `parental#v1`, `accessibility#v1` for updates.
- **Sync Scheduler**: Periodic checks for updates.
- **Mobile Vault**: Plan for mobile key store integration.
- **Tutorials**: In-app guides for ADP, Cashtab, media, work, library, access, Solid, AI, parental controls, bookmarks, annotations, accessibility.
- **Beaker Lessons**: Avoid feature bloat (e.g., Beaker’s web-based CLI); focus on user-driven social-web features.[](https://github.com/beakerbrowser/beaker/blob/master/archive-notice.md)

## 14. Next Steps

1. Review specification with stakeholders.
2. Fork `github.com/beakerbrowser/beaker` to `github.com/mediaprophet/socialweb`.
3. Begin Phase 1 development.
4. Iterate through phases, testing each milestone.
5. Publish alpha to `github.com/WebCivics/SocialWeb`.