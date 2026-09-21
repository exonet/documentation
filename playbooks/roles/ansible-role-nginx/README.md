# Ansible Role: Nginx

This Ansible role installs and configures Nginx with a focus on flexibility, security, and production-readiness. It supports advanced features such as custom headers, security controls, caching, rate limiting, and optional modules (Brotli, ModSecurity, Passenger, and more). All configuration is managed via role variables, with practical examples included below.

## Role variables

The default value of a variable is only listed if it's not defined in `defaults/main.yml` or `vars/main.yml`.

| Name                                                    | Type | Default   | Description |
| ------------------------------------------------------- | ---- | --------- | ----------- |
| nginx_access_log_format                                 | str  |           | The value for the access log format. |
| nginx_access_log_format_definitions                     | list |           | A list of custom logformats. |
| nginx_application_php                                   | bool |           | Whether to add the PHP location block. |
| nginx_auth_basic_message                                | str  |           | Set auth basic message. |
| nginx_bad_agents_abuse                                  | list |           | Default abusive user-agent list. |
| nginx_bad_agents_allowed                                | list |           | User-agents to exclude from the block list. |
| nginx_bad_agents_block                                  | bool |           | Whether to block user agents based on `nginx_bad_agents`. |
| nginx_bad_agents_block_missing                          | bool |           | Whether to block user agents that are empty or contain only blank spaces. |
| nginx_bad_agents_extra                                  | list |           | Additional user-agents to add to the block list. |
| nginx_brotli_module                                     | bool |           | Include Brotli module (default: false). |
| nginx_brotli_module_version                             | str  |           | The version of the Brotli module source to use. |
| nginx_buffer_logs                                       | str  |           | Buffer the writing to the log files. |
| nginx_cleanup                                           | bool |           | Whether to run the cleanup tasks. |
| nginx_client_body_timeout                               | str  |           | Client body timeout. |
| nginx_client_header_timeout                             | str  |           | Client header timeout. |
| nginx_client_max_body_size                              | str  |           | The nginx client_max_body_size. |
| nginx_configure                                         | str  |           | The options used in the configure command. |
| nginx_default_application                               | str  | _default  | Default application template name when a domain does not define one. |
| nginx_default_application_default                       | str  | _default  | Default template name for the default vhost includes. |
| nginx_default_applications                              | list | []        | Additional default vhost application templates to include. |
| nginx_default_fastcgi_settings                          | dict |           | Default FastCGI buffer settings. Automatically merged with `nginx_global_fastcgi_settings` and any per-vhost `domain.fastcgi_settings`. Use `nginx_global_fastcgi_settings` to modify these. |
| nginx_default_php                                       | str  |           | Whether to include the PHP location in the default vhost. |
| nginx_default_proxy_settings                            | dict |           | Default proxy buffer settings. Automatically merged with per-vhost `domain.proxy_settings` (or the legacy `domain.upstream_settings`). Use `nginx_global_proxy_settings` to modify these. |
| nginx_default_redirect                                  | str  | undefined | Redirect default vhost to this URL. |
| nginx_default_server                                    | bool |           | Add default server |
| nginx_default_ssl                                       | bool | false     | Redirect default vhost to https. |
| nginx_default_ssl_certificate_crt                       | str  |           | Default SSL certificate path. |
| nginx_default_ssl_certificate_key                       | str  |           | Default SSL certificate key path. |
| nginx_deny_dotfiles                                     | bool | true      | Whether to deny access to dotfiles for a domain. |
| nginx_dotfiles_allowed                                  | list | []        | Dot-prefixed paths to allow. |
| nginx_deny_files                                        | bool | true      | Whether to deny access to files listed in `nginx_security_deny_files` for a domain. |
| nginx_dhparams_size                                     | str  | 2048      | DH parameter size used to select the bundled ffdhe file. |
| nginx_fastcgi_cache                                     | bool |           | Enable fastcgi cache for all users. |
| nginx_fastcgi_cache_base_dir                            | str  |           | Define the base directory where all the cache files are stored. |
| nginx_fastcgi_cache_inactive                            | str  |           | Define the global inactive (expire) time of the cache. |
| nginx_fastcgi_cache_key                                 | str  |           | Define the global cache key which is used to generate a cache key hash. |
| nginx_fastcgi_cache_max_size                            | str  |           | Max size of the FastCGI cache. |
| nginx_fastcgi_cache_valid                               | list |           | Define the global cache valid configuration (only status 200 for 1 minute). |
| nginx_fastcgi_cache_zone_size                           | str  |           | Size of the FastCGI cache zone. |
| nginx_fastcgi_hide_headers                              | list |           | Define the global headers which FastCGI shouldn't pass on to a client. |
| nginx_fastcgi_ignore_headers                            | list |           | Define the global headers which caching should ignore. |
| nginx_geoip                                             | bool |           | Enable geoip blocking globally. |
| nginx_geoip_continents                                  | list | []        | Continent codes (AF, AN, AS, EU, NA, OC, SA) expanded into countries and unioned with `nginx_geoip_countries` for blocking. |
| nginx_geoip_countries                                   | list |           | List of countries to deny or allow depending on the `nginx_geoip_default` variable. |
| nginx_geoip_countries_exclude                           | list |           | Countries removed from the expanded `geoip_continents`/`geoip_countries` set; they get the `nginx_geoip_default` verdict instead. Per-user/domain overridable. |
| nginx_geoip_database_path                               | str  |           | Path of the GeoIP database file. Note: IPv6 contains both IPv4 and IPv6 addresses and should be used for servers with IPv6 enabled. |
| nginx_geoip_default                                     | str  |           | The default for GeoIP blocking. Allow means allow everything *except* what is in the list. Deny means deny everything *except* what is in the list. |
| nginx_geoip_return_status                               | int  |           | The http return status code when an IP is blocked by GeoIP. |
| nginx_global_fastcgi_settings                           | list |           | Set nginx fastcgi settings globally. |
| nginx_global_headers                                    | list | []        | Set nginx headers globally. |
| nginx_global_proxy_settings                             | list |           | Global proxy settings merged into all domain proxy configurations. |
| nginx_global_upstream_settings                          | list | []        | Global upstream settings merged into per-user and per-domain upstream settings. |
| nginx_graylog_address                                   | bool |           | The Graylog server for nginx logging. |
| nginx_graylog_port_access                               | int  |           | The Graylog server port for access logs. |
| nginx_graylog_port_error                                | int  |           | The Graylog server port for error logs. |
| nginx_greedy_user_agents_default                        | list |           | Default greedy user-agent list. |
| nginx_greedy_user_agents                                | bool |           | Whether to rate limit greedy user agents. |
| nginx_greedy_user_agents_allowed                        | list |           | User-agents to exclude from greedy rate limiting. |
| nginx_greedy_user_agents_burst                          | str  |           | Burst allowance before greedy rate limiting applies. |
| nginx_greedy_user_agents_extra                          | list |           | Additional user-agents to rate limit as greedy. |
| nginx_greedy_user_agents_memory_size                    | str  |           | Memory size for greedy user-agent limit zone. |
| nginx_greedy_user_agents_rate_limit                     | str  |           | Rate limit for greedy user agents. |
| nginx_gzip_comp_level                                   | int  |           | Sets a gzip compression level of a response. Acceptable values are in the range from 1 to 9. |
| nginx_gzip_min_length                                   | int  |           | Sets the minimum length of a response that will be gzipped. |
| nginx_gzip_proxied                                      | str  |           | Enables or disables gzipping of responses for proxied requests depending on the request and response. |
| nginx_gzip_types_extra                                  | list |           | Additional content types to gzip. |
| nginx_headers_more_module                               | bool |           | Include HttpHeadersMoreModule |
| nginx_headers_more_module_url                           | str  |           | The download location of the headers_more_module source. |
| nginx_headers_more_module_version                       | str  |           | The version of headers_more_module to install. |
| nginx_headers_to_reject                                 | list | []        | Header names to remove from the merged header list for a user or domain. |
| nginx_http_auth_request_module                          | bool |           | Include HttpAuthRequest module. |
| nginx_http_image_filter_module                          | bool |           | Include HttpImageFilter module. |
| nginx_keepalive_timeout                                 | str  |           | Keepalive timeout. |
| nginx_limit_open_files                                  | int  |           | The maximum amount of open file descriptors (soft limit). |
| nginx_limit_req_dry_run                                 | bool |           | Log rate limit hits without rejecting requests, for calibrating rates safely (per-user/domain overridable). |
| nginx_limit_req_status                                  | str  |           | HTTP status returned for `limit_req` rejections. |
| nginx_listen_address                                    | str  |           | The listen address that all vhosts will use. |
| nginx_listen_port_http                                  | int  |           | The listen port that all http vhosts will use. |
| nginx_listen_port_https                                 | int  |           | The listen port that all https vhosts will use. |
| nginx_log_format_graylog                                | str  |           | The logformat string when using Graylog as a backend. Pre-defined formats are `nginx_log_format_graylog_default` and `nginx_log_format_graylog_request_body`. |
| nginx_logrotate                                         | bool |           | Whether to ensure a logrotate configuration. |
| nginx_logrotate_frequency                               | str  |           | The frequency of the log rotation. |
| nginx_logrotate_rotate                                  | str  |           | The amount of days to keep for log rotation. |
| nginx_map_hash_bucket_size                              | int  |           | The value for map_hash_bucket_size. |
| nginx_map_hash_max_size                                 | int  |           | The value for map_hash_max_size. |
| nginx_maps                                              | list | []        | Map include definitions used to validate map include files. |
| nginx_mime_types_extra                                  | list |           | Extra Mime types for extensions. |
| nginx_modsecurity_audit_log_basedir                     | str  |           | Base directory for ModSecurity audit logs. |
| nginx_modsecurity_audit_log_format                      | str  |           | Audit log format (Native/JSON) |
| nginx_modsecurity_audit_log_parts                       | str  |           | The ModSecurity SecAuditLogParts setting. |
| nginx_modsecurity_audit_log_relevant_status             | str  |           | Configures which response status code is to be considered relevant for the purpose of audit logging. |
| nginx_modsecurity_bad_agents_allowed                    | list |           | Override `nginx_modsecurity_bad_agents_default` to allow agents that were on the blacklist. |
| nginx_modsecurity_bad_agents_extra                      | list |           | Add extra bad agents to the default list `nginx_modsecurity_bad_agents_default`. |
| nginx_modsecurity_block_missing_user_agent              | bool |           | Whether to block user agents that are empty or contain only blank spaces. |
| nginx_modsecurity_crs_url                               | str  |           | The download location of the ModSecurity Core Rule Set. |
| nginx_modsecurity_crs_version                           | str  |           | The version of the ModSecurity Core Rule Set to install. |
| nginx_modsecurity_custom_rules                          | list |           | Custom Secrules Modsecurity will use. |
| nginx_modsecurity_datadir                               | str  |           | Path where persistent data (e.g., IP address data, session data, and so on) is to be stored. |
| nginx_modsecurity_module_url                            | str  |           | The download location of the ModSecurity module source. |
| nginx_modsecurity_module_version                        | str  |           | The version of the ModSecurity module to install. |
| nginx_modsecurity_module_version_major                  | str  | undefined | Major ModSecurity module version used to select a module release. |
| nginx_modsecurity_request_argument_limit                | int  |           | Configures the maximum amount of args provided in an request. |
| nginx_modsecurity_request_body_access                   | str  |           | Configures whether request bodies will be buffered and processed by ModSecurity. |
| nginx_modsecurity_request_body_limit                    | int  |           | Configures the maximum request body size ModSecurity will accept for buffering. |
| nginx_modsecurity_request_body_limit_action             | str  |           | Controls what happens once a request body limit, configured with SecRequestBodyLimit, is encountered.  [Reject/ProcessPartial] |
| nginx_modsecurity_request_body_no_files_limit           | int  |           | Configures the maximum request body size ModSecurity will accept for buffering, excluding the size of any files being transported in the request. |
| nginx_modsecurity_response_body_access                  | str  |           | Configures whether response bodies will be buffered and processed by ModSecurity. |
| nginx_modsecurity_response_body_limit                   | int  |           | Configures the maximum response body size that ModSecurity will store in memory. (bytes) |
| nginx_modsecurity_response_body_limit_action            | str  |           | Controls what happens once a response body limit, configured with SecResponseBodyLimit, is encountered.  [Reject/ProcessPartial] |
| nginx_modsecurity_response_mime_types                   | list |           | Configures which MIME types are to be considered for response body buffering. |
| nginx_modsecurity_rule_engine                           | str  |           | What mode the ModSecurity rule engine should be in. |
| nginx_modsecurity_ruleset_bad_agents                    | bool |           | Load the ModSecurity bad agents ruleset. |
| nginx_modsecurity_ruleset_owasp_crs                     | bool |           | Load the ModSecurity OWASP Core Rule Set. |
| nginx_modsecurity_ruleset_owasp_crs_global              | bool |           | Include / apply the ModSecurity OWASP Core Rule Set globally. |
| nginx_modsecurity_tmpdir                                | str  |           | Configures the directory where temporary files will be created. |
| nginx_modsecurity_url                                   | str  |           | The download location of the ModSecurity library. |
| nginx_modsecurity_version                               | str  |           | The version of ModSecurity to install. |
| nginx_modsecurity_version_major                         | str  |           | Major ModSecurity version used to select a release. |
| nginx_monitoring_allowed_addresses_extra                | list |           | Gives the defined addresses access to the `/monitoring` location in the default vhost. |
| nginx_openssl_checksum                                  | str  |           | The md5 checksum of the OpenSSL archive. |
| nginx_openssl_source                                    | bool |           | Include OpenSSL from source. |
| nginx_openssl_url                                       | str  |           | The download location of OpenSSL. |
| nginx_openssl_version                                   | str  |           | The version of OpenSSL to install. |
| nginx_openssl_version_checksums                         | list |           | Known checksums for OpenSSL source versions. |
| nginx_passenger                                         | bool | false     | Include the Phusion Passenger config. |
| nginx_passenger_enterprise_download_key_file            | str  |           | Passenger Enterprise download key file path. |
| nginx_passenger_enterprise_install_path                 | str  |           | Installation path for Passenger Enterprise. |
| nginx_passenger_enterprise_license_file                 | str  |           | Passenger Enterprise license file path. |
| nginx_passenger_enterprise_module                       | bool |           | Include Phusion Passenger Enterprise module. |
| nginx_passenger_enterprise_module_version               | str  |           | The version of Phusion Passenger Enterprise module to install. |
| nginx_passenger_enterprise_module_version_major         | str  |           | The major version of the Phusion Passenger enterprise module to install. |
| nginx_passenger_enterprise_module_version_major_default | str  |           | Default major version for Passenger Enterprise module. |
| nginx_passenger_enterprise_module_version_major_map     | dict |           | Map of Passenger Enterprise major versions to full versions. |
| nginx_passenger_enterprise_package_directory            | str  |           | Passenger Enterprise package directory name. |
| nginx_passenger_enterprise_package_download_url         | str  |           | Passenger Enterprise download URL. |
| nginx_passenger_enterprise_package_file                 | str  |           | Passenger Enterprise package archive filename. |
| nginx_passenger_module                                  | bool |           | Include Phusion Passenger module. |
| nginx_passenger_module_url                              | str  |           | The download location of the Phusion Passenger module source. |
| nginx_passenger_module_version                          | str  |           | The version of Phusion Passenger module to install. |
| nginx_passenger_module_version_major                    | str  | undefined | The major version of Phusion Passenger module to install. |
| nginx_pid_path                                          | str  |           | Path to the nginx PID file. |
| nginx_proxy_buffering                                   | bool |           | Enable proxy buffering for all domains. |
| nginx_proxy_cache                                       | bool |           | Enable proxy cache for all users. Also consider enabling proxy_buffering for proxy caching to work correctly. |
| nginx_proxy_cache_base_dir                              | str  |           | Define the base directory where all the cache files are stored. |
| nginx_proxy_cache_inactive                              | str  |           | Define the global inactive (expire) time of the cache. |
| nginx_proxy_cache_key                                   | str  |           | Define the global cache key which is used to generate a cache key hash. |
| nginx_proxy_cache_max_size                              | str  |           | Max size of the proxy cache. |
| nginx_proxy_cache_valid                                 | list |           | Define the global cache valid configuration (only status 200 for 1 minute). |
| nginx_proxy_cache_zone_size                             | str  |           | Size of the proxy cache zone. |
| nginx_proxy_hide_headers                                | list |           | Define the global headers which the proxy shouldn't pass on to a client. |
| nginx_proxy_host                                        | str  |           | Default proxy host header value. |
| nginx_proxy_ignore_headers                              | list |           | Define the global headers which caching should ignore. |
| nginx_proxy_x_forwarded_proto                           | str  |           | Default X-Forwarded-Proto value. |
| nginx_rate_limit                                        | list | undefined | The nginx rate limit. |
| nginx_real_ip_header                                    | str  |           | Set the nginx real_ip_header. |
| nginx_release                                           | str  |           | Define the release line of nginx. "stable" for production, "latest" for dev/test/accp/staging servers. |
| nginx_require_ssl                                       | bool |           | When enabled, the role fails during preflight if any domain does not have an SSL certificate (`domain.ssl`) configured. |
| nginx_restart_graceful                                  | bool |           | Try to restart Nginx gracefully without interruption. |
| nginx_restricted                                        | bool |           | Default for `domain.restricted`. |
| nginx_role_mode                                         | str  |           | Specify how the role behaves. |
| nginx_rtmp_module                                       | bool |           | Include the RTMP module. |
| nginx_rtmp_module_url                                   | str  |           | The download location of rtmp module source. |
| nginx_rtmp_module_version                               | str  |           | The version of the rtmp module to install. |
| nginx_security_deny_file_extensions                     | list |           | List of file extensions to deny access to. |
| nginx_security_deny_files                               | list |           | List of files to deny access to. |
| nginx_security_txt                                      | bool |           | Whether to enable and include the default security.txt. |
| nginx_security_txt_acknowledgments_url                  | str  |           | Define a link to a page where you thank security researchers (optional). |
| nginx_security_txt_canonical_url                        | str  |           | Define the URL for the security.txt file. It is important to include this if you are digitally signing the security.txt file (optional). |
| nginx_security_txt_contact                              | list |           | Define a contact e-mail address, phone number or URL for security issues (1 contact required). |
| nginx_security_txt_expires                              | str  |           | Define a expire date and time when the content should be considered stale (required). |
| nginx_security_txt_hiring_url                           | str  |           | Define a link to any job openings in your organisation (optional). |
| nginx_security_txt_languages                            | str  |           | Define a comma-separated list of language codes (optional). |
| nginx_security_txt_pgp_encryption_url                   | str  |           | Define a link to a key which security researchers should use to securely talk to you (optional). |
| nginx_security_txt_policy_url                           | str  |           | Define a link to a policy detailing what security researchers should do when reporting security issues (optional). |
| nginx_send_timeout                                      | str  |           | Send timeout. |
| nginx_server_names_hash_bucket_size                     | int  |           | The value for server_names_hash_bucket_size. |
| nginx_service                                           | bool |           | Whether to install and use a systemd service. |
| nginx_set_real_ip_from                                  | list |           | List of IP's which are added to the nginx real_ip_from header. |
| nginx_shared_home                                       | bool |           | Option if nginx_user_logs is set to true and the users are shared. This ensures user logs tasks are only run on one host. |
| nginx_ssl_ciphers                                       | str  |           | The SSL/TLS ciphers that nginx will support. |
| nginx_ssl_prefer_server_ciphers                         | bool |           | The nginx ssl_prefer_server_ciphers. |
| nginx_ssl_protocols                                     | list |           | The SSL/TLS protocols that nginx will support. |
| nginx_ssl_certificate_search_paths                      | list |           | Paths where SSL certificates are installed. |
| nginx_task_nginx                                        | bool | true      | Whether to run the main nginx configuration task set. |
| nginx_task_nginx_modsecurity                            | bool | false     | Whether to run the ModSecurity configuration tasks. |
| nginx_task_nginx_removed                                | bool | true      | Whether to run the nginx removal tasks. |
| nginx_tiered_rate_limit                                 | bool |           | Enable the tiered rate limit baseline (per-user/domain overridable via `nginx_tiered_rate_limit` on that level). Requires/auto-enables GeoIP. |
| nginx_tiered_rate_limit_burst                           | str  |           | Default burst applied to the per-tier `limit_req` lines (per-tier overridable). |
| nginx_tiered_rate_limit_default_tier                    | str  |           | Tier applied to any country not matched by a tier's `continents`/`countries`. Must match a `name` in `nginx_tiered_rate_limit_tiers`. |
| nginx_tiered_rate_limit_key                             | str  |           | What shares a rate-limit counter: `ip` (per client IP), `country` (all traffic from a country), `continent` (all traffic from a continent), `geo` (per configured continent/country) or `tier` (all traffic in the tier together). Per-tier overridable via `tier.key`. |
| nginx_tiered_rate_limit_memory_size                     | str  |           | Default shared-memory size for each tier zone (per-tier overridable). |
| nginx_tiered_rate_limit_paths                           | list |           | URI prefixes the rate limit applies to (empty list = all URIs). Per-user/domain overridable. |
| nginx_tiered_rate_limit_tiers                           | list |           | Ordered rate-limit tiers (low to high restriction). Each has a `name`, `rate` (`false` = exempt) and optional `continents`/`countries`/`ips`/`user_agents`/`burst`/`memory_size`/`key`. |
| nginx_timeout                                           | str  |           | The nginx timeout settings (default: 20s). |
| nginx_upstreams                                         | list | []        | List of upstream definitions to make available per user. |
| nginx_url                                               | str  |           | The download location of the Nginx source. |
| nginx_user_logs                                         | bool |           | The nginx logs in users home dir. |
| nginx_variables_hash_max_size                           | int  |           | Value for `variables_hash_max_size`. |
| nginx_varnish_backend_port                              | int  |           | Default Varnish backend port. |
| nginx_varnish_probe_location                            | str  |           | Default Varnish probe location. |
| nginx_version                                           | str  |           | The version of Nginx to install. |
| nginx_version_major                                     | str  |           | The major release of Nginx to install. |
| nginx_worker_connections                                | int  |           | The nginx worker_connections (default: 1024). |

## Global Settings

### Nginx Release Tiers

Nginx releases are organized into two tiers:

- **stable**: Production-ready, fully tested with this role and supported environments.
- **latest**: For staging/acceptance environments. Fully supported, but may include new features awaiting broader validation.

When a new Nginx release is available, it is integrated and tested in the role. After validation, it is promoted from `latest` to `stable`.

### MIME Types

Every Nginx installation includes a default [mime.types](https://github.com/nginx/nginx/blob/0427f5335f7abfbb733a72d6bf3561508f5d8a88/conf/mime.types) file. To add missing types/extensions:

```yaml
nginx_mime_types_extra:
  - type: "image/avif"
    extensions:
      - "avif"
```

### Security.txt

Add a dynamic security.txt file (published to `.well-known/security.txt`):

```yaml
nginx_security_txt: true
nginx_security_txt_contact:
  - "mailto:security@domain.tld"
nginx_security_txt_languages: "nl, en"
```

### Robots.txt

Serve a managed `robots.txt` that blocks AI crawlers by default and supports per-domain sitemap URLs.

Enable the feature globally:

```yaml
nginx_robots_txt: true
```

The generated file blocks the following agents with `Disallow: /` by default:
`GPTBot`, `ClaudeBot`, `anthropic-ai`, `CCBot`, `Google-Extended`, `Bytespider`, `Applebot-Extended`.
All other crawlers are allowed (`User-agent: *` / `Disallow:`).

#### Customising blocked agents

Each entry in `nginx_robots_txt_agents_list` is a dict with `name` (required), `disallow`, `allow`, and `crawl_delay`. Both `disallow` and `allow` accept a single path string or a list of paths. `allow` is written before `disallow`, and `crawl_delay` (in seconds) is written last in the output block.

```yaml
nginx_robots_txt_agents_list:
  # Block everything (default for all AI crawlers)
  - name: GPTBot
    disallow: /

  # Block a specific path only
  - name: GPTBot
    disallow: /private

  # Allow a subtree, block everything else
  - name: GPTBot
    allow: /public
    disallow: /

  # Multiple paths
  - name: GPTBot
    disallow:
      - /private
      - /api
    allow:
      - /api/public

  # Throttle crawl rate (Crawl-delay in seconds)
  - name: Googlebot
    crawl_delay: 10

  # Catch-all — controls User-agent: * (allow everything by default)
  - name: "*"
    disallow: ""

  # Catch-all — deny everything for all other bots
  - name: "*"
    disallow: /
```

The list can be set globally or overridden at domain level:

```yaml
users:
  - name: example
    domains:
      - name: example.nl
        nginx_robots_txt_agents_list:
          - name: GPTBot
            allow: /news
            disallow: /
```

#### Sitemaps

Sitemap URLs are added at domain level and are written into the domain-specific robots.txt:

```yaml
users:
  - name: example
    domains:
      - name: example.nl
        nginx_robots_txt_sitemap_urls:
          - https://example.nl/sitemap.xml
          - https://example.nl/sitemap-news.xml
```

Global sitemap URLs (applied to the default robots.txt) can be set with `nginx_robots_txt_sitemap_urls`.

#### File resolution order

For each domain, nginx's `try_files` checks in this order:

1. `{{ nginx_robots_txt_base_path }}/<domain-name>.txt` — auto-generated domain file (created when any of `domain.nginx_robots_txt_sitemap_urls`, `domain.nginx_robots_txt_agents_list`, or `domain.nginx_robots_txt` is set)
2. `nginx_robots_txt_file_playbook` — playbook-managed static file
3. `nginx_robots_txt_file_default` — role-generated default (all domains)

### Global Headers

To apply headers to all sites on the server, define them in the role variables:

```yaml
nginx_global_headers:
  - name: X-Clacks-Overhead
    value: GNU Terry Pratchett
```

## Specific Settings

### Headers

#### Default vhost

Configure headers for the default vhost by defining `nginx_headers` on the default domain entry. Provide a list of header names and values.

#### Domain headers

Domain-specific headers can override or remove global headers. Defining a header with the same name as a global header overrides its value. Add domain-specific headers as needed.

Architectural note: Decide which headers should be served statically by Nginx and which (such as CSP) are better generated by the application layer.

```yaml
users:
  - name: example
    uid: 1500
    nginx_headers_to_reject:
      - name: X-Clacks-Overhead
    domains:
      - name: example.nl
        nginx_headers:
          - name: Strict-Transport-Security
            value: max-age=31536000;
```

### Security Options

#### Bad User-Agents

Block or rate limit based on user-agents. By default, a short list of known abusive user-agents is blocked. You can always allow or block specific user-agents as needed.

```yaml
nginx_bad_agents_allowed: [] # User-agents to always allow
nginx_bad_agents_extra: []   # User-agents to block (e.g., YandexBot)
nginx_bad_agents_block: true # Enable blocking of user-agents on the curated list
nginx_bad_agents_block_missing: true # Block requests without a user-agent string
```

#### Greedy User-Agents

```yaml
nginx_greedy_user_agents: true # Rate limit user-agents making many requests
nginx_greedy_user_agents_extra: [] # User-agents to rate-limit
nginx_greedy_user_agents_allowed: [] # User-agents to never rate-limit
nginx_greedy_user_agents_rate_limit: "20r/m" # Rate limit
nginx_greedy_user_agents_burst: "10" # Allow this many requests before rate limiting
```

### GeoIP blocking via `users.yml`

Warning: if you enable GeoIP blocking globally it will be active for all domains! The `nginx_variables_hash_bucket_size` and `nginx_variables_hash_max_size` are automatically increased when GeoIP blocking is active (every per-domain GeoIP map declares an nginx variable). You may need to increase them even more with a lot of domains.

You can enable it per domain or user. Both `geoip_countries` and `geoip_continents` (continent codes AF, AN, AS, EU, NA, OC, SA) are accepted at the global, user, and domain level; continents are expanded into their countries and unioned with any explicit countries. Countries listed in `geoip_countries_exclude` are removed from that set and fall back to the `geoip_default` verdict — for example, allow all of Europe except Russia. Look at the example below:

```yaml
users:
  - name: example
    uid: 1500
    php_max_children: "32"
    composer_version: "2"
    domains:
      - name: example.nl
      - name: admin.example.nl
        geoip: true
        geoip_default: "deny"  # Set default to deny every country
        geoip_continents:      # Allow all of Europe...
          - "EU"
        geoip_countries:       # ...plus these extra countries
          - "US"
          - "CA"
        geoip_countries_exclude: # ...but keep these countries (in EU) denied
          - "RU"
      - name: forum.example.nl
        geoip: true
        geoip_default: "allow" # Set default to allow every country
        geoip_countries:       # Except countries in this list, which will be denied
          - "NL"

  - name: demo
    uid: 1501
    php_max_children: "32"
    composer_version: "2"
    geoip: true
    geoip_default: "deny" # Deny all countries by default for all domains
    geoip_countries:      # Allow only listed countries
      - "NL"
      - "DE"
    domains:
      - name: demo.org       # Inherits user-level GeoIP settings
      - name: api.demo.org
        geoip_default: "allow" # Overrides user-level default: allow all countries
        geoip_countries:       # Overrides user-level countries, which will be denied
          - "FR"
```

### Logging

By default, access and error logs are written using the domain name as the filename (e.g. `example.nl.access.log`). You can override the filename per domain using `access_log_file` and `error_log_file`. Setting either to a string uses that value as the log filename; setting it to `false` disables that log entirely.

> **Note:** Custom log filenames must end in `.log` to be picked up by logrotate. Files with a different extension will not be rotated automatically.

```yaml
users:
  - name: example
    uid: 1500
    domains:
      - name: example.nl
        access_log_file: "custom-access.log"  # writes to /var/log/nginx/custom-access.log
        error_log_file: false                  # disables error logging for this domain
```

### Rate Limiting

Define rate limit counters for IPs to use in custom applications (e.g., `/api` location block):

```yaml
nginx_rate_limit:
   - name: custom_rate_limit_zone
     rate: 200r/m
```

Enable the rate limit in a location by adding a `limit_req` with the defined zone and options, e.g.: `limit_req zone=custom_rate_limit_zone burst=2 nodelay;`

### Tiered rate limiting

Apply a site-wide rate limit whose budget depends on how much you trust the traffic source: exempt your home market and your own apps, give trusted regions and search engine crawlers a generous budget, and progressively throttle lower-trust regions and AI crawlers. This is a baseline defence against distributed scraping and abuse, where any single IP makes few requests but the source is a useful risk signal.

It is disabled by default. Enable it globally with `nginx_tiered_rate_limit` (or per user/domain by setting `nginx_tiered_rate_limit` on that level) and define an ordered list of tiers, low to high restriction. There are no tiers by default, so the applied rates are always explicit in the playbook — enabling the limit without tiers fails the preflight checks:

```yaml
nginx_tiered_rate_limit: true
nginx_tiered_rate_limit_key: ip
nginx_tiered_rate_limit_default_tier: standard
nginx_tiered_rate_limit_tiers:
  - name: unlimited
    rate: false
    ips: "{{ ansible_local.base.searchbots.ip_ranges_grouped }}" # Search engine crawlers
    countries: ["NL", "BE", "DE"]
  - name: trusted
    rate: "30r/s"
    burst: "120"
    continents: ["EU", "NA"]
  - name: standard
    rate: "20r/s"
    burst: "60"
  - name: restricted
    rate: "20r/s"
    burst: "60"
    user_agents: ["example-agent"]
    ips: "{{ ansible_local.base.aibots.ip_ranges_grouped }}" # AI crawlers
    key: tier # One shared budget for the whole tier
  - name: untrusted
    rate: "10r/s"
    continents: ["AS", "AF"]
    countries: ["RU"] # Explicit country, even though Russia is in the Europe continent list
    key: tier # One shared budget for the whole tier
```

#### Tier assignment

Every request is assigned to exactly one tier. Three signals can assign a tier, strongest wins:

1. Client IP (`ips`): plain addresses or CIDR ranges, matched with the nginx `geo` module. Accepts a flat list or a mapping of `label: [ranges]` — with a mapping, each group is preceded by a `# <tier>: <label>` comment in the generated config. The strongest signal because the source IP cannot be spoofed — use this when the assignment must be tamper-proof, such as crawler allowances.
2. User agent (`user_agents`): matched case-insensitively as whole words anywhere in the `User-Agent` header (the same matching used for `nginx_greedy_user_agents_extra`). Trivially spoofed: a forged user agent gets that tier's budget instead of its country tier, so weigh what an abuser could gain from it.
3. Geolocation (`continents` and `countries`): the country of the client IP via the GeoIP module. The module only exposes country codes, so continents are expanded into their countries by the role (`nginx_geoip_continent_countries`); explicit `countries` win over `continents`. Countries not matched by any tier land in `nginx_tiered_rate_limit_default_tier`.

In the example above this means: search engine crawlers get the `trusted` budget no matter where they crawl from, AI crawlers and the user agent `example-agent` are pinned to `restricted` even when fetching from the EU.

The crawler IP lists come from the base role, which fetches the published ranges of the major search engines and AI vendors at deploy time and persists them as local facts: enable `base_facts_searchbots` and/or `base_facts_aibots` and use `ansible_local.base.<searchbots|aibots>.ip_ranges` (flat) or `.ip_ranges_grouped` (labeled per vendor, rendered with a comment per group); alternatively, supply a static list. Crawlers do not have to join a geographic tier — a dedicated tier gives them their own budget:

```yaml
  - name: aibots
    rate: "5r/s"
    key: tier # One shared budget for all matched crawlers
    ips: "{{ ansible_local.base.aibots.ip_ranges_grouped }}"
```

#### Counters

Each tier with a `rate` becomes a `limit_req` zone. Rates use the nginx units `r/s` or `r/m`; a rate below one request per second can only be expressed in `r/m` (for example `30r/m`), nginx does not accept fractional rates. Note that the limit applies to every request including static assets (unless scoped with paths, see below), so a single page view can be dozens of requests — size `burst` to absorb a full page load. A request is only counted by the single zone matching its tier (other tiers' keys evaluate to an empty string, which nginx does not count), so all tiers' `limit_req` lines coexist safely. Tiers with `rate: false` get no zone and are never limited. Rejections return the status from `nginx_limit_req_status` (429 by default). To calibrate rates safely on production traffic, enable `nginx_limit_req_dry_run` (per-user/domain overridable): requests are counted and over-limit hits are logged to the error log, but nothing is rejected. It applies to all `limit_req` directives in the vhost, so it also covers the greedy user agent limit and custom zones.

What shares a counter is controlled by `nginx_tiered_rate_limit_key` (per-tier overridable via `key`):

- `ip` (default): every client IP gets its own counter at the tier's rate (classic per-IP limiting).
- `country`: all traffic from the same country shares one counter, so the tier's rate is a per-country budget. A tier covering a continent gives each of its countries its own counter. IPs GeoIP cannot resolve share a single fallback counter in their tier (they are routed to `nginx_tiered_rate_limit_default_tier`).
- `continent`: all traffic from the same continent shares one counter, so the tier's rate is a per-continent budget (derived from the country code via `nginx_geoip_continent_countries`). A tier covering multiple continents gives each continent its own counter. Unresolvable IPs share a single fallback counter in their tier.
- `geo`: one counter per configured geographic unit. Countries matched through a tier's `continents` share their continent's counter, while countries explicitly listed in any tier's `countries` get their own. Countries not matched by any tier (the default tier) share their continent's counter. Use this for tiers that combine `continents` and `countries` — for example `continents: ["AS", "AF"]` plus `countries: ["RU"]` yields separate AS, AF and RU counters.
- `tier`: all traffic in the tier shares a single counter, so the tier's rate is the total budget for the whole tier.

Note that rates mean different things per key: with `ip` the rate bounds a single client (size it to one visitor's page loads), while the shared keys cap an entire traffic class across all vhosts on the server (size them to the total expected class throughput, measured with `nginx_limit_req_dry_run`, rather than per-visitor intuition). Shared keys suit crawler classes and deliberate chokes for low-trust regions; avoid them for tiers serving regular visitors, where one busy site would exhaust the budget for all other sites.

#### Limiting specific paths

The limit can optionally be restricted to specific URI paths via `nginx_tiered_rate_limit_paths` (default `[]` = all URIs). Entries are case-sensitive regular expressions anchored to the start of the normalized URI, so plain prefixes work as-is. The list can be overridden per user or per domain; an explicit `[]` on a domain restores limiting on all URIs there. Paths only take effect for domains where the tiered rate limit itself is enabled (setting only paths does not enable it):

```yaml
nginx_tiered_rate_limit_paths: ["/api/"] # baseline: only limit /api/*
users:
  - name: example
    domains:
      - name: example.com
        nginx_tiered_rate_limit_paths: ["/wp-login.php", "/xmlrpc.php"] # domain-specific paths
      - name: example.net
        nginx_tiered_rate_limit_paths: [] # limit all URIs for this domain
```

Requests outside the configured paths are not counted at all (the zone key evaluates to an empty string). Path selection uses the server's primary name (`$server_name`), so it applies equally to requests arriving via domain aliases.

#### Notes

- Enabling this automatically compiles the GeoIP module and emits `geoip_country`.
- Zone names are `tier_<tier>` — do not reuse those names in `nginx_rate_limit`.
- Geolocation and IP matching use the connecting IP. Behind a proxy/load balancer, set `nginx_set_real_ip_from` / `nginx_real_ip_header` so the real client is evaluated.
- Make sure crawler user agents are not simultaneously matched by the greedy user agent list, as both limits would apply.

## Routing, caching, and logic

### Custom upstream support

Define the custom upstreams on the user level. Servers can be defined as sockets and addresses. The options for servers can be found in the [Nginx documentation](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#server). An option witout a parameter such as `backup` can be defined as `backup: true`.

The custom upstreams can be used via the `custom_upstream_proxy()` macro call. Specific proxy parameters are automatically applied if they are defined on the domain level. Since custom upstreams are probably used within different location blocks and we can't define location blocks in the domains list, an application template is used to implement the specific location blocks each location block calls the specific custom upstream.

Example:

```yaml
users:
  - name: example
    uid: 1500
    nginx_upstreams:
      - name: app_server
        servers:
          - socket: "unix:/var/socket/app_server.sock"
            options:
              fail_timeout: 0
          - address: "127.0.0.1:8081"
            options:
              fail_timeout: 0
              backup: true
        keepalive: 20
        balancing_method: "least_conn"
      - name: legacy_app_server
        servers:
          - socket: "unix:/var/socket/webshop_legacy.sock"
            options:
              fail_timeout: 0
    domains:
      - name: customupstream.nl
        application: custom_upstream
        application_custom_php: true
        proxy_hide_headers:
          - "Test-Header"
        aliases:
          - www.customupstream.nl
```

The `custom_upstream` (name as desired) application template can call `nginx_upstream_proxy` with the upstream name:

```jinja2
{% from "templates/macros/upstream_proxy.j2" import nginx_upstream_proxy with context %}

  location /legacy {
{{ nginx_upstream_proxy('legacy_app_server') }}
  }

  location / {
{{ nginx_upstream_proxy('app_server') }}
  }
```

### Multiple endpoints per domain

Define the different nginx upstreams under `nginx_upstreams` on the user level and add `uris` under the domain to link the paths to the nginx upstream. Alternatively, define `proxy_url` directly on a `uris` item to proxy a path to a URL without a named upstream.

Example:

```yaml
users:
  - name: "example"
    uid: "2100"
    nginx_upstreams:
      - name: test1
        servers:
          - address: 127.0.0.1:21010
      - name: test2
        servers:
          - address: 127.0.0.1:21011
    domains:
      - name: "cms-test01.example.nl"
        uris:
          - path: /some/path
            nginx_upstream: test1
            restricted: true
          - path: /some/other/path
            nginx_upstream: test2
          - path: /some/proxied/path
            proxy_url: http://127.0.0.1:21012
```

## Extensions

### Passenger Enterprise Support

To use Passenger Enterprise, a download key and license file are required. Place the download key at `/root/.passenger-enterprise-download-key` and the license at `/root/.passenger-enterprise-license`. The role will fail if these files are missing. Download them from the Passenger Enterprise user portal.

## Testing

Run Molecule from the role directory:

```shell
molecule test
```
