# MySQL

This role will install MySQL.

## Supported versions

This role supports MySQL 5.6, 5.7, 8.0 and 8.4.

Latest supported versions:

| Version |
| ------- |
| 8.4.8   |
| 8.0.45  |
| 5.7.44  |
| 5.6.51  |

## Authentication

Ansible [mysql_user module](https://docs.ansible.com/ansible/latest/collections/community/mysql/mysql_user_module.html#notes) currently (2024-05-21) does not support caching_sha2_password so mysql_native_password is still required to manage users with Ansible.

## Role Variables

Default values are only listed when they are not defined in `defaults/` or `vars/`.

| Name                                    | Type | Default        | Description |
| --------------------------------------- | ---- | -------------- | ----------- |
| `mysqld_bind_address`                   | str  |                | Define which IP address to bind MySQL on. |
| `mysqld_binlog_expire_logs_seconds`     | int  |                | Set binlog expire logs in seconds (MySQL >= 8.4). |
| `mysqld_character_set_server`           | str  |                | Set the server default character_set_server. Role defaults are: 'latin1' if < 8.0 else 'utf8mb4'. |
| `mysqld_collation_server`              | str  |                | Set the server default collation_server. Role defaults are: 'latin1_swedish_ci' if < 8.0 else 'utf8mb4_0900_ai_ci'. |
| `mysqld_config_path`                   | str  |                | Path to the config directory. |
| `mysqld_connect_timeout`               | int  |                | Set connect timeout. |
| `mysqld_container_image`               | bool |                | Whether the installation type is a container image. This is only used in container builds. |
| `mysqld_data_path`                     | str  |                | Path to the data directory. |
| `mysqld_default_authentication_plugin` | str  |                | Set the default authentication plugin. |
| `mysqld_event_scheduler`               | str  |                | The scheduler has 3 states: ON, OFF, DISABLED. By default the scheduler is set to ON or OFF depending on MySQL version. |
| `mysqld_expire_logs_days`              | int  |                | Set binlog expire logs in days (MySQL <= 8.0). |
| `mysqld_i_love_defaults`               | bool |                | Override the defaults check. |
| `mysqld_init_connect`                  | str  |                | A string to be executed by the server for each client that connects. |
| `mysqld_innodb_buffer_pool_chunk_size` | str  |                | Set innodb_buffer_pool_chunk_size. |
| `mysqld_innodb_buffer_pool_instances`  | int  |                | Set number of innodb_buffer_pool_instances. |
| `mysqld_innodb_buffer_pool_size`       | str  |                | Set innodb_buffer_pool_size. |
| `mysqld_innodb_lock_wait_timeout`      | int  |                | Set innodb lock_wait_timeout. |
| `mysqld_innodb_log_file_size`          | str  |                | Set innodb_log_file_size (deprecated as of MySQL 8.0.30). |
| `mysqld_innodb_print_all_deadlocks`    | int  |                | Set innodb_print_all_deadlocks. |
| `mysqld_innodb_redo_log_capacity`      | str  |                | Set innodb_redo_log_capacity. |
| `mysqld_innodb_rollback_on_timeout`    | int  |                | Enable (1) or disable (0) innodb_rollback_on_timeout. |
| `mysqld_innodb_temp_data_file_path`    | str  |                | Set innodb_temp_data_file_path. |
| `mysqld_instance`                      | str  |                | Instance name. When provided, the role will install a separate instance of MySQL. Paths will append the instance name. |
| `mysqld_interactive_timeout`           | int  |                | Set interactive_timeout in seconds. |
| `mysqld_jdbc_connector`               | bool |                | Whether to install the JDBC connector. |
| `mysqld_join_buffer_size`              | str  |                | Set join_buffer_size. |
| `mysqld_local_infile`                  | int  |                | Set local-infile. Disabled by default for security reasons. |
| `mysqld_log_bin_trust_function_creators` | int |               | Enable (1) or disable (0) log_bin_trust_function_creators. |
| `mysqld_log_path`                      | str  |                | Path to the log directory. |
| `mysqld_logrotate_error_files`         | int  |                | Set the maximum amount of error files that logrotate keeps. |
| `mysqld_logrotate_slow_files`          | int  |                | Set the maximum amount of slow files that logrotate keeps. |
| `mysqld_long_query_time`              | int  |                | Set long_query_time in seconds. |
| `mysqld_lower_case_table_names`       | int  |                | Set case sensitivity of table names in queries. Changing this var after initial setup requires reinitialization of MySQL. |
| `mysqld_manage_passwords`             | bool |                | Update MySQL user password when the password in the `/root/.mysql/.my.user.cnf` file is changed. |
| `mysqld_manage_passwords_slave`       | bool |                | Update MySQL user password when the password in the `/root/.mysql/.my.user.cnf` file is changed when MySQL is slave. Note: normally the master takes care of the users so only use this in special cases where the slave needs to manage the users. |
| `mysqld_max_allowed_packet`           | str  |                | Set max_allowed_packet size. |
| `mysqld_max_connections`              | int  |                | Set max_connections. |
| `mysqld_max_heap_table_size`          | str  |                | Set max_heap_table_size. |
| `mysqld_max_user_connections`         | int  |                | Set max_user_connections. |
| `mysqld_mysqlx`                       | int  |                | Whether to enable (1) or disable (0) the mysqlx interface. |
| `mysqld_net_read_timeout`             | int  |                | Set net_read_timeout in seconds. |
| `mysqld_net_write_timeout`            | int  |                | Set net_write_timeout in seconds. |
| `mysqld_open_files_limit`             | int  |                | Set open_files_limit. |
| `mysqld_options_extra`                | dict |                | Provide extra configuration options. |
| `mysqld_performance_schema`           | int  |                | Enable (1) or disable (0) performance_schema. |
| `mysqld_port`                         | int  |                | Port number on which the instance will listen. |
| `mysqld_query_cache_limit`            | str  |                | Set query_cache_limit (deprecated in MySQL 8). |
| `mysqld_query_cache_size`             | str  |                | Set query_cache_size (deprecated in MySQL 8). |
| `mysqld_query_cache_type`             | int  |                | Set query_cache_type (deprecated in MySQL 8). |
| `mysqld_replicate_wild_ignore_table`  | str  |                | Filter tables to ignore in replication with wildcard support. |
| `mysqld_role_mode`                    | str  |                | Whether to run install, config or all tasks. |
| `mysqld_secure_file_priv`            | str  |                | Set secure_file_priv. `NULL` disables this functionality which is preferred for security reasons. |
| `mysqld_slow_query_log`              | int  |                | Enable (1) or disable (0) slow_query_log. |
| `mysqld_sql_mode`                    | str  |                | Set the mysql_mode to `mysqld_sql_mode_default_57` or `mysqld_sql_mode_default_8` depending on MySQL version. |
| `mysqld_sql_mode_default_57`         | str  |                | Default mysql_mode of MySQL 5.7. |
| `mysqld_sql_mode_default_8`          | str  |                | Default mysql_mode of MySQL 8. |
| `mysqld_table_definition_cache`      | int  |                | Set table_definition_cache. |
| `mysqld_table_open_cache`            | int  |                | Set table_open_cache. |
| `mysqld_tmp_table_size`              | str  |                | Set tmp_table_size. |
| `mysqld_transaction_isolation`       | str  |                | Set the transaction isolation level. |
| `mysqld_type`                        | str  |                | The type of MySQL to install (server or client). |
| `mysqld_version`                     | str  |                | The version of MySQL to install. |
| `mysqld_wait_timeout`                | int  |                | Set wait_timeout. |

Because of backwards compatibility the default for the `mysqld_data_path` is `/home/mysql` as opposed to `/var/lib/mysql`.
However when an instance name is given with `mysqld_instance`, the `mysqld_data_path` will default to
`/var/lib/mysql-[instance name]` which conforms to the new `/var/lib/` location for the mysqld data directory.
When `/home/mysql` is not used anymore the default for `mysqld_data_path` can be changed to `/var/lib/mysql`
for single instance also.

## How to reinitializate MySQL when changes are made to mysqld_lower_case_table_names

1. Export all databases
2. Stop MySQL service
3. Add lower_case_table_names = 1 to /etc/mysql/my.cnf
4. Remove /var/lib/mysql directory (This will delete all MySQL database data)
5. Remove /root/.my.cnf file
6. Run playbook
7. Import all databases

## Example Playbook

### Single instance

```yaml
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

```yaml
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

Run Molecule from the role directory:

```shell
molecule test
```

## Docker image

This role also contains a docker image that can be used for containers. The data in the container is non-persistent, all data is removed when the container is rebuild. Do NOT use this in production!
