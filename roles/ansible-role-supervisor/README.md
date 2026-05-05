# Supervisor

This role will install and manage Supervisor.

## Variables

The `processes` variable can be added to an user when using this role for configuration as it provides the information the role needs. Check the example playbook below for possible options in this variable.

The following additional variables can be passed to the role from the playbook.

Default values are only listed when they are not defined in `defaults/` or `vars/`.

| Name                               | Type  | Default | Description                                                        |
| ---------------------------------- | ----- | ------- | ------------------------------------------------------------------ |
| `supervisor_http_server_address`   | `str` |         | The address to listen on for the built-in http server.             |
| `supervisor_logging_version_major` | `str` |         | The major version of the Supervisor logging plugin to install.     |
| `supervisor_numprocs`              | `int` |         | The default number of processes for each supervised program.       |
| `supervisor_role_mode`             | `str` |         | Specifies whether to run install tasks, config tasks or all tasks. |
| `supervisor_startretries`          | `int` |         | The default number of start retries for each supervised program.   |
| `supervisor_version_major`         | `str` |         | The major version of Supervisor to install.                        |

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
    - name: Install Supervisor
      ansible.builtin.include_role:
        name: ansible-role-supervisor
      vars:
        supervisor_role_mode: all
        supervisor_version_major: "4.2"
      tags: [supervisor]
```

## Testing

Run Molecule from the role directory:

```shell
molecule test
```
