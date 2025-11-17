# dbt + Snowflake — Data Warehouse Project

This repository contains a dbt project and supporting files to build a data warehouse in Snowflake. The `Data-Build-Tool/` folder contains the dbt project (models, seeds, snapshots, macros, and configuration). Use this project to develop, test, and deploy transformations for your Snowflake data warehouse.

**Quick links**
- dbt project: `Data-Build-Tool/dbt_project.yml`
- Example models: `Data-Build-Tool/models/example/`
- Compiled artifacts: `Data-Build-Tool/target/compiled/`
- Logs: `logs/dbt.log`

**Project Overview**

This project shows a typical dbt workflow for building a Snowflake-based data warehouse: ingest seeds, run transformations, validate tests, and produce documentation. It's organized to separate models, seeds, snapshots, and macros so you can extend and maintain transformations easily.

**Repository Structure (important paths)**

- `Data-Build-Tool/` — main dbt project directory
  - `models/` — dbt models (SQL files)
  - `seeds/` — CSVs loaded by `dbt seed`
  - `snapshots/` — snapshot definitions
  - `macros/` — reusable jinja macros
  - `dbt_project.yml` — dbt project config
- `logs/` — run logs (contains `dbt.log`)
- `target/` — compiled and run artifacts produced by dbt (also under `snowflake/target`)

Getting Started
---------------

Prerequisites

- Python 3.8+ (or your system Python)
- `virtualenv` or Python venv for isolation
- dbt (dbt-core + `dbt-snowflake` adapter)
- A Snowflake account with credentials (user, role, warehouse, database, schema)

Local setup (recommended)

1. Create and activate a virtual environment

```bash
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
```

2. Install dbt (adapter for Snowflake)

```bash
# Install the dbt Snowflake package (choose a version compatible with your environment)
pip install "dbt-snowflake"
```

If this project includes a `requirements.txt` or `pyproject.toml`, prefer installing from those instead.

3. Configure dbt profiles

dbt looks for `profiles.yml` in the `~/.dbt/` directory by default. Create or update `~/.dbt/profiles.yml` with a Snowflake target similar to the example below (replace placeholders with your values):

```yaml
my_dbt_profile:
  target: dev
  outputs:
    dev:
      type: snowflake
      account: "<account>"
      user: "<user>"
      password: "<password>"
      role: "<role>"
      database: "<database>"
      warehouse: "<warehouse>"
      schema: "<schema>"
      threads: 4
      client_session_keep_alive: False
```

Set the profile name in `Data-Build-Tool/dbt_project.yml` under `profile:` if it differs from the example above.

Common commands
---------------

Run these from the repository root (or `cd Data-Build-Tool` to run them with default project directory):

```bash
# Validate configuration & connectivity
cd Data-Build-Tool
dbt debug

# Install dependencies (if any)
dbt deps

# Load seeds
dbt seed

# Run models
dbt run

# Run tests
dbt test

# Run snapshots
dbt snapshot

# Build everything (models + tests + docs)
dbt build

# Generate and serve documentation
dbt docs generate
dbt docs serve
```

Notes about compiled artifacts and logs
-------------------------------------

- dbt outputs compiled SQL and run artifacts to `Data-Build-Tool/target/` (compiled SQL is useful for debugging).
- Logs are written to `logs/dbt.log` by default in this repository — check this file for detailed run output.

Development workflow
--------------------

- Branch from `main` for feature work.
- Run `dbt run` and `dbt test` locally before opening a pull request.
- Keep models modular and add `schema.yml` tests for assertions (not-null, unique, relationships).

CI / Deployment (suggested)
---------------------------

- Use environment variables or secrets in CI to provide Snowflake credentials — do not commit secrets.
- In CI, run `dbt deps`, `dbt seed` (if seeds needed), `dbt run --profiles-dir .` (or set `DBT_PROFILES_DIR`), and `dbt test`.
- Optionally publish docs to a static site or serve docs via an environment that runs `dbt docs generate`.

Troubleshooting
---------------

- If `dbt debug` fails, re-check `~/.dbt/profiles.yml` and network access to Snowflake.
- For permission/role errors, ensure the Snowflake role has rights to the target database/schema and warehouse.
- Look at `logs/dbt.log` for stack traces and detailed error messages.

Contributing
------------

Contributions are welcome. Typical workflow:

1. Fork and create a branch
2. Run and verify changes locally (`dbt run`, `dbt test`)
3. Open a pull request with a brief description of changes

License
-------

This repository does not include a license file. Add a `LICENSE` if you want to make licensing explicit.

Contact
-------

If you need help or want additional templates (CI examples, profiles, or more models), open an issue or ask in a PR.
