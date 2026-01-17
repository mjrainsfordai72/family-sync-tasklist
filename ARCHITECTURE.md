# ARCHITECTURE.md

---

## Introduction

This document describes the architecture of the **family-sync-tasklist** Flutter/Dart application. The app is designed to provide families with a simple, shared space for managing tasks and shopping lists, with every item supporting inline comments for seamless coordination. The architecture is grounded in a set of core principles—**offline-first, data safety, simplicity, predictability, separation of concerns, and cross-platform support**—as outlined in the project’s PRINCIPLES.md and PROJECT_OVERVIEW.md files. This document details the high-level system design, main components, offline-first and sync mechanisms, data flow for tasks and comments, and the strategies that ensure robust, predictable behavior across all supported platforms.

---

## High-Level System Overview

The **family-sync-tasklist** application is architected as a modular, layered system that separates the user interface, local data storage, synchronization logic, and backend integration. This separation of concerns is essential for maintainability, testability, and long-term scalability, as emphasized in the project’s core principles.

At a high level, the system comprises the following layers:

- **UI Layer**: Presents the task list and inline comments, handles user input, and provides immediate feedback.
- **Local Storage Layer**: Persists all user data (tasks, comments, sync metadata) on the device, enabling full offline functionality.
- **Sync Engine**: Manages the queueing, conflict resolution, and background synchronization of local changes with the backend.
- **Backend API**: Provides a central, cloud-based data store for family lists, enabling cross-device and cross-user synchronization.

The architecture is designed to ensure that **all user actions are honored immediately and locally**, regardless of connectivity, and that **eventual consistency** is achieved across all devices through robust, background synchronization.

---

## Main Components

### 1. User Interface (UI) Layer

#### Responsibilities

- Display the unified task and shopping list, including inline comments for each item.
- Allow users to create, edit, and delete tasks and comments, even when offline.
- Provide clear, intuitive feedback about data state (e.g., unsynced changes, sync status).
- Support accessibility and usability for all ages, per the principle: “Simple Enough for a 5-Year-Old.”

#### Cross-Platform Considerations

- Built with Flutter’s widget system, enabling a **single codebase** for web, Windows, macOS, Linux, Android, and iOS.
- Adapts UI layouts and controls for each platform (e.g., Material for Android, Cupertino for iOS, responsive layouts for desktop and web).
- Uses platform-aware widgets and conditional imports to handle platform-specific nuances (e.g., keyboard navigation on desktop, touch gestures on mobile).

#### State Management

- Employs the Model-View-ViewModel (MVVM) pattern, with **ViewModels** mediating between the UI and data layers.
- UI widgets are stateless or minimally stateful; all business logic and data transformations reside in ViewModels.
- UI reacts to changes in ViewModel state, which is updated via streams or change notifiers.

---

### 2. Local Storage Layer

#### Responsibilities

- Acts as the **primary source of truth** for all user data, including tasks, comments, and sync metadata.
- Enables full offline functionality: users can create, edit, and delete data without connectivity.
- Stores a persistent sync queue of pending changes for later synchronization.

#### Storage Engine Selection

- **Drift (formerly Moor)** is recommended as the default local database engine:
  - Provides type-safe, reactive SQL on top of SQLite.
  - Supports robust schema migrations, cross-platform compatibility (including web), and efficient queries.
  - Enables reactive streams for live UI updates when data changes.
- **Hive** may be used for simpler key-value storage needs or for lightweight data (e.g., app settings), but Drift is preferred for structured, relational data like tasks and comments.

#### Data Model

- **Tasks**: Each task has a unique ID, title, status, timestamps, and a list of associated inline comments.
- **Inline Comments**: Each comment is linked to a task, with its own ID, author, content, timestamps, and sync status.
- **Sync Metadata**: Each record includes fields for sync status (e.g., pending, syncing, synced, failed), last updated timestamp, and conflict markers.

#### Schema Evolution

- Database schema is versioned and supports migrations using Drift’s migration tools, ensuring safe upgrades and data integrity as the app evolves.

#### Security

- Sensitive data is encrypted at rest using AES-256, with keys managed via platform-secure storage (e.g., Android Keystore, iOS Keychain).
- The storage layer is designed to prevent data loss, accidental overwrites, or orphaned records, per the principle: “Never Lose Data” and “Every Byte Has a Home.”

---

### 3. Sync Engine

#### Responsibilities

- Detects local changes (creates, updates, deletes) and queues them for synchronization.
- Handles background synchronization with the backend when connectivity is available.
- Resolves conflicts gracefully, ensuring no data is lost and users are guided through any necessary resolution.
- Maintains eventual consistency across all devices and users.

#### Architecture

- **Sync Queue**: All local changes are appended to a persistent sync queue (outbox pattern), ensuring durability even if the app crashes or restarts.
- **Background Processing**: Uses Dart isolates and plugins like `workmanager` to perform sync operations in the background, even when the app is not in the foreground.
- **Connectivity Monitoring**: Listens for network state changes (via `connectivity_plus`) and triggers sync automatically when the device comes online.
- **Delta Sync**: Only changed records (since the last sync) are sent to the backend, minimizing bandwidth and improving efficiency.
- **Retry and Backoff**: Implements exponential backoff and retry logic for failed sync attempts, with a configurable maximum number of retries.
- **Conflict Detection and Resolution**: Uses timestamps, version vectors, or CRDTs (where appropriate) to detect and resolve conflicts, favoring user intent and data preservation.

#### Sync Triggers

- On app launch.
- When connectivity is restored.
- Periodically (configurable interval).
- On explicit user action (e.g., manual “Sync Now” button).

#### Observability

- Sync status and errors are surfaced to the UI for transparency, but never block user actions.
- All sync operations are logged for debugging and telemetry.

---

### 4. Backend API

#### Responsibilities

- Provides a secure, centralized data store for all family lists, tasks, and comments.
- Exposes RESTful endpoints for CRUD operations, delta sync, and conflict resolution.
- Manages user authentication, authorization, and family membership.
- Supports push notifications or webhooks for real-time sync triggers (future enhancement).

#### API Design

- Follows RESTful conventions with resource-based endpoints (e.g., `/tasks`, `/tasks/{id}/comments`).
- Supports delta queries (e.g., `GET /tasks/delta?since=timestamp`) for efficient sync.
- Accepts batched changes and provides clear conflict feedback.
- Uses stateless endpoints and lightweight JSON payloads for cross-platform compatibility.

#### Security

- All API calls are authenticated and encrypted (HTTPS/TLS).
- Sensitive data is never exposed in logs or error messages.

#### Scalability

- Designed to support multiple families, users, and devices with efficient indexing and rate limiting to prevent abuse.

---

## Offline-First Behavior

### Core Principle

> **Offline Comes First**  
> The app must work fully without an internet connection. Users should be able to create, view, and edit their data anytime, anywhere.

#### Implementation

- **Local-First Writes**: All user actions (create, edit, delete) are immediately applied to the local database and reflected in the UI, regardless of connectivity.
- **Optimistic UI**: The UI updates instantly, providing a fast, responsive experience. Sync status indicators (e.g., “pending sync”) are shown unobtrusively.
- **Sync Queue**: Changes are queued for background sync. No user action is lost, even if the app crashes or the device is restarted.
- **Read-Through Cache**: All data is read from the local database. The app never blocks on network requests to display content.
- **Graceful Degradation**: If the device is offline, features that require the backend (e.g., inviting new family members) are disabled or deferred, but core task and comment functionality remains fully available.

#### User Experience

- Users are never blocked by network errors or delays.
- All actions are honored and preserved, with clear feedback about sync status.
- The app feels fast and reliable, building user trust.

---

## Eventual Sync and Consistency

### Core Principle

> **Sync Happens Quietly and Reliably**  
> Changes made on one device should eventually appear on all others. Sync should be automatic, conflict-aware, and never interrupt the user.

#### Eventual Consistency Model

- **Asynchronous Propagation**: Updates are propagated to the backend and other devices asynchronously, allowing temporary inconsistencies but guaranteeing eventual convergence.
- **Conflict Detection**: When the same task or comment is modified on multiple devices before syncing, the sync engine detects conflicts using timestamps, version vectors, or CRDTs.
- **Conflict Resolution**:
  - **Last-Write-Wins (LWW)**: For simple fields, the most recent update (by timestamp) prevails, but this is only used where data loss is acceptable.
  - **Three-Way Merge**: For complex data (e.g., task with multiple comments), a three-way merge is performed, combining non-overlapping changes and prompting the user if manual intervention is needed.
  - **Application-Driven Merge**: For comments and collaborative edits, merge strategies are tailored to preserve all user input (e.g., union of comment sets).
  - **User-Guided Resolution**: If a conflict cannot be resolved automatically, the app guides the user through a clear, friendly resolution process, never discarding data silently.
- **Sync Status Tracking**: Each record includes a sync status flag (pending, syncing, synced, failed) to manage the sync lifecycle and retry logic.
- **Audit Trail**: All changes are logged with timestamps and device/user identifiers for traceability.

#### Reliability

- **No Data Loss**: The sync engine is designed to never lose user data, even in the face of crashes, network failures, or conflicts.
- **Retry and Backoff**: Failed sync attempts are retried with exponential backoff, and errors are surfaced to the user only when necessary.
- **Background Sync**: Sync runs in the background, triggered by connectivity changes, app lifecycle events, or scheduled intervals.

---

## Task and Inline Comment Data Flow

### Data Model

- **Task**
  - `id`: Unique identifier (UUID)
  - `title`: String
  - `status`: Enum (e.g., pending, done)
  - `createdAt`, `updatedAt`: Timestamps
  - `syncStatus`: Enum (pending, syncing, synced, failed)
  - `comments`: List of InlineComment IDs

- **InlineComment**
  - `id`: Unique identifier (UUID)
  - `taskId`: Foreign key to Task
  - `author`: User identifier
  - `content`: String
  - `createdAt`, `updatedAt`: Timestamps
  - `syncStatus`: Enum (pending, syncing, synced, failed)

### Data Flow Diagram

1. **User Action (Create/Edit/Delete Task or Comment)**
   - UI captures the action and passes it to the ViewModel.
   - ViewModel updates the local database immediately.
   - The change is appended to the sync queue with metadata (action type, payload, timestamp).

2. **UI Update**
   - The UI listens to the local database via reactive streams.
   - The change is reflected instantly, with a subtle indicator if the item is pending sync.

3. **Sync Engine**
   - Monitors the sync queue for pending actions.
   - When connectivity is available, processes the queue:
     - Sends batched changes to the backend.
     - Receives server responses (including conflict feedback).
     - Applies any server-side updates or merges to the local database.
     - Marks items as synced or flags them for user resolution if needed.

4. **Backend**
   - Receives changes, applies them to the central data store.
   - Detects and resolves conflicts, returning merged data or conflict markers.
   - Provides delta updates for other devices.

5. **Other Devices**
   - On next sync, receive delta updates from the backend.
   - Apply changes to local database, updating UI reactively.

### Inline Comments

- Inline comments are tightly coupled to their parent task.
- When a comment is added, edited, or deleted, the change is treated as a first-class sync event.
- Comments are merged using union strategies (for additions) and three-way merge (for edits/deletes), ensuring no user input is lost.
- The UI displays comments in chronological order, with clear attribution and sync status.

---

## Repositories, Services, and Separation of Concerns

### Core Principle

> **Separation of Concerns**  
> Data storage, sync logic, and user interface must be cleanly separated in the codebase. This ensures flexibility, testability, and long-term maintainability.

#### Architectural Layers

- **UI Layer**: Stateless widgets and minimal stateful widgets, responsible only for rendering and user input.
- **ViewModel Layer**: Handles UI state, data transformations, and commands. Mediates between UI and repositories.
- **Repository Layer**: Abstracts data access, combining local and remote sources. Acts as the single source of truth for the app’s data.
- **Service Layer**: Encapsulates platform-specific APIs (e.g., network, storage, background tasks).
- **Sync Engine**: Orchestrates synchronization, conflict resolution, and background processing.

#### Dependency Injection

- Uses provider or similar DI frameworks to inject repositories and services, enabling modularity and testability.

#### Testing

- Each layer is unit tested independently.
- Integration tests cover the full data flow, including offline/online transitions and conflict scenarios.

---

## Sync Protocols and Backend API Design

### RESTful API Design

- **Resource-Oriented**: Endpoints represent resources (tasks, comments), not actions.
- **Consistent Naming**: Uses plural nouns, predictable patterns (e.g., `/tasks`, `/tasks/{id}/comments`).
- **Delta Sync**: Supports endpoints for fetching only changed data since a given timestamp or sync token (e.g., `/tasks/delta?since=...`).
- **Batch Operations**: Allows batching of multiple changes in a single request for efficiency.
- **Conflict Feedback**: Returns clear conflict markers and merged data when conflicts are detected.
- **Statelessness**: Each request is independent, with all necessary context provided in the payload.

### Security

- All endpoints require authentication (e.g., JWT tokens).
- All traffic is encrypted (HTTPS/TLS).
- Rate limiting and abuse prevention are enforced at the API gateway.

### Scalability

- Backend is designed to scale horizontally, supporting multiple families, users, and devices.
- Efficient indexing and caching are used to optimize performance for large lists and high-frequency sync.

---

## Security and Data Safety

### Core Principle

> **Never Lose Data**  
> User data is sacred. Every action must be designed to prevent accidental loss—whether from crashes, sync conflicts, or user error.

#### Data Safety Strategies

- **Local Persistence**: All data is written to disk immediately, with transactional guarantees (ACID) provided by the local database engine (Drift/SQLite).
- **Sync Queue Durability**: The sync queue is persisted in the local database, ensuring no pending action is lost.
- **Encryption at Rest**: All sensitive data is encrypted on disk using AES-256, with keys managed securely via platform keystores.
- **Encryption in Transit**: All network communication uses HTTPS/TLS.
- **Access Control**: Only authenticated users can access or modify family data.
- **Audit Logging**: All changes are logged with timestamps and user/device identifiers for traceability.
- **No Orphaned Data**: Referential integrity is enforced in the database schema; no silent overwrites or data loss.

#### User Experience

- Users are warned before destructive actions (e.g., deleting a task or comment).
- Undo functionality is provided where feasible.
- All actions are reversible until successfully synced.

---

## Background Sync and Platform-Specific Considerations

### Background Processing

- **Mobile (Android/iOS)**: Uses `workmanager` or platform-specific background task APIs to run sync jobs even when the app is not in the foreground.
- **Desktop (Windows/macOS/Linux)**: Leverages Dart isolates and OS scheduling to perform background sync.
- **Web**: Uses browser APIs (e.g., Service Workers, IndexedDB) for background sync where supported; otherwise, sync occurs on app focus or user action.

### Platform Nuances

- **Android**: Requires foreground service notification for long-running background tasks.
- **iOS**: Background fetch is limited; sync is triggered on app launch, foreground, or via push notifications (future enhancement).
- **Web**: Background sync is limited by browser capabilities; falls back to periodic sync on user interaction.
- **Desktop**: Sync runs in a background isolate or thread, with minimal impact on UI responsiveness.

### Battery and Resource Management

- Sync frequency and batch size are configurable to balance freshness and resource usage.
- Sync is paused or throttled when the device is low on battery or bandwidth.

---

## Testing, Observability, and Telemetry

### Testing

- **Unit Tests**: Cover all ViewModels, repositories, and sync logic.
- **Integration Tests**: Simulate offline/online transitions, conflict scenarios, and data migrations.
- **Widget Tests**: Ensure UI behaves correctly across platforms and screen sizes.

### Observability

- **Logging**: All sync operations, errors, and conflicts are logged locally and (optionally) sent to a telemetry backend for analysis.
- **Error Reporting**: Critical errors are surfaced to the user with clear, actionable messages.
- **Performance Metrics**: Sync latency, conflict rates, and data integrity checks are monitored for continuous improvement.

---

## Cross-Platform Build and Deployment Considerations

### Supported Platforms

- **Web**: Runs in all modern browsers, leveraging Flutter’s web renderer (CanvasKit or HTML).
- **Windows/macOS/Linux**: Native desktop apps with platform-specific integrations (e.g., file dialogs, notifications).
- **Android/iOS**: Native mobile apps with full offline and background sync support.

### Build System

- Uses Flutter’s build tools and CI/CD pipelines for automated builds, testing, and deployment.
- Platform-specific assets and configurations are managed via conditional imports and build scripts.

### Deployment

- **Web**: Deployed as a Progressive Web App (PWA) for offline access and installability.
- **Mobile**: Distributed via app stores (Google Play, Apple App Store).
- **Desktop**: Distributed via platform-specific installers or app stores.

### Platform Adaptation

- UI adapts to platform conventions (e.g., Material/Cupertino, keyboard/mouse vs. touch, window resizing).
- Platform-specific plugins are used for features like notifications, secure storage, and background tasks.

---

## Data Migration and Schema Evolution

### Schema Versioning

- The local database schema is versioned, with migrations managed via Drift’s migration tools.
- Migrations are tested to ensure data integrity and prevent loss during upgrades.

### Data Migration

- On schema changes, data is migrated in-place, with fallback to backup/restore if needed.
- Old data formats are supported for a grace period to allow seamless upgrades across devices.

### Forward Compatibility

- The sync protocol and backend API are versioned to support rolling upgrades and backward compatibility.

---

## Performance and Scalability

### Local Performance

- All data access is optimized for low latency, using indexed queries and batch operations.
- UI updates are driven by reactive streams, minimizing unnecessary rebuilds.

### Sync Performance

- Delta sync and batching minimize network usage and server load.
- Sync operations are performed in background isolates to avoid blocking the UI.

### Scalability

- Backend is designed to scale horizontally, supporting large families and high-frequency usage.
- Rate limiting and resource quotas prevent abuse and ensure fair usage.

---

## Conclusion

The **family-sync-tasklist** architecture is designed to deliver a **fast, reliable, and delightful experience** for families managing shared tasks and shopping lists. By adhering to strict offline-first principles, robust data safety guarantees, and a clean separation of concerns, the app ensures that **users never lose data, are never blocked by connectivity issues, and always feel in control**. The architecture is fully cross-platform, supporting web, desktop, and mobile with a single codebase, and is built to scale as the app grows in features and adoption.

This document serves as the foundation for ongoing development, ensuring that every new feature and enhancement remains true to the project’s core principles and technical rigor.

---

**End of ARCHITECTURE.md**
Great — I’ve started researching and drafting your `ARCHITECTURE.md` based on the project overview and core principles you provided. I’ll be covering the high-level system design, key components, offline-first behavior, sync mechanisms, data flow, and cross-platform support.

This will take me several minutes, so feel free to leave — I'll keep working in the background. Your report will be saved in this conversation.
