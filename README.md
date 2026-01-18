# family-sync-tasklist

A cross-platform Flutter/Dart proof of concept that gives families a single, reliable place to manage shared tasks and shopping lists with inline comments, durable local storage, and automatic background sync.

---

## Project summary

**family-sync-tasklist** solves the fragmentation of family task management by providing a single shared list that:

- Works **fully offline** with immediate local persistence  
- Syncs automatically and reliably across devices with **eventual consistency**  
- Supports **inline comments** attached to each item  
- Runs on **web, desktop, and mobile** via Flutter  
- Prioritises **data durability**, **predictable conflict resolution**, and **accessibility**

This repository contains the POC codebase and the canonical architecture, principles, requirements, and decisions that guide development.

---

## Internal documentation

All core project documents are included in the repository and form the canonical reference for contributors and maintainers:

- **PROJECT_OVERVIEW.md** — High level summary and goals  
- **PRINCIPLES.md** — Core architectural and UX principles  
- **ARCHITECTURE.md** — Detailed system architecture and component responsibilities  
- **REQUIREMENTS.md** — Functional and non‑functional requirements, user stories, and edge cases  
- **DECISIONS.md** — Architectural decision log and rationale

Refer to these files for design rationale, constraints, and the expected behavior of each subsystem.

---

## Quick start

### Prerequisites

- **Flutter SDK** (stable channel recommended)  
- **Dart SDK** (bundled with Flutter)  
- A supported IDE such as **VS Code** or **Android Studio**  
- Platform toolchains for your targets:
  - **Android**: Android SDK and an emulator or device  
  - **iOS**: Xcode on macOS for simulator or device builds  
  - **Windows/macOS/Linux**: Desktop support enabled in Flutter

Verify your environment:

```bash
flutter doctor
```

### Clone and install

```bash
git clone https://github.com/<your-org>/family-sync-tasklist.git
cd family-sync-tasklist
flutter pub get
```

### Run locally

- **Web**  
  ```bash
  flutter run -d chrome
  ```

- **Windows**  
  ```bash
  flutter run -d windows
  ```

- **macOS**  
  ```bash
  flutter run -d macos
  ```

- **Linux**  
  ```bash
  flutter run -d linux
  ```

- **Android**  
  ```bash
  flutter run -d <android-device-id>
  ```

- **iOS**  
  ```bash
  flutter run -d <ios-device-id>
  ```

> Note: iOS builds require macOS and Xcode.

---

## Development notes

### Project structure suggestion

```
lib/
  models/
  repositories/
  sync/
  viewmodels/
  ui/
  platform_adapters/
test/
assets/
docs/
  ARCHITECTURE.md
  DECISIONS.md
  PRINCIPLES.md
  PROJECT_OVERVIEW.md
  REQUIREMENTS.md
```

### Storage backends

- Mobile and desktop targets use SQLite (Drift or sqflite), Isar, or Hive depending on platform needs  
- Web uses IndexedDB or a wrapped localStorage fallback  
- All persistence is accessed through repository interfaces to keep the rest of the codebase platform-agnostic

### Sync engine

- Tracks local changes with metadata and a change queue  
- Performs batched push and pull operations with the backend API  
- Default conflict resolution is **Last-Write-Wins** with CRDT-friendly merges for comment lists and other complex types  
- Sync scheduling uses platform-specific background mechanisms and is resource-aware

---

## Roadmap and next steps

### Phase 1 POC (current)
- Core data models and local persistence  
- Basic UI for tasks, shopping items, and inline comments  
- Offline-first behavior and immediate persistence  
- Sync engine skeleton and metadata handling

### Phase 2 Prototype
- Full push/pull sync implementation with batching and retries  
- CRDT-friendly merges for comments and checklist items  
- Background sync across platforms and sync indicators in UI  
- Export/import and backup support  
- Local and push notifications

### Phase 3 Pre-release
- Authentication and family group management  
- Hardened backend API with idempotency and audit trails  
- Advanced permissions and sharing controls  
- Comprehensive test coverage and performance tuning

### Phase 4 Future enhancements
- Real-time collaboration features  
- Calendar and reminder integrations  
- Widget and desktop tray integrations  
- Plugin ecosystem and third-party integrations

---

## Contributor guidance

**How to contribute**

- Read **PRINCIPLES.md** and **DECISIONS.md** before proposing architectural changes  
- Open issues for bugs, feature requests, or design discussions  
- Create feature branches named `feat/<short-description>` or `fix/<short-description>`  
- Include tests for new logic and update documentation when behavior changes

**Pull request checklist**

- Clear description of the change and motivation  
- Linked issue when applicable  
- Unit and integration tests added or updated  
- Documentation updated for public APIs or UX changes  
- CI checks passing

**Coding conventions**

- Prefer small, focused commits with descriptive messages  
- Keep UI logic in ViewModels and business logic in repositories or sync modules  
- Use dependency injection to enable easy mocking in tests

---

## Testing and observability

- Unit tests for models, repositories, and sync logic  
- Widget tests for UI components and accessibility checks  
- Integration tests for end-to-end sync scenarios and offline workflows  
- Structured logging and error reporting for sync health and diagnostics

Run tests:

```bash
flutter test
```

---

## Security and privacy

- **Encryption**: Local storage should be encrypted where platform support exists  
- **Transport security**: All backend communication must use HTTPS/TLS  
- **Access control**: Only authorized family members can view or modify shared lists  
- **Data portability**: Users must be able to export and delete their data

Refer to **ARCHITECTURE.md** and **REQUIREMENTS.md** for detailed security and privacy expectations.

---

## License

Choose and add a license file such as **MIT** or **Apache 2.0**. Update this section with the chosen license name and a short summary.

---

## Contact and support

For questions about architecture, design decisions, or contribution workflow, open an issue or contact the maintainers via the repository issue tracker.

---
# Acknowledgements
