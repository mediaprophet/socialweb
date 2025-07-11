# Social Web Project: Software Development Specification (Version 5)

## 1. Project Overview

**Date**: July 11, 2025  
**Purpose**: The Social Web project is a decentralized application delivered as a WebExtension for Chrome and Firefox and an Electron desktop app, enabling social interactions via an address book, chat, semantic bookmarks, RDF-based agreements, media sharing, work tracking, and a semantic library. It integrates with WebCard Viewer’s Agent Discovery Protocol (ADP) for identity discovery, uses Cashtab for eCash-based service activation and access control, Chronik for blockchain queries, GUN.eco for chat, WebTorrent/WebRTC/WebSockets for media and communication, Quadstore for local RDF storage, and supports secure backups to IPFS, Google Drive, local HDD, or optional Solid pods. The system is modular, extensible, uses Turtle/N3 for permissions/network functions and JSON-LD for marketing/Web 2.0 functionality, provides device synchronization, and integrates local AI analysis via LM Studio. Access requires a small eCash payment or token until development costs are recouped, after which it becomes obligation-free.

### 1.1 Goals
- **Access Control**: Require eCash payment (e.g., 100 XEC) or token for app access until development costs (e.g., 1,000,000 XEC) are met; then obligation-free.
- **Service Activation**: Verify eCash address ownership via Cashtab.
- **Identity Discovery**: Fetch user details (eCash address, optional WebID) via ADP.
- **Address Book**: Manage contacts from web history or ADP, with agreement-based relationships.
- **Chat and Annotations**: Support 1:1, group chats, and URL annotations (e.g., `https://www.youtube.com/watch?v=nBJhO-o5q2s`).
- **Semantic Bookmarks**: Store browser history or manual bookmarks per SemBookmarks ontology in JSON-LD.
- **Connection Requests**: Negotiate RDF-based agreements (UDHR, CRC terms).
- **Media Sharing**: Share videos/PDFs via WebTorrent, with free previews and paid full access via eCash.
- **Content Creation**: Tool for uploading media and associating with ADP/eCash.
- **Work Tracking**: Track active tab time, project associations, Git integration, and contribution metadata.
- **Semantic Library**: Share resources by topics/groups with faceted search and permissioned access.
- **Secure Backups**: Encrypt data with Cashtab key; store in IPFS, Google Drive, local HDD, or Solid pods.
- **Device Synchronization**: Sync data across devices using encrypted backups or Solid pods.
- **Local AI**: Analyze chats/agreements via LM Studio’s REST API.
- **Deployment**: Deliver as WebExtension (Chrome, Firefox) and Electron app, with demo site on GitHub Pages.

### 1.2 Key Features
- **Modular Design**: Independent modules for access, ADP, Cashtab, Chronik, address book, chat, bookmarks, agreements, media, work, library, backups, and AI.
- **Extensibility**: Plugin system and dynamic RDF ontology support.
- **Data Formats**: Turtle/N3 for permissions/network; JSON-LD for marketing/Web 2.0.
- **Faceted Search**: SPARQL-based search for library resources.
- **Device Sync**: Cross-device consistency via encrypted backups or Solid pods.
- **Access Model**: eCash payment or token until obligation-free.
- **Dual Implementation**: WebExtension (sidebar) and Electron app (main window).
- **Decentralized**: No server dependencies; uses GUN, WebTorrent, WebRTC, IPFS.

### 1.3 Client Requirements
- **Cashtab Wallet**: Browser extension (WebExtension) or integrated (Electron) for eCash payments/tokens.
- **Browser/OS**: Chrome (v120+), Firefox (v115+) for WebExtension; Windows, macOS, Linux for Electron.
- **Storage**: IndexedDB (50MB) for Quadstore.
- **Internet**: Required for ADP, Chronik, GUN, WebTorrent, IPFS, Google Drive, LM Studio.
- **Optional**: Solid pod for sync; LM Studio for AI.

## 2. Architecture

### 2.1 Components
- **Access Control**: Verifies eCash payment or token via Cashtab/Chronik; tracks obligation status.
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
- **Backups**: Encrypted storage in Quadstore, IPFS, Google Drive, local HDD, or Solid pods.
- **Device Sync**: Sync via IPFS/Google Drive/Solid; future mobile vault.
- **AI (LM Studio)**: Analyzes chats/agreements for compliance.
- **UI**: React-based sidebar (WebExtension) or main window (Electron) with Tailwind CSS.

### 2.2 Modularity
- **Modules**: Separate directories (`src/modules/`) for each feature.
- **Interfaces**: Standard module interface (`init`, `handleEvent`, `getData`).
- **Dependency Injection**: `src/services/` provides shared services.
- **Config**: `src/config.js` toggles modules:
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
    backups: { ipfs: true, googleDrive: true, local: true, solid: false },
    ai: true,
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
│   │   └── Backups.js      # Backup/restore
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
│   │   ├── backups/        # IPFS, Google Drive, local, Solid
│   │   │   └── index.js
│   │   └── ai/             # LM Studio integration
│   │       └── index.js
│   ├── services/
│   │   ├── quadstore.js    # Quadstore setup
│   │   ├── jsonld.js       # JSON-LD parsing
│   │   ├── turtle.js       # Turtle/N3 parsing
│   │   ├── ipfs.js         # IPFS client
│   │   ├── webtorrent.js   # WebTorrent client
│   │   ├── webrtc.js       # WebRTC signaling
│   │   ├── websockets.js   # WebSocket client
│   │   ├── permissions.js  # Turtle/N3 permission system
│   │   └── eventBus.js     # Event bus
│   ├── platforms/
│   │   ├── webextension/   # WebExtension-specific logic
│   │   │   └── index.js
│   │   └── electron/       # Electron-specific logic
│   │       ├── main.js     # Electron main process
│   │       └── index.js
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
- **Identity/Data**: N3.js (`^1.26.0`), Quadstore (`^10.0.0`), quadstore-comunica (`^4.0.0`), @comunica/query-sparql (`^3.0.0`), jsonld.js (`^8.3.2`), ipfs-http-client (`^60.0.0`), @inrupt/solid-client (`^2.0.0`), @inrupt/solid-client-authn-browser (`^2.0.0`).
- **Communication**: GUN.eco (`^0.2020.1236`), WebTorrent (`^2.0.0`), WebRTC (native), WebSockets (`ws@^8.18.0`).
- **eCash**: Cashtab (`@cashtab/wallet-lib`), Chronik (`chronik-client@^0.11.0`).
- **Backups**: CryptoJS (`^4.1.1`), googleapis (`^144.0.0`).
- **AI**: LM Studio REST API (`http://localhost:1234/v1/chat/completions`).
- **RDF Vocabularies**:
  - SemBookmarks (`https://mediaprophet.github.io/init-draft-standards-wip/SemBookmarks#`).
  - ActivityStreams (`https://www.w3.org/ns/activitystreams#`).
  - ADP (`https://webcivics.github.io/adp/ontdev/adp#`).
  - Agreements (`https://mediaprophet.github.io/init-draft-standards-wip/agreements#`).
  - Media (`https://mediaprophet.github.io/init-draft-standards-wip/media#`).
  - Work (`https://mediaprophet.github.io/init-draft-standards-wip/work#`).
  - Library (`https://mediaprophet.github.io/init-draft-standards-wip/library#`).
  - Access (`https://mediaprophet.github.io/init-draft-standards-wip/access#`).

## 4. Development Phases

### Phase 1: Core Infrastructure (Weeks 1-2)
- **Objective**: Set up modular WebExtension and Electron app.
- **Tasks**:
  1. Initialize `github.com/mediaprophet/socialweb`.
  2. Configure `manifest.json` for WebExtension:
     ```json
     {
       "name": "Social Web",
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
     app.whenReady().then(() => {
       const win = new BrowserWindow({ width: 400, height: 600 });
       win.loadFile('public/index.html');
     });
     ```
  4. Reuse WebCard Viewer’s Tailwind/PostCSS configs.
  5. Implement `modules/adp`, `modules/cashtab`, `modules/chronik`.
  6. Set up `services/quadstore.js`, `services/jsonld.js`, `services/turtle.js`, `services/ipfs.js`, `services/webtorrent.js`, `services/webrtc.js`, `services/websockets.js`, `services/permissions.js`.
  7. Create `services/eventBus.js`.
- **Deliverables**:
  - WebExtension and Electron skeleton.
  - Modular ADP, Cashtab, Chronik, Quadstore, JSON-LD, Turtle/N3, WebTorrent, WebRTC, WebSockets.

### Phase 2: Access Control (Weeks 3-4)
- **Objective**: Implement payment/token access model.
- **Tasks**:
  1. Implement `modules/access`:
     - Check payment to fund address (`ecash:qp...socialwebfund`):
       ```javascript
       import { ChronikClient } from 'chronik-client';
       const chronik = new ChronikClient('https://chronik.be.cash');
       async function verifyPayment(userAddress, amount) {
         const txs = await chronik.address(userAddress).history(0, 10);
         return txs.txs.some(tx => tx.outputs.some(out => out.value === amount && out.address === fundAddress));
       }
       ```
     - Verify token ownership:
       ```javascript
       async function verifyToken(userAddress, tokenId) {
         const utxos = await chronik.address(userAddress).utxos();
         return utxos.some(utxo => utxo.token?.tokenId === tokenId);
       }
       ```
     - Track obligation status in Quadstore:
       ```turtle
       <https://sailingdigital.com/access> a access:AccessRecord ;
         access:user <ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a> ;
         access:status access:Paid.
       ```
     - Check fund total; update `config.js` when obligation-free.
  2. Create `components/Access.js` for payment/token UI.
  3. Handle errors (e.g., no Cashtab, insufficient payment).
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

### Phase 4: Address Book (Weeks 6-7)
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

### Phase 5: Chat and URL Annotations (Weeks 8-9)
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

### Phase 6: Semantic Bookmarks (Weeks 10-11)
- **Objective**: Implement SemBookmarks in JSON-LD.
- **Tasks**:
  1. Implement `modules/bookmarks`:
     - Auto-capture history or manual entry.
     - Store as JSON-LD:
       ```json
       {
         "@context": "https://mediaprophet.github.io/init-draft-standards-wip/SemBookmarks#",
         "@type": "Bookmark",
         "url": "https://www.youtube.com/watch?v=nBJhO-o5q2s",
         "creator": "ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a"
       }
       ```
  2. Implement SPARQL queries.
  3. Create `components/Bookmarks.js`.
- **Deliverables**:
  - Bookmarks module.
  - JSON-LD serialization.
  - Bookmarks UI.

### Phase 7: Connection Requests (Weeks 12-13)
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

### Phase 8: Work Tracking (Weeks 14-15)
- **Objective**: Implement work tracking and contribution metadata.
- **Tasks**:
  1. Implement `modules/work`:
     - Track tab time; associate with projects:
       ```turtle
       <https://sailingdigital.com/project/123> a work:Project ;
         work:gitRepo <https://github.com/mediaprophet/project> ;
         work:contributor <ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a> ;
         work:timeSpent "3600s" ;
         work:fairValue "10 XEC".
       ```
     - Non-linear timelines:
       ```turtle
       <https://sailingdigital.com/contribution/123> a work:Contribution ;
         work:discoveredAt "2025-01-01T00:00:00Z".
       ```
     - Publish to IPFS/WebTorrent.
  2. Create `components/Work.js`.
- **Deliverables**:
  - Work tracking module.
  - Work UI.

### Phase 9: Semantic Library (Weeks 16-17)
- **Objective**: Implement library with faceted search.
- **Tasks**:
  1. Implement `modules/library`:
     - Store resources:
       ```turtle
       <https://sailingdigital.com/library/123> a lib:Resource ;
         lib:topic "video" ;
         lib:permission perm:Public.
       ```
     - Faceted search via SPARQL.
     - Serialize as JSON-LD.
  2. Create `components/Library.js`.
- **Deliverables**:
  - Library module.
  - Faceted search UI.

### Phase 10: Media Sharing and Creation (Weeks 18-19)
- **Objective**: Implement WebTorrent-based media sharing and creation.
- **Tasks**:
  1. Implement `modules/media`:
     - Share via WebTorrent; verify payment with Chronik.
  2. Create `components/Media.js` with file input and preview generation.
- **Deliverables**:
  - Media module.
  - Content creation tool.
  - Media UI.

### Phase 11: Secure Backups and Device Sync (Weeks 20-21)
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

### Phase 12: Local AI Integration (Week 22)
- **Objective**: Integrate LM Studio for analysis.
- **Tasks**:
  1. Implement `modules/ai`:
     - Query agreements; send to LM Studio.
  2. Update UI with notifications.
- **Deliverables**:
  - AI module.
  - Analysis UI.

### Phase 13: Testing and Refinement (Weeks 23-24)
- **Objective**: Ensure reliability and security.
- **Tasks**:
  1. Unit tests: All modules.
  2. Integration tests: Workflows.
  3. E2E tests: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
  4. Security audit: Encryption, E2EE, OAuth.
- **Deliverables**:
  - Test suite (>80% coverage).
  - Bug fixes.
  - Security report.

### Phase 14: Deployment (Week 25)
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
- **Address Book**: Web history/ADP → Quadstore → Agreement → Chat/Annotations.
- **Chat/Annotations**: GUN/WebRTC/WebSockets → Quadstore → JSON-LD.
- **Bookmarks**: History API or manual → Quadstore (JSON-LD) → SPARQL.
- **Agreements**: GUN share → Quadstore → Turtle/N3.
- **Work**: Track tab time → Project association → Git/IPFS → Contribution metadata.
- **Library**: Resource storage → Faceted search → Turtle/N3 permissions, JSON-LD sharing.
- **Media**: WebTorrent share → Chronik payment → Quadstore metadata.
- **Backups/Sync**: Encrypt with Cashtab key → IPFS/Google Drive/local/Solid.
- **AI**: Query agreements → LM Studio → UI display.

## 6. Security

- **Cashtab**: Secure key handling.
- **Encryption**: CryptoJS AES-256; GUN SEA; WebTorrent encryption.
- **Permissions**: Turtle/N3 for private/public/permissive access.
- **Chronik**: Cache results.
- **Google Drive**: Minimal OAuth scopes (`drive.file`).
- **IPFS**: Encrypted data only.
- **WebExtension/Electron**: Sanitize inputs; minimal permissions.

## 7. Testing

- **Unit**: Modules (access, Cashtab, Chronik, GUN, WebTorrent, WebRTC, WebSockets, address book, work, library, Quadstore, JSON-LD, Turtle/N3, backups, AI).
- **Integration**: Workflows.
- **E2E**: WebExtension (Chrome, Firefox), Electron (Windows, macOS, Linux).
- **Security**: Encryption, E2EE, OAuth.

## 8. Deployment

- **Repository**: `github.com/mediaprophet/socialweb` (development); `github.com/WebCivics/SocialWeb` (alpha).
- **WebExtension**: Chrome Web Store, Firefox Add-ons, GitHub Releases.
- **Electron App**: GitHub Releases (Windows, macOS, Linux).
- **Demo Site**: GitHub Pages (`https://mediaprophet.github.io/socialweb`).
- **Documentation**: README, user/developer guides.

## 9. Timeline

- **Duration**: 25 weeks (1-2 developers).
- **Milestones**:
  - Weeks 1-2: Core infrastructure.
  - Weeks 3-4: Access control.
  - Week 5: Service activation.
  - Weeks 6-7: Address book.
  - Weeks 8-9: Chat and annotations.
  - Weeks 10-11: Semantic bookmarks.
  - Weeks 12-13: Connection requests.
  - Weeks 14-15: Work tracking.
  - Weeks 16-17: Semantic library.
  - Weeks 18-19: Media sharing/creation.
  - Weeks 20-21: Backups and sync.
  - Week 22: AI integration.
  - Weeks 23-24: Testing.
  - Week 25: Deployment.

## 10. Risks and Mitigations

- **Cashtab**: Error if not installed; provide link (`https://chromewebstore.google.com/detail/cashtab/obldfcmebhllhjlhjbnghaipekcppeag`).
- **Chronik**: Cache results; fallback to manual.
- **GUN/WebTorrent**: Multiple relays; IPFS fallback.
- **Google Drive**: Clear OAuth guide; fallback to IPFS/local.
- **LM Studio**: Disable if unavailable.
- **Electron Compatibility**: Test across OSes; debug logs.
- **Adoption**: In-app tutorials and docs.

## 11. Future Enhancements

- Mobile vault integration (Cashtab-compatible).
- Advanced media formats.
- Social analytics dashboard.
- WebPush notifications.

## 12. Recommendations

- **Hybrid P2P**: WebTorrent for media, WebRTC for low-latency chat, GUN for messaging.
- **Ontologies**: Define `access#`, `work#`, `library#` for consistency.
- **Sync Scheduler**: Periodic checks for updates.
- **Mobile Vault**: Plan for mobile key store integration.
- **Tutorials**: In-app guides for ADP, Cashtab, media, work, library, and access.

## 13. Next Steps

1. Review specification with stakeholders.
2. Initialize `github.com/mediaprophet/socialweb`.
3. Begin Phase 1 development.
4. Iterate through phases, testing each milestone.
5. Publish alpha to `github.com/WebCivics/SocialWeb`.