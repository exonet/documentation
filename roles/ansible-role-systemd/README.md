# Systemd

This role will manage the systemd services for users.

## Variables

The `services` variable must always be present as it provides the information the role needs, check the example below for possible options.

## Example Playbook

```yaml
---
- hosts: example

  vars:
    users:
      - name: user1
        uid: 1500
        services:
          - name: test1
            pre_command: /bin/sleep 1
            command: /usr/bin/yes
            command_reload: /bin/kill -USR2 $MAINPID
            directory: /tmp
            env_vars:
              - name: APPLICATION_ENV
                value: production
            env_file: /home/user1/.env
            requires: mysqld.service
            after: nginx.service
            requires_nfs_mount: true
            memory_limit: 1G
            file_limit: 65536
            cpu_quota: 200%
            restart_sec: 5
            restart: "always"
            roles:
              - server01.exonetcloud.nl
            allow_sudo_extra:
              - is-active
              - is-failed
        sockets:
          - name: test1-socket
            listenstream: /var/run/test1.sock
            service: test1.service

  tasks:
    - name: systemd
      block:
        - include_role:
            name: ansible-role-systemd
      tags: [systemd]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
