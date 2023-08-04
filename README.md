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


#### PostgreSQL **memory configuration**

Head to conf file or use `SELECT * FROM pg_settings ;` command
```shell
/etc/postgresql/15/main/postgresql.conf
```
- _shared_buffers_
  - Amout of memory allocated for shared memory buffers used for caching data.
  - Typically 25% of the available memory.
  - Can improve read performance by reducing the frequency of disk reads.
- _work_mem_
  - Memory allocated for each worker process used for sorting and hashing
  - Typically 4MB
  - Can improve query performance by allowing more data to be sorted or hashe in memory
- _maintenance_work_mem_
  - Memory allocated for maintenance tasks - vacuuming and indexing
  - The default value is typically 64MB
  - Can improve maintenance perf by allowing data to be processed in memory
- _max_connections_
  - Max number of concurrent connections allowed to the server
  - Typically 100
  - Can improve stability by allowing more clients to connect simultaneously

TIP - **monitor memory usage** and adjust the configuration parameters as needed to optimize performance and avoid resource exhaustion.

#### Understanding Use of Key Conf params

- _effective_cache_size_
  - Specifies the estimated size of the operating system's disk cache.
  - Adjusting this value can help the query planner make better decisions about query execution plans by providing a more accurate estimate of the available cache.
- _autovacuum_
  - whether automatic vacuuming of tables and indexes should be performed. Enabling can help maintain DB perf by cleaning up dead rows and improving index efficiency.
- _checkpoint_timeout_
  - Specifies frequency at which checkpoints are performed.
  - Adjusting this value can help balance the trade-off between recovery time and server performance by reducing the amount of data that needs to be recovered after a crash.
- _checkpoint_completion_target_
  - Amount of time that a checkpoint should take as a fraction of the _checkpoint_timeout_ value.
  - Adjusting can help optimize server perf by balancing trade-off between checkpoint frequency and checkpoint overhead.
- _effective_io_concurrency_
  - number of concurrent I/O operations that can be performed on the server
  - Adjusting can help optimize server perfor by allowing the server to perform more IO operations in parallel
- _max_wal_size_
  - max size of the write-ahead log (WAL) files. 
  - Adjusting can help optimize server perf by balancing the trade-off between WAL size and recovery time after a crash.
- _temp_buffers_
  - Amount of memory allocated for temp data used in sorting and hashing
  - Can improve query perf by allowing more data to be sorted or hashed in memory
- _random_page_cost_
  - This param specifies the estimated cost of a random page access.
  - Can help the query planner make better decisions about query execition plan by providing a more accurate estimate of the cost of a random IO operations
- seq_page_cost
  - estimated cost of a sequential page access
  - can help query planner make better decisions about query execition plans by providing a more accurate estimate of the cost of seq IO operations
- max_worker_processes
  - max number of background worker processes that can be spawned by the server
  - can help improve scalability by allowing more background processes to be used for maintenance and other tasks

# 2] Performing Basic PG Operations
