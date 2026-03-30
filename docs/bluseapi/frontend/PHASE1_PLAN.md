# BluseAPI Phase 1 — Integrated Frontend Architecture Plan

## Goal
Move from an externally-downloaded `management.html` control panel to a first-party integrated BluseAPI web application served by the same backend.

## Current state
- Root route `/` returns a JSON placeholder.
- `/management.html` serves a downloaded static file.
- `internal/managementasset/updater.go` fetches the panel from an external GitHub release.
- Management APIs already exist under `/v0/management/*`.

## Target state
- Root route `/` serves BluseAPI frontend app.
- `/management.html` becomes a compatibility alias to the same app entrypoint.
- Frontend source lives in this repository under `frontend/management-center`.
- Backend web UI serving logic lives under `internal/webui`.
- External panel downloader remains temporarily as a legacy mode, then gets retired.

## Recommended rollout
### Phase 1A — Repository convergence
- Add first-party frontend directory to the repo.
- Add backend webui package placeholder.
- Keep current runtime behavior unchanged.

### Phase 1B — Dual-mode backend support
- Introduce UI mode config (`legacy` vs `bundled`).
- In `legacy`, keep serving downloaded `management.html`.
- In `bundled`, serve frontend app shell from local assets.
- Keep `/v0/management/*` API contract stable.

### Phase 1C — Routing transition
- `/` -> BluseAPI app shell.
- `/management.html` -> redirect or alias to `/`.
- Preserve old behavior as fallback until bundled mode is stable.

### Phase 1D — Release integration
- Build frontend during release pipeline.
- Package frontend assets with backend release artifact / Docker image.
- Stop depending on `panel-github-repository` for primary UI.

## Files/modules likely involved next
- `internal/api/server.go` — root route, management entrypoint, static serving.
- `internal/managementasset/updater.go` — legacy asset downloader to deprecate.
- `internal/config/config.go` — add `ui.mode` or equivalent config.
- `cmd/server/main.go` — initialize bundled UI mode / static asset flow.
- `frontend/management-center/*` — new first-party frontend source.
- `internal/webui/*` — embedded/static asset serving abstraction.

## Compatibility strategy
- Keep `/v0/management/*` unchanged.
- Keep `/management.html` working during migration.
- Only switch `/` after bundled UI has parity for login and core management flows.

## Rollback strategy
- Config switch back to `legacy` mode.
- Restore `/management.html` external asset path.
- Keep old asset updater until bundled UI proves stable.

## Branding strategy
- UI-visible strings and titles become `BluseAPI` first.
- Runtime/container/service names already partially migrated.
- Deep internal rename (module path/import path/binary internals) stays for a later phase.
