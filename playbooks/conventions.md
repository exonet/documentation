# Playbook Conventions

These are the conventions that apply to changes in our playbooks. They are a subset of our internal conventions, limited to what is relevant for customers. A change that does not follow them is not approved.

## General

- Everything is written in English: task names, comments, variable values, commit messages and pull requests.
- Follow the patterns that already exist in the repository before introducing new ones.
- Change only what is needed. Formatting or cleanup changes must not be mixed with functional changes, and lines you do not need to change stay as they are.
- Indentation is 2 spaces, no tabs. Use the `.editorconfig` in the repository.
- Use double quotes (`"`), not single quotes (`'`).
- Booleans are `true` and `false`, not `yes`/`no` or `True`/`False`.
- Never add sensitive information such as passwords, keys, tokens or certificates to the playbook.

## Variables

- Variable names are lowercase `snake_case`.
- Only use variables that are documented in the README of the role. Do not invent variables.
- Use a unique `uid` when adding a new user.
- Add `removed: true` when removing users, domains and databases instead of deleting the entry.
- Software versions are set as a variable. Prefer a major version over a fully pinned version.

## Tasks

- Ansible syntax must be compatible with Ansible 10 and higher and pass `ansible-lint` with the `.ansible-lint` configuration in the repository.
- Tasks must be idempotent: running the playbook twice must not change anything the second time.
- Task names are imperative, for example "Create the deploy directory".
- Order task parameters consistently: `become` first, then the module and its parameters, then `loop`, `register`, `changed_when`, `failed_when`, `notify`, and `when` last.
- Only set `become` or `become_user` when a task must run as a user other than `root`. Playbooks already run with privilege escalation.
- Use `loop`, not `with_items` or other `with_*` lookups.
- Write conditions with `and` or `or` on multiple lines, one operator per line, aligned with the first condition.
- Always set `mode` on `file`, `template`, `copy` and `unarchive` tasks, as a quoted octal string (`"0644"`). Use the most restrictive permissions that work: `"0644"` for files, `"0755"` for directories and scripts, `"0600"` and `"0700"` for secrets.
- Reload or restart the service with `notify` when its configuration changes. Do not rename existing handlers.

## Templates

- Template syntax must be compatible with Jinja 3.1 and higher.
- Templates have the extension `.j2` and start with `{{ ansible_managed }}`.
- Use explicit filters instead of relying on implicit conversion.
