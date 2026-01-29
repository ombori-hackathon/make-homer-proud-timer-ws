# Hackathon Workspace

Multi-repo workspace for Ombori hackathon.

## Key Rules

1. **Plan-mode-first**: ALL features start with spec creation in `specs/` folder
2. **TDD where applicable**: Write tests before implementation
3. **Specs in workspace**: All specs centralized in `specs/` folder
4. **Evolve the config**: Update CLAUDE.md and agents with learnings as you build

## Continuous Improvement

As you build, update these files with learnings:
- `CLAUDE.md` - Add new patterns, gotchas, project-specific conventions
- `.claude/agents/*.md` - Refine agent instructions based on what works
- `apps/macos-client/CLAUDE.md` - Swift-specific learnings
- `services/api/CLAUDE.md` - Python/FastAPI-specific learnings

Examples of things to capture:
- "Always use X pattern for Y"
- "Don't forget to run Z after changing W"
- "API endpoint naming follows this convention..."
- "Database migrations require this step..."

## Quick Start

```bash
# Terminal 1: Start database
docker compose up -d

# Terminal 2: Start API
cd services/api && uv run uvicorn app.main:app --port 8000 --reload

# Terminal 3: Start Swift client
cd apps/macos-client && swift run MakeHomerProudTimerClient
```

## Structure
- `apps/macos-client/` - SwiftUI desktop app (submodule)
- `services/api/` - FastAPI Python backend (submodule)
- `specs/` - Feature specifications (plan-mode output)
- `docker-compose.yml` - PostgreSQL database

## Skills (Commands)
Available in `.claude/skills/`:
- `/feature` - **Start here!** Asks questions → creates spec → TDD implementation

## Agents
Available in `.claude/agents/`:
- `/architect` - System design, API contracts → outputs to `specs/`
- `/swift-coder` - Swift client development
- `/python-coder` - FastAPI backend development
- `/reviewer` - Code review across all repos
- `/debugger` - Issue investigation
- `/tester` - Test-driven development

## Development Workflow

### New Features (MANDATORY)
1. **Plan mode first** - Create spec in `specs/YYYY-MM-DD-feature-name.md`
2. **Write tests** - TDD: tests before implementation
3. **Implement** - Use coder agents (can run in parallel)
4. **Review** - Use reviewer agent
5. **Commit** - Submodules first, then workspace

### Git Workflow (use `gh` CLI, not GitHub web)
- **Target branch**: Always create PRs against `develop`, not `main`
- **Feature branches**: Create a new branch for each feature (e.g., `feature/add-settings-screen`)
- **Branch naming**: Use `feature/<name>`, `fix/<name>`, or `chore/<name>` prefixes

```bash
# In each submodule
git checkout develop
git pull origin develop
git checkout -b feature/<name>
git add . && git commit -m "feat: ..."
git push -u origin feature/<name>
gh pr create --base develop --title "feat: ..." --body "Description"

# After PRs merged to develop, update workspace
cd ../..
git add . && git commit -m "Update submodules"
git push
```

## API Reference
- Swagger: http://localhost:8000/docs
- Health: http://localhost:8000/health
- Gods: http://localhost:8000/gods (list all Greek gods)
- Sessions: http://localhost:8000/sessions (timer sessions)
- Stats: http://localhost:8000/stats (user statistics)

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
