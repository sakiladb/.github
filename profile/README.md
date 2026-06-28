# Sakila DB

Docker images preloaded with the [Sakila](https://dev.mysql.com/doc/sakila/en/) sample database,
one per database engine.

> [!NOTE]
> **Why?** The various sakiladb images exist as test fixtures.
> Mainly for [`sq`](https://github.com/neilotoole/sq).
> You can talk about it over here in the [discussions](https://github.com/orgs/sakiladb/discussions/1).

Every image carries the same dataset: a uniform **16 tables + 7 views**, identical across engines,
derived from the canonical MySQL Sakila (via [jOOQ](https://www.jooq.org/sakila)) and adapted to each
engine. They exist primarily as test fixtures for [`sq`](https://github.com/neilotoole/sq), but
they're free for anyone to use.

[`sq inspect`](https://sq.io/docs/inspect) shows the whole schema at a glance — tables, views, row
counts, and columns. It's the same on every engine; here it is on the canonical
[sakiladb/mysql](https://github.com/sakiladb/mysql) image:

```shell
$ sq inspect @sakila_my
SOURCE      DRIVER  NAME    FQ NAME     SIZE   TABLES  VIEWS  LOCATION
@sakila_my  mysql   sakila  def.sakila  1.8MB  16      7      mysql://sakila:xxxxx@localhost:3306/sakila

NAME                        TYPE   ROWS   COLS
actor                       table  200    actor_id, first_name, last_name, last_update
address                     table  603    address_id, address, address2, district, city_id, postal_code, phone, last_update
category                    table  16     category_id, name, last_update
city                        table  600    city_id, city, country_id, last_update
country                     table  109    country_id, country, last_update
customer                    table  599    customer_id, store_id, first_name, last_name, email, address_id, active, create_date, last_update
film                        table  1000   film_id, title, description, release_year, language_id, original_language_id, rental_duration, rental_rate, length, replacement_cost, rating, special_features, last_update
film_actor                  table  5462   actor_id, film_id, last_update
film_category               table  1000   film_id, category_id, last_update
film_text                   table  1000   film_id, title, description
inventory                   table  4581   inventory_id, film_id, store_id, last_update
language                    table  6      language_id, name, last_update
payment                     table  16049  payment_id, customer_id, staff_id, rental_id, amount, payment_date, last_update
rental                      table  16044  rental_id, rental_date, inventory_id, customer_id, return_date, staff_id, last_update
staff                       table  2      staff_id, first_name, last_name, address_id, picture, email, store_id, active, username, password, last_update
store                       table  2      store_id, manager_staff_id, address_id, last_update
actor_info                  view   200    actor_id, first_name, last_name, film_info
customer_list               view   599    ID, name, address, zip code, phone, city, country, notes, SID
film_list                   view   997    FID, title, description, category, price, length, rating, actors
nicer_but_slower_film_list  view   997    FID, title, description, category, price, length, rating, actors
sales_by_film_category      view   16     category, total_sales
sales_by_store              view   2      store, manager, total_sales
staff_list                  view   2      ID, name, address, zip code, phone, city, country, SID
```

## Images

Every engine is published to **both** Docker Hub and the GitHub Container Registry (GHCR) as
identical, cosign-signed images. Pull from whichever you prefer.

| Engine                                               | Docker Hub                                                            | GHCR                                                                                                    | Versions                   | Architectures                    |
| ---------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- | -------------------------- | -------------------------------- |
| [PostgreSQL](https://github.com/sakiladb/postgres)   | [`sakiladb/postgres`](https://hub.docker.com/r/sakiladb/postgres)     | [`ghcr.io/sakiladb/postgres`](https://github.com/orgs/sakiladb/packages/container/package/postgres)     | `9` to `17`, **`18`**      | `amd64`, `arm64`                 |
| [MySQL](https://github.com/sakiladb/mysql)           | [`sakiladb/mysql`](https://hub.docker.com/r/sakiladb/mysql)           | [`ghcr.io/sakiladb/mysql`](https://github.com/orgs/sakiladb/packages/container/package/mysql)           | `5.6`, `5.7`, `8`, **`9`** | `amd64` (all), `arm64` (`8`/`9`) |
| [SQL Server](https://github.com/sakiladb/sqlserver)  | [`sakiladb/sqlserver`](https://hub.docker.com/r/sakiladb/sqlserver)   | [`ghcr.io/sakiladb/sqlserver`](https://github.com/orgs/sakiladb/packages/container/package/sqlserver)   | `2019`, **`2022`**         | `amd64`                          |
| [Oracle](https://github.com/sakiladb/oracle)         | [`sakiladb/oracle`](https://hub.docker.com/r/sakiladb/oracle)         | [`ghcr.io/sakiladb/oracle`](https://github.com/orgs/sakiladb/packages/container/package/oracle)         | **`23`**                   | `amd64`, `arm64`                 |
| [ClickHouse](https://github.com/sakiladb/clickhouse) | [`sakiladb/clickhouse`](https://hub.docker.com/r/sakiladb/clickhouse) | [`ghcr.io/sakiladb/clickhouse`](https://github.com/orgs/sakiladb/packages/container/package/clickhouse) | **`25`**                   | `amd64`, `arm64`                 |
| [rqlite](https://github.com/sakiladb/rqlite)         | [`sakiladb/rqlite`](https://hub.docker.com/r/sakiladb/rqlite)         | [`ghcr.io/sakiladb/rqlite`](https://github.com/orgs/sakiladb/packages/container/package/rqlite)         | **`10`**                   | `amd64`, `arm64`                 |

The **bold** version is what `:latest` points at; every image is also tagged by its major version
(e.g. `sakiladb/postgres:16`). The `amd64`/`arm64` arch values above are short for `linux/amd64` and
`linux/arm64`.

Every image declares a Docker **[`HEALTHCHECK`](https://docs.docker.com/reference/dockerfile/#healthcheck)** using its engine's native readiness probe, so the
container reports `healthy` once it's ready to serve. Orchestrators can wait on that (Compose
[`service_healthy`](https://docs.docker.com/reference/compose-file/services/#depends_on), GitHub Actions `services:`) instead of a fixed `sleep`.

## Quick start

For example, PostgreSQL:

```shell
docker run -p 5432:5432 -d sakiladb/postgres
```

The same image is on GHCR, if you prefer: `docker pull ghcr.io/sakiladb/postgres`.

> [!TIP]
> **Building or testing on GitHub Actions? Pull from GHCR** (`ghcr.io/sakiladb/…`). Docker Hub
> rate-limits pulls and CI runners share IP addresses, so the limit is reached quickly; GHCR isn't
> throttled the same way, especially from within GitHub's network.

Most images default to database / user / password `sakila` / `sakila` / `p_ssW0rd`. Image tags,
connection details, and engine-specific notes live in each repo's README.

## File-based fixtures

The same Sakila dataset also ships as **file-based fixtures** (for the formats that aren't a database
server), embedded in the [`sq`](https://github.com/neilotoole/sq) repo. They carry the same data,
byte-faithful to the canonical MySQL Sakila.

| Format    | Location (in the `sq` repo)                                                                         | Contents                                                                                                                     |
| --------- | --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| SQLite    | [`drivers/sqlite3/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/sqlite3/testdata) | `sakila.db`, the canonical source the other file fixtures derive from (plus `_diff` / `_whitespace` / `_fts5` variants)      |
| DuckDB    | [`drivers/duckdb/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/duckdb/testdata)   | `sakila.duckdb` (plus the same variants)                                                                                     |
| CSV / TSV | [`drivers/csv/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/csv/testdata)         | one file per table, with and without a header row (`sakila-csv`, `sakila-csv-noheader`, `sakila-tsv`, `sakila-tsv-noheader`) |
| Excel     | [`drivers/xlsx/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/xlsx/testdata)       | `sakila.xlsx`, one sheet per table (plus subset and no-header workbooks)                                                     |
| JSON      | [`drivers/json/testdata`](https://github.com/neilotoole/sq/tree/master/drivers/json/testdata)       | `actor` / `film_actor` / `payment` as `.json`, `.jsona`, and `.jsonl`                                                        |

## Built for sq

[`sq`](https://github.com/neilotoole/sq) is a command-line tool for querying SQL databases and
structured data. These images give it a Sakila fixture on every supported engine, so the same
queries can be exercised across databases. See the per-driver guides at
[sq.io/docs/drivers](https://sq.io/docs/drivers/).
