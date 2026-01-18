# REQUIREMENTS.md  

**family-sync-tasklist — Requirements Specification (v1)**

## 1. Core Features
- Shared family task list  
- Shared family shopping list  
- Inline comments on every item  
- Offline‑first interaction model  
- Automatic background sync with eventual consistency  
- Deterministic conflict resolution (LWW + CRDT‑friendly structures)  
- Cross‑platform support (Web, Windows, macOS, Linux, Android, iOS)  
- Local durable storage with crash‑safe writes  
- Visual indicators for offline mode, sync status, and conflicts  
- Accessible, family‑friendly UI  
- Secure storage and encrypted communication  
- Data export, backup, and migration support (future‑proofing)

---

## 2. User Stories

### 2.1 Tasks & Shopping Items
- **As a family member**, I want to add tasks or shopping items so everyone knows what needs doing.  
- **As a family member**, I want to edit or delete items so the list stays accurate.  
- **As a family member**, I want to mark items as completed so others know they’re done.  
- **As a family member**, I want to add inline comments to clarify details or ask questions.  
- **As a family member**, I want comments to remain attached to their parent item.

### 2.2 Offline Use
- **As a user**, I want the app to work fully offline so I can use it in shops or areas with poor reception.  
- **As a user**, I want my changes saved immediately so I never lose work.  
- **As a user**, I want the app to sync automatically when I reconnect.

### 2.3 Multi‑Device & Family Collaboration
- **As a family member**, I want updates from others to appear on my device once I’m online.  
- **As a user with multiple devices**, I want my data to stay consistent across all my devices.  
- **As a user**, I want conflicts to be resolved predictably without losing my changes.

### 2.4 Transparency & Feedback
- **As a user**, I want to see when the app is offline.  
- **As a user**, I want to know when my changes are pending sync.  
- **As a user**, I want clear explanations if a sync fails or a conflict occurs.

### 2.5 Accessibility & Inclusivity
- **As a child, adult, or senior**, I want the interface to be simple and readable.  
- **As a user with accessibility needs**, I want support for screen readers, high contrast, and adjustable text.

---

## 3. Functional Requirements

### 3.1 Data Model
- The system must support:
  - Tasks  
  - Shopping items  
  - Inline comments  
  - User profiles (lightweight)  
- Each entity must include:
  - Unique ID  
  - Creation timestamp  
  - Last‑modified timestamp  
  - Device/user metadata  
  - Sync state metadata (e.g., pending, synced, conflict)

### 3.2 Local Storage
- All changes must be written to durable local storage immediately.  
- Storage must support:
  - Crash‑safe writes  
  - Transactional updates  
  - Schema migrations  
  - Integrity checks on startup  

### 3.3 Offline Interaction
- Users must be able to:
  - View all lists  
  - Add/edit/delete items  
  - Add/edit/delete comments  
  - Mark items as complete  
- No operation may require network connectivity.

### 3.4 Sync Engine
- Must track unsynced changes.  
- Must retry failed syncs automatically.  
- Must support push (upload) and pull (download).  
- Must merge remote updates into local state.  
- Must resolve conflicts deterministically using:
  - LWW for simple fields  
  - CRDT‑friendly structures for lists/sets/comments  
- Must expose sync status to the UI.

### 3.5 Conflict Handling
- Automatic resolution must be deterministic.  
- When automatic resolution is ambiguous, the UI must:
  - Surface the conflict  
  - Explain the issue in plain language  
  - Offer user‑driven resolution options  

### 3.6 Security
- Local storage should be encrypted where possible.  
- All network communication must use HTTPS/TLS.  
- Access control must ensure only family members can view/modify shared lists.  
- Users must be able to export or delete their data.

### 3.7 Cross‑Platform Behaviour
- Core features must behave identically across all platforms.  
- Platform‑specific adaptations must be abstracted behind a unified API.  
- The UI must adapt to touch, mouse, keyboard, and screen sizes.

---

## 4. Non‑Functional Requirements

### 4.1 Performance
- UI updates must occur within 16ms for smooth interaction.  
- Sync operations must run in the background without blocking the UI.  
- Large lists must load efficiently (pagination or lazy loading).  
- Heavy operations (e.g., merges, encryption) must run in isolates/background threads.

### 4.2 Reliability
- No user data may be lost under any circumstances.  
- The app must recover gracefully from:
  - Crashes  
  - Power failures  
  - Partial writes  
  - Interrupted syncs  

### 4.3 Scalability
- The architecture must support future features without major rewrites.  
- Sync logic must scale to multiple users and devices.

### 4.4 Usability & Accessibility
- UI must be intuitive for all ages.  
- Must support:
  - Screen readers  
  - High contrast  
  - Adjustable font sizes  
  - Clear icons and large touch targets  

### 4.5 Observability
- The app must include:
  - Logging  
  - Error reporting  
  - Optional diagnostics for advanced users  
- Failures must be visible and actionable.

---

## 5. Offline Behaviour Requirements
- Local device is always the primary source of truth.  
- All operations must succeed offline.  
- All changes must be persisted immediately.  
- Pending changes must be queued for sync.  
- UI must show:
  - Offline indicator  
  - Pending‑sync indicator  
- No blocking operations may depend on the network.

---

## 6. Sync Behaviour Requirements
- Sync must be automatic and transparent.  
- Sync must run opportunistically when connectivity is available.  
- Sync must:
  - Upload local changes  
  - Download remote changes  
  - Merge changes deterministically  
  - Retry failures  
- Sync must not block user actions.  
- Sync must expose:
  - Current status  
  - Errors  
  - Conflict notifications  

---

## 7. Multi‑Device Behaviour Requirements
- All devices must eventually converge to the same state.  
- Devices may temporarily diverge while offline.  
- Each device must maintain its own local durable store.  
- Sync must reconcile:
  - Edits from multiple devices  
  - Deletes vs edits  
  - Concurrent comments  
- User actions on one device must appear on others after sync.

---

## 8. Edge Cases

### 8.1 Offline Edge Cases
- Device loses connection mid‑edit.  
- Device loses power during a write.  
- Local database corruption detected on startup.  
- User edits an item that was deleted remotely.

### 8.2 Sync Edge Cases
- Conflicting edits to the same field.  
- Conflicting edits to different fields of the same item.  
- Comment added offline to an item that was deleted remotely.  
- Sync interrupted mid‑operation.  
- Duplicate items created on different devices.

### 8.3 Multi‑Device Edge Cases
- Device A edits an item while Device B deletes it.  
- Device A adds comments while Device B renames the item.  
- Device A and B both create items with identical content but different IDs.  
- Device clock skew causing timestamp anomalies (handled via logical clocks).

### 8.4 Data Integrity Edge Cases
- Schema migration failure.  
- Partial migration due to crash.  
- Storage full / write failure.  
- Backup/restore mismatch.

---
# End of REQUIREMENTS.md