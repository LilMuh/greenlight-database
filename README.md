# greenlight-database

Schema and local database infrastructure for **GreenLight**
([greenlight-frontend](https://github.com/LilMuh/greenlight-frontend) is the
project front page).

This repository is the **single source of truth for the schema**. The backend
does not run migrations — it connects to a database this repo has already
migrated (`spring.liquibase.enabled=false`, `ddl-auto=validate`).

> The three tables are **placeholders for now** — only `id` + `created_at`. Real
> columns get added later via new changesets, once the design is settled.

## What's here

```
greenlight-database/
├── docker-compose.yml        # postgres + pgadmin + one-shot liquibase
├── .env.example              # copy to .env to override credentials
├── liquibase.properties      # for running the Liquibase CLI directly (optional)
└── changelog/
    ├── db.changelog-master.yaml
    └── changes/
        ├── 001-create-watch-config.yaml
        ├── 002-create-tee-time.yaml
        └── 003-create-notification-ledger.yaml
```

## Run it

```bash
docker compose up -d
```

That starts Postgres, applies the Liquibase changelog (the one-shot `liquibase`
container runs `update` and exits), and starts pgAdmin.

- Postgres: `localhost:5432` (db/user/pass default to `greenlight`)
- pgAdmin:  http://localhost:5050 (login from `.env`, defaults `admin@greenlight.local` / `admin`)

Check the migration ran:

```bash
docker compose logs liquibase
docker compose exec postgres psql -U greenlight -d greenlight -c "\dt"
```

Reset everything (drops the data volume):

```bash
docker compose down -v
```

## Adding a migration

Add a `changes/NNN-*.yaml` changeset and `include` it from
`db.changelog-master.yaml`. Changesets are append-only — never edit one that has
already been applied; add a new one instead.
