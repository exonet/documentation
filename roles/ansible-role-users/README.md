# Users

This role will configure the users on a system, setup the virtualhost directory structure and configure the ssh keys.

## Variables

The `users`, `user_groups`, `system_users` and `ssh_keys` variables must be present as it provides the information the role needs. Check the example below for possible options.

The following other variables can be passed to the role from the playbook.

| Variable                                    | Type      | Default value           | Required | Description                                                                |
| ------------------------------------------- | --------- | ----------------------- | -------- | -------------------------------------------------------------------------- |
| `users_role_mode`                           | `string`  | `tasks`                 | No       | Whether to run the create (`tasks`) or the removed (`post_tasks`) tasks.   |
| `users_exonet_allowed`                      | `boolean` | `false`                 | No       | Allow the usage of 'exonet' in the username, for backwards compatibility.  |
| `users_generate_ssh_key`                    | `boolean` | `false`                 | No       | Whether a ssh key is created by default for each user.                     |
| `users_home_mode`                           | `string`  | `0711`                  | No       | The permissions set on the /home folder.                                   |
| `users_manage_shellrc`                      | `boolean` | `false`                 | No       | Whether to use the managed `[.bashrc, .zshrc, etc]` file for each user.    |
| `users_shared`                              | `boolean` | `false`                 | No       | Whether a shared environment (like NFS) is used for the /home folder.      |
| `users_shell_history_central_logging_path`  | `string`  | `/var/log/shell_history`| No       | Set shell history file path.                                               |
| `users_shell_history_central_logging`       | `boolean` | `false`                 | No       | Log shell history files centrally.                                         |

## Example Playbook

```yaml
---
- hosts: example

  vars:
    user_groups:
      - name: group1
        gid: 3000

    users:
      - name: example1
        uid: 1500
        group: example
        groups:
          - group1
          - sudo
        home: /home/example1
        shell: /bin/bash
        generate_ssh_key: true
        ssh_key_type: ed25519
        ssh_key_bits: 4096
        ssh_key_file: .ssh/id_ed25519
        authorized_keys: false
        manage_shellrc: true
        shell_env:
          APP_ENVIRONMENT: prod
        roles:
          - server01.exonetcloud.nl

      - name: example2
        uid: 1501
        removed: true
        removed_home: false

    system_users:
      - name: admin
      - name: example3
        groups:
          - sudo
        ssh_keys_exclude_users_all: true

    ssh_keys:
      - name: firstname lastname
        public_keys:
          - ssh-rsa abc==
        addresses:
          - 192.168.165.10
        options:
          - no-agent-forwarding
          - permitopen="127.0.0.1:3306"
        users:
          - name: user1
          - name: user2

      # Public key that is only added to a role (or hostname)
      - name: firstname lastname
        public_keys:
          - ssh-rsa abc==
        addresses:
          - 192.168.165.9
        users:
          - name: user1
            roles:
              - webserver-production
          - name: user2
            roles:
              - server01.exonet.nl

      # Public key that is provisioned to all users
      - name: firstname lastname
        public_keys:
          - ssh-rsa abc==
        users_all:
          - server01.exonet.nl

      # Public key that inherits a group
      - name: firstname lastname
        public_keys:
          - ssh-rsa abc==
        inherits:
          - team-operations

    ssh_key_groups:
      team-operations:
        users:
          - name: admin
        roles:
          - server01.exonetcloud.nl

  tasks:
    - name: users
      include_role:
        name: ansible-role-users
      vars:
        users_role_mode: tasks
        users_home_mode: "0755"
        users_shared: true
        users_generate_ssh_key: true
        users_shell_history_central_logging: true
      tags: [users]

  post_tasks:
    - name: users
      include_role:
        name: ansible-role-users
      vars:
        users_role_mode: post_tasks
      tags: [users]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
