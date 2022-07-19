# Certbot

Let's Encrypt certificates are managed with `certbot` as the `root` user. After a successful certificate request, certbot will show the location of the combined .pem file. The combined .pem file contains the private key, certificate and intermediate (chain) certificates.

### Playbook

Use the .pem file location in a playbook (users.yml) to enable a certificate (HTTPS).

```
- name: example.com
  docroot: /home/example/domains/example.com/public
  ssl: /etc/letsencrypt/combined/example_com.pem
```

### DNS

To obtain or renew a certificate, the A and AAAA records for a given domain must point to the IP address of the server.

### Examples

**Show current certificates**
```
certbot certificates
```
**Request a certificate**
```
certbot certonly --authenticator webroot -d example.com,www.example.com
```
**Expand existing certificate**
```
certbot certonly --authenticator webroot --expand -d example.com,www.example.com,sub1.example.com
```
**Force renew a certificate**
```
certbot certonly --authenticator webroot --force-renewal --cert-name example.com
```
**Delete a certificate**
```
certbot delete --cert-name example.com
```
