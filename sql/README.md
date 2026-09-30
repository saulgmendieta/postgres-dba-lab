# sql/

SQL for the `workout_lab` mini-project database (a PostgreSQL version of a workout log domain):

- schema: `users`, `exercises`, `sessions`, `sets`
- roles: `workout_owner` (owns the schema), `workout_app` (DML only), `workout_ro` (read-only)
- data generator (`generate_series`) to reach millions of rows
- declarative partitioning of the large tables

Numbered files run in order (`01_schema.sql`, `02_roles.sql`, ...). Never hard-code passwords: pass them with psql variables (`psql -v app_pw=...`) or set them interactively with `\password`.
