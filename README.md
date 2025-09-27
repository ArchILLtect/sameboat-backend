![Backend CI](https://github.com/ArchILLtect/sameboat-backend/actions/workflows/backend-ci.yml/badge.svg)
# SameBoat Backend (Spring Boot + Java 21)

> Quick Links: [Instructions (setup & migrations)](./docs/instructions.md) | [API Reference](./docs/api.md)

## Run locally
1. Copy `.env.example` → `.env` and fill appropriate JDBC DS values.
2. Or set `SPRING_DATASOURCE_URL`, `SPRING_DATASOURCE_USERNAME`, `SPRING_DATASOURCE_PASSWORD` directly.
3. Start:
   ```bash
   DB_URL=... ./mvnw spring-boot:run
    ```
4. Health check: GET /health → ok

## Migrations
Flyway runs automatically on startup from src/main/resources/db/migration.

### Migration Immutability Policy
Once a migration version (e.g. `V1__*.sql`, `V2__*.sql`) has been merged to `main` and applied to any environment, **do not edit the file**. If schema changes are required, create a new migration with the next version (e.g. `V3__add_new_column.sql`). This preserves checksum integrity and avoids drift. If a previous file was accidentally changed and Flyway reports a checksum mismatch, prefer creating a corrective follow-up migration over editing history; use `flyway:repair` only after confirming the live schema truly matches the intended SQL.

### Legacy Alignment
`V3__users_additional_columns.sql` exists to reconcile differences between the original `V1` schema and the expanded fields/constraints added conceptually in `V2` without rewriting history. New adjustments must continue with `V4+`.

### Immutability Tooling
Scripts (cross‑platform) enforce the "no edits to applied migrations" rule:

| Script | Platform | Usage |
|--------|----------|-------|
| `scripts/check-migration-immutability.sh` | CI / Bash | `scripts/check-migration-immutability.sh origin/main` |
| `scripts/check-migration-immutability.ps1` | PowerShell | `pwsh scripts/check-migration-immutability.ps1 -BaseRef origin/main` |
| `scripts/check-migration-immutability.cmd` | Windows (cmd shim) | `scripts\check-migration-immutability.cmd` |

Pre-commit hook (optional):
1. Run: `git config core.hooksPath .githooks`
2. The provided `.githooks/pre-commit` executes the check; commit aborts if an existing migration is modified.

CI: The GitHub Actions workflow runs the Bash script before tests; it fails the build if a historical migration was altered.

---

## 📅 Project Schedule
See the [Semester Project Plan](./schedule/SemesterPlan.md) for calendar files, screenshots, and live links.