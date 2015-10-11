# sites-mgr
sites-mgr is a project to help the management of Apache virtual hosts.

## Installation
### Getting github repository
```sh
git clone https://github.com/csdt/sites-mgr.git /opt/sites-mgr
```
### Creating symlinks
```sh
# Apache conf
ln -s /opt/sites-mgr/apache/load_every_sites.conf /etc/apache2/conf-available/
ln -s /opt/sites-mgr/apache/macro.conf /etc/apache2/conf-available/
# binaries
ln -s /opt/sites-mgr/sites /usr/local/sbin/
ln -s /opt/sites-mgr/domain /usr/local/sbin/
# completion
ln -s /opt/sites-mgr/sites.completion /etc/bash_completion.d/
# logrotate
ln -s /opt/sites-mgr/sites.logrotate /etc/logrotate.d/
```
### Enabling Apache conf
```sh
a2enconf load_every_sites
a2enconf macro
```
### Reloading services
```sh
# reloading apache conf
apache2ctl graceful
# reloading logrotate
logrotate -f -v /etc/logrotate.d/sites.logrotate
# reloading bash PATH cache
hash -d sites
hash -d domain
# reloading bash completion
. /etc/bash_completion.d/sites.completion
```
## Usage
Every actions on sites doesn't reload apache configuration, so you need to do it manually:
```sh
apache2ctl graceful
```
### List domains
```sh
domain ls
```
### Create domain
```sh
domain new <domain-name> <ip>
```
This will create a directory <domain-name> inside `/var/www/`.
If you want this domain to be available on all IPs, just put `'*'` as ip.
### Get path of a domain
```sh
domain path <domain-name>
```
### Delete domain
```sh
domain remove <domain-name>
```
### List sites of a domain
```sh
sites ls <domain-name>
```
### Create a site
```sh
sites new <domain-name> <site-name>
# for example: sites new example.com subdomain
# or with the FQDN syntax:
# sites new subdomain.example.com
```
Options:
- `--no-https`: don't configure https at all
- `--https`: configure https
- `--force-https`: force clients to use https
- `--private`: configure a private folder
- `--no-private`: don't use private folder
- `--user <username>`: configure site to be executed as the user <username>. It will be created if it doesn't exist.
- `--no-user`: configure site to be executed as the default user of apache (`www-data`)
- `--group <username>`: configure site to be executed as the group <username>. It will be created with the user if the `--user` option is used.
- `--no-group`: configure site to be executed as the default group of apache (`www-data`)
### Disable a site
```sh
sites disable <domain-name> <site-name>
# for example: sites disable example.com subdomain
# or with the FQDN syntax:
# sites disable subdomain.example.com
```
A disabled site won't be visible by Apache when reloading its configuration:
```sh
apache2ctl graceful
```
### Enable a site
```sh
sites enable <domain-name> <site-name>
# for example: sites enable example.com subdomain
# or with the FQDN syntax:
# sites enable subdomain.example.com
```
### Get status of a site
If you know if a site is enabled or disabled.
```sh
sites status <domain-name> <site-name>
# for example: sites status example.com subdomain
# or with the FQDN syntax:
# sites status subdomain.example.com
```
### Get path of a site
```sh
sites path <domain-name> <site-name>
# for example: sites path example.com subdomain
# or with the FQDN syntax:
# sites path subdomain.example.com
```
### Remove a site
```sh
sites remove <domain-name> <site-name>
# for example: sites remove example.com subdomain
# or with the FQDN syntax:
# sites remove subdomain.example.com
```
Options:
- `--ask`: ask user before actually remove it
- `--force`: force the removal (use `rm -rf` instead of `rm -r`)
- `--remove-user`: remove the user who owns the site. Removes it only if the user's home is the site path and user's UID is compatible with the script (between 10 000 and 20 000).
- `--keep-user`: Don't try to remove the user with the site.
### Generate ssl certificate
```sh
sites ssl generate <domain-name> <site-name>
# for example: sites ssl generate example.com subdomain
# or with the FQDN syntax:
# sites ssl generate subdomain.example.com
```
Try to sign it with your certification authority (used by `openssl ca`: `/etc/ssl/ca/cacert.pem`).
### Get info about a certificate
```sh
sites ssl info <domain-name> <site-name>
# for example: sites ssl info example.com subdomain
# or with the FQDN syntax:
# sites ssl info subdomain.example.com
```
### Revoke a certificate
```sh
sites ssl revoke <domain-name> <site-name>
# for example: sites ssl revoke example.com subdomain
# or with the FQDN syntax:
# sites ssl revoke subdomain.example.com
```
### Delete certificate files
```sh
sites ssl clear <domain-name> <site-name>
# for example: sites ssl clear example.com subdomain
# or with the FQDN syntax:
# sites ssl clear subdomain.example.com
```
Warning: this command doesn't revoke it and should be used carefully.
## Files
```
/var/www/
+-> domain1.com/
|    +-> .domain
|    +-> ip
|    +-> subdomain1/
|    |    +-> .site
|    |    +-> .owner
|    |    +-> conf/
|    |    |    +-> http.conf
|    |    |    +-> https.conf
|    |    +-> logs/
|    |    |    +-> access.log
|    |    |    +-> errors.log
|    |    +-> public/
|    |    |    +-> index.html
|    |    |    ...
|    |    +-> private/
|    |    +-> ssl/
|    |    |    +-> openssl.cnf
|    |    |    +-> private.key
|    |    |    +-> public.crt
|    |    |    +-> signing-request.csr
|    +-> subdomain2/
|         ...
+-> domain2.com/
     ...
```
## Dependencies
- `apache`
- `openssl`: to manage certificates
## Disclaimer
### Permissions
This tool needs root permissions as it need to `chown` directories.
Considering this, you need to really trust this if you want to use it.

### Security and Stability
This tool is used on my own personal server, so I try to keep it secure and stable, but it is not mistake-free.
