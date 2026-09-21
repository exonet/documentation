# Deployment

This role will perform deployments.

## Role Variables

Default values are only listed when they are not defined in `defaults/` or `vars/`.

| Name                                              | Type | Default | Description |
| ------------------------------------------------- | ---- | ------- | ----------- |
| `deployment_docker_compose_container_check`       | bool |         | Whether to check if the container is healthy and running. |
| `deployment_docker_compose_facts`                 | bool |         | Whether to generate the Docker Compose Ansible facts. |
| `deployment_docker_compose_files`                 | list |         | Docker Compose file names loaded and merged in the order given. |
| `deployment_docker_compose_force_recreate`        | bool |         | Whether to force the recreation of the Docker Compose services. |
| `deployment_docker_compose_generate`              | bool |         | Whether to generate Docker Compose configuration files (`users` needs to be defined). |
| `deployment_docker_compose_log_output`            | bool |         | Whether to show log output of the deployment. |
| `deployment_docker_compose_profiles`              | list |         | Docker Compose profile names to execute. |
| `deployment_docker_compose_pull`                  | str  |         | Method to use for pulling the Docker image. |
| `deployment_docker_compose_registry_owner`        | str  |         | Registry owner to fetch images from. |
| `deployment_docker_compose_registry_owner_enabled` | bool |        | Whether to set the registry owner in the image URL. |
| `deployment_docker_compose_registry_url`          | str  |         | Registry URL to fetch images from. |
| `deployment_docker_compose_remove_orphans`        | bool |         | Whether to remove orphans (disable for rolling upgrades). |
| `deployment_method`                               | str  |         | Deployment method to use. |
| `deployment_verbose`                              | bool |         | Whether to show verbose output (can contain secrets). |

### Rolling deployments

This role supports rolling deployments when using Docker Compose. This is not natively supported by Docker Compose so this is our own implementation. A rolling deployment will keep the old container running until the new container is fully up and running, then it will seamlessly switch to this new container by changing the port redirect rule in the firewall. Afterwards, the old container will be stopped and cleaned up automatically. A container healthcheck should be defined as this is used to determine if the new container is ready to replace the old container.

When using rolling deployments you must set variables in this role and other roles for rolling deployments to work properly.

In this role you must set `deployment_docker_compose_remove_orphans` to `false`. An orphan is a container that still exists but is no longer referenced by the docker-compose file. This will happen with rolling upgrades because the name of the container will change. We don't want Docker Compose to clean these up automatically because the old container might still be needed while the new one is starting up. This role will do the cleanup once the rolling deployment is finished.

In the firewall role you must set `firewall_csf_faststart` to `false`. CSF uses faststart by default, which means that on a restart of the server the existing firewall rules will be saved and used again on boot. When using rolling deployments the port of the container is dynamic and can change on a restart of the server. In that case we don't want the previous firewall rules (as those contain the old ports), but new rules need to be generated as defined in the csfpost.sh script. For this reason faststart should be disabled.

### Per-user variables

When using the `deployment_docker_compose_generate` option you will need to pass the `users` variable to this role and define a `container_services` (only `name` is required, other variables will be dynamically set based on the deployment). In addition you can set the following vars per user. Check the tables and example below for all possible options.

| Name                   | Type | Default | Description |
| ---------------------- | ---- | ------- | ----------- |
| `container_monitoring` | bool | `true`  | Whether the containers of this user should be monitored. |
| `container_networks`   | list |         | Additional Docker networks to create for this user. |
| `container_secrets`    | list |         | Docker secrets to make available to this user's services. |
| `container_services`   | list |         | Docker Compose services to generate for this user. |
| `container_volumes`    | list |         | Named Docker volumes to create for this user. |

#### `container_services` entries

| Name                          | Type | Default                                 | Description |
| ----------------------------- | ---- | --------------------------------------- | ----------- |
| `name`                        | str  |                                         | Name of the service (required). |
| `command`                     | str  |                                         | Command to run in the container. |
| `container_name`              | str  | `<user>_<service>`                      | Explicit container name. |
| `depends_on`                  | list |                                         | Services this service depends on. |
| `deployment_host_port`        | int  |                                         | Static host port for rolling deployments (used by firewall redirect). |
| `deployment_oneoff`           | bool | `false`                                 | Whether this is a one-off service (e.g. migration); forces `restart: no`. |
| `deployment_rolling`          | bool | `false`                                 | Whether to use rolling deployment for this service. |
| `deployment_shutdown_command` | str  |                                         | Command executed in the old container before it is replaced during a rolling deployment. |
| `env_files`                   | list |                                         | Environment files to load into the container. |
| `env_vars`                    | list |                                         | Environment variables to set in the container. |
| `healthcheck_command`         | str  |                                         | Healthcheck command; enables the healthcheck block. |
| `healthcheck_interval`        | str  | `30s`                                   | Interval between healthchecks. |
| `healthcheck_retries`         | int  | `3`                                     | Number of retries before marking unhealthy. |
| `healthcheck_start_period`    | str  | `0s`                                    | Start period before healthchecks count. |
| `healthcheck_timeout`         | str  | `10s`                                   | Healthcheck timeout. |
| `hosts`                       | list |                                         | Extra hosts to add to `/etc/hosts` in the container. |
| `image_name`                  | str  | `${APP_NAME}` / `app_name`              | Image name. |
| `image_tag`                   | str  | `${DOCKER_IMAGE_TAG}` / `latest`        | Image tag. |
| `init`                        | bool | `false`                                 | Whether to run an init process inside the container. |
| `links`                       | list |                                         | Legacy container links. |
| `networks`                    | list | `[<user>]`                              | Networks to attach the container to. |
| `ports`                       | list |                                         | Ports to publish. |
| `profiles`                    | list |                                         | Docker Compose profiles the service belongs to. |
| `registry_owner`              | str  | `deployment_docker_compose_registry_owner` / `${APP_OWNER}` | Registry owner (namespace) for the image. |
| `registry_owner_enabled`      | bool | `true`                                  | Whether to include the registry owner in the image URL. |
| `registry_url`                | str  | `deployment_docker_compose_registry_url` | Registry URL for the image. |
| `resource_limit_cpu`          | str  |                                         | CPU limit (e.g. `'0.50'`). |
| `resource_limit_memory`       | str  |                                         | Memory limit (e.g. `512M`). |
| `restart_policy`              | str  | `unless-stopped`                        | Docker restart policy (ignored when `deployment_oneoff` is `true`). |
| `secrets`                     | list |                                         | Secrets (defined under `container_secrets`) to expose to the service. |
| `user`                        | str  |                                         | User to run the container as. |
| `volumes`                     | list |                                         | Volume mounts for the service. |
| `working_dir`                 | str  |                                         | Working directory inside the container. |

#### `container_networks` entries

| Name     | Type | Default  | Description |
| -------- | ---- | -------- | ----------- |
| `name`   | str  |          | Network name (required). |
| `driver` | str  | `bridge` | Docker network driver. |

#### `container_secrets` entries

| Name   | Type | Default | Description |
| ------ | ---- | ------- | ----------- |
| `name` | str  |         | Secret name (required). |
| `file` | str  |         | Path to the file containing the secret (required). |

#### `container_volumes` entries

| Name   | Type | Default | Description |
| ------ | ---- | ------- | ----------- |
| `name` | str  |         | Volume name (required). |

## Example Playbook

```yaml
---
- hosts: example

  vars:
    users:
      - name: minimal-example
        uid: 1500
        container_services:
          - name: backend
      - name: full-example
        uid: 1501
        container_monitoring: false
        container_networks:
          - name: network1
          - name: network2
            driver: bridge
        container_secrets:
          - name: test_secret
            file: ./secrets/test_secret
        container_volumes:
          - name: test_volume
        container_services:
          - name: frontend
            container_name: full-example_frontend
            registry_url: registry.exonet.nl
            registry_owner: exonet
            registry_owner_enabled: true
            image_name: frontend
            image_tag: latest
            command: yarn start
            restart_policy: unless-stopped
            user: root
            init: true
            working_dir: /home/userapp
            healthcheck_command: echo test || exit 1
            healthcheck_interval: 30s
            healthcheck_timeout: 10s
            healthcheck_retries: 3
            healthcheck_start_period: 0s
            resource_limit_cpu: '0.50'
            resource_limit_memory: 512M
            env_files:
              - .env
            env_vars:
              - env1=test
            ports:
              - "15001:8080"
            hosts:
              - "db01:127.0.0.1"
            links:
              - "db01:database"
            secrets:
              - test_secret
            volumes:
              - "./tmp:/tmp"
              - test_volume:/home/userapp/test
            profiles:
              - webserver
            networks:
              - network1
              - network2
            depends_on:
              - migration
            deployment_rolling: true
            deployment_host_port: 15001
            deployment_shutdown_command: echo bye
          - name: migration
            image_name: backend
            command: migration.sh
            deployment_oneoff: true
        domains:
          - name: exonetcloud.nl
            upstream:
              - 127.0.0.1:15001

  tasks:
    - name: Deployment
      block:
        - ansible.builtin.include_role:
            name: ansible-role-deployment
          vars:
            deployment_method: "docker-compose"
            deployment_docker_compose_generate: true
      tags: [deployment]
```

## Testing

To test this role you will also need to add several extra variables that are normally added by the deployment pipeline. At a minimum, include the `deploy_user` to deploy to and the `docker_image_tag` of the image to deploy. Since these variables are dynamic they should not be added to the playbook, so they are not included in the example above. Use extra vars in Ansible to include these when testing locally. When not using the default Docker registry, log in to the registry on the server (for example `docker login registry.exonet.nl`) to pull images.


Run Molecule from the role directory:

```shell
molecule test
```
