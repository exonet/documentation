# Supervisor

This role will install and manage Supervisor.

## Variables

The `processes` variable can be added to an user when using this role for configuration as it provides the information the role needs. Check the example playbook below for possible options in this variable.

The following additional variables can be passed to the role from the playbook.

| Variable                           | Type     | Default value  | Required | Description                                                                |
| ---------------------------------- | -------- | -------------- | -------- | -------------------------------------------------------------------------- |
| `supervisor_version_major`         | `string` | `4.2`          | Yes      | The major version of Supervisor to install.                                |
| `supervisor_role_mode`             | `string` | `install`      | No       | Specifies whether to run install tasks, config tasks or all tasks.         |
| `supervisor_logging_version_major` | `string` | `none`         | No       | The major version of the Supervisor logging plugin to install.             |
| `supervisor_http_server_address`   | `string` | `none`         | No       | The address to listen on for the built-in http server.                     |

## Example Playbook

```yaml
- hosts: example

  vars:
    users:
      - name: user1
        uid: 1500
        processes_application: _default
        processes:
          - name: test1
            command: /usr/bin/yes
            group: groupname
            directory: /tmp
            application: _default
            env_vars:
              - name: APPLICATION_ENV
                value: production
            roles:
              - server01.exonetcloud.nl

  tasks:
    - name: supervisor
      block:
        - include_role:
            name: ansible-role-supervisor
          vars:
            supervisor_role_mode: all
            supervisor_version_major: "4.2"
      tags: [supervisor]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
