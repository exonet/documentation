# PostgreSQL

This role will install PostgreSQL.

## Supported versions

The following versions are supported by this role.

| Main  | Latest | EOL |
| ----- | ------ | --- |
| 18    | 18.3   | No  |
| 17    | 17.9   | No  |
| 16    | 16.13  | No  |
| 15    | 15.17  | No  |
| 14    | 14.22  | No  |
| 13    | 13.22  | Yes |
| 12    | 12.22  | Yes |
| 11    | 11.21  | Yes |
| 10    | 10.21  | Yes |
| 9.6   | 9.6.24 | Yes |
| 9.5   | 9.5.25 | Yes |

## Role variables

Variables with a default value defined in `defaults/` or `vars/` have their Default column left empty.

| Name                                       | Type   | Default | Description |
| ------------------------------------------ | ------ | ------- | ----------- |
| `postgresql_allowed_hosts_extra`           | list   |         | Specify which extra IPs or IP ranges to add to `pg_hba`. |
| `postgresql_archive_dir`                   | str    |         | The directory used for PostgreSQL WAL archiving. |
| `postgresql_config_dir`                    | str    |         | The directory containing PostgreSQL configuration files. |
| `postgresql_config_options_extra`          | dict   |         | A dictionary of extra configuration options to add to `postgresql.conf`. |
| `postgresql_data_dir`                      | str    |         | The directory used for PostgreSQL data storage. |
| `postgresql_listen_address`                | str    |         | Define which IP address PostgreSQL should listen on. |
| `postgresql_listen_port`                   | int    |         | Define which port PostgreSQL should listen on. |
| `postgresql_log_directory`                 | str    |         | Specify the logging collector directory. |
| `postgresql_log_error_verbosity`           | str    |         | Configure error logging verbosity (TERSE, DEFAULT, or VERBOSE). |
| `postgresql_log_filename`                  | str    |         | Specify the logging collector filename format. |
| `postgresql_log_min_duration_statement`    | int    |         | Set `log_min_duration_statement` in milliseconds. |
| `postgresql_log_rotation_age`              | int    |         | Set `log_rotation_age` in minutes. |
| `postgresql_log_rotation_size`             | int    |         | Set `log_rotation_size` in kilobytes. |
| `postgresql_logging_collector`             | bool   |         | Whether to enable the logging collector. |
| `postgresql_max_connections`               | int    |         | Set the maximum number of connections. |
| `postgresql_max_locks_per_transaction`     | int    |         | Configure the maximum locks per transaction. |
| `postgresql_max_slot_wal_keep_size`        | str    |         | Configure the replication slot WAL keep size. |
| `postgresql_max_standby_streaming_delay`   | str    |         | Configure the delay before canceling standby queries. |
| `postgresql_max_wal_senders`               | int    |         | Configure the maximum number of WAL senders. |
| `postgresql_pg_hba_config_extra`           | list   |         | Add extra custom configuration entries to `pg_hba`. |
| `postgresql_pg_hba_local_auth_method`      | str    |         | Set the authentication method for local connections in `pg_hba`. |
| `postgresql_pg_repack`                     | bool   |         | Whether to install the pg_repack extension. |
| `postgresql_pg_repack_version`             | str    |         | The version of pg_repack to install. |
| `postgresql_pgbackrest`                    | bool   |         | Whether to install and configure the pgBackRest utility. |
| `postgresql_pgbackrest_pgs`                | list   |         | A list of PostgreSQL instances to configure for pgBackRest. |
| `postgresql_pgbackrest_repos`              | list   |         | A list of pgBackRest repository configurations. |
| `postgresql_pgbackrest_version`            | str    |         | The version of pgBackRest to install. |
| `postgresql_pgroonga`                      | bool   |         | Whether to install the PGroonga extension. |
| `postgresql_pgtop`                         | bool   |         | Whether to install the pgtop utility. |
| `postgresql_pgvector`                      | bool   |         | Whether to install the pgvector extension. |
| `postgresql_pgvector_version`              | str    |         | The version of pgvector to install. |
| `postgresql_postgis`                       | bool   |         | Whether to install and configure the PostGIS extension. |
| `postgresql_postgis_version`               | str    |         | The version of PostGIS to install. |
| `postgresql_role_mode`                     | str    |         | Whether to run install, config, or all tasks. |
| `postgresql_service_dependencies`          | list   |         | A list of other services to start before starting PostgreSQL. |
| `postgresql_shared_buffers`                | str    |         | Configure the shared buffer size. |
| `postgresql_shared_preload_libraries`      | list   |         | A list of shared preload libraries. |
| `postgresql_timescaledb`                   | bool   |         | Whether to install the TimescaleDB extension. |
| `postgresql_timescaledb_plugin_version`    | str    |         | The version of TimescaleDB to install. |
| `postgresql_type`                          | str    |         | The type of PostgreSQL to install (server or client). |
| `postgresql_version`                       | str    |         | The version of PostgreSQL to install. |
| `postgresql_wal_keep_segments`             | int    |         | Configure the WAL keep segments for PostgreSQL versions below 13. |
| `postgresql_wal_keep_size`                 | int    |         | Configure the WAL keep size in megabytes. |
| `postgresql_wal_level`                     | str    |         | Configure the WAL level (replica, minimal, or logical). |
| `postgresql_work_mem`                      | str    |         | Configure the work memory size. |

Example for `postgresql_pg_hba_config_extra`:

```yaml
postgresql_pg_hba_config_extra:
  - type: "host"
    database: "replication"
    user: "replication"
    address: "10.5.136.70/32"
    method: "trust"
  - type: "host"
    database:  "all"
    user: "barman"
    address: "10.5.136.70/32"
    method: "md5"
```

Example for `postgresql_config_options_extra`:

```yaml
postgresql_config_options_extra:
  "log_connections": "yes"
  "log_destination": "'syslog'"
```

## Pgbackrest secrets

Pgbackrest secrets should be set manually in /etc/pgbackrest/conf.d/ directory.
You can define [directives] in these that already exists in the global config to set a secret.

```bash
[global]
repo1-s3-key=$access-key
repo1-s3-key-secret=$secret-key
repo1-cipher-pass=$long-passphrase
```

For example using Minio Object Storage with encrypted backups:

```yml
postgresql_pgbackrest_repos:
  - name: repo1
    # example retention 6 week + last 7 days
    retention_full: 6
    retention_diff: 7 # if not set diff expires when the full which the diff depends on is expired, a full counts towards diff total
    type: s3
    s3_bucket: 10000-bucketname
    s3_endpoint: shared.s3.exonet.io
    s3_region: us-east-1 # required for Minio
    s3_uri_style: path # required for Minio, use `host` for AWS
    encrypted: true
    cipher_type: aes-256-cbc
    path: /pgbackrest
```

## Pgvector

Enable the extension per database (run once in each DB that should use it):

```sql
CREATE EXTENSION vector;
```

Pgvector upgrades are a two-step process:

1. Install the new pgvector version using this role.
2. Manually update each database that uses the extension (run once per DB that uses pgvector):

```sql
ALTER EXTENSION vector UPDATE;
```

## TimescaleDB

This extension will probably only run on the latest Postgresql version within the major version. For example, you need
Postgresql 15.14 instead of 15.13.

**Note**: Installing TimescaleDB requires adding it to shared_preload_libraries, which means PostgreSQL must be restarted for this change to take effect.

## Testing

Run Molecule from the role directory:

```shell
molecule test
```
