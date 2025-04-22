# PHP

This role will install and manage PHP.

## Variables

The `users` variable should be present when using this role for configuration as it provides the information the role needs. Check the example playbook below for possible options in this variable.

The following additional variables can be passed to the role from the playbook.

| Variable                        | Type      | Default value     | Required | Description                                                                         |
| ------------------------------- | --------- | ----------------- | -------- | ----------------------------------------------------------------------------------- |
| `php_version_major`             | `string`  | `8.0`             | Yes      | The major version of PHP to install.                                                |
| `php_role_mode`                 | `string`  | `install`         | No       | Whether to run install tasks, config tasks or all tasks.                            |
| `php_type`                      | `string`  | `fpm`             | No       | Which type of PHP to install (fpm, apxs2 or cli).                                   |
| `php_webserver`                 | `string`  | `nginx`           | No       | Which webserver is being used with PHP (nginx or apache2).                          |
| `php_service`                   | `boolean` | `true`            | No       | Whether to install and use a systemd service.                                       |
| `php_multiple_instances`        | `boolean` | `false`           | No       | Whether to enable multiple instances support.                                       |
| `php_removed`                   | `boolean` | `false`           | No       | Whether to remove this PHP version.                                                 |
| `php_resource_limit`            | `boolean` | `false`           | No       | Whether to enable cgroup resource limits.                                           |
| `php_resource_limit_memory`     | `string`  | none              | No       | The cgroup memory resource limit.                                                   |
| `php_zend_extensions_enable`    | `list`    | Active extensions | No       | The list of Zend extensions to enable in the `extensions.ini` config file.          |
| `php_pecl_extensions_enable`    | `list`    | Active extensions | No       | The list of extensions to enable in the `extensions.ini` config file.               |
| `php_pecl_extensions`           | `list`    | `[]`              | No       | The list of custom PECL extensions to install. See example for structure details.   |
| `php_cachetool`                 | `boolean` | `false`           | No       | Whether to install CacheTool for PHP.                                               |
| `php_extension_*`               | `boolean` | `false`           | No       | Whether to install this PHP extension (see matrix below for a list).                |
| `php_extension_*_version_major` | `string`  | none              | No       | The major version of the extension to install (see matrix below for compatibility). |
| `php_extension_newrelic_error_collector_ignore_exceptions` | `string` | none | No | Exceptions to ignore in the New Relic error collector.                       |
| `php_fpm_pm`                    | `string`  | `ondemand`        | No       | Choose how the process manager will control the number of child processes.          |
| `php_fpm_pm_max_children`       | `int`     | `32`              | No       | The default amount of maximum spawned php-fpm children.                             |
| `php_fpm_pm_max_requests`       | `int`     | `0`               | No       | The maximum requests each php-fpm child process should execute before respawning.   |
| `php_fpm_pm_max_spare_servers`  | `int`     | `2`               | No       | The desired maximum number of idle server processes. Used only when pm is set to dynamic.
| `php_fpm_pm_min_spare_servers`  | `int`     | `1`               | No       | The desired minimum number of idle server processes. Used only when pm is set to dynamic.
| `php_fpm_pm_start_servers`      | `int`     | `min_spare_servers + (max_spare_servers - min_spare_servers) / 2.` | The number of child processes created on startup. Used only when pm is set to dynamic. |
| `php_fpm_request_terminate_timeout` | `string` | `0`            | No       | The timeout for serving a single request after which the worker process will be killed. |
| `php_fpm_admin_values`          | `dict`    | `{}`              | No       | The dict of custom added php-fpm admin values.                                      |
| `php_fpm_values`                | `dict`    | `{}`              | No       | The dict of custom added php-fpm values.                                            |

Check `defaults/main.yml` for more variables to build certain options in PHP and set various settings in the `custom.ini` and `extensions.ini`.
All variables that can be overridden are in `defaults/main.yml`. Due to the fact that in Ansible a variable
can't be set to "undefined", the variables that should be "undefined" are listed in
`defaults/main.yml` but commented out.

## Extensions Compatibility Matrix

| Extension  | PHP 8.4   | PHP 8.3   | PHP 8.2   | PHP 8.1   | PHP 8.0   | PHP 7.4   |
| ---------- | --------- | --------- | --------- | --------- | --------- | --------- |
| amqp       | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| apcu       | >=5.1.24  | Yes       | Yes       | Yes       | Yes       | Yes       |
| blackfire  | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| decimal    | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| event      | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| excimer    | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| gearman    | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| geoip      | No        | No        | No        | No        | No        | Yes       |
| grpc       | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| igbinary   | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| imagick    | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| imap       | Yes       | No        | No        | No        | No        | No        |
| ioncube    | No        | Yes       | Yes       | Yes       | Yes       | Yes       |
| libsodium  | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| mailparse  | >=3.1.8   | Yes       | Yes       | Yes       | Yes       | Yes       |
| memcache   | Yes       | Yes       | Yes       | Yes       | >=8.0     | <8.0      |
| memcached  | Yes       | Yes       | Yes       | Yes       | >=3.1.4   | Yes       |
| mongodb    | Yes       | Yes       | >=1.15.0  | Yes       | Yes       | Yes       |
| msgpack    | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| newrelic   | >=11.5    | >=10.15   | >=10.7    | Yes       | Yes       | Yes       |
| oauth      | Yes       | Yes       | Yes       | Yes       | >=2.0.7   | Yes       |
| oci8       | Yes       | Yes       | >=3.3.0   | >=3.2.0   | >=3.0.0   | >=2.2.0   |
| pdflib     | No        | >=10.0.2  | >=10.0.1  | Yes       | >=9.3.0p5 | Yes       |
| pdo_sqlsrv | No        | Yes       | >=5.11.0  | Yes       | Yes       | <5.11     |
| phalcon    | No        | Yes       | >=5.2.0   | Yes       | Yes       | Yes       |
| protobuf   | Yes       | Yes       | Yes       | Yes       | Yes       | >=3.21.12 |
| psr        | No        | No        | No        | >=1.2.0   | >=1.0.0   | Yes       |
| raphf      | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| redis      | Yes       | Yes       | Yes       | Yes       | >=5.3.0   | >=5.0.2   |
| sqlsrv     | No        | Yes       | >=5.11.0  | >=5.10.0  | >=5.9.0   | Yes       |
| ssh2       | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| swoole     | >=6.0.0   | Yes       | Yes       | Yes       | Yes       | Yes       |
| uuid       | Yes       | Yes       | Yes       | Yes       | >=1.2.0   | Yes       |
| xdebug     | >=3.4.0   | >=3.3.0   | >=3.2.0   | >=3.1.0   | >=3.0.0   | Yes       |
| xlswriter  | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |
| yaml       | Yes       | Yes       | >=2.2.3   | >=2.2.2   | >=2.2.0   | Yes       |
| zmq        | Yes       | Yes       | Yes       | Yes       | Yes       | Yes       |

## Compiling PHP with SNMP on Debian 11

Compiling PHP with `php_snmp` also installs necessary Debian packages. This works up to Debian 10 because for Debian 11 the required package `libsnmp-dev` has a dependency on the `mariadb-common` package which we block by default. So if you want to compile PHP with SNMP you will need to compile/install the dependencies by hand.

## Ioncube loader

This extension will not be automatically loaded because it must be loaded at a very specific place in the `php.ini` file (first extension to load). Please modify the `php.ini` to activate the Ioncube loader.

## Memcache for PHP 8+

Support for memcache 8.0 has been dropped because it had issues and is not actively maintained. Use the memcached extension instead.

## OPcache

There are three possible settings for OPcache. From fastest to safest.

### No timestamp validation (fastest)

This option never validates timestamps so the cache will never expire until it's manually reset. Always use this option when the client has a deployment process that resets the OPcache after each deployment.

```
opcache.validate_timestamps = 0
```

### Periodic timestamp validation (fast)

This option validates the timestamp every X seconds. Use this option if the client does not use a deployment process to reset the OPcache after a deployment. This is also the default option.

```
opcache.revalidate_freq = 60
```

### Validate on every request (slow but secure)

These options always validate timestamps and permissions to make sure the correct user is accessing the OPcache. Use these options on servers which have untrusted users (like shared hosting).

```
opcache.enable_file_override = 1
opcache.validate_permission = 1
opcache.revalidate_freq = 0
opcache.revalidate_path = 1
```

## Example Playbook

```yaml
---
- hosts: example

  vars:
    users:
      - name: user1
        uid: 1500
        php_version: "7.4"
        php_max_children: "16"
        domains:
          - name: site.nl
            docroot: /home/user1/domains/site.nl/public_html
            php_version: "7.3"
            php_values:
              - name: max_execution_time
                value: 600
            php_flags:
              - name: session.auto_start
                value: off # yamllint disable-line rule:truthy

  tasks:
    - name: php
      block:
        - include_role:
            name: ansible-role-php
          vars:
            php_role_mode: all
            php_version_major: "7.4"
            php_multiple_instances: true
            php_type: fpm
            php_webserver: nginx
            php_extension_redis: true
            php_extension_redis_version_major: "5.3"
        - include_role:
            name: ansible-role-php
          vars:
            php_role_mode: all
            php_version_major: "8.0"
            php_multiple_instances: true
            php_type: fpm
            php_webserver: nginx
            php_extension_redis: true
            php_extension_redis_version_major: "5.3"
            php_pecl_extensions:
              - name: mailparse
                version: "3.1.3"
              - name: mcrypt
                version: "1.0.5"
              - name: swoole
                version: "5.0.0"
        - include_role:
            name: ansible-role-php
          vars:
            php_role_mode: all
            php_version_major: "8.4"
            php_multiple_instances: true
            php_type: fpm
            php_webserver: nginx
            php_extension_redis: true
            php_settings_extra:
              "redis.session.locking_enabled": 1
      tags: [php]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
