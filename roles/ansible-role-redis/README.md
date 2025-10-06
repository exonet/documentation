# Redis

This role will install and manage Redis.

## Variables

The `redis_databases` variable should be present when using this role for configuration as it provides the information the role needs. Check the example playbook below for possible options in this variable.

The following additional variables can be passed to the role from the playbook.

| Variable                      | Type      | Default value        | Required | Description                                                                                                                         |
|-------------------------------|-----------|----------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| `redis_version_major`         | `string`  | `7.2`                | Yes      | The major version of Redis to install.                                                                                              |
| `redis_role_mode`             | `string`  | `install`            | No       | Whether to run install tasks, config tasks or all tasks.                                                                            |
| `redis_bind_address`          | `string`  | `127.0.0.1`          | No       | The address to bind the Redis process on.                                                                                           |
| `redis_protected_mode`        | `boolean` | `true`               | No       | Whether to enable or disable protected-mode on user generated instances. On the default instance protected-mode is always disabled. |
| `redis_keepalive`             | `string`  | `300`                | No       | Configure the Redis tcp-keepalive. This can be defined for all instances globally as well as per separate instance.                 |
| `redis_timeout`               | `string`  | `120`                | No       | Configure the Redis timeout. This timeout can be defined for all instances globally as well as per separate instance.               |
| `redis_type`                  | `string`  | `server`             | No       | Whether to install the server or only the client utilities.                                                                         |
| `redis_service_dependencies`  | `list`    | `[]`                 | No       | A list of other services to start before starting Redis.                                                                            |
| `redis_skip_appendonly_error` | `boolean` | `false`              | No       | Whether to skip the appendonly error message.                                                                                       |
| `redis_loglevel`              | `string`  | `notice`             | No       | The loglevel used. This can be set to: debug, verbose, notice or warning.                                                           |
| `redis_logfile`               | `string`  | `""`                 | No       | The path for Redis to log to, normally not defined.                                                                                 |
| `redis_pidfile`               | `string`  | `/var/run/redis.pid` | No       | The path to the Redis PID file.                                                                                                     |
| `redis_dir`                   | `string`  | `/var/lib/redis`     | No       | Location of the Redis database directory.                                                                                           |
| `redis_unixsocket`            | `string`  | `""`                 | No       | The Unix socket path for the redis.sock file.                                                                                       |
| `redis_unixsocketperm`        | `string`  | `755`                | No       | The Unix socket permissions for the redis.sock file.                                                                                |


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
    - name: redis
      block:
        - include_role:
            name: ansible-role-redis
          vars:
            redis_role_mode: all
            redis_version_major: "7.2"
            redis_timeout: "240"
            redis_keepalive: "400"
      tags: [redis]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
