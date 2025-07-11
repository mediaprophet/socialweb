# Webizen v1: Software Development Specification (Version 6)

## 1. Project Overview

**Date**: July 11, 2025  
**Purpose**: Webizen v1 is a decentralized application delivered as a WebExtension (Chrome, Firefox) and Electron desktop app, enabling social interactions via an address book, chat, semantic bookmarks, RDF-based agreements, media sharing, work tracking, a semantic library, and AI assistants. It integrates with WebCard Viewer’s Agent Discovery Protocol (ADP) for identity discovery, uses Cashtab for eCash-based access and transactions, Chronik for blockchain queries, GUN.eco for chat, WebTorrent/WebRTC/WebSockets for media and communication, Quadstore for local RDF storage, and supports secure backups to IPFS, Google Drive, local HDD, or Solid pods. The Electron app hosts a local Solid pod and local apps, with a WebExtension tracking web usage. Access requires a small eCash payment or token until development costs are recouped, then becomes obligation-free. The system is modular, extensible, uses Turtle/N3 for permissions/network and JSON-LD for marketing/Web 2.0, and provides device synchronization.

### 1.1 Goals
- **Access Control**: Require eCash payment (e.g., 100 XEC) or token until development costs (e.g., 1,000,000 XEC) are met; then obligation-free.
- **Service Activation**: Verify eCash address ownership via Cashtab.
- **Identity Discovery**: Fetch user details (eCash address, optional WebID) via ADP.
- **Address Book**: Manage contacts from web history or ADP, with agreement-based relationships.
- **Chat and Annotations**: Support 1:1, group chats, and URL annotations (e.g., `https://www.youtube.com/watch?v=nBJhO-o5q2s`).
- **Semantic Bookmarks**: Store browser history or manual bookmarks per SemBookmarks ontology in JSON-LD.
- **Connection Requests**: Negotiate RDF-based agreements (UDHR, CRC terms).
- **Media Sharing**: Share videos/PDFs via WebTorrent, with free previews and paid full access.
- **Content Creation**: Tool for uploading media and associating with ADP/eCash.
- **Work Tracking**: Track active tab time, project associations, Git integration, contribution metadata.
- **Semantic Library**: Share resources by topics/groups with faceted search and permissioned access.
- **Web Usage Tracking**: WebExtension tracks web usage (toggleable) and syncs with Electron app.
- **Solid Pod**: Host local Solid pod in Electron for storage/sync.
- **Local Apps**: Host Solid, Webizen v1, and third-party apps (JSON-based) with translator.
- **AI Assistants**: Support local/online LLMs as user-associated agents with RDF/JSON characteristics.
- **Secure Backups**: Encrypt data with Cashtab key; store in IPFS, Google Drive, local HDD, or Solid pods.
- **Device Synchronization**: Sync data across devices using encrypted backups or Solid pods.
- **Deployment**: Deliver as WebExtension, Electron app, and demo site on GitHub Pages.

### 1.2 Key Features
- **Modular Design**: Independent modules for access, ADP, Cashtab, Chronik, address book, chat, bookmarks, agreements, media, work, library, solid, apps, translator, AI, backups.
- **Extensibility**: Plugin system and dynamic RDF ontology support.
- **Data Formats**: Turtle/N3 for permissions/network/WAC; JSON-LD for marketing/Web 2.0; RDF/JSON for AI assistants.
- **Faceted Search**: SPARQL-based search for library resources.
- **Device Sync**: Cross-device consistency via encrypted backups or Solid pods.
- **Access Model**: eCash payment or token until obligation-free.
- **Dual Implementation**: WebExtension (sidebar) and Electron app (main window with Solid pod and local apps).
- **Decentralized**: No server dependencies; uses GUN, WebTorrent, WebRTC, IPFS, Solid.

### 1.3 Client Requirements
- **Cashtab Wallet**: Browser extension (WebExtension) or integrated (Electron) for payments/tokens.
- **Browser/OS**: Chrome (v120+), Firefox (v115+) for WebExtension; Windows, macOS, Linux for Electron.
- **Storage**: IndexedDB (50MB) for Quadstore; 100MB for Solid pod (Electron).
- **Internet**: Required for ADP, Chronik, GUN, WebTorrent, IPFS, Google Drive, online LLMs.
- **Optional**: LM Studio for local AI; Solid pod for sync.
- **Hardware**: Minimum 4GB RAM, 1GHz CPU for Electron (Solid pod, local apps).

## 2. Architecture

### 2.1 Components
- **Access Control**: Verifies eCash payment/token via Cashtab/Chronik; tracks obligation status.
- **Identity (ADP)**: Fetches eCash address (`adp:hasEcashAccount`) from DNS TXT + IPFS.
- **Service Activation (Cashtab)**: Signs message to verify eCash address; Chronik validates.
- **Address Book**: Stores contacts from web history or ADP; relationships via agreements.
- **Chat (GUN.eco, WebRTC, WebSockets)**: Real-time 1:1, group chats, and URL annotations.
- **Bookmarks (SemBookmarks)**: JSON-LD bookmarks from browser history or manual entry.
- **Agreements**: RDF-based connection requests shared via GUN.
- **Media (WebTorrent)**: P2P sharing of videos/PDFs; eCash transactions for full access.
- **Content Creation**: Tool for media upload and metadata association.
- **Work Tracking**: Tracks active tab time, project associations, Git integration, contribution metadata.
- **Semantic Library**: Permissioned resource sharing with faceted search.
- **Web Usage Tracking**: WebExtension tracks usage; syncs with Electron app.
- **Solid Pod**: Local Solid server in Electron for storage/sync.
- **Local Apps**: Host Solid, Webizen v1, and third-party apps with JSON-to-RDF translator.
- **AI Assistants**: Local/online LLMs with RDF/JSON characteristics.
- **Backups**: Encrypted storage in Quadstore, IPFS, Google Drive, local HDD, or Solid pods.
- **Device Sync**: Sync via IPFS/Google Drive/Solid; future mobile vault.
- **UI**: React-based sidebar (WebExtension) or main window (Electron) with Tailwind CSS.

### 2.2 Modularity
- **Modules**: Separate directories (`src/modules/`) for each feature.
- **Interfaces**: Standard module interface (`init`, `handleEvent`, `getData`).
- **Dependency Injection**: `src/services/` provides shared services.
- **Config**: `src/config.js` toggles modules and access settings:
  ```javascript
  export const modules = {
    access: true,
    adp: true,
    cashtab: true,
    chronik: true,
    addressbook: true,
    chat: { gun: true, webrtc: true, websockets: true },
    bookmarks: true,
    agreements: true,
    media: true,
    work: true,
    library: true,
    solid: true,
    apps: true,
    translator: true,
    ai: true,
    backups: { ipfs: true, googleDrive: true, local: true, solid: true },
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
│   │   └── AI.js           # AI assistant UI
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
│   │   ├── chat/           # GUN, WebRTC, WebSockets
│   │   │   ├── gun.js
│   │   │   ├── webrtc.js
│   │   │   ├── websockets.js
│   │   │   └── index.js
│   │   ├── bookmarks/      # Semantic bookmarks
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
│   ├── config.js           # Module and access configuration
│   ├── App.js              # Main React app
│   └── background.js       # WebExtension background script
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
- **Electron**: Electron (`^32.0.0`), React 17, Tailwind CSS.
- **Identity/Data**: N3.js (`^1.26.0`), Quadstore (`^10.0.0`), quadstore-comunica (`^4.0.0`), @comunica/query-sparql (`^3.0.0`), jsonld.js (`^8.3.2`), ipfs-http-client (`^60.0.0`), @inrupt/solid-client (`^2.0.0`), @inrupt/solid-client-authn-browser (`^2.0.0`), node-solid-server (`^6.0.0`).
- **Communication**: GUN.eco (`^0.2020.1236`), WebTorrent (`^2.0.0`), WebRTC (native), WebSockets (`ws@^8.18.0`).
- **eCash**: Cashtab (`@cashtab/wallet-lib`), Chronik (`chronik-client@^0.11.0`).
- **Backups**: CryptoJS (`^4.1.1`), googleapis (`^144.0.0`).
- **AI**: LM Studio (`http://localhost:1234/v1/chat/completions`), xAI API (`https://x.ai/api`).
- **Apps**: `cimba` (updated fork), ThemeForest JSON-based apps.
- **RDF Vocabularies**:
  - SemBookmarks (`https://mediaprophet.github.io/init-draft-standards-wip/SemBookmarks#`).
  - ActivityStreams (`https://www.w3.org/ns/activitystreams#`).
  - ADP (`https://webcivics.github.io/adp/ontdev/adp#`).
  - Agreements (`https://mediaprophet.github.io/init-draft-standards-wip/agreements#`).
  - Media (`https://mediaprophet.github.io/init-draft-standards-wip/media#`).
  - Work (`https://mediaprophet.github.io/init-draft-standards-wip/work#`).
  - Library (`https://mediaprophet.github.io/init-draft-standards-wip/library#`).
  - Access (`https://mediaprophet.github.io/init-draft-standards-wip/access#`).
  - AI (`https://mediaprophet.github.io/init-draft-standards-wip/ai#`).

## 4. Development Phases

### Phase 1: Core Infrastructure (Weeks 1-2)
- **Objective**: Set up modular WebExtension and Electron app.
- **Tasks**:
  1. Initialize `github.com/mediaprophet/socialweb`.
  2. Configure `manifest.json` for WebExtension:
     ```json
     {
       "name": "Webizen v1",
       "version": "0.1.0",
       "permissions": ["storage", "activeTab", "sidebarAction", "tabs", "history", "https://*/*"],
       "sidebar_action": { "default_panel": "sidebar.html", "default_icon": "icon.png" },
       "background": { "scripts": ["background.js"], "persistent": false },
       "browser_action": { "default_icon": "icon.png" }
     }
     ```
  3. Configure Electron in `platforms/electron/main.js`:
     ```javascript
     const { app, BrowserWindow } = require('electron');
     const { startServer } = require('node-solid-server');
     app.whenReady().then(async () => {
       await startServer({ port: 3000, root: './solid-data' });
       const win = new BrowserWindow({ width: 400, height: 600 });
       win.loadFile('public/index.html');
     });
     ```
  4. Reuse WebCard Viewer’s Tailwind/PostCSS configs.
  5. Implement `modules/adp`, `modules/cashtab`, `modules/chronik`, `modules/solid`.
  6. Set up `services/quadstore.js`, `services/jsonld.js`, `services/turtle.js`, `services/ipfs.js`, `services/webtorrent.js`, `services/webrtc.js`, `services/websockets.js`, `services/permissions.js`.
  7. Create `services/eventBus.js`.
- **Deliverables**:
  - WebExtension and Electron skeleton.
  - Modular ADP, Cashtab, Chronik, Solid, Quadstore, JSON-LD, Turtle/N3, WebTorrent, WebRTC, WebSockets.

### Phase 2: Access Control (Weeks 3-4)
- **Objective**: Implement payment/token access model.
- **Tasks**:
  1. Implement `modules/access`:
     - Verify payment to `ecash:qp...socialwebfund` or token ownership:
       ```javascript
       import { ChronikClient } from 'chronik-client';
       const chronik = new ChronikClient('https://chronik.be.cash');
       async function verifyAccess(userAddress) {
         const paid = await chronik.address(userAddress).history(0, 10).then(txs => 
           txs.txs.some(tx => tx.outputs.some(out => out.value === '100' && out.address === fundAddress)));
         const hasToken = await chronik.address(userAddress).utxos().then(utxos => 
           utxos.some(utxo => utxo.token?.tokenId === tokenId));
         return paid || hasToken || accessConfig.obligationFree;
       }
       ```
     - Track obligation status in Quadstore:
       ```turtle
       @prefix access: <https://mediaprophet.github.io/init-draft-standards-wip/access#>.
       <https://sailingdigital.com/access> a access:AccessRecord ;
         access:user <ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a> ;
         access:status access:Paid.
       ```
     - Update `config.js` when obligation-free.
  2. Create `components/Access.js` for payment/token UI.
  3. Handle errors (e.g., no Cashtab).
- **Deliverables**:
  - Access module.
  - Access UI.
  - Obligation tracking.

### Phase 3: Service Activation (Week 5)
- **Objective**: Enable Cashtab-based activation.
- **Tasks**:
  1. Implement `modules/cashtab/activateService.js`:
     - Sign message with Cashtab.
     - Verify with Chronik.
     - Store in Quadstore (`adp:serviceActivated`).
  2. Update `components/Sidebar.js` (WebExtension) and `components/MainWindow.js` (Electron).
  3. Handle errors.
- **Deliverables**:
  - Activation module.
  - Platform-specific UI.

### Phase 4: Web Usage Tracking (Weeks 6-7)
- **Objective**: Implement toggleable web usage tracking in WebExtension.
- **Tasks**:
  1. Update `modules/work`:
     - Track active tab time (WebExtension):
       ```javascript
       browser.tabs.onActivated.addListener((tab) => {
         if (trackingEnabled) {
           store.put([
             DataFactory.quad(
               DataFactory.namedNode(`https://sailingdigital.com/work/${Date.now()}`),
               DataFactory.namedNode('https://mediaprophet.github.io/init-draft-standards-wip/work#url'),
               DataFactory.literal(tab.url)
             ),
           ]);
         }
       });
       ```
     - Sync with Electron via WebSockets:
       ```javascript
       const ws = new WebSocket('ws://localhost:3001');
       ws.send(JSON.stringify({ type: 'tracking', data: workData }));
       ```
  2. Create `components/Tracking.js` for toggle UI.
  3. Store tracking state in Quadstore.
- **Deliverables**:
  - Tracking module.
  - Tracking UI.

### Phase 5: Address Book (Weeks 8-9)
- **Objective**: Implement address book with ADP and semantic bookmarks.
- **Tasks**:
  1. Implement `modules/addressbook`:
     - Create entries from web history or ADP:
       ```turtle
       @prefix ab: <https://mediaprophet.github.io/init-draft-standards-wip/addressbook#>.
       <https://sailingdigital.com/contact/123> a ab:Contact ;
         ab:domain <https://sailingdigital.com> ;
         ab:ecashAddress <ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a>.
       ```
     - Permissions in Turtle/N3:
       ```turtle
       @prefix perm: <https://mediaprophet.github.io/init-draft-standards-wip/permissions#>.
       <https://sailingdigital.com/contact/123> perm:access perm:Private ;
         perm:allowedActions [ perm:chat true ; perm:annotate true ].
       ```
     - Initiate relationships via `modules/agreements`.
  2. Create `components/AddressBook.js`.
  3. Serialize as JSON-LD for Web 2.0.
- **Deliverables**:
  - Address book module.
  - Turtle/N3 permissions.
  - Address book UI.

### Phase 6: Chat and URL Annotations (Weeks 10-11)
- **Objective**: Implement chat and annotations with GUN, WebRTC, WebSockets.
- **Tasks**:
  1. Implement `modules/chat`:
     - GUN: `social-web-chat/conversation-<domain>`, `annotation-<clean-url>`.
     - WebRTC: P2P chats with WebSocket signaling.
     - WebSockets: Fallback for GUN.
     - Store in Quadstore (`as:Note`).
  2. Use `tabs` API (WebExtension) or `window.location` (Electron) for clean URL.
  3. Verify group access with `modules/chronik`.
  4. Create `components/Chat.js`.
- **Deliverables**:
  - Chat/annotation module.
  - Quadstore storage.
  - Token-based access.

### Phase 7: Semantic Bookmarks (Weeks 12-13)
- **Objective**: Implement SemBookmarks in JSON-LD.
- **Tasks**:
  1. Implement `modules/bookmarks`:
     - Auto-capture history or manual entry.
     - Store as JSON-LD.
  2. Implement SPARQL queries.
  3. Create `components/Bookmarks.js`.
- **Deliverables**:
  - Bookmarks module.
  - JSON-LD serialization.
  - Bookmarks UI.

### Phase 8: Connection Requests (Weeks 14-15)
- **Objective**: Implement agreements without Solid.
- **Tasks**:
  1. Implement `modules/agreements`:
     - Store in Quadstore; share via GUN.
  2. Create `components/Agreements.js`.
  3. Set permissions in Turtle/N3.
- **Deliverables**:
  - Agreements module.
  - Turtle/N3 permissions.
  - Agreements UI.

### Phase 9: Work Tracking (Weeks 16-17)
- **Objective**: Implement work tracking and contribution metadata.
- **Tasks**:
  1. Update `modules/work`:
     - Track tab time; associate with projects:
       ```turtle
       <https://sailingdigital.com/project/123> a work:Project ;
         work:gitRepo <https://github.com/mediaprophet/project> ;
         work:contributor <ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a> ;
         work:timeSpent "3600s".
       ```
     - Non-linear timelines for contributions.
     - Publish to IPFS/WebTorrent.
  2. Create `components/Work.js`.
- **Deliverables**:
  - Work tracking module.
  - Work UI.

### Phase 10: Semantic Library (Weeks 18-19)
- **Objective**: Implement library with faceted search.
- **Tasks**:
  1. Implement `modules/library`:
     - Store resources; faceted search via SPARQL.
     - Permissions in Turtle/N3; serialize as JSON-LD.
  2. Create `components/Library.js`.
- **Deliverables**:
  - Library module.
  - Faceted search UI.

### Phase 11: Media Sharing and Creation (Weeks 20-21)
- **Objective**: Implement WebTorrent-based media sharing and creation.
- **Tasks**:
  1. Implement `modules/media`:
     - Share via WebTorrent; verify payment with Chronik.
  2. Create `components/Media.js` with file input and preview generation.
- **Deliverables**:
  - Media module.
  - Content creation tool.
  - Media UI.

### Phase 12: Solid Pod and Local Apps (Weeks 22-23)
- **Objective**: Implement local Solid pod and app hosting.
- **Tasks**:
  1. Implement `modules/solid`:
     - Run `node-solid-server` at `http://localhost:3000`.
     - Store/sync data with `@inrupt/solid-client`.
  2. Implement `modules/apps`:
     - Host apps (Solid, Webizen v1, third-party) in iframes/BrowserWindows.
     - Example: Updated `cimba` fork.
  3. Implement `modules/translator`:
     - Convert JSON to RDF/JSON for third-party apps:
       ```javascript
       import jsonld from 'jsonld';
       async function translateJson(json, config) {
         const rdf = await jsonld.toRDF({ ...json, '@context': config.context });
         return rdf;
       }
       ```
  4. Create `components/Apps.js`.
  5. Store app configs in `apps/configs/`.
- **Deliverables**:
  - Solid pod module.
  - App hosting module.
  - Translator module.
  - Apps UI.

### Phase 13: AI Assistants (Week 24)
- **Objective**: Implement local/online AI assistants.
- **Tasks**:
  1. Update `modules/ai`:
     - Define agents in RDF/JSON:
       ```json
       {
         "@context": "https://mediaprophet.github.io/init-draft-standards-wip/ai#",
         "@type": "ai:Assistant",
         "ai:role": "coding",
         "ai:endpoint": "http://localhost:1234/v1/chat/completions"
       }
       ```
     - Support LM Studio and xAI API.
  2. Create `components/AI.js`.
- **Deliverables**:
  - AI assistant module.
  - AI UI.

### Phase 14: Secure Backups and Device Sync (Weeks 25-26)
- **Objective**: Implement encrypted backups and synchronization.
- **Tasks**:
  1. Implement `modules/backups`:
     - Encrypt with Cashtab key.
     - IPFS, Google Drive, local HDD, Solid pods.
     - Sync scheduler (every 5 minutes).
  2. Create `components/Backups.js`.
- **Deliverables**:
  - Backup/sync module.
  - Backup UI.

### Phase 15: Testing and Refinement (Weeks 27-28)
- **Objective**: Ensure reliability and security.
- **Tasks**:
  1. Unit tests: All modules.
  2. Integration tests: Workflows.
  3. E2E tests: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
  4. Security audit: Encryption, E2EE, OAuth, WAC.
- **Deliverables**:
  - Test suite (>80% coverage).
  - Bug fixes.
  - Security report.

### Phase 16: Deployment (Week 29)
- **Objective**: Deploy WebExtension, Electron app, and demo site.
- **Tasks**:
  1. Build: `npm run build`.
  2. Package WebExtension: `.crx`, `.xpi`.
  3. Package Electron app: Windows, macOS, Linux.
  4. Publish to Chrome Web Store, Firefox Add-ons, GitHub Releases.
  5. Deploy demo to `https://mediaprophet.github.io/socialweb`.
  6. Publish alpha to `github.com/WebCivics/SocialWeb`.
  7. Document usage and development.
- **Deliverables**:
  - Deployed WebExtension and Electron app.
  - Demo site.
  - Documentation.

## 5. Workflows

- **Access Control**: Verify payment/token → Store status → Enable app.
- **Activation**: ADP → Cashtab sign → Chronik verify → Quadstore store.
- **Web Usage Tracking**: Toggle tracking → Sync with Electron via WebSockets/IPFS.
- **Address Book**: Web history/ADP → Quadstore → Agreement → Chat/Annotations.
- **Chat/Annotations**: GUN/WebRTC/WebSockets → Quadstore → JSON-LD.
- **Bookmarks**: History API or manual → Quadstore (JSON-LD) → SPARQL.
- **Agreements**: GUN share → Quadstore → Turtle/N3.
- **Work**: Track tab time → Project association → Git/IPFS → Contribution metadata.
- **Library**: Resource storage → Faceted search → Turtle/N3 permissions, JSON-LD sharing.
- **Media**: WebTorrent share → Chronik payment → Quadstore metadata.
- **Solid Pod**: Store/sync data at `http://localhost:3000`.
- **Local Apps**: Host Solid/Webizen v1/third-party apps; translate JSON to RDF/JSON.
- **AI Assistants**: Define agents in RDF/JSON → Query agreements → LLM response → UI display.
- **Backups/Sync**: Encrypt with Cashtab key → IPFS/Google Drive/local/Solid.

## 6. Security

- **Cashtab**: Secure key handling.
- **Encryption**: CryptoJS AES-256; GUN SEA; WebTorrent encryption.
- **Permissions**: Turtle/N3 for private/public/permissive access; WAC for future VPN.
- **Chronik**: Cache results.
- **Google Drive**: Minimal OAuth scopes (`drive.file`).
- **IPFS/Solid**: Encrypted data only.
- **WebExtension/Electron**: Sanitize inputs; minimal permissions.

## 7. Testing

- **Unit**: Modules (access, Cashtab, Chronik, GUN, WebTorrent, WebRTC, WebSockets, address book, work, library, solid, apps, translator, AI, backups).
- **Integration**: Workflows.
- **E2E**: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
- **Security**: Encryption, E2EE, OAuth, WAC.

## 8. Deployment

- **Repository**: `github.com/mediaprophet/socialweb` (development); `github.com/WebCivics/SocialWeb` (alpha).
- **WebExtension**: Chrome Web Store, Firefox Add-ons, GitHub Releases.
- **Electron App**: GitHub Releases (Windows, macOS, Linux).
- **Demo Site**: GitHub Pages (`https://mediaprophet.github.io/socialweb`).
- **Documentation**: README, user/developer guides.

## 9. Timeline

- **Duration**: 29 weeks (1-2 developers).
- **Milestones**:
  - Weeks 1-2: Core infrastructure.
  - Weeks 3-4: Access control.
  - Week 5: Service activation.
  - Weeks 6-7: Web usage tracking.
  - Weeks 8-9: Address book.
  - Weeks 10-11: Chat and annotations.
  - Weeks 12-13: Semantic bookmarks.
  - Weeks 14-15: Connection requests.
  - Weeks 16-17: Work tracking.
  - Weeks 18-19: Semantic library.
  - Weeks 20-21: Media sharing/creation.
  - Weeks 22-23: Solid pod and local apps.
  - Week 24: AI assistants.
  - Weeks 25-26: Backups and sync.
  - Weeks 27-28: Testing.
  - Week 29: Deployment.

## 10. Risks and Mitigations

- **Cashtab**: Error if not installed; provide link (`https://chromewebstore.google.com/detail/cashtab/obldfcmebhllhjlhjbnghaipekcppeag`).
- **Chronik**: Cache results; fallback to manual.
- **GUN/WebTorrent**: Multiple relays; IPFS fallback.
- **Google Drive/Solid**: Clear OAuth/setup guide; fallback to IPFS/local.
- **LM Studio**: Disable if unavailable.
- **Electron Compatibility**: Test across OSes; debug logs.
- **Local Apps**: Validate third-party app configs; sandbox iframes.
- **Adoption**: In-app tutorials and docs.

## 11. Future Enhancements

- Social VPN using Tailscale and WAC (pending study).
- Mobile vault integration (Cashtab-compatible).
- Advanced media formats.
- Social analytics dashboard.
- WebPush notifications.

## 12. Recommendations

- **Hybrid P2P**: WebTorrent for media, WebRTC for low-latency chat, GUN for messaging.
- **Ontologies**: Define `access#`, `work#`, `library#`, `ai#` for consistency.
- **Sync Scheduler**: Periodic checks for updates.
- **Mobile Vault**: Plan for mobile key store integration.
- **Tutorials**: In-app guides for ADP, Cashtab, media, work, library, access, Solid, and AI.

## 13. Next Steps

1. Review specification with stakeholders.
2. Initialize `github.com/mediaprophet/socialweb`.
3. Begin Phase 1 development.
4. Iterate through phases, testing each milestone.
5. Publish alpha to `github.com/WebCivics/SocialWeb`.