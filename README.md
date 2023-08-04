# postgres-cookbook
PostgreSQL 15 Cookbook notes

# 1] Getting Postgres ready

Install Postgres on your system using official guide: https://www.postgresql.org/download/

```shell
sudo systemctl start postgresql
sudo systemctl status postgresql
sudo -i -u postgres
psql
ALTER USER postgres PASSWORD 'postgres';
```

Check whether you are able to connect using some visual client like DBeaver using `jdbc:postgresql://localhost:5432/postgres`

Database startup logs

```shell
/var/log/postgresql/postgresql-15-main.log
```

PostgreSQL Server **Access** Solutions
- CLI `psql`
- GUI - pgAdmin or DBeaver
- API - libpq and JDBC

Discovering PG Database Structural **Objects**
- Connect using psql
  - List available databases `\l`
  - Select the database of interest `\c`
  - List available schema `\dn`
    - Set specific schema `SET search_path=public`
  - List available objects in the selected schema
    - `\dt`, `\di`, `\ds`, `\df`


PostgreSQL **memory configuration**

Head to
```shell
/etc/postgresql/15/main/postgresql.conf
```
- _shared_buffers_
  - Amout of memory allocated for shared memory buffers used for caching data.
  - Typically 25% of the available memory.
- _work_mem_
  - Memory allocated for each worker process used for sorting and hashing
  - Typically 4MB
- _maintenance_work_mem_
  - Memory allocated for maintenance tasks - vacuuming and indexing
  - The default value is typically 64MB
- _max_connections_
  - Max number of concurrent connections allowed to the server
  - Typically 100

TIP - **monitor memory usage** and adjust the configuration parameters as needed to optimize performance and avoid resource exhaustion.

