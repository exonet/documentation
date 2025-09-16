NodeJS
======

This role will install NodeJS, NPM and optionally PM2.

Role variables
--------------

| Variable             | Description                                                                      |
| -------------------- | -------------------------------------------------------------------------------- |
| nodejs_version       | The version of NodeJS to install.                                                |
| nodejs_url           | The download location of the NodeJS source.                                      |
| nodejs_npm_version   | The version of NPM to install instead of the default one that comes with NodeJS. |
| nodejs_pm2           | Install the pm2 process manager (default: false).                                |
| nodejs_zbarimg       | Install the zbarimg module (default: false).                                     |
| nodejs_prefix        | The location in which the node binary will be installed.                         |
| nodejs_user_symlinks | Ensures symlinks are set in the /home/user/bin/ directories                      |
| nodejs_npm_packages  | Install extra npm packages, defined in a list.                                   |


Examples
--------

The `nodejs_default_version` variable is required for `nodejs_user_symlinks` . This will create symlinks
to the default version for users that do not have a specific `nodejs_version` defined.

Example playbook with two nodejs versions:
```
- hosts: example

  vars:
    nodejs_default_version: "18"

  tasks:
    - name: nodejs
      block:
        - include_role:
            name: ansible-role-nodejs
          vars:
            nodejs_version_major: "18"
            nodejs_prefix: /usr/local/node-18
            nodejs_user_symlinks: true
            nodejs_npm_packages:
              - name: foo
                state: "latest"
              - name: bar
                version: "1.0.0"
        - include_role:
            name: ansible-role-nodejs
          vars:
            nodejs_version_major: "20"
            nodejs_prefix: /usr/local/node-20
            nodejs_user_symlinks: true
            nodejs_npm_packages:
              - name: foo
                state: "latest"
              - name: bar
                version: "2.0.0"
      tags: [nodejs]
```

Example user with a specific `nodejs_version` defined:
```
users:
  - name: example
    uid: 1500
    nodejs_version: "20"
    domains:
      - name: www.example.com
```

Migration
---------

To migrate from single node version to multiple node versions, manually actions are needed after the multiple versions are installed. The example below migrates a single node-20 version with existing node_modules to a multiple versions setup.

```
rm /usr/local/bin/node
ln -s /usr/local/node-20/bin/node /usr/local/bin/node

rm -rf /usr/local/node-20/lib/node_modules
mv /usr/local/lib/node_modules /usr/local/node-20/lib/
```

Testing
-------

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
