# Node.js

This role will install Node.js, NPM and optionally PM2.

## Role variables

Default values are only listed when they are not defined in `defaults/` or `vars/`.

| Name                       | Type   | Default | Description                                                                         |
| -------------------------- | ------ | ------- | ----------------------------------------------------------------------------------- |
| `nodejs_default_version`   | `str`  |         | The default Node.js version used for symlinks when no user-specific version is set. |
| `nodejs_npm_packages`      | `list` |         | Install extra npm packages, defined in a list.                                      |
| `nodejs_npm_version_major` | `str`  |         | The version of NPM to install instead of the default one that comes with Node.js.   |
| `nodejs_nvm_version`       | `str`  |         | The version of NVM (Node Version Manager) to install for each user.                 |
| `nodejs_pm2_version_major` | `str`  |         | Install the pm2 process manager.                                                    |
| `nodejs_prefix`            | `str`  |         | The location in which the node binary will be installed.                            |
| `nodejs_user_symlinks`     | `bool` |         | Ensures symlinks are set in the /home/user/bin/ directories.                        |
| `nodejs_vars`              | `list` |         | List of variable dicts to manage multiple Node.js versions.                         |
| `nodejs_version_major`     | `str`  |         | The version of Node.js to install.                                                  |

## Examples

The `nodejs_default_version` variable is required for `nodejs_user_symlinks` . This will create symlinks
to the default version for users that do not have a specific `nodejs_version` defined.

Example playbook with two Node.js versions using `nodejs_vars`:

```yaml
- hosts: example

  vars:
    nodejs_default_version: "18"

  tasks:
    - name: Install Node.js
      block:
        - ansible.builtin.include_role:
            name: ansible-role-nodejs
          vars:
            nodejs_vars:
              - nodejs_version_major: "18"
                nodejs_prefix: /usr/local/node-18
                nodejs_user_symlinks: true
                nodejs_npm_packages:
                  - name: foo
                    state: "latest"
                  - name: bar
                    version: "1.0.0"
              - nodejs_version_major: "20"
                nodejs_prefix: /usr/local/node-20
                nodejs_user_symlinks: true
                nodejs_npm_packages:
                  - name: foo
                    state: "latest"
                  - name: bar
                    version: "2.0.0"
      tags: [nodejs]
```

When `nodejs_vars` is provided, the role loops internally over each entry. Only variables present in the entry override the role defaults — absent variables use their defaults.

When `nodejs_vars` is empty (default `[]`), the role runs once using directly passed variables:

```yaml
- ansible.builtin.include_role:
    name: ansible-role-nodejs
  vars:
    nodejs_version_major: "20"
    nodejs_prefix: /usr/local/node-20
```

**Note:** Do not combine `nodejs_vars` with directly passed per-version variables (e.g. `nodejs_version_major`) in the same `include_role` call. Direct variables take precedence over the entries in `nodejs_vars`, causing every iteration to use the same values. Variables that are not part of the version loop, such as `nodejs_nvm_version`, can safely be passed alongside `nodejs_vars`:

```yaml
- ansible.builtin.include_role:
    name: ansible-role-nodejs
  vars:
    nodejs_vars: "{{ nodejs_role_vars }}"
    nodejs_nvm_version: "0.40.4"
```

Example user with a specific `nodejs_version` defined:

```yaml
users:
  - name: example
    uid: 1500
    nodejs_version: "20"
    domains:
      - name: www.example.com
```

## Migration

To migrate from single node version to multiple node versions, manually actions are needed after the multiple versions are installed. The example below migrates a single node-20 version with existing node_modules to a multiple versions setup.

```shell
rm /usr/local/bin/node
ln -s /usr/local/node-20/bin/node /usr/local/bin/node

rm -rf /usr/local/node-20/lib/node_modules
mv /usr/local/lib/node_modules /usr/local/node-20/lib/
```

## Testing

Run Molecule from the role directory:

```shell
molecule test
```
