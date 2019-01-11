# Playbook Conventions

This is a subset of our playbook conventions which are relevant for our customers. When you make changes to our playbooks you must adhere to these conventions or your change will not be approved.

### General

- English is used for all text
- Use lowercase text (except for comments)
- Indentation is 2 spaces (no tabs)
- Booleans use `true` and `false` (not `yes`/`no`, or `True`/`False`)
- Use double quotes (`"`) instead of single quotes (`'`)
- Never add sensitive information (passwords, keys, etc.) to the playbook

### Variables

- Use an unique `uid` when adding a new user
- Add `removed: true` when removing users and databases

### Tasks

- Ansible syntax must be compatible with Ansible 2.6 and higher
- A `when` should always be last and a `become` should always be first
- Software roles should always have a fixed version (or major version) as variable
- Reload the service with a `notify` when changing configuration

### Templates

- Template syntax must be compatible with Jinja 2.10 and higher
- Templates should have the extension `.j2`
- Templates should have `{{ ansible_managed }}` at the top of the file
