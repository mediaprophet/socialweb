# Webizen v1: Software Development Specification (Version 8)

## 1. Project Overview

**Date**: July 11, 2025  
**Purpose**: Webizen v1 is a decentralized application delivered as a WebExtension (Chrome, Firefox) and Electron desktop app, enabling social interactions via an address book, chat with Context Markup Language (CML) annotations, semantic bookmarks with media processing, RDF-based agreements, media sharing, work tracking, a semantic library, web usage tracking, local Solid pod, local apps, AI assistants, and parental controls. It integrates with WebCard Viewer’s Agent Discovery Protocol (ADP) for identity discovery, uses Cashtab for eCash-based access and transactions, Chronik for blockchain queries, GUN.eco for chat, WebTorrent/WebRTC/WebSockets for media and communication, Quadstore for local RDF storage, and supports secure backups to IPFS, Google Drive, local HDD, or Solid pods. Access requires a small eCash payment or token until development costs are recouped, then becomes obligation-free. The system is modular, extensible, uses Turtle/N3 for permissions/network/WAC, JSON-LD for marketing/Web 2.0, and RDF/JSON for AI configs, with independent module updates to avoid breaking changes.

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
- **Local Apps**: Host Solid, Webizen v1, and third-party apps with JSON-to-RDF translator.
- **AI Assistants**: Support local (LM Studio, Ollama, AnythingLLM) and online (Grok, Gemini, OpenAI, Claude) LLMs with RDF/JSON configs and translation.
- **Parental Controls**: Guardians manage ward accounts (subdomains) with rules (e.g., block adult content).
- **Secure Backups**: Encrypt data with Cashtab key; store in IPFS, Google Drive, local HDD, or Solid pods.
- **Device Synchronization**: Sync data across devices using encrypted backups or Solid pods.
- **Deployment**: Deliver as WebExtension, Electron app, and demo site on GitHub Pages.

### 1.2 Key Features
- **Modular Design**: Independent modules with versioned interfaces for updates without breaking changes.
- **Extensibility**: Plugin system and dynamic RDF ontology support (e.g., `SemBookmarks#v1`).
- **Data Formats**: Turtle/N3 for permissions/network/WAC; JSON-LD for marketing/Web 2.0; RDF/JSON for AI configs.
- **Faceted Search**: SPARQL-based search for library resources.
- **Device Sync**: Cross-device consistency via encrypted backups or Solid pods.
- **Access Model**: eCash payment or token until obligation-free.
- **Dual Implementation**: WebExtension (sidebar) and Electron app (main window with Solid pod, local apps).
- **Decentralized**: No server dependencies; uses GUN, WebTorrent, WebRTC, IPFS, Solid.

### 1.3 Client Requirements
- **Cashtab Wallet**: Browser extension (WebExtension) or integrated (Electron) for payments/tokens.
- **Browser/OS**: Chrome (v120+), Firefox (v115+) for WebExtension; Windows, macOS, Linux for Electron.
- **Storage**: IndexedDB (50MB) for Quadstore; 100MB for Solid pod (Electron).
- **Internet**: Required for ADP, Chronik, GUN, WebTorrent, IPFS, Google Drive, online LLMs.
- **Optional**: LM Studio, Ollama, AnythingLLM for local AI; Solid pod for sync.
- **Hardware**: Minimum 4GB RAM, 1GHz CPU for Electron (Solid pod, local apps, media processing).

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
- **Local Apps**: Host Solid, Webizen v1, and third-party apps with JSON-to-RDF translator.
- **AI Assistants**: Local/online LLMs with RDF/JSON configs and translation.
- **Parental Controls**: Guardian-managed ward accounts with rule-based restrictions.
- **Backups**: Encrypted storage in Quadstore, IPFS, Google Drive, local HDD, or Solid pods.
- **Device Sync**: Sync via IPFS/Google Drive/Solid; future mobile vault.
- **UI**: React-based sidebar (WebExtension) or main window (Electron) with Tailwind CSS.

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
│   │   └── ParentalControls.js # Parental controls UI
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
│   ├── ontologies/
│   │   ├── SemBookmarks-v1.ttl # Versioned bookmark ontology
│   │   ├── cml-v1.ttl         # Versioned CML ontology
│   │   ├── ai-v1.ttl          # Versioned AI ontology
│   │   ├── parental-v1.ttl     # Versioned parental ontology
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
- **Identity/Data**: N3.js (`^1.26.0`), Quadstore (`^10.0.0`), quadstore-comunica (`^4.0.0`), @comunica/query-sparql (`^3.0.0`), jsonld.js (`^8.3.2`), ipfs-http-client (`^60.0.0`), @inrupt/solid-client (`^2.0.0`), @inrupt/solid-client-authn-browser (`^2.0.0`), node-solid-server (`^6.0.0`), pdf.js (`^4.0.0`).
- **Communication**: GUN.eco (`^0.2020.1236`), WebTorrent (`^2.0.0`), WebRTC (native), WebSockets (`ws@^8.18.0`).
- **eCash**: Cashtab (`@cashtab/wallet-lib`), Chronik (`chronik-client@^0.11.0`).
- **Backups**: CryptoJS (`^4.1.1`), googleapis (`^144.0.0`).
- **AI**: LM Studio (`http://localhost:1234/v1/chat/completions`), Ollama (`http://localhost:11434`), AnythingLLM (local), Grok (`https://x.ai/api`), Gemini, OpenAI, Claude.
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
       "permissions": ["storage", "activeTab", "sidebarAction", "tabs", "history", "webRequest", "webRequestBlocking", "https://*/*"],
       "sidebar_action": { "default_panel": "sidebar.html", "default_icon": "icon.png" },
       "background": { "scripts": ["background.js"], "persistent": true },
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
  8. Define versioned ontologies in `ontologies/`.
- **Deliverables**:
  - WebExtension and Electron skeleton.
  - Modular ADP, Cashtab, Chronik, Solid, Quadstore, JSON-LD, Turtle/N3, WebTorrent, WebRTC, WebSockets.

### Phase 2: Access Control (Weeks 3-4)
- **Objective**: Implement payment/token access model.
- **Tasks**:
  1. Implement `modules/access`:
     - Verify payment or token; track obligation status in Quadstore.
  2. Create `components/Access.js` for payment/token UI.
  3. Handle errors (e.g., no Cashtab).
- **Deliverables**:
  - Access module (v1.0.0).
  - Access UI.
  - Obligation tracking.

### Phase 3: Service Activation (Week 5)
- **Objective**: Enable Cashtab-based activation.
- **Tasks**:
  1. Implement `modules/cashtab/activateService.js`:
     - Sign message with Cashtab; verify with Chronik; store in Quadstore.
  2. Update `components/Sidebar.js` (WebExtension) and `components/MainWindow.js` (Electron).
  3. Handle errors.
- **Deliverables**:
  - Activation module (v1.0.0).
  - Platform-specific UI.

### Phase 4: Parental Controls (Weeks 6-7)
- **Objective**: Implement guardian-managed ward accounts.
- **Tasks**:
  1. Implement `modules/parental`:
     - Link ward subdomain; enforce rules via `webRequest`.
     - Store rules in Quadstore (Turtle/N3).
  2. Create `components/ParentalControls.js`.
- **Deliverables**:
  - Parental controls module (v1.0.0).
  - Parental controls UI.

### Phase 5: Web Usage Tracking (Weeks 8-9)
- **Objective**: Implement toggleable web usage tracking in WebExtension.
- **Tasks**:
  1. Update `modules/work`:
     - Track active tab time; sync with Electron via WebSockets/IPFS.
  2. Create `components/Tracking.js`.
- **Deliverables**:
  - Tracking module (v1.0.0).
  - Tracking UI.

### Phase 6: Semantic Bookmarks (Weeks 10-11)
- **Objective**: Implement SemBookmarks with media processing and magnet links.
- **Tasks**:
  1. Update `modules/bookmarks`:
     - Process PDFs/media with `pdf.js`:
       ```javascript
       import { getDocument } from 'pdf.js';
       async function processMedia(url) {
         const pdf = await getDocument(url).promise;
         const text = await pdf.getPage(1).getTextContent();
         const summary = await fetch('https://x.ai/api', {
           method: 'POST',
           body: JSON.stringify({
             model: 'grok',
             messages: [{ role: 'user', content: `Summarize: ${text.items.map(i => i.str).join(' ')}` }],
           }),
         }).then(res => res.json());
         const client = new WebTorrent();
         const magnetURI = await new Promise(resolve => client.createTorrent({ path: url }, torrent => resolve(torrent.magnetURI)));
         return { summary: summary.data.choices[0].message.content, magnetURI };
       }
       ```
     - Store in Quadstore (RDF Schema):
       ```turtle
       @prefix sembm: <https://mediaprophet.github.io/init-draft-standards-wip/SemBookmarks#v1>.
       <https://sailingdigital.com/bookmark/123> a sembm:Bookmark ;
         sembm:url <https://example.com/doc.pdf> ;
         sembm:contentSummary "AI-generated summary" ;
         sembm:processedBy <ai:grok> ;
         sembm:magnetLink "magnet:?xt=urn:btih:...".
       ```
  2. Create `components/Bookmarks.js`.
- **Deliverables**:
  - Bookmarks module (v1.0.0).
  - JSON-LD/RDF Schema serialization.
  - Bookmarks UI.

### Phase 7: Chat and URL Annotations (Weeks 12-13)
- **Objective**: Implement chat and annotations with CML support.
- **Tasks**:
  1. Update `modules/chat`:
     - Support CML for annotations:
       ```turtle
       @prefix cml: <https://mediaprophet.github.io/init-draft-standards-wip/cml#v1>.
       @prefix as: <https://www.w3.org/ns/activitystreams#>.
       <https://sailingdigital.com/annotation/123> a as:Note ;
         as:context <https://www.youtube.com/watch?v=nBJhO-o5q2s> ;
         cml:contextDescription "Video about decentralized systems".
       ```
     - Share via GUN.eco for social discovery.
  2. Create `components/Chat.js`.
- **Deliverables**:
  - Chat/annotation module (v1.0.0).
  - CML support.
  - Quadstore storage.

### Phase 8: Connection Requests (Weeks 14-15)
- **Objective**: Implement agreements without Solid.
- **Tasks**:
  1. Implement `modules/agreements`:
     - Store in Quadstore; share via GUN.
  2. Create `components/Agreements.js`.
- **Deliverables**:
  - Agreements module (v1.0.0).
  - Turtle/N3 permissions.
  - Agreements UI.

### Phase 9: Work Tracking (Weeks 16-17)
- **Objective**: Implement work tracking and contribution metadata.
- **Tasks**:
  1. Update `modules/work`:
     - Track tab time; associate with projects; publish to IPFS/WebTorrent.
  2. Create `components/Work.js`.
- **Deliverables**:
  - Work tracking module (v1.0.0).
  - Work UI.

### Phase 10: Semantic Library (Weeks 18-19)
- **Objective**: Implement library with faceted search.
- **Tasks**:
  1. Implement `modules/library`:
     - Store resources; faceted search via SPARQL.
  2. Create `components/Library.js`.
- **Deliverables**:
  - Library module (v1.0.0).
  - Faceted search UI.

### Phase 11: Media Sharing and Creation (Weeks 20-21)
- **Objective**: Implement WebTorrent-based media sharing and creation.
- **Tasks**:
  1. Implement `modules/media`:
     - Share via WebTorrent; verify payment with Chronik.
  2. Create `components/Media.js`.
- **Deliverables**:
  - Media module (v1.0.0).
  - Content creation tool.
  - Media UI.

### Phase 12: Solid Pod and Local Apps (Weeks 22-23)
- **Objective**: Implement local Solid pod and app hosting.
- **Tasks**:
  1. Implement `modules/solid`, `modules/apps`, `modules/translator`.
  2. Create `components/Apps.js`.
  3. Store app configs in `apps/configs/`.
- **Deliverables**:
  - Solid pod module (v1.0.0).
  - App hosting module (v1.0.0).
  - Translator module (v1.0.0).
  - Apps UI.

### Phase 13: AI Assistants (Weeks 24-25)
- **Objective**: Implement local/online AI assistants with translation.
- **Tasks**:
  1. Update `modules/ai`:
     - Support multiple LLMs; store configs in `ai/configs/`.
     - Translate content with collapsed original display.
  2. Create `components/AI.js`.
- **Deliverables**:
  - AI assistant module (v1.0.0).
  - Translation UI.

### Phase 14: Secure Backups and Device Sync (Weeks 26-27)
- **Objective**: Implement encrypted backups and synchronization.
- **Tasks**:
  1. Implement `modules/backups`:
     - Encrypt with Cashtab key; sync via IPFS, Google Drive, local HDD, Solid pods.
  2. Create `components/Backups.js`.
- **Deliverables**:
  - Backup/sync module (v1.0.0).
  - Backup UI.

### Phase 15: Testing and Refinement (Weeks 28-29)
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

### Phase 16: Deployment (Week 30)
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
- **Local Apps**: Host Solid/Webizen v1/third-party apps; translate JSON to RDF/JSON.
- **AI Assistants**: Define agents in RDF/JSON → Query/translate content → UI display.
- **Backups/Sync**: Encrypt with Cashtab key → IPFS/Google Drive/local/Solid.

## 6. Security

- **Cashtab**: Secure key handling.
- **Encryption**: CryptoJS AES-256; GUN SEA; WebTorrent encryption.
- **Permissions**: Turtle/N3 for private/public/permissive access; WAC for future VPN.
- **Chronik**: Cache results.
- **Google Drive/Solid**: Minimal OAuth scopes (`drive.file`).
- **IPFS**: Encrypted data only.
- **WebExtension/Electron**: Sanitize inputs; minimal permissions.

## 7. Testing

- **Unit**: Modules (access, cashtab, chronik, GUN, WebTorrent, WebRTC, WebSockets, addressbook, chat, bookmarks, agreements, media, work, library, solid, apps, translator, ai, parental, backups).
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

- **Duration**: 30 weeks (1-2 developers).
- **Milestones**:
  - Weeks 1-2: Core infrastructure.
  - Weeks 3-4: Access control.
  - Week 5: Service activation.
  - Weeks 6-7: Parental controls.
  - Weeks 8-9: Web usage tracking.
  - Weeks 10-11: Semantic bookmarks.
  - Weeks 12-13: Chat and annotations.
  - Weeks 14-15: Connection requests.
  - Weeks 16-17: Work tracking.
  - Weeks 18-19: Semantic library.
  - Weeks 20-21: Media sharing/creation.
  - Weeks 22-23: Solid pod and local apps.
  - Weeks 24-25: AI assistants.
  - Weeks 26-27: Backups and sync.
  - Weeks 28-29: Testing.
  - Week 30: Deployment.

## 10. Risks and Mitigations

- **Cashtab**: Error if not installed; provide link (`https://chromewebstore.google.com/detail/cashtab/obldfcmebhllhjlhjbnghaipekcppeag`).
- **Chronik**: Cache results; fallback to manual.
- **GUN/WebTorrent**: Multiple relays; IPFS fallback.
- **Google Drive/Solid**: Clear OAuth/setup guide; fallback to IPFS/local.
- **LM Studio/Ollama/AnythingLLM**: Disable if unavailable.
- **Electron Compatibility**: Test across OSes; debug logs.
- **Local Apps**: Validate third-party app configs; sandbox iframes.
- **Parental Controls**: Ensure robust rule enforcement; provide guardian tutorials.
- **Media Processing**: Handle large files efficiently; fallback to manual metadata.
- **Adoption**: In-app tutorials and docs.

## 11. Future Enhancements

- Social VPN using Tailscale and WAC (pending study).
- Mobile vault integration (Cashtab-compatible).
- Advanced media formats.
- Social analytics dashboard.
- WebPush notifications.

## 12. Recommendations

- **Hybrid P2P**: WebTorrent for media, WebRTC for low-latency chat, GUN for messaging.
- **Ontologies**: Versioned `SemBookmarks#v1`, `cml#v1`, `ai#v1`, `parental#v1` for updates.
- **Sync Scheduler**: Periodic checks for updates.
- **Mobile Vault**: Plan for mobile key store integration.
- **Tutorials**: In-app guides for ADP, Cashtab, media, work, library, access, Solid, AI, parental controls, bookmarks, annotations.

## 13. Next Steps

1. Review specification with stakeholders.
2. Initialize `github.com/mediaprophet/socialweb`.
3. Begin Phase 1 development.
4. Iterate through phases, testing each milestone.
5. Publish alpha to `github.com/WebCivics/SocialWeb`.