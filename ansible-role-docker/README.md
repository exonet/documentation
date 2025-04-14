# Docker

This role will install and manage Docker.

## Variables

The following variables can be passed to the role from the playbook.

| Variable                          | Type      | Default value     | Required | Description                                              |
|-----------------------------------|-----------|-------------------|----------|----------------------------------------------------------|
| `docker_version_major`            | `string`  | `27.5`            | Yes      | The major version of Docker to install.                  |
| `docker_role_mode`                | `string`  | `install`         | No       | Whether to run install tasks, config tasks or all tasks. |
| `docker_buildx_version_major`     | `string`  | `0.14`            | No       | The major version of Docker Compose to install.          |
| `docker_buildx`                   | `boolean` | `false`           | No       | Whether to install Docker buildx.                        |
| `docker_compose_version_major`    | `string`  | `2`               | No       | The major version of Docker buildx to install.           |
| `docker_compose`                  | `boolean` | `false`           | No       | Whether to install Docker Compose.                       |
| `docker_containerd_version_major` | `string`  | `1.6`             | No       | The major version of containerd to install.              |
| `docker_containerd`               | `boolean` | `true`            | No       | Whether to install containerd.                           |
| `docker_default_address_pools`    | `list`    | list of addresses | No       | Which base IP ranges are used for container networks.    |
| `docker_iptables`                 | `boolean` | `false`           | No       | Whether to enable Docker iptables management.            |
| `docker_log_address`              | `string`  |                   | No       | The address for the Docker log driver.                   |
| `docker_log_driver`               | `string`  | `json-file`       | No       | The Docker log driver to use.                            |

Check `defaults/main.yml` for other internal variables.

## Example Playbook

```yaml
---
- hosts: example

  tasks:
    - name: docker
      block:
        - include_role:
            name: ansible-role-docker
          vars:
            docker_role_mode: all
            docker_version_major: "27.5"
            docker_compose: true
            docker_compose_version_major: "2"
            docker_default_address_pools:
              - address: 172.17.0.0/12
                size: 28
            docker_log_driver: gelf
            docker_log_address: "udp://127.0.0.1:1000"
      tags: [docker]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
