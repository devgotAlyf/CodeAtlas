# CodeAtlas — preview run doc

## Reproduce uncommitted artifacts (fresh checkout)

1. Copy the local environment file from the main checkout (never symlink — ports may need adapting per worktree):
   - `cp .env.example .env`, then fill in `DATABASE_URL`, `AUTH_SECRET` (use `openssl rand -base64 32`), `GITHUB_CLIENT_ID`/`GITHUB_CLIENT_SECRET`.
   - If the main checkout has a working local `.env`, copy it: `cp ../CodeAtlas/.env .env`.
   - `.env` is gitignored; it is never committed.
2. Install dependencies with the project's package manager (pnpm):
   - `pnpm install`
   - Post-install hooks generate the Prisma client via `pnpm.onlyBuiltDependencies`.
3. Create the PostgreSQL databases if absent and apply migrations:
   - `psql -U postgres -h localhost -c "CREATE DATABASE codeatlas;"` (and `codeatlas_test` for tests)
   - `pnpm db:migrate`

## Run the dev server

The dev server is `next dev`. IMPORTANT: the shell exports `PORT=0`, so always pin the port explicitly with `-p 3000` — otherwise Next binds a random port and the preview URL breaks.

Detached start (Windows, PowerShell; stdout and stderr MUST go to different files):

```powershell
powershell -NoProfile -Command "(Start-Process -FilePath 'npm.cmd' -ArgumentList 'run','dev','--','-p','3000' -WorkingDirectory 'C:\Users\sriva\Downloads\CodeAtlas' -RedirectStandardOutput 'C:\Users\sriva\Downloads\CodeAtlas\.freebuff\preview-92270ff0-cd0b-411d-8e1c-58ba233999b9.log' -RedirectStandardError 'C:\Users\sriva\Downloads\CodeAtlas\.freebuff\preview-92270ff0-cd0b-411d-8e1c-58ba233999b9.log.err' -WindowStyle Hidden -PassThru).Id"
```

Confirm it survived: `powershell -NoProfile -Command "Get-Process -Id <pid>"`.

Health check: `curl -s http://localhost:3000/api/health` should return `{"status":"ok",...}`.

URL: http://localhost:3000
