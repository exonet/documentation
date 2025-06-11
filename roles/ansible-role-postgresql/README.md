PostgreSQL
==========

This role will install PostgreSQL.

Supported versions
------------------

The following versions are supported by this role.

| Main  | Latest | EOL |
| ----- | ------ | --- |
| 17    | 17.5   | No  |
| 16    | 16.9   | No  |
| 15    | 15.13  | No  |
| 14    | 14.18  | No  |
| 13    | 13.21  | No  |
| 12    | 12.22  | Yes |
| 11    | 11.21  | Yes |
| 10    | 10.21  | Yes |
| 9.6   | 9.6.24 | Yes |
| 9.5   | 9.5.25 | Yes |

Role variables
--------------

| Variable                                  | Default                                                                 | Description                                                         |
| ----------------------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `postgresql_role_mode`                    | `install`                                                               | Whether to run install, config or all tasks.                        |
| `postgresql_type`                         | `server`                                                                | The type of PostgreSQL to install (server or client).               |
| `postgresql_listen_address`               | `localhost`                                                             | Define which IP address PostgreSQL should listen on.                |
| `postgresql_listen_port`                  | `5432`                                                                  | Define which port PostgreSQL should listen on.                      |
| `postgresql_version`                      | `17.3`                                                                  | The version of PostgreSQL to install.                               |
| `postgresql_allowed_hosts_extra`          | `[]`                                                                    | Specify which extra IPs or IP ranges to add to `pg_hba`             |
| `postgresql_logging_collector`            | `false`                                                                 | Wether to enable the logging collector.                             |
| `postgresql_log_error_verbosity`          | `DEFAULT`                                                               | Configure error logging verbosity. Options: TERSE, DEFAULT, VERBOSE |
| `postgresql_log_directory`                | `/var/log/postgresql`                                                   | Specify the logging collector directory.                            |
| `postgresql_log_filename`                 | `/var/log/postgresql/postgresql-{{ postgresql_version_main }}-main.log` | Specify the logging collector file format to be used.               |
| `postgresql_log_min_duration_statement`   | `2000`                                                                  | Set log_min_duration_statement in milliseconds.                     |
| `postgresql_log_rotation_age`             | `1440`                                                                  | Set log_rotation_age in minutes.                                    |
| `postgresql_log_rotation_size`            | `10240`                                                                 | Set log_rotation_size in kilobytes. Default is 10240KB.             |
| `postgresql_max_connections`              | `100`                                                                   | Set the amount of max connections.                                  |
| `postgresql_max_locks_per_transaction`    | `64`                                                                    | Configure the max locks per transaction.                            |
| `postgresql_max_standby_streaming_delay`  | `30s`                                                                   | Configure the delay before canceling standby queries                |
| `postgresql_max_wal_senders`              | `3`                                                                     | Configure the max_wal_senders.                                      |
| `postgresql_pgbackrest`                   | `false`                                                                 | Whether to install and configure the pgBackRest utility.            |
| `postgresql_pgtop`                        | `false`                                                                 | Whether to install the pgtop utility                                |
| `postgresql_postgis`                      | `false`                                                                 | Whether to install and configure Postgis.                           |
| `postgresql_service_dependencies`         | `[]`                                                                    | A list of other services to start before starting PostgreSQL.       |
| `postgresql_shared_buffers`               | `128M`                                                                  | Configure the shared buffer size.                                   |
| `postgresql_wal_keep_size`                | `128`                                                                   | Configure the wal_keep_size.                                        |
| `postgresql_wal_keep_segments`            | `16`                                                                    | Configure the wal_keep_settings.                                    |
| `postgresql_work_mem`                     | `4M`                                                                    | Configure the work_mem size.                                        |

Pgbackrest secrets
------------------
Pgbackrest secrets should be set manually in /etc/pgbackrest/conf.d/ directory.
You can define [directives] in these that already exists in the global config to set a secret.

Testing
-------

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
