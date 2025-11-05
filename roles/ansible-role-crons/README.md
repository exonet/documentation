# Crons

This role manages cron jobs for users.

## Structure

Each item in `crons` defines the cron configuration for a specific user.
The `name` field specifies the username, and under `tasks` the cron jobs for that user are listed.

## Variables

The `crons` variable must always be present as it provides the information the role needs, check the example below for possible options.

The following optional variables can be passed to the role from the playbook.

| Variable                 | Type      | Default value | Required | Description                                                                                              |
|--------------------------|-----------|---------------|----------|----------------------------------------------------------------------------------------------------------|
| `crons_random_delay`     | `boolean` | `false`       | No       | Whether to add a random delay between 0 and `crons_random_delay_max` seconds before the job is executed. |
| `crons_random_delay_max` | `integer` | `10`          | No       | Maximum number of seconds for the delay.                                                                 |
| `crons_user_logs`        | `boolean` | `false`       | No       | Whether to activate cron logs per user (`users` variable must be present).                               |

## Time fields

Time fields (`minute`, `hour`, `day`, `month`, `weekday`) are optional.
If a field is not provided, it defaults to `*` (every value) in the cron expression.

## Disabled jobs

Tasks can be temporarily disabled without being removed from the playbook by setting `disabled: true`
This makes it possible to toggle jobs on and off while keeping the configuration.

## Random delay

Random delay helps spread the load when many jobs are scheduled to run at the same time.
Random delay is disabled by default. It can be enabled globally with `crons_random_delay: true` and configured with a maximum delay in seconds using `crons_random_delay_max`.
For individual tasks, the `random_delay` option can be used to enable or disable the delay, overriding the global setting.

## Logging

Logging can be enabled per task with `log: true`.
By default, logs are written to `/var/log/users/<user>/crons/<task_name>.log`.
This location can be overridden with `log_file`.

If `crons_user_logs: true` is set, a symlink makes logs available under `/home/<user>/logs/crons`.
The symlink only covers logs in the default path, so custom `log_file` paths are not included.

## Environment variables

Environment variables can be defined with the `env` option and are exported before the job runs.

## Special time

The `special_time` option provides shorthand for common cron schedules (`annually`, `daily`, `hourly`, `monthly`, `reboot`, `weekly`, `yearly`).
It cannot be combined with `minute`, `hour`, `day`, `month`, or `weekday`.

## Mail delivery

Job outputs can be delivered by email if the `mailto` option is set for the user.
If configured, output is sent to the specified address (or addresses).
If unset, mail delivery is disabled.

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
            disabled: true
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
