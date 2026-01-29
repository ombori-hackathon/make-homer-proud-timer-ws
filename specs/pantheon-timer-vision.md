# Pantheon Timer - Product Vision

## Overview

**Pantheon Timer** is a Pomodoro/study timer macOS app where each focus session is "overseen" by a Greek god. Gods provide motivational pep talks at session start, celebrate breaks, and comment on your focus. Different gods suit different work types, creating a gamified productivity experience.

## Core Concept

Transform mundane productivity tracking into an engaging experience where users "train" with different gods based on their task type. The app combines proven Pomodoro technique with personality-driven encouragement.

## God Roster

| God | Domain | Session Type | Coaching Style |
|-----|--------|--------------|----------------|
| **Athena** | Wisdom & Strategy | Deep work, Study, Research | Wise, measured encouragement |
| **Apollo** | Arts & Light | Creative work, Writing, Design | Artistic, inspiring |
| **Ares** | War & Strength | Physical tasks, Intense sprints | Aggressive, warrior motivation |
| **Hephaestus** | Forge & Craft | Building, Coding, Making | Craftsmanship focus, patient |
| **Dionysus** | Wine & Celebration | Breaks, Rest periods | Playful, encourages enjoyment |

## Screens & Features

### 1. Home/Timer Screen (Main)
- Large circular timer display
- Selected god's avatar/icon
- Start/Pause/Reset controls
- Session type indicator (Focus/Break)
- Today's stats widget (sessions completed)

### 2. God Selection Screen
- Grid/list of available gods
- Each god shows domain and coaching style preview
- Tap to select for next session
- Visual indication of most-used gods

### 3. Settings Screen
- Focus session length (default: 25 min)
- Break length (default: 5 min)
- Notification toggles
- Stats summary view

### 4. Session Complete Modal
- God-specific congratulatory message
- Session stats
- Quick actions: "Start Break" or "Another Session"

## Data Model

```
God
├── name: String
├── domain: String
├── icon: String (SF Symbol or asset name)
├── focusMessages: [String]
├── breakMessages: [String]
└── sessionStartMessages: [String]

Session
├── godId: String
├── type: SessionType (focus/break)
├── duration: TimeInterval
├── completedAt: Date
└── wasCompleted: Bool

UserStats
├── totalSessions: Int
├── sessionsToday: Int
├── sessionsByGod: [String: Int]
└── currentStreak: Int
```

## Technical Architecture

### No External Dependencies Required
- **Timer**: `Timer.publish()` or `Task.sleep()` (built-in)
- **Notifications**: `UserNotifications` framework (built-in)
- **Persistence**: `UserDefaults` for settings/stats (expandable to Core Data later)
- **UI**: Pure SwiftUI with macOS 14+ target

### Future API Integration
The FastAPI backend can later support:
- Cross-device sync
- Leaderboards / social features
- Cloud backup of statistics
- Achievement system

## MVP Scope (5-Hour Build)

### Must Have (Hours 1-3)
- [ ] Timer countdown with start/pause/reset
- [ ] God selection (minimum 3 gods)
- [ ] God-specific messages on session start/complete
- [ ] Basic notifications when timer ends
- [ ] Session counter for today

### Nice to Have (Hour 4)
- [ ] All 5 gods with unique personalities
- [ ] Customizable timer lengths
- [ ] Session statistics view
- [ ] Polished UI

### Stretch Goals (Hour 5)
- [ ] Sound effects on completion
- [ ] Animated transitions
- [ ] Multiple message variants per god
- [ ] Dark mode styling

## Long-Term Roadmap

### Phase 2: Enhanced Experience
- More gods (Hermes, Poseidon, Hera, etc.)
- Achievement system ("Trained with Athena 100 times")
- Daily/weekly goals
- Focus music integration

### Phase 3: Social & Sync
- API integration for cloud sync
- Friend leaderboards
- Share sessions to social media
- "Guild" productivity groups

### Phase 4: Advanced Features
- AI-generated god messages
- Adaptive session recommendations
- Calendar integration
- Detailed analytics dashboard

## Design Principles

1. **Simplicity First**: Core timer must work flawlessly before adding features
2. **Personality Over Polish**: God personalities matter more than animations
3. **Respect User Time**: Quick actions, minimal friction to start sessions
4. **Positive Reinforcement**: Gods encourage, never shame

## File Structure (Proposed)

```
Sources/
├── PantheonTimerApp.swift     # App entry point
├── Views/
│   ├── TimerView.swift        # Main timer screen
│   ├── GodSelectionView.swift # God picker
│   ├── SettingsView.swift     # Configuration
│   └── SessionCompleteView.swift
├── Models/
│   ├── God.swift              # God data model
│   ├── Session.swift          # Session tracking
│   └── UserStats.swift        # Statistics
├── Services/
│   ├── TimerService.swift     # Timer logic
│   └── NotificationService.swift
└── Data/
    └── GodDatabase.swift      # God definitions & messages
```

## Success Metrics

- Users complete more Pomodoro sessions than with plain timers
- Users develop "favorite" gods (personality resonance)
- App feels fun, not like a chore
- Simple enough to use without tutorial
