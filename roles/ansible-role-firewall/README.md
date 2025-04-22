# Firewall

This role will manage the firewall.

## Variables

The `firewall` variable should be present as it provides the information the role needs, check the example below for possible options.

The following other variables can be passed to the role from the playbook.

| Variable                   | Type      | Default value  | Required | Description                                                                |
| -------------------------- | --------- | -------------- | -------- | -------------------------------------------------------------------------- |
| `firewall_csf_version`     | `string`  | `14.18`        | No       | The version of csf to install.                                             |
| `firewall_csf_faststart`   | `boolean` | `true`         | No       | Whether to enable the csf faststart setting.                               |
| `firewall_csf_waitlock`    | `boolean` | `false`        | No       | Whether to enable the csf waitlock setting.                                |
| `firewall_csf_directadmin` | `boolean` | `false`        | No       | Ensures Exonet IP whitelists for directadmin installations.                |

## Example Playbook

```yaml
---
- hosts: example

  vars:
    firewall:
      filter:
        - name: http
          dst_port: 80
        - name: https
          dst_port: 443
        - name: example with incoming access from a specific address
          src_address: 127.0.0.1
        - name: example with incoming access from multiple addresses
          src_address:
            - 127.0.0.1
            - 127.0.0.2
        - name: example with incoming access from a specific address to a specific port
          src_address: 127.0.0.1
          dst_port: 80
        - name: example with incoming access from multiple addresses to a specific port
          src_address:
            - 127.0.0.1
            - 127.0.0.2
          dst_port: 80
        - name: example with outgoing access from a specific user to a specific port
          dst_port: 80
          direction: out
          uid: 1500
        - name: example with outgoing access from a specific group to a specific port
          dst_port: 80
          direction: out
          gid: 1500
        - name: example with all default options
          src_address: 127.0.0.1
          dst_port: 80
          protocol: tcp
          direction: in
          policy: allow
          version: 4
        - name: example with incoming access from a specific address and on a specific role
          src_address: 127.0.0.1
          roles:
            - server01.exonetcloud.nl

        # Examples below also require to set `base_facts_xxxx` in the `vars` of the `ansible-role-base`. In the README of the base role you will find the available vars.
        - name: example with incoming access from Github ranges
          src_address: "{{ ansible_local.base.github.ip_ranges }}"
          dst_port: 22
        - name: example with incoming access from Gitlab API/webhooks ranges. If Gitlab CI is needed include base_facts_gcp as well
          src_address: "{{ ansible_local.base.gitlab.ip_ranges }}"
          dst_port: 22
        - name: example with incoming access from Google GCP ranges
          src_address: "{{ ansible_local.base.gcp.ip_ranges }}"
          dst_port: 22
        - name: example with incoming access from Bitbucket ranges
          src_address: "{{ ansible_local.base.bitbucket.ip_ranges }}"
          dst_port: 22

# To maintain a shared firewall config for servers within a setup or server group it can be useful to have a
# shared firewall configuration. By using the example below in a "vars/firewall-shared.yml" file a list of IPs
# can be specified which need to be added to all servers within a setup or server group.
# If an IP or IP range only needs access to the webservers for example a list like the one above can be used
# here in "vars/webservers/firewall.yml". The same can be done for any of the other servers in the setup
# like database servers or workers.
  vars:
    firewall_shared:
      filter:
        - name: Internal network
          src_address: 10.0.0.1/28
        - name: OpenVPN range
          src_address: 10.5.0.1/28

  tasks:
    - name: base
      block:
        - include_role:
            name: ansible-role-base
            public: true
          vars:
            base_hostname: "{{ inventory_hostname }}"
            base_facts_github: true
            base_facts_gitlab: true
            base_facts_gcp: true
            base_facts_bitbucket: true
      tags: [base]

    - name: firewall
      block:
        - include_role:
            name: ansible-role-firewall
      tags: [firewall]
```

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
