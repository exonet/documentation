# Crons

This role will manage the cronjobs for users.

## Variables

The `crons` variable must always be present as it provides the information the role needs, check the example below for possible options.

The following optional variables can be passed to the role from the playbook.

| Variable                 | Type      | Default value | Required | Description                                                                                              |
|--------------------------|-----------|---------------|----------|----------------------------------------------------------------------------------------------------------|
| `crons_random_delay`     | `boolean` | `false`       | No       | Whether to add a random delay between 0 and `crons_random_delay_max` seconds before the job is executed. |
| `crons_random_delay_max` | `integer` | `10`          | No       | Maximum number of seconds for the delay.                                                                 |
| `crons_user_logs`        | `boolean` | `false`       | No       | Whether to activate cron logs per user (`users` variable must be present).                               |


## Example Playbook

```yaml
---
- hosts: example

  vars:
    crons:
      - name: example
        mailto:
          - example@example.com
        cronvars:
          - name: WEATHER
            value: "Will it rain?"
        tasks:
          - name: example_cron
            minute: 0
            hour: 0
            day: 1
            month: 1
            weekday: 1
            job: "/usr/local/bin/php /tmp/example.php"
            random_delay: true
            log: true
            log_file: /var/log/example_cron.log
            env:
              - "EXAMPLE=variable"
              - "EXAMPLE2=another"
            roles:
              - server01.example.com

  tasks:
    - name: crons
      block:
        - include_role:
            name: ansible-role-crons
          vars:
            crons_random_delay: true
            crons_random_delay_max: 5
            crons_user_logs: true
      tags: [crons]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
