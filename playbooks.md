# Playbook variables

Playbooks use vars files to manage things like users, virtualhosts, databases, cronjobs, firewall rules etc. on a server. The vars files are located in the `vars` directory. This document covers information and examples for the availabe features (variables).

### crons.yml
The `crons.yml` file is used to manage users cronjobs.

**Examples:**

User with two cronjobs.
```
crons:
  - name: alice
    tasks:
      - name: run script.sh every minute
        job: /bin/bash /home/alice/bin/script.sh >/dev/null 2>&1
      - name: run cron.php every weekday at midnight
        job: /home/alice/bin/php /home/alice/domains/example.com/public/cron.php
        minute: "0"
        hour: "0"
        weekday: "1-5"
```

Multiple users with cronjobs, mailto, cronvars and environment variables.
```
crons:
  - name: alice
    mailto:
      - alice@example.com
    cronvars:
      - name: PATH
        value: "/opt/bin"
    tasks:
      - name: run script.py every minute (using /opt/bin/python3)
        job: python3 /home/alice/bin/script.py

  - name: bob
    mailto:
      - bob@example.com
    tasks:
      - name: run script.py every minute
        job: /usr/bin/python /home/alice/bin/script.py
        env:
          - "APP_ENV=production"
```
