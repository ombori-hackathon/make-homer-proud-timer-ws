# Feature: Home/Timer Screen with Database Backend

## Summary
Build the main Pomodoro timer screen with Greek god theming, backed by a PostgreSQL database with Alembic migrations and FastAPI DAOs. Includes setup of code quality guardrails and comprehensive documentation.

## Trigger
User opens the app and interacts with timer controls (Start/Pause/Reset).

## Expected Result
- Circular timer countdown with visual progress ring
- **Randomly selected god** displayed on app launch (from the 5 available gods)
- Start/Pause/Reset button controls
- Session type indicator (Focus/Break)
- Stats widget showing today's completed sessions
- Sessions persisted to database when completed
- Code quality guardrails (linting, formatting, pre-commit hooks) in place
- Comprehensive CLAUDE.md documentation for each component

---

## Phase 0: Research & Guardrails Setup

### 0.0 Update Main CLAUDE.md with Self-Improving Context Rules
Add to `/Users/juditbartha/hackathon/terminal-velocity/CLAUDE.md`:

```markdown
## Self-Improving Context (MANDATORY)

Every feature implementation MUST include iterative documentation updates:

1. **Research First**: Before implementing, research best practices for the technologies involved
2. **Document as You Go**: When you discover patterns, gotchas, or conventions, immediately add them to the nearest CLAUDE.md file
3. **Proximity Rule**: Add learnings to the CLAUDE.md closest to the relevant code:
   - Model patterns → `app/models/CLAUDE.md`
   - Router patterns → `app/routers/CLAUDE.md`
   - View patterns → `Sources/Views/CLAUDE.md`
   - General patterns → submodule root CLAUDE.md
4. **Concise Files**: Keep each CLAUDE.md under 500 lines. If approaching this limit:
   - Split into dedicated agent (`.claude/agents/`)
   - Create a new skill (`.claude/skills/`)
   - Create a sub-component CLAUDE.md file
5. **Agents & Skills**: If a pattern becomes complex enough to warrant detailed instructions, create a dedicated agent or skill file

This self-improving context is paramount and applies to ALL feature work.
```

### 0.1 Research Best Practices
- [ ] Research Python/FastAPI patterns: DAO layer, Alembic migrations, Pydantic v2 best practices
- [ ] Research Swift/SwiftUI patterns: ObservableObject, async/await networking, SF Symbols usage
- [ ] Document findings in updated CLAUDE.md files

### 0.2 Implement Guardrails (from guardrails.md)
- [ ] Add Ruff for Python linting/formatting (`services/api/ruff.toml`)
- [ ] Update `services/api/pyproject.toml` with ruff dev dependency
- [ ] Add pre-commit hooks (`services/api/.pre-commit-config.yaml`)
- [ ] Install pre-commit: `uv add --dev pre-commit && uv run pre-commit install`
- [ ] Add SwiftLint config (`apps/macos-client/.swiftlint.yml`)
- [ ] Create workspace git hooks (`.githooks/pre-commit`)
- [ ] Enable hooks: `chmod +x .githooks/pre-commit && git config core.hooksPath .githooks`
- [ ] Verify all guardrails work: `uv run pre-commit run --all-files`

### 0.3 Create Dedicated CLAUDE.md Files

**New files to create:**

1. `services/api/app/models/CLAUDE.md` - SQLAlchemy model conventions
2. `services/api/app/daos/CLAUDE.md` - DAO pattern documentation
3. `services/api/app/routers/CLAUDE.md` - Router/endpoint conventions
4. `services/api/app/schemas/CLAUDE.md` - Pydantic schema patterns
5. `services/api/alembic/CLAUDE.md` - Migration conventions
6. `apps/macos-client/Sources/Views/CLAUDE.md` - SwiftUI view patterns
7. `apps/macos-client/Sources/Models/CLAUDE.md` - Swift model conventions
8. `apps/macos-client/Sources/Services/CLAUDE.md` - Service layer patterns

---

## Database Schema

### Gods Table
```sql
CREATE TABLE gods (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,
    domain VARCHAR(100) NOT NULL,
    icon VARCHAR(50) NOT NULL,
    coaching_style VARCHAR(200) NOT NULL,
    focus_messages JSONB NOT NULL,
    break_messages JSONB NOT NULL,
    session_start_messages JSONB NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### Sessions Table
```sql
CREATE TABLE sessions (
    id SERIAL PRIMARY KEY,
    god_id INTEGER NOT NULL REFERENCES gods(id),
    session_type VARCHAR(10) NOT NULL CHECK (session_type IN ('focus', 'break')),
    duration_seconds INTEGER NOT NULL,
    started_at TIMESTAMP WITH TIME ZONE NOT NULL,
    completed_at TIMESTAMP WITH TIME ZONE,
    was_completed BOOLEAN NOT NULL DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
CREATE INDEX idx_sessions_started_at ON sessions(started_at);
```

### User Stats Table
```sql
CREATE TABLE user_stats (
    id SERIAL PRIMARY KEY,
    user_id VARCHAR(100) DEFAULT 'default',
    total_sessions INTEGER NOT NULL DEFAULT 0,
    total_focus_minutes INTEGER NOT NULL DEFAULT 0,
    current_streak INTEGER NOT NULL DEFAULT 0,
    last_session_date DATE,
    sessions_by_god JSONB NOT NULL DEFAULT '{}',
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    UNIQUE(user_id)
);
```

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/gods` | List all 5 gods |
| GET | `/gods/{id}` | Get specific god by ID |
| POST | `/sessions` | Create new session record |
| PATCH | `/sessions/{id}/complete` | Mark session as completed |
| GET | `/sessions/today` | Get today's session count and list |
| GET | `/stats` | Get user statistics |

---

## Agent Orchestration Strategy

### Parallel Execution Groups

**Group A: Python Backend** (python-coder agent)
- Database models, DAOs, schemas, routers
- Alembic migrations
- API tests

**Group B: Swift Client** (swift-coder agent)
- SwiftUI views and components
- Models and services
- Timer logic

**Group C: Cross-cutting** (main orchestrator)
- Guardrails setup
- CLAUDE.md documentation
- Integration testing

### Agent Assignment

| Task | Agent | Submodule |
|------|-------|-----------|
| Research Python best practices | python-coder | services/api |
| Research Swift best practices | swift-coder | apps/macos-client |
| Setup Python guardrails | python-coder | services/api |
| Setup Swift guardrails | swift-coder | apps/macos-client |
| Create Python CLAUDE.md files | python-coder | services/api |
| Create Swift CLAUDE.md files | swift-coder | apps/macos-client |
| Alembic init + migrations | python-coder | services/api |
| SQLAlchemy models | python-coder | services/api |
| DAO layer implementation | python-coder | services/api |
| Pydantic schemas | python-coder | services/api |
| API routers | python-coder | services/api |
| API tests | tester | services/api |
| Swift models | swift-coder | apps/macos-client |
| TimerService | swift-coder | apps/macos-client |
| APIClient | swift-coder | apps/macos-client |
| SwiftUI views | swift-coder | apps/macos-client |
| Integration test | debugger | both |
| Code review | reviewer | both |

---

## File Changes

### Backend (services/api/)

**New Files:**
- `ruff.toml` - Ruff linting configuration
- `.pre-commit-config.yaml` - Pre-commit hooks
- `alembic.ini` - Alembic configuration
- `alembic/` - Migration directory
- `app/models/god.py` - God SQLAlchemy model
- `app/models/session.py` - Session SQLAlchemy model
- `app/models/user_stats.py` - UserStats SQLAlchemy model
- `app/models/CLAUDE.md` - Model conventions
- `app/schemas/god.py` - God Pydantic schemas
- `app/schemas/session.py` - Session Pydantic schemas
- `app/schemas/stats.py` - Stats Pydantic schemas
- `app/schemas/CLAUDE.md` - Schema conventions
- `app/daos/__init__.py` - DAO package
- `app/daos/base.py` - Base DAO class
- `app/daos/god_dao.py` - God data access
- `app/daos/session_dao.py` - Session data access
- `app/daos/user_stats_dao.py` - Stats data access
- `app/daos/CLAUDE.md` - DAO conventions
- `app/routers/gods.py` - Gods endpoints
- `app/routers/sessions.py` - Sessions endpoints
- `app/routers/stats.py` - Stats endpoints
- `app/routers/CLAUDE.md` - Router conventions
- `alembic/CLAUDE.md` - Migration conventions
- `tests/test_gods.py` - Gods API tests
- `tests/test_sessions.py` - Sessions API tests

**Modified Files:**
- `pyproject.toml` - Add ruff, pre-commit dependencies
- `app/main.py` - Register new routers
- `CLAUDE.md` - Add linting/DAO/migration guidance

### Swift Client (apps/macos-client/)

**New Files:**
- `.swiftlint.yml` - SwiftLint configuration
- `Sources/Views/TimerView.swift` - Main timer screen
- `Sources/Views/CircularProgressView.swift` - Timer ring
- `Sources/Views/TimerControlsView.swift` - Start/Pause/Reset
- `Sources/Views/GodAvatarView.swift` - God icon display
- `Sources/Views/SessionTypeIndicator.swift` - Focus/Break badge
- `Sources/Views/StatsWidgetView.swift` - Sessions count
- `Sources/Views/CLAUDE.md` - View conventions
- `Sources/Models/God.swift` - God data model
- `Sources/Models/TimerState.swift` - Timer state enum
- `Sources/Models/SessionType.swift` - Focus/Break enum
- `Sources/Models/CLAUDE.md` - Model conventions
- `Sources/Services/TimerService.swift` - Timer logic
- `Sources/Services/APIClient.swift` - API calls
- `Sources/Services/CLAUDE.md` - Service conventions

**Modified Files:**
- `Sources/ContentView.swift` - Replace with TimerView
- `CLAUDE.md` - Add patterns and conventions

### Workspace Root

**New Files:**
- `.githooks/pre-commit` - Workspace git hook

---

## Implementation Steps

### Phase 0: Research & Guardrails (python-coder + swift-coder in parallel)

**Python (python-coder):**
1. [ ] Research: FastAPI DAO patterns, Alembic best practices, Pydantic v2
2. [ ] Create `services/api/ruff.toml`
3. [ ] Update `services/api/pyproject.toml` with ruff, pre-commit
4. [ ] Create `services/api/.pre-commit-config.yaml`
5. [ ] Run `uv sync && uv run pre-commit install`
6. [ ] Create CLAUDE.md files for models/, daos/, routers/, schemas/
7. [ ] Update `services/api/CLAUDE.md` with new patterns

**Swift (swift-coder):**
8. [ ] Research: SwiftUI ObservableObject, SF Symbols, async/await patterns
9. [ ] Create `apps/macos-client/.swiftlint.yml`
10. [ ] Create CLAUDE.md files for Views/, Models/, Services/
11. [ ] Update `apps/macos-client/CLAUDE.md` with new patterns

**Workspace:**
12. [ ] Create `.githooks/pre-commit`
13. [ ] Run `chmod +x .githooks/pre-commit && git config core.hooksPath .githooks`

### Phase 1: Database Setup (python-coder)
14. [ ] Initialize Alembic: `uv run alembic init alembic`
15. [ ] Configure `alembic.ini` with database URL
16. [ ] Update `alembic/env.py` to import models
17. [ ] Create `alembic/CLAUDE.md` with migration conventions
18. [ ] Create SQLAlchemy models (God, Session, UserStats)
19. [ ] Generate migration: `uv run alembic revision --autogenerate -m "create_gods_sessions_stats"`
20. [ ] Run migration: `uv run alembic upgrade head`
21. [ ] Create seed migration for 5 gods

### Phase 2: Backend DAOs & Schemas (python-coder)
22. [ ] Create Pydantic schemas for God, Session, Stats
23. [ ] Implement BaseDAO with generic CRUD
24. [ ] Implement GodDAO (get_all, get_by_id)
25. [ ] Implement SessionDAO (create, complete, get_today_count)
26. [ ] Implement UserStatsDAO (get_or_create, update_on_session_complete)

### Phase 3: Backend API Endpoints (python-coder + tester)
27. [ ] Write tests for gods endpoints
28. [ ] Implement gods router (GET /gods, GET /gods/{id})
29. [ ] Write tests for sessions endpoints
30. [ ] Implement sessions router (POST, PATCH, GET /sessions/today)
31. [ ] Write tests for stats endpoint
32. [ ] Implement stats router (GET /stats)
33. [ ] Register routers in main.py
34. [ ] Run all tests: `uv run pytest`

### Phase 4: Swift Models & API Client (swift-coder)
35. [ ] Create God.swift model with CodingKeys
36. [ ] Create SessionType.swift enum
37. [ ] Create TimerState.swift enum
38. [ ] Implement APIClient.swift

### Phase 5: Swift Timer UI (swift-coder)
39. [ ] Implement TimerService.swift
40. [ ] Create CircularProgressView.swift
41. [ ] Create TimerControlsView.swift
42. [ ] Create GodAvatarView.swift
43. [ ] Create SessionTypeIndicator.swift
44. [ ] Create StatsWidgetView.swift
45. [ ] Compose TimerView.swift (with random god selection)
46. [ ] Update ContentView.swift to show TimerView

### Phase 6: Integration & Review (debugger + reviewer)
47. [ ] Connect timer completion to POST /sessions API
48. [ ] Fetch and display today's sessions count
49. [ ] Add error handling for API failures
50. [ ] Run code review across both repos
51. [ ] Test full flow end-to-end

---

## CLAUDE.md Content Outlines

### services/api/app/models/CLAUDE.md
```markdown
# SQLAlchemy Models

## Conventions
- All models inherit from `Base` (from app/db.py)
- Use `__tablename__` for explicit table names
- Primary keys: `id = Column(Integer, primary_key=True, index=True)`
- Timestamps: Use `TIMESTAMP WITH TIME ZONE` with `DEFAULT NOW()`
- JSON fields: Use `JSONB` type for PostgreSQL
- Foreign keys: Always add index on FK columns

## Patterns
- Keep models simple - business logic goes in DAOs
- Use relationships sparingly; prefer explicit joins in DAOs
```

### services/api/app/daos/CLAUDE.md
```markdown
# Data Access Objects (DAOs)

## Pattern
- Each model has a corresponding DAO class
- DAOs inherit from BaseDAO for common CRUD operations
- DAOs receive `db: Session` in constructor
- All database queries go through DAOs, not in routers

## BaseDAO Methods
- `get(id)` - Get by primary key
- `get_all()` - Get all records
- `create(obj_in: dict)` - Create new record
- `update(id, obj_in: dict)` - Update existing
- `delete(id)` - Delete record
```

### services/api/app/routers/CLAUDE.md
```markdown
# API Routers

## Conventions
- One router file per resource (gods.py, sessions.py, etc.)
- Use `APIRouter(prefix="/resource", tags=["resource"])`
- Register in main.py: `app.include_router(router)`
- Always specify `response_model` on endpoints
- Use dependency injection for DAOs: `Depends(get_db)`

## HTTP Methods
- GET for retrieval (list, detail)
- POST for creation
- PATCH for partial updates
- DELETE for removal
```

### apps/macos-client/Sources/Services/CLAUDE.md
```markdown
# Swift Services

## APIClient
- Use `actor` for thread-safe singleton
- All methods are `async throws`
- Base URL: `http://localhost:8000`
- Use `URLSession.shared` for requests
- Encode/decode with `JSONEncoder`/`JSONDecoder`

## TimerService
- Use `@MainActor class` with `ObservableObject`
- Published properties: state, timeRemaining, progress, sessionType
- Timer uses `Timer.scheduledTimer` with 1-second interval
- Always update UI on main thread
```

---

## Tests

### API Tests
- [ ] `test_get_all_gods` - Returns 5 gods with all fields
- [ ] `test_get_god_by_id` - Returns specific god
- [ ] `test_get_god_not_found` - Returns 404 for invalid ID
- [ ] `test_create_session` - Creates session with valid god_id
- [ ] `test_create_session_invalid_god` - Returns 400 for invalid god
- [ ] `test_complete_session` - Marks session as completed
- [ ] `test_get_today_sessions` - Returns correct count for today
- [ ] `test_get_stats` - Returns aggregated statistics

### Manual Verification
- [ ] Run `uv run pre-commit run --all-files` - all checks pass
- [ ] Run `swift build` - compiles without warnings
- [ ] Run `swift run MakeHomerProudTimerClient` - timer UI appears
- [ ] Start timer, verify countdown works
- [ ] Pause and resume timer
- [ ] Reset timer to initial state
- [ ] Complete a session, verify API call succeeds
- [ ] Check stats widget updates after session completion

---

## Seed Data: The 5 Gods

| God | Domain | Icon (SF Symbol) | Style |
|-----|--------|------------------|-------|
| Athena | Wisdom & Strategy | `brain.head.profile` | Wise, measured |
| Apollo | Arts & Light | `sun.max.fill` | Artistic, inspiring |
| Ares | War & Strength | `flame.fill` | Aggressive, warrior |
| Hephaestus | Forge & Craft | `hammer.fill` | Patient craftsman |
| Dionysus | Wine & Celebration | `party.popper.fill` | Playful, celebratory |
