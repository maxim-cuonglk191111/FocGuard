# FocGuard — Engineering Product Document (EPD)

Version: 1.0
Status: Production Specification
Last Updated: 2026-05-27

---

# 1. Product Definition

## 1.1 Product Name

FocGuard

---

## 1.2 Product Category

Desktop productivity and digital wellbeing application.

---

## 1.3 Product Mission

Reduce burnout and unhealthy continuous screen usage by enforcing structured breaks using adaptive interruption systems that balance productivity, flexibility, and behavioral psychology.

---

## 1.4 Product Philosophy

The product must:

* Encourage intentional work habits
* Prevent accidental overwork
* Avoid malware-like behavior
* Respect user autonomy while introducing behavioral friction
* Be local-first and privacy-first

The product must NOT:

* Hijack operating system security mechanisms
* Prevent users from force quitting the application
* Install kernel-level hooks
* Behave like ransomware or parental control spyware

---

# 2. Core Product Principles

## 2.1 Behavioral Friction Instead of Hard Control

The system should make bypassing breaks intentional and psychologically costly, not impossible.

---

## 2.2 Escalation Instead of Immediate Punishment

Reminder intensity increases gradually.

Order:

1. Passive notification
2. Persistent banner
3. Partial dim overlay
4. Fullscreen break overlay

---

## 2.3 Privacy First

All activity tracking remains local by default.

The application must never:

* capture screen contents
* upload keyboard input
* record audio
* record webcam footage

Webcam-based movement detection must process frames locally only.

---

# 3. Supported Platforms

| Platform           | Supported | Priority |
| ------------------ | --------- | -------- |
| Windows 10+        | Yes       | High     |
| macOS 13+          | Yes       | High     |
| Ubuntu-based Linux | Yes       | Medium   |

---

# 4. Technology Stack

# 4.1 Frontend

| Area             | Technology    |
| ---------------- | ------------- |
| UI Framework     | React 19      |
| Build Tool       | Vite          |
| Styling          | TailwindCSS   |
| Animation        | Framer Motion |
| State Management | Zustand       |
| Validation       | Zod           |

---

# 4.2 Desktop Runtime

| Area         | Technology       |
| ------------ | ---------------- |
| Runtime      | Electron 33+     |
| Packaging    | electron-builder |
| Auto Updates | electron-updater |

---

# 4.3 Backend Runtime

| Area          | Technology     |
| ------------- | -------------- |
| Runtime       | Node.js        |
| Language      | TypeScript     |
| DB            | SQLite         |
| SQLite Driver | better-sqlite3 |
| ORM           | Drizzle ORM    |

---

# 4.4 Testing

| Type              | Tool                  |
| ----------------- | --------------------- |
| Unit Testing      | Vitest                |
| E2E Testing       | Playwright            |
| Component Testing | React Testing Library |

---

# 5. Non-Negotiable Engineering Rules

## 5.1 TypeScript Rules

Mandatory:

* `"strict": true`
* `"noImplicitAny": true`
* `"exactOptionalPropertyTypes": true`

Forbidden:

* `any`
* `@ts-ignore`
* implicit returns

---

## 5.2 Renderer Restrictions

Renderer process MUST NOT:

* access filesystem
* access database
* access OS APIs
* spawn processes

All privileged access must go through IPC.

---

## 5.3 IPC Security Rules

Mandatory:

* `contextIsolation=true`
* `nodeIntegration=false`
* preload bridge only

Forbidden:

* exposing raw electron APIs
* synchronous IPC

---

## 5.4 React Rules

React components MUST:

* remain presentation-focused
* remain stateless where possible

React components MUST NOT:

* own timers
* contain scheduling logic
* directly access persistence

---

## 5.5 Timekeeping Rules

All timing logic must use monotonic timers.

Mandatory:

* `performance.now()`

Forbidden:

* `Date.now()` for scheduler calculations

Reason:

* clock drift
* timezone changes
* sleep/wake inconsistencies

---

# 6. Repository Structure

```text
FocGuard/
├── apps/
│   ├── desktop/
│   │   ├── electron/
│   │   ├── renderer/
│   │   ├── preload/
│   │   └── assets/
│   │
│   └── website/
│
├── packages/
│   ├── shared/
│   ├── ui/
│   ├── scheduler-engine/
│   ├── overlay-engine/
│   ├── activity-engine/
│   ├── analytics-engine/
│   ├── database/
│   └── event-bus/
│
├── docs/
├── scripts/
├── tests/
├── tooling/
└── .github/
```

---

# 7. Git Strategy

## 7.1 Branching

| Branch    | Purpose     |
| --------- | ----------- |
| main      | production  |
| develop   | integration |
| feature/* | features    |
| fix/*     | bug fixes   |

---

## 7.2 Commit Convention

Mandatory Conventional Commits.

Examples:

```bash
feat(overlay): add fullscreen break window
fix(timer): resolve sleep wake drift
refactor(ipc): isolate event serialization
```

---

# 8. Required .gitignore

```gitignore
node_modules
dist
out
coverage
playwright-report
.env
.env.*
.DS_Store
*.log
*.sqlite
*.sqlite-journal
*.db
```

---

# 9. Architecture

# 9.1 High-Level Architecture

```text
Renderer UI
   ↓
IPC Layer
   ↓
Main Process
   ├── Scheduler Engine
   ├── Activity Engine
   ├── Overlay Engine
   ├── Analytics Engine
   ├── Database Layer
   └── Notification Engine
```

---

# 9.2 Architecture Rules

Business logic belongs ONLY in:

* engines
* services
* domain modules

Forbidden:

* business logic in React components
* database logic in IPC handlers
* scheduler logic in overlay engine

---

# 10. Core Engines

# 10.1 Scheduler Engine

Responsible for:

* work timers
* break timers
* escalation timing
* snooze scheduling
* emergency pass expiration

Must survive:

* sleep/wake
* timezone changes
* app reloads

---

## Scheduler State Machine

```text
IDLE
 → WORKING
 → WARNING
 → BREAK_REQUIRED
 → BREAK_ACTIVE
 → BREAK_COMPLETED
 → WORKING
```

---

## Scheduler Escalation

```text
WORKING (60m)
  ↓
Passive Notification
  ↓ +2m
Persistent Banner
  ↓ +2m
Dim Overlay
  ↓ +1m
Fullscreen Break Overlay
```

---

# 10.2 Activity Engine

Tracks:

* keyboard activity
* mouse movement
* active application
* idle state

Must NOT:

* log keystrokes
* store typed content

---

## Idle Rules

| Idle Duration | Behavior                   |
| ------------- | -------------------------- |
| < 2 min       | continue session           |
| 2–10 min      | pause fatigue accumulation |
| > 10 min      | auto pause session         |

---

# 10.3 Overlay Engine

Responsible for:

* fullscreen overlays
* dim overlays
* animations
* break UI
* multi-monitor behavior

---

## Overlay Window Requirements

Mandatory:

* alwaysOnTop=true
* fullscreen=true
* visibleOnAllWorkspaces=true
* skipTaskbar=true

Forbidden:

* disabling task manager
* blocking OS shortcuts
* kernel hooks

---

## Multi-Monitor Rules

Overlay must:

* cover all active displays
* preserve z-order consistency
* recover from monitor hot-plugging

---

# 10.4 Analytics Engine

Responsible for:

* session summaries
* fatigue trends
* skip statistics
* streaks

Must aggregate locally.

---

# 11. Emergency Pass System

## Rules

Default:

* 3 passes/day

Each pass:

* delays enforcement by 30 minutes

Unused passes:

* expire daily
* do not roll over

---

## Abuse Prevention

If:

* user uses >5 passes/day repeatedly

Then:

* app surfaces wellness warnings

App must NOT:

* hard block usage

---

# 12. Focus Session System

## Purpose

Temporarily suppress hard interruptions during:

* meetings
* exams
* deep work

---

## Rules

During focus session:

* fullscreen overlays disabled
* only soft reminders allowed

After session:

* required break duration increases

Formula:

```text
extra_break_minutes =
focus_session_minutes / 6
```

---

# 13. Meeting Detection

Supported:

* Zoom
* Google Meet
* Microsoft Teams
* Discord screen sharing

Detection Methods:

* active window title
* fullscreen state
* known process names

Must NOT:

* inspect network traffic
* capture screen contents

---

# 14. Database Schema

# 14.1 Tables

## sessions

```sql
id TEXT PRIMARY KEY
started_at INTEGER NOT NULL
ended_at INTEGER
duration_seconds INTEGER
breaks_taken INTEGER
passes_used INTEGER
```

---

## breaks

```sql
id TEXT PRIMARY KEY
session_id TEXT NOT NULL
started_at INTEGER NOT NULL
ended_at INTEGER
completed INTEGER NOT NULL
skip_reason TEXT
```

---

## settings

```sql
key TEXT PRIMARY KEY
value TEXT NOT NULL
```

---

## analytics_daily

```sql
date TEXT PRIMARY KEY
focus_minutes INTEGER
break_minutes INTEGER
passes_used INTEGER
skip_count INTEGER
```

---

# 15. IPC Contracts

# 15.1 Naming Convention

Format:

```text
domain:event.action
```

Example:

```text
scheduler:session.start
overlay:break.show
analytics:daily.fetch
```

---

# 15.2 Forbidden IPC Patterns

Forbidden:

* bidirectional event ambiguity
* anonymous payloads
* untyped channels

---

# 15.3 IPC Validation

All IPC payloads must:

* use Zod schemas
* reject unknown fields

---

# 16. Event Bus

# 16.1 Canonical Events

```ts
type AppEvent =
  | "session.started"
  | "session.paused"
  | "session.resumed"
  | "break.warning"
  | "break.started"
  | "break.completed"
  | "break.skipped"
  | "overlay.shown"
  | "overlay.dismiss_attempt"
  | "focus.started"
  | "focus.completed"
```

---

# 17. UI/UX Specification

# 17.1 Design Language

Themes:

* calm
* low stimulation
* minimal visual noise

Animations:

* soft easing
* non-gaming aesthetic

---

# 17.2 Overlay Behavior

Break overlay must:

* appear within 300ms
* animate smoothly
* support keyboard navigation

---

# 17.3 Accessibility

Mandatory:

* keyboard-only navigation
* screen reader labels
* high contrast mode
* scalable fonts

---

# 18. Failure Handling

# 18.1 Overlay Failure

If overlay crashes:

1. restart overlay process
2. restore state
3. log diagnostic event

---

# 18.2 Database Corruption

If SQLite corruption detected:

* backup corrupted DB
* rebuild schema
* preserve recoverable records

---

# 18.3 Sleep/Wake Recovery

Upon wake:

* recompute timers
* resync scheduler state
* prevent duplicate overlays

---

# 19. Performance Budgets

| Metric             | Target  |
| ------------------ | ------- |
| Cold Start         | < 3s    |
| Overlay Appearance | < 300ms |
| Idle CPU Usage     | < 2%    |
| Memory Usage       | < 250MB |
| DB Query Time      | < 50ms  |

---

# 20. Security Requirements

Mandatory:

* signed builds
* secure IPC
* sandboxed renderer

Forbidden:

* remote code execution
* eval()
* dynamic script injection

---

# 21. Logging Strategy

## Levels

* debug
* info
* warn
* error
* fatal

---

## Rules

Production logs must:

* redact PII
* rotate automatically
* remain local-first

---

# 22. Testing Requirements

# 22.1 Minimum Coverage

| Layer            | Coverage |
| ---------------- | -------- |
| Scheduler Engine | 95%      |
| Overlay Engine   | 85%      |
| UI Components    | 70%      |

---

# 22.2 Required E2E Scenarios

* sleep/wake cycle
* multi-monitor
* rapid snoozing
* timezone change
* overlay crash recovery
* meeting detection
* focus session overlap

---

# 23. CI/CD

# 23.1 GitHub Actions

Required pipelines:

* lint
* typecheck
* unit tests
* E2E tests
* packaging

---

# 23.2 Release Channels

| Channel | Purpose     |
| ------- | ----------- |
| nightly | unstable    |
| beta    | pre-release |
| stable  | production  |

---

# 24. Packaging

## Windows

* NSIS installer

## macOS

* notarized dmg

## Linux

* AppImage

---

# 25. AI Agent Instructions

# 25.1 Mandatory Rules

Agents MUST:

* preserve module boundaries
* avoid circular dependencies
* maintain strict typing
* add tests for all scheduler changes

---

# 25.2 Forbidden Actions

Agents MUST NOT:

* move business logic into React
* bypass IPC validation
* introduce new state libraries
* directly mutate Zustand stores

---

# 25.3 Refactor Rules

Large refactors require:

* migration plan
* compatibility notes
* rollback path

---

# 26. Implementation Order

# Phase 1

* repo bootstrap
* Electron shell
* IPC layer
* renderer architecture

# Phase 2

* scheduler engine
* activity engine

# Phase 3

* overlay engine

# Phase 4

* analytics system

# Phase 5

* adaptive fatigue system

# Phase 6

* cloud sync

---

# 27. Future Architecture

Potential future services:

* AI fatigue prediction
* wearable integration
* posture detection
* team wellness analytics
* cross-device synchronization

---

# 28. Explicit Non-Goals

The product is NOT:

* spyware
* parental control software
* employee surveillance software
* anti-cheat software
* productivity scoring surveillance

---

# 29. Final Engineering Philosophy

The codebase must optimize for:

1. Predictability
2. Determinism
3. Recoverability
4. Low cognitive overhead
5. Long-term maintainability

Every subsystem should be understandable independently and testable in isolation.
