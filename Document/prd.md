# Product Requirements Document: FocGuard

---
Version: 1.0
Status: Production Requirement Document
Last Updated: 2026-05-27

# Product Overview

## Product Vision

FocGuard is a desktop productivity and digital wellbeing application that helps users maintain sustainable work habits by enforcing structured break intervals through intelligent fullscreen interventions, adaptive reminders, and flexible override systems.

The product aims to reduce burnout, eye strain, and unhealthy continuous computer usage without disrupting genuinely important work sessions.

---

## Target Users

### Primary Users

* Software developers
* Designers
* Freelancers
* Students
* Remote workers
* Knowledge workers with long screen-time sessions

### Secondary Users

* ADHD users seeking behavioral structure
* Gamers
* Writers
* Startup founders
* Productivity enthusiasts

---

## Business Objectives

* Improve user wellbeing and reduce burnout risk
* Increase sustained productivity through structured recovery
* Build a habit-forming productivity utility with high retention
* Establish a differentiated product between Pomodoro timers and strict parental-control style blockers
* Create monetization opportunities through premium analytics and advanced automation

---

## Success Metrics

### Product Metrics

* Daily Active Users (DAU)
* Weekly Retention Rate
* Average Daily Focus Time
* Break Compliance Rate
* Session Completion Rate

### Behavioral Metrics

* Reduction in continuous work sessions exceeding 2 hours
* Average breaks taken per day
* Emergency pass usage frequency
* Snooze frequency trend over time

### Technical Metrics

* Overlay trigger reliability
* Crash-free sessions
* CPU and memory usage
* Notification delivery latency

---

# User Personas

## Persona 1: Minh — Remote Software Engineer

* **Demographics:** 27 years old, full-stack engineer, highly technical
* **Goals:**

  * Maintain deep work focus
  * Avoid burnout and eye strain
  * Build healthier work habits
* **Pain Points:**

  * Frequently works 3–5 hours without breaks
  * Ignores standard notifications
  * Burnout after intense coding periods
* **User Journey:**

  * Installs app
  * Sets 60m work / 5m break cycle
  * Uses Focus Session during meetings
  * Reviews analytics weekly

---

## Persona 2: Lan — University Student

* **Demographics:** 21 years old, moderate technical proficiency
* **Goals:**

  * Stay productive while studying
  * Avoid procrastination
  * Prevent exhaustion during exam periods
* **Pain Points:**

  * Loses track of time
  * Often studies continuously for hours
  * Needs flexibility during online exams
* **User Journey:**

  * Uses predefined templates
  * Activates Emergency Pass during exams
  * Receives wellness reminders
  * Tracks study consistency

---

# Feature Requirements

| Feature                             | Description                                | User Stories                                                                  | Priority | Acceptance Criteria                                               | Dependencies              |
| ----------------------------------- | ------------------------------------------ | ----------------------------------------------------------------------------- | -------- | ----------------------------------------------------------------- | ------------------------- |
| **Work Session Timer**              | Tracks active work duration                | As a user, I want the app to track my active screen time automatically        | Must     | Timer accurately tracks active input and pauses during idle state | Activity detection engine |
| **Fullscreen Break Overlay**        | Displays mandatory fullscreen break screen | As a user, I want a strong interruption that forces me to rest                | Must     | Overlay covers entire screen and cannot be accidentally dismissed | Electron window manager   |
| **Break Countdown**                 | Countdown timer during break period        | As a user, I want to know how much break time remains                         | Must     | Countdown remains visible and accurate                            | Overlay system            |
| **Snooze / Delay Break**            | Allows short postponement of break         | As a user, I want flexibility during critical moments                         | Must     | User can delay by configurable intervals                          | Session scheduler         |
| **Emergency Pass System**           | Limited daily hard overrides               | As a user, I want emergency flexibility without disabling the system entirely | Must     | Passes decrement correctly and reset daily                        | User settings             |
| **Focus Session Mode**              | Scheduled uninterrupted work blocks        | As a user, I want to temporarily suppress interruptions during important work | Must     | Breaks are softened during focus sessions                         | Scheduler                 |
| **Progressive Reminder Escalation** | Reminder intensity increases over time     | As a user, I want warnings before hard interruptions                          | Should   | Reminder stages transition correctly                              | Notification engine       |
| **Meeting Detection**               | Detect active meetings or presentations    | As a user, I want fewer interruptions during calls                            | Should   | App detects Zoom, Meet, Teams fullscreen state                    | OS process detection      |
| **Analytics Dashboard**             | Displays work/break statistics             | As a user, I want insights into my work habits                                | Should   | Dashboard updates daily metrics correctly                         | Local database            |
| **Fatigue Score System**            | Dynamic fatigue estimation                 | As a user, I want smarter break timing                                        | Could    | Fatigue score adapts based on activity intensity                  | Activity analytics        |
| **Webcam Movement Verification**    | Detect physical movement during breaks     | As a user, I want accountability for actual breaks                            | Could    | Webcam detection recognizes movement                              | Camera permissions        |
| **Cloud Sync**                      | Sync settings across devices               | As a user, I want consistency across machines                                 | Could    | Settings sync successfully                                        | Backend API               |

---

# User Flows

## Flow 1: Standard Work Cycle

1. User launches app
2. App tracks active work session
3. After 60 minutes:

   * Soft notification appears
4. After escalation threshold:

   * Fullscreen overlay activates
5. User chooses:

   * Start break
   * Snooze 5 minutes
   * Use Emergency Pass

### Alternative Paths

* User activates Focus Session before timer ends
* Meeting detection suppresses fullscreen overlay

### Error States

* Overlay fails to appear
* Activity tracking pauses unexpectedly

---

## Flow 2: Emergency Override

1. Fullscreen break overlay appears
2. User selects “Emergency Pass”
3. App verifies remaining passes
4. Session extends by configured duration
5. App schedules re-reminder

### Alternative Paths

* No remaining passes
* User switches to Focus Session

### Error States

* Pass counter desync
* Reminder rescheduling failure

---

# Non-Functional Requirements

## Performance

* **Load Time:** < 3 seconds
* **Concurrent Users:** Single local user
* **Response Time:** Overlay trigger latency < 300ms

---

## Security

### Authentication

* Local authentication optional
* Cloud sync authentication for premium users

### Authorization

* Single-user permission model

### Data Protection

* Local analytics encrypted at rest
* No screen content collection
* Webcam data processed locally only

---

## Compatibility

### Devices

* Desktop and laptop devices

### Operating Systems

* Windows 10+
* macOS 13+
* Linux (Ubuntu-based distributions)

### Screen Sizes

* 13-inch laptops to ultrawide monitors
* Multi-monitor support required

---

## Accessibility

### Compliance Level

* WCAG 2.1 AA target

### Specific Requirements

* Keyboard navigability
* Screen reader compatibility
* Adjustable text scaling
* Colorblind-safe overlays

---

# Technical Specifications

## Frontend

### Technology Stack

* Electron
* React
* TypeScript
* TailwindCSS
* Framer Motion

### Design System

* Minimal distraction-first UI
* Calm wellness-oriented design language

### Responsive Design

* Support all desktop resolutions
* Multi-monitor overlay support

---

## Backend

### Technology Stack

* Node.js
* Electron Main Process

### API Requirements

* Local IPC communication
* Optional cloud REST API

### Database

* SQLite for local analytics
* Optional cloud sync storage

---

## Infrastructure

### Hosting

* Optional cloud backend on AWS or Supabase

### Scaling

* Primarily local-first architecture

### CI/CD

* GitHub Actions
* Auto-update system
* Cross-platform build pipeline

---

# Analytics & Monitoring

## Key Metrics

* Daily focus hours
* Break completion rate
* Average continuous work duration
* Snooze frequency
* Emergency pass usage

---

## Events

* Break started
* Break skipped
* Snooze activated
* Focus Session started
* Overlay dismissed
* Idle state detected

---

## Dashboards

* Daily productivity dashboard
* Weekly fatigue trend
* Break consistency graph

---

## Alerting

* Crash reporting
* Overlay failure alerts
* Excessive CPU usage alerts

---

# Release Planning

## MVP (v1.0)

### Features

* Work session timer
* Fullscreen break overlay
* Countdown timer
* Snooze system
* Emergency pass system
* Focus Session mode
* Basic analytics

### Timeline

* 6–8 weeks

### Success Criteria

* 40% weekly retention
* 70% successful break completion rate
* < 2% crash rate

---

## Future Releases

### v1.1

* Meeting detection
* Smart escalation system
* Improved analytics
* Themes and customization

### v1.2

* Fatigue scoring
* Adaptive scheduling
* Cloud sync

### v2.0

* AI-powered work pattern analysis
* Team wellness mode
* Cross-device ecosystem
* Wearable integration

---

# Open Questions & Assumptions

## Questions

* How aggressive should overlay enforcement be?
* Should users be able to fully disable hard lock mode?
* What is the optimal default work/break ratio?

---

## Assumptions

* Users value flexibility over strict enforcement
* Most users prefer local-first privacy
* Strong UX matters more than extreme restriction

---

# Appendix

## Competitive Analysis

### Competitor 1: Stretchly

* **Strengths:** Lightweight, open-source, simple
* **Weaknesses:** Weak enforcement, limited analytics

### Competitor 2: TimeOut (macOS)

* **Strengths:** Polished UX
* **Weaknesses:** macOS-only, limited behavioral flexibility

### Competitor 3: Freedom

* **Strengths:** Strong blocking system
* **Weaknesses:** Feels punitive and restrictive

---

## User Research Findings

### Finding 1

Users ignore passive notifications after repeated exposure.

### Finding 2

Users accept interruptions more willingly if emergency flexibility exists.

### Finding 3

Behavioral friction works better than absolute blocking.

---

## AI Conversation Insights

### Conversation 1

* **Date:** 2026-05-27
* **AI Model Used:** ChatGPT
* **Key Insights:**

  * Progressive reminder escalation improves compliance
  * Emergency Pass system reduces frustration
  * Focus Session mode prevents workflow disruption

### AI-Generated Edge Cases

* Online examinations
* Live presentations
* Screen sharing sessions
* Deadline crunch periods
* User attempting repeated bypasses

### AI-Suggested Improvements

* Adaptive fatigue scoring
* Meeting detection
* Behavioral analytics
* Accountability-based break verification

---

# Glossary

### Emergency Pass

A limited override allowing users to postpone or skip enforced breaks.

### Focus Session

A scheduled uninterrupted work period with softened reminders.

### Snooze

Temporary short-duration postponement of a break.

### Fatigue Score

A dynamic estimate of user cognitive strain based on activity intensity.
