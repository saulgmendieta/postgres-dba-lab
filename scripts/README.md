# scripts/

Automation kept small and readable:

- backups (`pg_dump`, `pg_basebackup`) and their scheduling
- WAL archiving helpers
- maintenance (`ANALYZE`, reindexing, log rotation)

Every script starts with a comment explaining what it does, which instance it targets (app 5433 / lab 5434) and what it needs. Credentials come from `~/.pgpass` or environment variables, never from the script.
