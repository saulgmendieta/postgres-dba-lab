# postgres-dba-lab

Hands-on PostgreSQL **administration** lab on PostgreSQL 18. The goal is to go below the SQL surface: server internals, configuration, roles and privileges, backup and point-in-time recovery, maintenance and upgrades. Everything is kept as reproducible scripts, runbooks and short notes.

Learning path: the Udemy course *PostgreSQL Database Administration on Windows/Linux – Part 1* (Sadiq Basha), adapted to PostgreSQL 18, plus a mini-project database (`workout_lab`) to administer. Notes are written in my own words, recall-first: predict, watch, recall from memory, then verify on a real server.

## Environment

Two separate instances, never mixed:

| Instance | Where | Port | Purpose |
|---|---|---|---|
| **app** | Docker container `postgres:18` (Docker Desktop, WSL2 integration) | 5433 | Real data and apps. Kept clean. |
| **lab** | Native PostgreSQL 18 in Ubuntu on WSL2 (PGDG apt packages) | 5434 | Course exercises: config files, archiving, recovery, upgrades. Safe to break. |

Managed with `psql` in WSL and pgAdmin 4 on Windows, both through `localhost`.

## Quick start

**app instance (Docker)**

```bash
cd docker
cp .env.example .env        # then set a real password in .env
docker compose up -d
psql -h localhost -p 5433 -U postgres
```

**lab instance (Ubuntu / WSL)**

```bash
sudo apt install -y postgresql-common
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
sudo apt install -y postgresql-18
sudo sed -i 's/^port = .*/port = 5434/' /etc/postgresql/18/main/postgresql.conf
sudo systemctl restart postgresql
echo 'export PATH=/usr/lib/postgresql/18/bin:$PATH' >> ~/.bashrc
```

Set the `postgres` password without putting it on the command line (psql prompts for it):

```bash
sudo -u postgres psql -p 5434 -c '\password postgres'
```

## Repository layout

```
docker/      docker-compose.yml and .env.example for the app instance
sql/         schema, roles, data generators, partitioning (workout_lab)
scripts/     backup, archiving and maintenance automation
runbooks/    step-by-step procedures, each tested on the lab (PITR, upgrade, ...)
notes/       one recall-first note per course section, plus an environment log
```

## Progress

- [ ] Architecture and processes
- [ ] Storage internals
- [ ] Cluster, directory layout
- [ ] Configuration files (`postgresql.conf`, `pg_hba.conf`, `pg_ident.conf`)
- [ ] Roles, schemas and privileges
- [ ] psql, `.pgpass`, `.psqlrc`, system catalog
- [ ] `workout_lab` database and declarative partitioning
- [ ] Backup, WAL archiving and point-in-time recovery
- [ ] Maintenance: vacuum, statistics, query plans, reindexing
- [ ] Upgrade with `pg_upgrade`

See [`notes/`](notes/) for the details of each item and [`runbooks/`](runbooks/) for tested procedures.

## Safety

- `.env`, dumps, backups and WAL archives are git-ignored. Never commit credentials or database contents.
- Passwords are never passed as command arguments (they end up in the process list and shell history).

## License

MIT. See [LICENSE](LICENSE).
