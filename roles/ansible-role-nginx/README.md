# Nginx

This role will install and configure Nginx.

## Role variables
| Parameter                                       | Default                                  | Description                                                                                                                                                   |
|-------------------------------------------------|------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nginx_access_log_format                         | combined                                 | The value for the access log format.                                                                                                                          |
| nginx_access_log_format_definitions             | `[]`                                     | A list of custom logformats.                                                                                                                                  |
| nginx_application_php                           | true                                     | Whether to add the PHP location block.                                                                                                                        |
| nginx_auth_basic_message                        | "Authentication required"                | Set auth basic message.                                                                                                                                       |
| nginx_brotli_module                             | false                                    | Include Brotli module (default: false).                                                                                                                       |
| nginx_brotli_module_url                         |                                          | The download location of the brotli module source.                                                                                                            |
| nginx_buffer_logs                               | true                                     | Buffer the writing to the log files.                                                                                                                          |
| nginx_cleanup                                   | true                                     | Whether to run the cleanup tasks.                                                                                                                             |
| nginx_client_max_body_size                      | 64m                                      | The nginx client_max_body_size.                                                                                                                               |
| nginx_configure                                 |                                          | The options used in the configure command.                                                                                                                    |
| nginx_default_headers                           | false                                    | The nginx default headers used for the default vhost.                                                                                                         |
| nginx_default_php                               | true                                     | Whether to include the PHP location in the default vhost.                                                                                                     |
| nginx_default_redirect                          |                                          | Redirect default vhost to this URL.                                                                                                                           |
| nginx_default_server                            | true                                     | Add default server                                                                                                                                            |
| nginx_default_ssl                               | false                                    | Redirect default vhost to https.                                                                                                                              |
| nginx_fastcgi_cache                             | false                                    | Enable fastcgi cache for all users.                                                                                                                           |
| nginx_fastcgi_cache_base_dir                    | /var/cache/nginx                         | Define the base directory where all the cache files are stored.                                                                                               |
| nginx_fastcgi_cache_inactive                    | 1m                                       | Define the global inactive (expire) time of the cache.                                                                                                        |
| nginx_fastcgi_cache_key                         | $scheme$request_method$host$request_uri  | Define the global cache key which is used to generate a cache key hash.                                                                                       |
| nginx_fastcgi_cache_size                        | 2m                                       | Define the global fastcgi cache size.                                                                                                                         |
| nginx_fastcgi_cache_valid                       | ['200 1m']                               | Define the global cache valid configuration (only status 200 for 1 minute).                                                                                   |
| nginx_fastcgi_hide_headers                      | []                                       | Define the global headers which FastCGI shouldn't pass on to a client.                                                                                        |
| nginx_fastcgi_ignore_headers                    | []                                       | Define the global headers which caching should ignore.                                                                                                        |
| nginx_geoip                                     | false                                    | Enable geoip blocking globally.                                                                                                                               |
| nginx_geoip_database_path                       | `/usr/share/GeoIP/GeoIPv6.dat`           | Path of the GeoIP database file. Note: IPv6 contains both IPv4 and IPv6 addresses and should be used for servers with IPv6 enabled.                           |
| nginx_geoip_return_status                       | 403                                      | The http return status code when an IP is blocked by GeoIP.                                                                                                   |
| nginx_geoip_default                             | allow                                    | The default for GeoIP blocking. Allow means allow everything *except* what is in the list. Deny means deny everything *except* what is in the list.           |
| nginx_geoip_countries                           | []                                       | List of countries to deny or allow depending on the `nginx_geoip_default` variable.                                                                           |
| nginx_global_fastcgi_settings                   |                                          | Set nginx fastcgi settings globally.                                                                                                                          |
| nginx_global_headers                            |                                          | Set nginx headers globally.                                                                                                                                   |
| nginx_graylog_address                           | false                                    | The Graylog server for nginx logging.                                                                                                                         |
| nginx_graylog_port_access                       | 12202                                    | The Graylog server port for access logs.                                                                                                                      |
| nginx_graylog_port_error                        | 12203                                    | The Graylog server port for error logs.                                                                                                                       |
| nginx_gzip_comp_level                           | 4                                        | Sets a gzip compression level of a response. Acceptable values are in the range from 1 to 9.                                                                  |
| nginx_gzip_min_length                           | 256                                      | Sets the minimum length of a response that will be gzipped.                                                                                                   |
| nginx_gzip_proxied                              | any                                      | Enables or disables gzipping of responses for proxied requests depending on the request and response.                                                         |
| nginx_gzip_types_extra                          |                                          | Additional content types to gzip.                                                                                                                             |
| nginx_headers_more_module                       | false                                    | Include HttpHeadersMoreModule                                                                                                                                 |
| nginx_headers_more_module_url                   |                                          | The download location of the headers_more_module source.                                                                                                      |
| nginx_headers_more_module_version               |                                          | The version of headers_more_module to install.                                                                                                                |
| nginx_http_auth_request_module                  | false                                    | Include HttpAuthRequest module.                                                                                                                               |
| nginx_http_image_filter_module                  | false                                    | Include HttpImageFilter module.                                                                                                                               |
| nginx_limit_open_files                          | 4096                                     | The maximum amount of open file descriptors (soft limit).                                                                                                     |
| nginx_listen_address                            | [::]                                     | The listen address that all vhosts will use.                                                                                                                  |
| nginx_listen_port_http                          | 80                                       | The listen port that all http vhosts will use.                                                                                                                |
| nginx_listen_port_https                         | 443                                      | The listen port that all https vhosts will use.                                                                                                               |
| nginx_log_format_graylog                        | `{{ nginx_log_format_graylog_default }}` | The logformat string when using Graylog as a backend. Pre-defined formats are `nginx_log_format_graylog_default` and `nginx_log_format_graylog_request_body`. |
| nginx_logrotate                                 | true                                     | Whether to ensure a logrotate configuration.                                                                                                                  |
| nginx_logrotate_frequency                       | daily                                    | The frequency of the log rotation.                                                                                                                            |
| nginx_logrotate_rotate                          | 7                                        | The amount of days to keep for log rotation.                                                                                                                  |
| nginx_map_hash_bucket_size                      | 64                                       | The value for map_hash_bucket_size.                                                                                                                           |
| nginx_mime_types_extra                          | []                                       | Extra Mime types for extensions.                                                                                                                              |
| nginx_modsecurity_audit_log_format              | Native                                   | Audit log format (Native/JSON)                                                                                                                                |
| nginx_modsecurity_audit_log_parts               | ABIJDEFHZ                                | The ModSecurity SecAuditLogParts setting.                                                                                                                     |
| nginx_modsecurity_audit_log_relevant_status     |                                          | Configures which response status code is to be considered relevant for the purpose of audit logging.                                                          |
| nginx_modsecurity_bad_agents_allowed            | []                                       | Override `nginx_modsecurity_bad_agents_default` to allow agents that were on the blacklist.                                                                   |
| nginx_modsecurity_bad_agents_extra              | []                                       | Add extra bad agents to the default list `nginx_modsecurity_bad_agents_default`.                                                                              |
| nginx_modsecurity_block_missing_user_agent      | false                                    | Whether to block user agents that are empty or contain only blank spaces.                                                                                     |
| nginx_modsecurity_crs_url                       |                                          | The download location of the ModSecurity Core Rule Set.                                                                                                       |
| nginx_modsecurity_crs_version                   | 4.10.0                                   | The version of the ModSecurity Core Rule Set to install.                                                                                                      |
| nginx_modsecurity_custom_rules                  | []                                       | Custom Secrules Modsecurity will use.                                                                                                                         |
| nginx_modsecurity_datadir                       | /tmp/                                    | Path where persistent data (e.g., IP address data, session data, and so on) is to be stored.                                                                  |
| nginx_modsecurity_module_url                    |                                          | The download location of the ModSecurity module source.                                                                                                       |
| nginx_modsecurity_module_version                |                                          | The version of the ModSecurity module to install.                                                                                                             |
| nginx_modsecurity_request_argument_limit        | 1000                                     | Configures the maximum amount of args provided in an request.                                                                                                 |
| nginx_modsecurity_request_body_access           | On                                       | Configures whether request bodies will be buffered and processed by ModSecurity.                                                                              |
| nginx_modsecurity_request_body_limit            | 13107200                                 | Configures the maximum request body size ModSecurity will accept for buffering.                                                                               |
| nginx_modsecurity_request_body_limit_action     | ProcessPartial                           | Controls what happens once a request body limit, configured with SecRequestBodyLimit, is encountered.  [Reject/ProcessPartial]                                |
| nginx_modsecurity_request_body_no_files_limit   | 131072                                   | Configures the maximum request body size ModSecurity will accept for buffering, excluding the size of any files being transported in the request.             |
| nginx_modsecurity_response_body_access          | On                                       | Configures whether response bodies will be buffered and processed by ModSecurity.                                                                             |
| nginx_modsecurity_response_body_limit           | 524288                                   | Configures the maximum response body size that ModSecurity will store in memory. (bytes)                                                                      |
| nginx_modsecurity_response_body_limit_action    | "ProcessPartial"                         | Controls what happens once a response body limit, configured with SecResponseBodyLimit, is encountered.  [Reject/ProcessPartial]                              |
| nginx_modsecurity_response_mime_types           | ["text/plain", "text/html", text/xml"]   | Configures which MIME types are to be considered for response body buffering.                                                                                 |
| nginx_modsecurity_rule_engine                   | DetectionOnly                            | What mode the ModSecurity rule engine should be in.                                                                                                           |
| nginx_modsecurity_ruleset_bad_agents            | false                                    | Load the ModSecurity bad agents ruleset.                                                                                                                      |
| nginx_modsecurity_ruleset_owasp_crs             | false                                    | Load the ModSecurity OWASP Core Rule Set.                                                                                                                     |
| nginx_modsecurity_ruleset_owasp_crs_global      | true                                     | Include / apply the ModSecurity OWASP Core Rule Set globally.                                                                                                 |
| nginx_modsecurity_tmpdir                        | /tmp/                                    | Configures the directory where temporary files will be created.                                                                                               |
| nginx_modsecurity_url                           |                                          | The download location of the ModSecurity library.                                                                                                             |
| nginx_modsecurity_version                       |                                          | The version of ModSecurity to install.                                                                                                                        |
| nginx_monitoring_allowed_addresses_extra        | []                                       | Gives the defined addresses access to the `/monitoring` location in the default vhost.                                                                        |
| nginx_openssl_checksum                          |                                          | The md5 checksum of the OpenSSL archive.                                                                                                                      |
| nginx_openssl_source                            | false                                    | Include OpenSSL from source.                                                                                                                                  |
| nginx_openssl_url                               |                                          | The download location of OpenSSL.                                                                                                                             |
| nginx_openssl_version                           |                                          | The version of OpenSSL to install.                                                                                                                            |
| nginx_passenger                                 | false                                    | Include the Phusion Passenger config.                                                                                                                         |
| nginx_passenger_enterprise_module               | false                                    | Include Phusion Passenger Enterprise module.                                                                                                                  |
| nginx_passenger_enterprise_module_version       | 6.0.7                                    | The version of Phusion Passenger Enterprise module to install.                                                                                                |
| nginx_passenger_enterprise_module_version_major | 6.0                                      | The major version of the Phusion Passenger enterprise module to install.                                                                                      |
| nginx_passenger_module                          | false                                    | Include Phusion Passenger module.                                                                                                                             |
| nginx_passenger_module_url                      |                                          | The download location of the Phusion Passenger module source.                                                                                                 |
| nginx_passenger_module_version                  |                                          | The version of Phusion Passenger module to install.                                                                                                           |
| nginx_passenger_module_version_major            |                                          | The major version of Phusion Passenger module to install.                                                                                                     |
| nginx_proxy_cache                               | false                                    | Enable proxy cache for all users.                                                                                                                             |
| nginx_proxy_cache_base_dir                      | /var/cache/nginx                         | Define the base directory where all the cache files are stored.                                                                                               |
| nginx_proxy_cache_inactive                      | 1m                                       | Define the global inactive (expire) time of the cache.                                                                                                        |
| nginx_proxy_cache_key                           | $scheme$request_method$host$request_uri  | Define the global cache key which is used to generate a cache key hash.                                                                                       |
| nginx_proxy_cache_size                          | 2m                                       | Define the global proxy cache size.                                                                                                                           |
| nginx_proxy_cache_valid                         | ['200 1m']                               | Define the global cache valid configuration (only status 200 for 1 minute).                                                                                   |
| nginx_proxy_hide_headers                        | []                                       | Define the global headers which the proxy shouldn't pass on to a client.                                                                                      |
| nginx_proxy_ignore_headers                      | []                                       | Define the global headers which caching should ignore.                                                                                                        |
| nginx_rate_limit                                |                                          | The nginx rate limit.                                                                                                                                         |
| nginx_real_ip_header                            | X-Forwarded-For                          | Set the nginx real_ip_header.                                                                                                                                 |
| nginx_release                                   | "stable"                                 | Define the release line of nginx. "stable" for production, "latest" for dev/test/accp/staging servers.                                                        |
| nginx_restart_graceful                          | true                                     | Try to restart Nginx gracefully without interruption.                                                                                                         |
| nginx_role_mode                                 | installation                             | Specify how the role behaves.                                                                                                                                 |
| nginx_rtmp_module_url                           |                                          | The download location of rtmp module source.                                                                                                                  |
| nginx_rtmp_module_version                       |                                          | The version of the rtmp module to install.                                                                                                                    |
| nginx_security_deny_file_extensions             | `[bak,save,swp,sql,sql.gz]`              | List of file extensions to deny access to.                                                                                                                    |
| nginx_security_txt                              | false                                    | Whether to enable and include the default security.txt.                                                                                                       |
| nginx_security_txt_acknowledgments_url          |                                          | Define a link to a page where you thank security researchers (optional).                                                                                      |
| nginx_security_txt_canonical_url                |                                          | Define the URL for the security.txt file. It is important to include this if you are digitally signing the security.txt file (optional).                      |
| nginx_security_txt_contact                      | []                                       | Define a contact e-mail address, phone number or URL for security issues (1 contact required).                                                                |
| nginx_security_txt_expires                      | {{ year +1 }}-[01-06]-01T00:00:00.000Z   | Define a expire date and time when the content should be considered stale (required).                                                                         |
| nginx_security_txt_hiring_url                   |                                          | Define a link to any job openings in your organisation (optional).                                                                                            |
| nginx_security_txt_languages                    |                                          | Define a comma-separated list of language codes (optional).                                                                                                   |
| nginx_security_txt_pgp_encryption_url           |                                          | Define a link to a key which security researchers should use to securely talk to you (optional).                                                              |
| nginx_security_txt_policy_url                   |                                          | Define a link to a policy detailing what security researchers should do when reporting security issues (optional).                                            |
| nginx_server_names_hash_bucket_size             | 128                                      | The value for server_names_hash_bucket_size.                                                                                                                  |
| nginx_service                                   | true                                     | Whether to install and use a systemd service.                                                                                                                 |
| nginx_set_real_ip_from                          | []                                       | List of IP's which are added to the nginx real_ip_from header.                                                                                                |
| nginx_shared_home                               | false                                    | Option if nginx_user_logs is set to true and the users are shared. This ensures user logs tasks are only run on one host.                                     |
| nginx_ssl_ciphers                               |                                          | The SSL/TLS ciphers that nginx will support.                                                                                                                  |
| nginx_ssl_prefer_server_ciphers                 | false                                    | The nginx ssl_prefer_server_ciphers.                                                                                                                          |
| nginx_ssl_protocols                             |                                          | The SSL/TLS protocols that nginx will support.                                                                                                                |
| nginx_timeout                                   | 20s                                      | The nginx timeout settings (default: 20s).                                                                                                                    |
| nginx_url                                       |                                          | The download location of the Nginx source.                                                                                                                    |
| nginx_user_logs                                 | false                                    | The nginx logs in users home dir.                                                                                                                             |
| nginx_version                                   | "1.28.0"                                 | The version of Nginx to install.                                                                                                                              |
| nginx_version_major                             | "1.28"                                   | The major release of Nginx to install.                                                                                                                        |
| nginx_worker_connections                        | 1024                                     | The nginx worker_connections (default: 1024).                                                                                                                 |


## GeoIP blocking via `users.yml`
Warning: if you enable GeoIP blocking globally it will be active for all domains! The `nginx_variables_hash_bucket_size` is automatically increased when GeoIP blocking is active. You may need to increase `nginx_variables_hash_bucket_size` even more with a lot of domains.

You can enable it per domain or user. Look at the example below:
```yml
users:
  - name: example
    uid: 1500
    php_max_children: "32"
    composer_version: "2"
    domains:
      - name: example.nl
      - name: admin.example.nl
        geoip: true
        geoip_default: "deny" # Set default to deny every country
        geoip_countries:      # Except countries in this list, which will be allowed
          - "NL"
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

## Passenger Enterprise support

For Passenger Enterprise to work a download key and a license file are needed. The download key must be located in `/root/.passenger-enterprise-download-key` and the license file in `/root/.passenger-enterprise-license`. Without those files the role will fail with a message that those files are missing. The files can be downloaded from the Passenger Enterprise user portal.

## Testing

This role can be tested with Molecule. Install Molecule and dependencies and run `molecule test`.
