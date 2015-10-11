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
TODO

## Disclaimer
### Permissions
This tool needs root permissions as it need to `chown` directories.
Considering this, you need to really trust this if you want to use it.

### Security and Stability
This tool is used on my own personal server, so I try to keep it secure and stable, but it is not mistake-free.
