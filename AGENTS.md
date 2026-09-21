# AGENTS.md

Instructions for AI agents that change an Exonet `ansible-playbooks-*` repository. The `AGENTS.md` in each playbook repository points here. Read this file completely before you change anything.

## Overview

- Each `ansible-playbooks-<customer>` repository contains the Ansible playbooks Exonet uses to manage the servers of one customer.
- Customers have read access and propose changes through a pull request from a fork. Exonet reviews the pull request, merges it and runs the playbook on the servers. Nothing is deployed until Exonet does this.
- You cannot run the playbook and you cannot reach the servers. Your deliverable is a small, correct pull request that Exonet can approve as is.
- Changes that do not follow the conventions in this repository are not approved.

## Read before you start

Fetch these files with the raw URLs. Links inside this repository are given as raw URLs because agents fetch them without a base path.

| What | Raw URL |
| ---- | ------- |
| Playbook conventions (mandatory) | <https://raw.githubusercontent.com/exonet/documentation/master/conventions.md> |
| Role variables, one README per role | see [Roles](#roles) |
| Example vars files | see [Common changes](#common-changes) |

## Repository layout

The layout differs per repository. Look at the repository before you change anything and follow the structure that is already there.

- A repository contains one or more playbook directories. A directory can be named after a server, for example `web01.example.nl/`, after a setup of several servers, for example `setup-webcluster/`, or hold files used by several playbooks, for example `shared/`. Some repositories keep the playbooks in the root of the repository.
- A playbook is a `*.yml` file in such a directory, for example `main.yml`, `webservers.yml` or `site.yml`. Roles are included with `include_role` in tagged blocks.
- The data the roles work with lives in `vars/`, `group_vars/` or `host_vars/` next to the playbook: users, SSH keys, domains, databases, crons, firewall rules. Nearly all customer changes belong in these files. Find the file that already contains the kind of entry you want to change and copy its structure.
- `tasks/` and `templates/` contain custom tasks and Jinja templates for things the roles do not cover. Prefer a role variable over a custom task.
- `Vagrantfile`, `vagrantconf.yml`, `.github/`, `.editorconfig`, `.ansible-lint` and `AGENTS.md` are maintained by Exonet. Do not change them.
- A repository or playbook directory can have a `README.md` with customer-specific instructions. Read it and follow it.

## Rules for changes

- Only use role variables that are documented in the README of that role (see [Roles](#roles)). Do not invent or guess variables. When a role is not documented here, describe what you need in the pull request instead of guessing.
- Do not add roles to a playbook, remove roles, or change the order of roles. Ask Exonet in the pull request when a change needs a role that is not in the playbook yet.
- Do not remove entries for users or databases. Mark them with `removed: true` as described in the conventions.
- Do not change version variables, for example `php_version`, unless the change is explicitly about upgrading that software. Prefer a major version over a fully pinned version.
- Never add secrets: passwords, API keys, private keys, tokens or certificates. Ask Exonet how a secret must be provided.
- Copy the structure of existing entries in the same file. Existing entries show which keys are in use in this repository.
- Do not reformat, reorder or "clean up" lines you do not need to change. The diff must contain only the intended change.
- Custom tasks and templates must follow the conventions: `become` first, `when` last, `notify` for service reloads, templates end in `.j2` and start with `{{ ansible_managed }}`.

## Pull requests

- One topic per pull request. Do not combine unrelated changes.
- Base the branch on the current `master` of the Exonet repository, not on an outdated fork.
- Use a short, descriptive branch name and commit message, for example `firewall` and `Add office address to firewall`.
- Fill in the pull request template. Describe what the change does and why, what was tested, and how Exonet can test it.
- Be honest about testing. You cannot run the playbook, so say what you checked (for example that the YAML is valid and follows existing entries) and do not claim the change was tested on the server.
- State explicitly when the change depends on something only Exonet can do, such as DNS, certificates, a load balancer change, a new role or a new server.
- Do not open a pull request for a change you are unsure about. Ask the question in an issue or in the pull request description and mark it as a draft.

## Common changes

Each entry lists the variable to change, the example file and the role README that documents the variables. The file name is the usual one; when it does not exist, search the repository for the variable name. Look at the existing entries first; they show which options this customer uses.

| Change | Variable (usual file) | Example | Role README |
| ------ | --------------------- | ------- | ----------- |
| Add or change a Linux user, a domain, PHP version per user or domain | `users` (`vars/users.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/users.yml> | users, nginx, php |
| Add or remove an SSH key, or the addresses a key may connect from | `ssh_keys` (`vars/users-ssh.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/users-ssh.yml> | users |
| Open a port or allow an address in the firewall | `firewall` (`vars/firewall.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/firewall.yml> | firewall |
| Restrict access to a site or phpMyAdmin to certain addresses | `restricted` (`vars/restricted.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/restricted.yml> | nginx |
| Add a MySQL or MariaDB database or user | `mysql_databases`, `mysql_users` (`vars/mysql.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/mysql.yml> | mysqld |
| Add a PostgreSQL database or user | `postgresql_databases`, `postgresql_users` (`vars/postgresql.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/postgresql.yml> | postgresql |
| Add a Redis database | `redis_databases` (`vars/redis.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/redis.yml> | redis |
| Add or change a cron job | `crons` (`vars/crons.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/crons.yml> | crons |
| Add a DKIM domain | `base_dkim_domains` (`vars/dkim.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/dkim.yml> | |
| Add or change a Supervisor program for a user | `processes` under the user in `users` (`vars/users.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/users.yml> | supervisor |
| Add or change a systemd service for a user | `services` under the user in `users` (`vars/users.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/users.yml> | systemd |
| Add or change a Docker container, its image, ports, volumes or environment for a user | `container_services`, `container_volumes`, `container_networks`, `container_secrets` under the user in `users` (`vars/users.yml`) | <https://raw.githubusercontent.com/exonet/documentation/master/vars/users.yml> | deployment |

A user, domain, database or key is removed by adding `removed: true` to the entry, not by deleting it.

## Roles

The README of a role lists every supported variable with its type, default and description. Variables that are not in the README are not supported.

| Role | Raw URL |
| ---- | ------- |
| crons | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-crons/README.md> |
| deployment | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-deployment/README.md> |
| firewall | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-firewall/README.md> |
| mysqld | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-mysqld/README.md> |
| nginx | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-nginx/README.md> |
| nodejs | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-nodejs/README.md> |
| php | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-php/README.md> |
| postgresql | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-postgresql/README.md> |
| redis | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-redis/README.md> |
| supervisor | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-supervisor/README.md> |
| systemd | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-systemd/README.md> |
| users | <https://raw.githubusercontent.com/exonet/documentation/master/roles/ansible-role-users/README.md> |

Playbooks can include roles that are not documented here, for example `ansible-role-base`, `ansible-role-monitoring` or `ansible-role-logging`. Those are managed by Exonet. Do not change their variables in a pull request; describe the need instead.

## Checklist before opening the pull request

- The change is in the right vars file and follows the structure of the existing entries.
- Every variable you used is documented in the role README.
- The diff contains only the intended change and no formatting changes elsewhere.
- No secrets and no deleted user or database entries.
- The pull request template is filled in and states honestly what was and was not tested.
- Anything Exonet has to do outside the repository is called out.
