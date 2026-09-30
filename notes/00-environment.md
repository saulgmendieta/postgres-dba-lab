# 00 - Environment setup log and gotchas

## Final setup

| Instance | Where | Port |
|---|---|---|
| app | Docker Desktop container `postgres:18` (WSL2 integration) | 5433 |
| lab | Native PostgreSQL 18 in Ubuntu on WSL2 | 5434 |

pgAdmin 4 runs on Windows and registers both servers through `localhost`. The `psql` client runs in WSL.

## Things that bit me, and the fix

1. **Two servers, easy to confuse.** Always confirm with `SELECT version();` (Windows build mentions Visual C++, the Docker image Debian, the native WSL install Ubuntu).
2. **psql shows a `-#` prompt and prints nothing.** The statement is not finished: it needs `;` (or `\g`). pgAdmin's query tool does not need it.
3. **`relation "x" does not exist`.** Usually the wrong database. Check with `\l`, switch with `\c dbname`, look at other schemas with `\dt *.*`.
4. **Restoring a dump from Windows into Linux fails** with `invalid LC_COLLATE locale name: "English_United States.1252"`. Windows locale names do not exist on Linux. Create the database first, then restore without `-C`:

   ```bash
   createdb -h localhost -p 5433 -U postgres -T template0 -E UTF8 --locale=en_US.utf8 mydb
   pg_restore -h localhost -p 5433 -U postgres -d mydb --no-owner mydb.dump
   ```

5. **Two Docker engines.** Docker Engine installed inside Ubuntu and Docker Desktop are separate; containers of one are invisible to the other. Use Docker Desktop with WSL integration enabled for the distro. `permission denied ... /var/run/docker.sock` means the user is not in the `docker` group yet (or the session was not restarted after adding it).
6. **`\password` takes a role name, not a password.** Typing a password as an argument leaks it into the process list and shell history. Run `\password postgres` and type the password at the prompt.
7. **Data directory in the `postgres:18` image** is `/var/lib/postgresql` (versioned subdirectory), not `/var/lib/postgresql/data`. Mount the volume there.
8. **Hyphens in database names** (`test-db`) force quoting everywhere. Prefer underscores.
9. **WSL to a server on Windows** works with mirrored networking (`networkingMode=mirrored` under `[wsl2]` in `.wslconfig`), but a server listening inside WSL on the same port wins over the Windows one.
