# MySQL

This role will install MySQL.

## Supported versions

This role supports MySQL 5.6, 5.7, 8.0 and 8.4.

Latest supported versions:

| Version |
| ------- |
| 8.4.5   |
| 8.0.42  |
| 5.7.44  |
| 5.6.51  |

## Authentication
Ansible [mysql_user module](https://docs.ansible.com/ansible/latest/collections/community/mysql/mysql_user_module.html#notes) currently (2024-05-21) does not support caching_sha2_password so mysql_native_password is still required to manage users with Ansible.

## Role variables

The following variables are most likely used in the playbook:

| Variable                               | Default                  | Description                                                                                                             | Extra
|----------------------------------------|--------------------------|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------
| `mysqld_role_mode`                     | `install`                | Whether to run install, config or all tasks.                                                                            |
| `mysqld_bind_address`                  | `127.0.0.1`              | Define which IP address to bind MySQL on                                                                                |
| `mysqld_version`                       |                          | The version of MySQL to install.                                                                                        |
| `mysqld_type`                          | `server`                 | The type of MySQL to install (server or client).                                                                        |
| `mysqld_data_path`                     | `/home/mysql`            | Path to the data directory                                                                                              |
| `mysqld_config_path`                   | `/etc/mysql`             | Path to the config directory                                                                                            |
| `mysqld_log_path`                      | `/var/log/mysql`         | Path to the log directory                                                                                               |
| `mysqld_instance`                      |                          | Instance name. When provided, the role will install a separate instance of MySQL. Paths will append the instance name.  |
| `mysqld_port`                          |                          | Port number on which the  instance will listen.                                                                         |
| `mysqld_manage_passwords`              | `false`                  | Update MySQL user password when the password in the `/root/.mysql/.my.user.cnf` file is changed                         |
| `mysqld_manage_passwords_slave`        | `false`                  | Update MySQL user password when the password in the `/root/.mysql/.my.user.cnf` file is changed when MySQL is slave.    | Note! Normally the master takes care of the users so only use this in special cases where the slave needs to manage the users.
| `mysqld_i_love_defaults`               | `false`                  | Override the defaults check.                                                                                            |
| `mysqld_options_extra`                 | `{}`                     | Provide extra configuration options.                                                                                    |
| `mysqld_connect_timeout`               | `10`                     | Set connect timeout                                                                                                     |
| `mysqld_event_scheduler`               | See description          | The scheduler has 3 states: ON, OFF, DISABLED. By default the scheduler is set to ON or OFF depending on MySQL version. |
| `mysqld_innodb_buffer_pool_chunk_size` | `128M`                   | Set innodb_buffer_pool_chunk_size                                                                                       |
| `mysqld_innodb_buffer_pool_instances`  | `1`                      | Set number of innodb_buffer_pool_instances                                                                              |
| `mysqld_innodb_buffer_pool_size`       | `128M`                   | Set innodb_buffer_pool_size                                                                                             |
| `mysqld_innodb_lock_wait_timeout`      | `50`                     | Set innodb lock_wait_timeout                                                                                            |
| `mysqld_innodb_log_file_size`          | `48M`                    | Set innodb_log_file_size (deprecated as of MySQL 8.0.30)                                                                |
| `mysqld_innodb_print_all_deadlocks`    | `0`                      | Set innodb_print_all_deadlocks                                                                                          |
| `mysqld_innodb_redo_log_capacity`      | `100M`                   | Set innodb_redo_log_capacity                                                                                            |
| `mysqld_innodb_rollback_on_timeout`    | `0`                      | Enable (1) or disable (0) innodb_rollback_on_timeout.                                                                   |
| `mysqld_innodb_temp_data_file_path`    | `ibtmp1:12M:autoextend`  | Set innodb_temp_data_file_path.                                                                                         |
| `mysqld_interactive_timeout`           | `28800`                  | Set interactive_timeout in seconds                                                                                      |
| `mysqld_join_buffer_size`              | `256K`                   | Set join_buffer_size                                                                                                    |
| `mysqld_local_infile`                  | `0`                      | Set local-infile. Disabled by default for security reasons                                                              |
| `mysqld_secure_file_priv`              | `NULL`                   | Set secure_file_priv. `NULL` disables this functionality which is preferred for security reasons.                       |
| `mysqld_long_query_time`               | `2`                      | Set long_query_time in seconds                                                                                          |
| `mysqld_max_allowed_packet`            | `64M`                    | Set max_allowed_packet size                                                                                             |
| `mysqld_lower_case_table_names`        | `0`                      | Set case sensitivity of table names in queries. Changing this var after initial setup requires reinitialization of MySQL|
| `mysqld_max_connections`               | `400`                    | Set max_connections                                                                                                     |
| `mysqld_max_heap_table_size`           | `16M`                    | Set max_heap_table_size                                                                                                 |
| `mysqld_max_user_connections`          | `200`                    | Set max_user_connections                                                                                                |
| `mysqld_net_read_timeout`              | `30`                     | Set net_read_timeout in seconds                                                                                         |
| `mysqld_net_write_timeout`             | `60`                     | Set net_write_timout in seconds                                                                                         |
| `mysqld_open_files_limit`              | `1024000`                | Set open_files_limit                                                                                                    |
| `mysqld_performance_schema`            | `1`                      | Enable (1) or disable (0) performance_schema                                                                            |
| `mysqld_replicate_wild_ignore_table`   | ""                       | Filter tables to ignore in replication with wildcard support                                                            |
| `mysqld_query_cache_limit`             | `1M`                     | Set query_cache_limit (deprecated in MySQL 8)                                                                           |
| `mysqld_query_cache_size`              | `1M`                     | Set query_cache_size (deprecated in MySQL 8)                                                                            |
| `mysqld_query_cache_type`              | `0`                      | Set query_cache_type (deprecated in MySQL 8)                                                                            |
| `mysqld_slow_query_log`                | `1`                      | Enable (1) or disabble (0) slow_query_log                                                                               |
| `mysqld_sql_mode_default_8`            |                          | Default mysql_mode of MySQL 8                                                                                           |
| `mysqld_sql_mode_default_57`           |                          | Default mysql_mode of MySQL 5.7                                                                                         |
| `mysqld_sql_mode`                      | See description          | Set the mysql_mode to `mysqld_sql_mode_default_57` or `mysqld_sql_mode_default_8` depending on MySQL version            |
| `mysqld_table_definition_cache`        | `-1`                     | Set table_definition_cache                                                                                              |
| `mysqld_table_open_cache`              | `400`                    | Set table_open_cache                                                                                                    |
| `mysqld_tmp_table_size`                | `16M`                    | Set tmp_table_size                                                                                                      |
| `mysqld_wait_timeout`                  | `28800`                  | Set wait_timeout                                                                                                        |
| `mysqld_mysqlx`                        | `0`                      | Whether to enable (1) or disable (0) the mysqlx interface                                                               |
| `mysqld_character_set_server`          | See description          | Set the server default character_set_server. Role defaults are: 'latin1' if < 8.0 else 'utf8mb4'.                       |
| `mysqld_collation_server`              | See description          | Set the server default collation_server. Role defaults are: 'latin1_swedish_ci' if < 8.0 else 'utf8mb4_0900_ai_ci'.     |
| `mysqld_logrotate_error_files`         | `14`                     | Set the maximum amount of error files  that logrotate keeps                                                             |
| `mysqld_logrotate_slow_files`          | `14`                     | Set the maximum amount of slow files that logrotate keeps                                                               |
| `mysqld_init_connect`                  | ""                       | A string to be executed by the server for each client that connects.                                                    |
| `mysqld_binlog_expire_logs_seconds`    | `345600` (4 days)        | Set binlog expire logs in seconds (MySQL >= 8.4).                                                                       |
| `mysqld_expire_logs_days`              | `4`                      | Set binlog expire logs in days (MySQL <= 8.0).                                                                          |


Because of backwards compatibility the default for the `mysqld_data_path` is `/home/mysql` as opposed to `/var/lib/mysql`.
However when an instance name is given with `mysqld_instance`, the `mysqld_data_path` will default to
`/var/lib/mysql-[instance name]` which conforms to the new `/var/lib/` location for the mysqld data directory.
When `/home/mysql` is not used anymore the default for `mysqld_data_path` can be changed to `/var/lib/mysql`
for single instance also.

## Internal role variables

The following parameters are automatically generated and should only be overridden in special cases.
Instead of modifying them, the role should be updated as needed.

| Variable                | Default                 | Description                                         |
| ----------------------- | ----------------------- | --------------------------------------------------- |
| `mysqld_download_url`   |                         | The download location of the MySQL source.          |
| `mysqld_glibc_version`  |                         | The glibc version in the MySQL download URL.        |

## How to reinitializate MySQL when changes are made to mysqld_lower_case_table_names

1. Export all databases
2. Stop MySQL service
3. Add lower_case_table_names = 1 to /etc/mysql/my.cnf
4. Remove /var/lib/mysql directory (This will delete all MySQL database data)
5. Remove /root/.my.cnf file
6. Run playbook
7. Import all databases

## Examples

### Single instance
```
- name: mysql
  block:
    - include_role:
        name: ansible-role-mysqld
      vars:
        mysqld_role_mode: all
        mysqld_version: "8.4.5"
        mysqld_data_path: "/var/lib/mysql"
        mysqld_options_extra:
          group_concat_max_len: 32000
  tags: [mysql]
```

### Multiple instances

When using multi-instance it is still possible to have a single-instance entry. It is not needed to convert the single instance to a multi instance entry.

```
- name: mysql
  block:
    - include_role:
        name: ansible-role-mysqld
      vars:
        mysqld_role_mode: all
        mysqld_version: "8.4.5"
        mysqld_data_path: "/var/lib/mysql"
        mysqld_options_extra:
          group_concat_max_len: 32000
  tags: [mysql]

- name: mysql
  block:
    - include_role:
        name: ansible-role-mysqld
      vars:
        mysqld_role_mode: all
        mysqld_version: "8.4.5"
    - include_role:
        name: ansible-role-mysqld
      vars:
        mysqld_role_mode: all
        mysqld_version: "8.0.42"
        mysqld_instance: instance01
        mysqld_port: 3309
    - include_role:
        name: ansible-role-mysqld
      vars:
        mysqld_role_mode: all
        mysqld_version: "5.7.44"
        mysqld_instance: instance02
        mysqld_port: 3308
  tags: [mysql]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
