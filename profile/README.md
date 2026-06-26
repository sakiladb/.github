# Sakila DB

Docker images preloaded with the [Sakila](https://dev.mysql.com/doc/sakila/en/) sample database —
one per database engine.

Every image carries the same dataset — a uniform **16 tables + 7 views**, identical across engines —
derived from the canonical MySQL Sakila (via [jOOQ](https://www.jooq.org/sakila)) and adapted to each
engine. They exist primarily as test fixtures for [`sq`](https://github.com/neilotoole/sq) — but
they're free for anyone to use.

## Images

| Engine         | Source                                                                | Image                                                                         |
|----------------|-----------------------------------------------------------------------|-------------------------------------------------------------------------------|
| MySQL          | [sakiladb/mysql](https://github.com/sakiladb/mysql)                   | [`sakiladb/mysql`](https://hub.docker.com/r/sakiladb/mysql)                   |
| PostgreSQL     | [sakiladb/postgres](https://github.com/sakiladb/postgres)             | [`sakiladb/postgres`](https://hub.docker.com/r/sakiladb/postgres)             |
| SQL Server     | [sakiladb/sqlserver](https://github.com/sakiladb/sqlserver)           | [`sakiladb/sqlserver`](https://hub.docker.com/r/sakiladb/sqlserver)           |
| Oracle         | [sakiladb/oracle](https://github.com/sakiladb/oracle)                 | [`sakiladb/oracle`](https://hub.docker.com/r/sakiladb/oracle)                 |
| ClickHouse     | [sakiladb/clickhouse](https://github.com/sakiladb/clickhouse)         | [`sakiladb/clickhouse`](https://hub.docker.com/r/sakiladb/clickhouse)         |
| rqlite         | [sakiladb/rqlite](https://github.com/sakiladb/rqlite)                 | [`sakiladb/rqlite`](https://hub.docker.com/r/sakiladb/rqlite)                 |

## Quick start

For example, PostgreSQL:

```shell
docker run -p 5432:5432 -d sakiladb/postgres
```

Most images default to database / user / password `sakila` / `sakila` / `p_ssW0rd`. Image tags,
connection details, and engine-specific notes live in each repo's README.

Every image is published to both [Docker Hub](https://hub.docker.com/u/sakiladb) and the
[GitHub Container Registry](https://github.com/orgs/sakiladb/packages) — pull from either, e.g.
`docker pull sakiladb/postgres` or `docker pull ghcr.io/sakiladb/postgres`. Builds are multi-arch
(`linux/amd64` + `linux/arm64`, except SQL Server which is amd64-only) and cosign-signed.

## Built for sq

[`sq`](https://github.com/neilotoole/sq) is a command-line tool for querying SQL databases and
structured data. These images give it a Sakila fixture on every supported engine, so the same
queries can be exercised across databases. See the per-driver guides at
[sq.io/docs/drivers](https://sq.io/docs/drivers/).
