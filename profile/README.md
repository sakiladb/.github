# Sakila DB

Docker images preloaded with the [Sakila](https://dev.mysql.com/doc/sakila/en/) sample database —
one per database engine.

Every image carries the same dataset — a uniform **16 tables + 7 views**, identical across engines —
derived from the canonical MySQL Sakila (via [jOOQ](https://www.jooq.org/sakila)) and adapted to each
engine. They exist primarily as test fixtures for [`sq`](https://github.com/neilotoole/sq) — but
they're free for anyone to use.

## Images

Every engine is published to **both** Docker Hub and the GitHub Container Registry (GHCR) as
identical, cosign-signed images. Pull from whichever you prefer.

| Engine | Docker Hub | GHCR | Versions | Architectures |
|--------|------------|------|----------|---------------|
| [PostgreSQL](https://github.com/sakiladb/postgres) | [`sakiladb/postgres`](https://hub.docker.com/r/sakiladb/postgres) | [`ghcr.io/sakiladb/postgres`](https://github.com/orgs/sakiladb/packages/container/package/postgres) | `9`–`17`, **`18`** | `amd64`, `arm64` |
| [MySQL](https://github.com/sakiladb/mysql) | [`sakiladb/mysql`](https://hub.docker.com/r/sakiladb/mysql) | [`ghcr.io/sakiladb/mysql`](https://github.com/orgs/sakiladb/packages/container/package/mysql) | `5.6`, `5.7`, `8`, **`9`** | `amd64` (all), `arm64` (`8`/`9`) |
| [SQL Server](https://github.com/sakiladb/sqlserver) | [`sakiladb/sqlserver`](https://hub.docker.com/r/sakiladb/sqlserver) | [`ghcr.io/sakiladb/sqlserver`](https://github.com/orgs/sakiladb/packages/container/package/sqlserver) | `2019`, **`2022`** | `amd64` |
| [Oracle](https://github.com/sakiladb/oracle) | [`sakiladb/oracle`](https://hub.docker.com/r/sakiladb/oracle) | [`ghcr.io/sakiladb/oracle`](https://github.com/orgs/sakiladb/packages/container/package/oracle) | **`23`** | `amd64`, `arm64` |
| [ClickHouse](https://github.com/sakiladb/clickhouse) | [`sakiladb/clickhouse`](https://hub.docker.com/r/sakiladb/clickhouse) | [`ghcr.io/sakiladb/clickhouse`](https://github.com/orgs/sakiladb/packages/container/package/clickhouse) | **`25`** | `amd64`, `arm64` |
| [rqlite](https://github.com/sakiladb/rqlite) | [`sakiladb/rqlite`](https://hub.docker.com/r/sakiladb/rqlite) | [`ghcr.io/sakiladb/rqlite`](https://github.com/orgs/sakiladb/packages/container/package/rqlite) | **`10`** | `amd64`, `arm64` |

The **bold** version is what `:latest` points at; every image is also tagged by its major version
(e.g. `sakiladb/postgres:16`). The `amd64`/`arm64` arch values above are short for `linux/amd64` and
`linux/arm64`.

## Quick start

For example, PostgreSQL:

```shell
docker run -p 5432:5432 -d sakiladb/postgres
```

The same image is on GHCR, if you prefer: `docker pull ghcr.io/sakiladb/postgres`.

Most images default to database / user / password `sakila` / `sakila` / `p_ssW0rd`. Image tags,
connection details, and engine-specific notes live in each repo's README.

## File-based fixtures

The same Sakila dataset also ships as **file-based fixtures** — for the formats that aren't a database
server — embedded in the [`sq`](https://github.com/neilotoole/sq) repo. They carry the same data,
byte-faithful to the canonical MySQL Sakila.

| Format | Location (in the `sq` repo) | Contents |
|--------|-----------------------------|----------|
| SQLite | [`drivers/sqlite3/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/sqlite3/testdata) | `sakila.db` — the canonical source the other file fixtures derive from (plus `_diff` / `_whitespace` / `_fts5` variants) |
| DuckDB | [`drivers/duckdb/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/duckdb/testdata) | `sakila.duckdb` (plus the same variants) |
| CSV / TSV | [`drivers/csv/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/csv/testdata) | one file per table, with and without a header row (`sakila-csv`, `sakila-csv-noheader`, `sakila-tsv`, `sakila-tsv-noheader`) |
| Excel | [`drivers/xlsx/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/xlsx/testdata) | `sakila.xlsx` — one sheet per table (plus subset and no-header workbooks) |
| JSON | [`drivers/json/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/json/testdata) | `actor` / `film_actor` / `payment` as `.json`, `.jsona`, and `.jsonl` |

## Built for sq

[`sq`](https://github.com/neilotoole/sq) is a command-line tool for querying SQL databases and
structured data. These images give it a Sakila fixture on every supported engine, so the same
queries can be exercised across databases. See the per-driver guides at
[sq.io/docs/drivers](https://sq.io/docs/drivers/).
