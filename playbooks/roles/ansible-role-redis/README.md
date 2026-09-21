# Redis

This role will install and manage Redis.

## Variables

The `redis_databases` variable should be present when using this role for configuration as it provides the information the role needs. Check the example playbook below for possible options in this variable.

The following additional variables can be passed to the role from the playbook. Default values are only listed if they are not defined in `defaults/` or `vars/`.

| Name                          | Type   | Default | Description |
| ----------------------------- | ------ | ------- | ----------- |
| `redis_bind_address`          | str    |         | The address to bind the Redis process on. |
| `redis_config_perm`           | str    |         | Changes the permissions of the instance config under the `/etc/redis/` directory to allow non-default Redis instances to run under their own user. |
| `redis_container_image`       | bool   |         | Whether the installation type is a container image. This is only used in container builds. |
| `redis_restart_on_config`     | bool   |         | Whether to automatically restart Redis instances when the configuration changes. When `false`, a `/.redis_restart_required` file is created instead. |
| `redis_restart_on_upgrade`    | bool   |         | Whether to automatically restart Redis when the version changes. When `false`, a `/.redis_restart_required` file is created instead. |
| `redis_role_mode`             | str    |         | Whether to run install tasks, config tasks or all tasks. |
| `redis_run_as_instance`       | bool   |         | Modifies the service file to allow non-default Redis instances to run under their corresponding system user (Redis instance name must match system user). |
| `redis_service_dependencies`  | list   |         | A list of other services to start before starting Redis. |
| `redis_skip_appendonly_error` | bool   |         | Whether to skip the appendonly error message. |
| `redis_start_timeout`         | str    |         | Changes the seconds for `TimeoutStartSec` in the Redis service file to prevent timeouts when starting. |
| `redis_type`                  | str    |         | Whether to install the server or only the client utilities. |
| `redis_version_major`         | str    |         | The major version of Redis to install. |

The following additional variables can be passed to the Redis instance config from the playbook.

| Name             | Type | Default | Description |
| ---------------- | ---- | ------- | ----------- |
| `dir`            | str  |         | Location of the Redis database directory. |
| `keepalive`      | str  |         | Configure the Redis tcp-keepalive. This can be defined for all instances globally as well as per separate instance. |
| `logfile`        | str  |         | The path for Redis to log to, normally not defined. |
| `loglevel`       | str  |         | The loglevel used. This can be set to: debug, verbose, notice or warning. |
| `pidfile`        | str  |         | The path to the Redis PID file. |
| `protected_mode` | bool |         | Whether to enable or disable protected-mode on user generated instances. On the default instance protected-mode is always disabled. |
| `timeout`        | str  |         | Configure the Redis timeout. This timeout can be defined for all instances globally as well as per separate instance. |
| `unixsocket`     | str  |         | The Unix socket path for the redis.sock file. |
| `unixsocketperm` | str  |         | The Unix socket permissions for the redis.sock file. |

## Example Playbook

```yaml
---
- hosts: example

  vars:
    redis_databases:
      - name: example-db
        policy: cache
        port: 6000
        memory: 64
        memory_policy: volatile-lru
        save: false
        timeout: "3600"
        keepalive: "600"
        logfile: "/var/log/redis/my_custom.log"
        loglevel: verbose
      - name: worker-db
        policy: persistent # default
        port: 6001
        memory: 64
        authentication: false # don't generate a password

  tasks:
    - name: Redis
      block:
        - name: Include Redis role
          ansible.builtin.include_role:
            name: ansible-role-redis
          vars:
            redis_role_mode: all
            redis_version_major: "7.2"
            redis_timeout: "240"
            redis_keepalive: "400"
      tags: [redis]
```

## Tags

| Tag                   | Description |
| --------------------- | ----------- |
| `redis_force_restart` | Restart Redis services only for detected configuration or upgrade changes, even when `redis_restart_on_config` or `redis_restart_on_upgrade` are `false`. |

## Docker image

This role also contains a docker image that can be used for containers. The data in the container is non-persistent, all data is removed when the container is rebuild. Do NOT use this in production!

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
