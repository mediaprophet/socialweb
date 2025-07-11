# Social Web Project: Software Development Specification (Version 2)

## 1. Project Overview

**Date**: July 11, 2025  
**Purpose**: The Social Web project is a decentralized WebExtension for Chrome and Firefox, enabling users to engage in social interactions via chat, semantic bookmarks, RDF-based agreements, and media sharing. It integrates with WebCard Viewer’s Agent Discovery Protocol (ADP) for identity discovery, uses Cashtab for eCash-based service activation and transactions, Chronik for blockchain queries, GUN.eco for chat, WebTorrent/WebRTC/WebSockets for media and communication, Quadstore for local RDF storage, and supports secure backups to IPFS, Google Drive, or local HDD. The system is modular, extensible, and supports JSON-LD for interoperability, with device synchronization and local AI analysis via LM Studio.

### 1.1 Goals
- **Service Activation**: Verify eCash address ownership via Cashtab to activate services.
- **Identity Discovery**: Fetch user details (eCash address, optional WebID) via ADP.
- **Chat and Annotations**: Support 1:1, group chats, and URL annotations (e.g., for `https://www.youtube.com/watch?v=nBJhO-o5q2s`).
- **Semantic Bookmarks**: Store browser history or manual bookmarks per SemBookmarks ontology in JSON-LD.
- **Connection Requests**: Negotiate RDF-based agreements (UDHR, CRC terms).
- **Media Sharing**: Share videos/PDFs via WebTorrent, with previews and paid full access via eCash.
- **Content Creation**: Browser-based tool for uploading media and associating with ADP/eCash.
- **Secure Backups**: Encrypt data with Cashtab key; store in IPFS, Google Drive, or local HDD.
- **Device Synchronization**: Sync data across devices using encrypted backups or optional Solid pods.
- **Local AI**: Analyze chats/agreements via LM Studio’s REST API.
- **Deployment**: Deliver as a WebExtension with a sidebar UI, reusing WebCard Viewer’s Tailwind styles.

### 1.2 Key Features
- **Modular Design**: Independent modules for ADP, Cashtab, Chronik, chat, bookmarks, agreements, media, backups, and AI.
- **Extensibility**: Plugin system and dynamic RDF ontology support.
- **JSON-LD**: Serialize data for interoperability.
- **URL Annotations**: Chats tied to clean URLs.
- **Media Transactions**: Free previews and paid access via eCash.
- **Device Sync**: Cross-device data consistency via encrypted backups.
- **Decentralized**: No server dependencies; uses GUN, WebTorrent, WebRTC, IPFS.

## 2. Architecture

### 2.1 Components
- **Identity (ADP)**: Fetches eCash address (`adp:hasEcashAccount`) from DNS TXT + IPFS.
- **Service Activation (Cashtab)**: Signs message to verify eCash address; Chronik validates.
- **Chat (GUN.eco, WebRTC, WebSockets)**: Real-time 1:1, group chats, and URL annotations.
- **Bookmarks (SemBookmarks)**: JSON-LD bookmarks from browser history or manual entry.
- **Agreements**: RDF-based connection requests shared via GUN.
- **Media (WebTorrent)**: P2P sharing of videos/PDFs; eCash transactions for full access.
- **Content Creation**: Browser tool for media upload and metadata association.
- **Backups**: Encrypted storage in Quadstore, IPFS, Google Drive, or local HDD.
- **Device Sync**: Sync via IPFS/Google Drive or optional Solid pods.
- **AI (LM Studio)**: Analyzes chats/agreements for compliance.
- **UI**: React-based sidebar with Tailwind CSS.

### 2.2 Modularity
- **Modules**: Separate directories (`src/modules/`) for each feature.
- **Interfaces**: Standard module interface (`init`, `handleEvent`, `getData`).
- **Dependency Injection**: `src/services/` provides shared services (Quadstore, IPFS, JSON-LD).
- **Config**: `src/config.js` toggles modules:
  ```javascript
  export const modules = {
    adp: true,
    cashtab: true,
    chronik: true,
    chat: { gun: true, webrtc: true, websockets: true },
    bookmarks: true,
    agreements: true,
    media: true,
    backups: { ipfs: true, googleDrive: true, local: true },
    ai: true,
  };
  ```
- **Event Bus**: `src/services/eventBus.js` for module communication:
  ```javascript
  export const eventBus = {
    on(event, callback) { /* ... */ },
    emit(event, data) { /* ... */ },
  };
  ```

### 2.3 File Structure
```
SocialWeb/
├── public/
│   ├── index.html          # Demo site entry
│   ├── sidebar.html        # WebExtension sidebar
│   ├── icon.png            # Extension icon
│   └── manifest.json       # WebExtension manifest
├── src/
│   ├── components/         # React components
│   │   ├── Sidebar.js      # Main sidebar UI
│   │   ├── Chat.js         # Chat and annotations
│   │   ├── Bookmarks.js    # Semantic bookmarks
│   │   ├── Agreements.js   # Connection requests
│   │   ├── Media.js        # Media sharing/creation
│   │   └── Backups.js      # Backup/restore
│   ├── modules/
│   │   ├── adp/            # ADP fetching
│   │   │   └── index.js
│   │   ├── cashtab/        # Cashtab integration
│   │   │   └── index.js
│   │   ├── chronik/        # Chronik queries
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
│   │   ├── backups/        # IPFS, Google Drive, local
│   │   │   └── index.js
│   │   └── ai/             # LM Studio integration
│   │       └── index.js
│   ├── services/
│   │   ├── quadstore.js    # Quadstore setup
│   │   ├── jsonld.js       # JSON-LD parsing
│   │   ├── ipfs.js         # IPFS client
│   │   ├── webtorrent.js   # WebTorrent client
│   │   ├── webrtc.js       # WebRTC signaling
│   │   ├── websockets.js   # WebSocket client
│   │   └── eventBus.js     # Event bus
│   ├── config.js           # Module configuration
│   ├── App.js              # Main React app
│   └── background.js       # Background script
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
- **Identity/Data**: N3.js (`^1.26.0`), Quadstore (`^10.0.0`), quadstore-comunica (`^4.0.0`), @comunica/query-sparql (`^3.0.0`), jsonld.js (`^8.3.2`), ipfs-http-client (`^60.0.0`).
- **Communication**: GUN.eco (`^0.2020.1236`), WebTorrent (`^2.0.0`), WebRTC (native browser), WebSockets (`ws@^8.18.0`).
- **eCash**: Cashtab (`@cashtab/wallet-lib`), Chronik (`chronik-client@^0.11.0`).
- **Backups**: CryptoJS (`^4.1.1`), googleapis (`^144.0.0`).
- **AI**: LM Studio REST API (`http://localhost:1234/v1/chat/completions`).
- **RDF Vocabularies**:
  - SemBookmarks (`https://mediaprophet.github.io/init-draft-standards-wip/SemBookmarks#`).
  - ActivityStreams (`https://www.w3.org/ns/activitystreams#`).
  - ADP (`https://webcivics.github.io/adp/ontdev/adp#`).
  - Agreements (`https://mediaprophet.github.io/init-draft-standards-wip/agreements#`).
  - Media (`https://mediaprophet.github.io/init-draft-standards-wip/media#`, proposed).

## 4. Development Phases

### Phase 1: Core Infrastructure (Weeks 1-2)
- **Objective**: Set up modular WebExtension with ADP, Cashtab, Chronik, and JSON-LD.
- **Tasks**:
  1. Initialize `WebCivics/SocialWeb` with file structure.
  2. Configure `manifest.json`:
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
  3. Reuse WebCard Viewer’s Tailwind/PostCSS configs.
  4. Implement `modules/adp` for eCash address fetching.
  5. Implement `modules/cashtab` for signing/address retrieval.
  6. Implement `modules/chronik` for blockchain queries.
  7. Set up `services/quadstore.js`, `services/jsonld.js`, `services/ipfs.js`, `services/webtorrent.js`, `services/webrtc.js`, `services/websockets.js`.
  8. Create `services/eventBus.js`.
- **Deliverables**:
  - WebExtension skeleton.
  - Modular ADP, Cashtab, Chronik, Quadstore, JSON-LD, WebTorrent, WebRTC, WebSockets.

### Phase 2: Service Activation (Week 3)
- **Objective**: Enable Cashtab-based activation.
- **Tasks**:
  1. Implement `modules/cashtab/activateService.js`:
     - Sign message with Cashtab.
     - Verify with Chronik.
     - Store in Quadstore (`adp:serviceActivated`).
  2. Update `components/Sidebar.js` with activation UI.
  3. Handle errors.
- **Deliverables**:
  - Activation module.
  - Sidebar UI.

### Phase 3: Chat and URL Annotations (Weeks 4-5)
- **Objective**: Implement chat and annotations with GUN, WebRTC, WebSockets.
- **Tasks**:
  1. Implement `modules/chat`:
     - GUN: `social-web-chat/conversation-<domain>`, `annotation-<clean-url>`.
     - WebRTC: P2P 1:1 chats with WebSocket signaling.
     - WebSockets: Fallback for GUN.
     - Store in Quadstore (`as:Note`).
  2. Use `tabs` API for clean URL:
     ```javascript
     browser.tabs.query({ active: true }, (tabs) => {
       return new URL(tabs[0].url).origin + new URL(tabs[0].url).pathname;
     });
     ```
  3. Verify group access with `modules/chronik`.
  4. Create `components/Chat.js`.
- **Deliverables**:
  - Chat/annotation module.
  - Quadstore storage.
  - Token-based access.

### Phase 4: Semantic Bookmarks (Weeks 6-7)
- **Objective**: Implement SemBookmarks in JSON-LD.
- **Tasks**:
  1. Implement `modules/bookmarks`:
     - Auto-capture history with `history` API.
     - Manual entry in sidebar.
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

### Phase 5: Connection Requests (Weeks 8-9)
- **Objective**: Implement agreements without Solid.
- **Tasks**:
  1. Implement `modules/agreements`:
     - Store in Quadstore:
       ```turtle
       <https://sailingdigital.com/request/123> a agr:ConnectionRequest ;
         agr:sender <ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a> ;
         agr:terms "UDHR Article 12".
       ```
     - Share via GUN.
  2. Create `components/Agreements.js`.
  3. Set domain-based ACLs.
- **Deliverables**:
  - Agreements module.
  - Agreements UI.

### Phase 6: Media Sharing and Creation (Weeks 10-11)
- **Objective**: Implement WebTorrent-based media sharing and content creation.
- **Tasks**:
  1. Implement `modules/media`:
     - Share media via WebTorrent:
       ```javascript
       import WebTorrent from 'webtorrent';
       const client = new WebTorrent();
       client.add(magnetURI, (torrent) => {
         torrent.files.forEach(file => file.renderTo('#media-player'));
       });
       ```
     - Associate with ADP/eCash:
       ```turtle
       @prefix media: <https://mediaprophet.github.io/init-draft-standards-wip/media#>.
       <https://sailingdigital.com/media/123> a media:Media ;
         media:domain <https://sailingdigital.com> ;
         media:ecashAddress <ecash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a> ;
         media:previewMagnetURI "magnet:?xt=urn:btih:..." ;
         media:fullMagnetURI "magnet:?xt=urn:btih:...".
       ```
     - Verify payment with Chronik for full access.
  2. Create `components/Media.js` for upload/preview:
     - File input for videos/PDFs.
     - Generate preview (e.g., first 30s of video).
     - Create WebTorrent torrent.
     - Store metadata in Quadstore.
  3. Update sidebar with media player and payment UI.
- **Deliverables**:
  - Media module.
  - Content creation tool.
  - Media UI.

### Phase 7: Secure Backups and Device Sync (Weeks 12-13)
- **Objective**: Implement encrypted backups and synchronization.
- **Tasks**:
  1. Implement `modules/backups`:
     - Encrypt with Cashtab key (CryptoJS).
     - IPFS: Store encrypted data, save CID.
     - Google Drive: OAuth 2.0 (`googleapis`).
     - Local HDD: Export/import JSON.
     - Sync scheduler: Check IPFS/Google Drive for updates.
  2. Optional Solid pod sync:
     ```javascript
     await saveSolidDatasetAt(`${webId}/private/social-web/`, dataset, { fetch: session.fetch });
     ```
  3. Create `components/Backups.js`.
- **Deliverables**:
  - Backup/sync module.
  - Backup UI.

### Phase 8: Local AI Integration (Week 14)
- **Objective**: Integrate LM Studio for analysis.
- **Tasks**:
  1. Implement `modules/ai`:
     - Query agreements from Quadstore.
     - Send to LM Studio:
       ```javascript
       fetch('http://localhost:1234/v1/chat/completions', {
         method: 'POST',
         body: JSON.stringify({
           model: 'llama-3.1-8b',
           messages: [{ role: 'user', content: `Analyze: ${message}` }],
         }),
       });
       ```
  2. Update `components/Sidebar.js` with notifications.
- **Deliverables**:
  - AI module.
  - Analysis UI.

### Phase 9: Testing and Refinement (Weeks 15-16)
- **Objective**: Ensure reliability and security.
- **Tasks**:
  1. Unit tests: All modules.
  2. Integration tests: Workflows.
  3. E2E tests: Browser interactions.
  4. Security audit: Encryption, E2EE, OAuth.
- **Deliverables**:
  - Test suite (>80% coverage).
  - Bug fixes.
  - Security report.

### Phase 10: Deployment (Week 17)
- **Objective**: Deploy WebExtension and demo site.
- **Tasks**:
  1. Build: `npm run build`.
  2. Package: `.crx`, `.xpi`.
  3. Publish to Chrome Web Store, Firefox Add-ons Store.
  4. Deploy demo to GitHub Pages.
  5. Document usage and development.
- **Deliverables**:
  - Deployed WebExtension.
  - Demo site.
  - Documentation.

## 5. Workflows

- **Activation**: ADP → Cashtab sign → Chronik verify → Quadstore store.
- **Chat/Annotations**: GUN/WebRTC/WebSockets → Quadstore → JSON-LD.
- **Bookmarks**: History API or manual → Quadstore (JSON-LD) → SPARQL.
- **Agreements**: GUN share → Quadstore → JSON-LD.
- **Media**: WebTorrent share → Chronik payment → Quadstore metadata.
- **Backups/Sync**: Encrypt with Cashtab key → IPFS/Google Drive/local/Solid.
- **AI**: Query agreements → LM Studio → Sidebar display.

## 6. Security

- **Cashtab**: Secure key handling.
- **Encryption**: CryptoJS AES-256; GUN SEA; WebTorrent encryption.
- **Chronik**: Cache results.
- **Google Drive**: Minimal OAuth scopes.
- **IPFS**: Encrypted data only.
- **WebExtension**: Sanitize inputs; minimal permissions.

## 7. Testing

- **Unit**: Modules (Cashtab, Chronik, GUN, WebTorrent, WebRTC, WebSockets, Quadstore, JSON-LD, backups, AI).
- **Integration**: Workflows.
- **E2E**: Browser interactions (Chrome, Firefox).
- **Security**: Encryption, E2EE, OAuth.

## 8. Deployment

- **WebExtension**: Chrome Web Store, Firefox Add-ons, GitHub Releases.
- **Demo Site**: GitHub Pages (`https://webcivics.github.io/SocialWeb`).
- **Documentation**: README, user/developer guides.

## 9. Timeline

- **Duration**: 17 weeks (1-2 developers).
- **Milestones**:
  - Weeks 1-2: Core infrastructure.
  - Week 3: Service activation.
  - Weeks 4-5: Chat and annotations.
  - Weeks 6-7: Semantic bookmarks.
  - Weeks 8-9: Connection requests.
  - Weeks 10-11: Media sharing/creation.
  - Weeks 12-13: Backups and sync.
  - Week 14: AI integration.
  - Weeks 15-16: Testing.
  - Week 17: Deployment.

## 10. Risks and Mitigations

- **Cashtab**: Error if not installed; provide link.
- **Chronik**: Cache results; fallback to manual.
- **GUN/WebTorrent**: Multiple relays; IPFS fallback.
- **Google Drive**: Clear OAuth guide; fallback to IPFS/local.
- **LM Studio**: Disable if unavailable.
- **Adoption**: In-app tutorials and docs.

## 11. Future Enhancements

- Mobile vault integration.
- Advanced media formats.
- Social analytics dashboard.
- WebPush notifications.

## 12. Recommendations

- **Hybrid P2P**: Use WebTorrent for media, WebRTC for low-latency chat, GUN for messaging.
- **Media Ontology**: Define `media#` for metadata consistency.
- **Sync Scheduler**: Periodic checks for cross-device updates.
- **Mobile Vault**: Plan for Cashtab-compatible mobile key store.
- **Tutorials**: In-app guides for ADP, Cashtab, and media creation.

## 13. Next Steps

1. Review specification with stakeholders.
2. Initialize `WebCivics/SocialWeb` repository.
3. Begin Phase 1 development.
4. Iterate through phases, testing each milestone.
5. Gather community feedback on alpha release.