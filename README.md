sensson.directadmin
=========

This module can be used to install DirectAdmin. It does not set an admin
password so you would have to set this yourself once the server is created
or retrieve it from /usr/local/directadmin/scripts/setup.txt.

It supports Ubuntu 20, CentOS 8 stream, Rocky Linux 8 and AlmaLinux 8.

Requirements
------------

We assume you have a valid DirectAdmin license.

Role Variables
--------------

* `license`: The DirectAdmin client id.
* `directadmin_configuration`: All DirectAdmin configurations
* `directadmin_custombuild_options`: All CustomBuild options.
* `directadmin_phpextensions`: All PHP extensions.
* `directadmin_lets_encrypt_host`: Create a certificate for the host's
  domain. If set to false it will not be created. Defaults to true.
* `directadmin_include_modsecurity`: Include ModSecurity playbook.
* `modsecurity`: Set to `'yes'` to enable.
* `modsecurity_ruleset`: This is the ruleset from DirectAdmin.
* `directadmin_packages`: User packages, grouped per (reseller) user. Each
  package is written as `key=value` to
  `/usr/local/directadmin/data/users/<user>/packages/<package>.pkg`. The user
  must already exist in DirectAdmin. Packages on disk that are not in this
  variable are removed (except those in `directadmin_packages_keep`).
* `directadmin_packages_keep`: Package names that are never removed even when
  not in `directadmin_packages`. Defaults to `[default]`.
* `directadmin_package_keys_extra`: Additional valid package keys to allow on
  top of the built-in list, for keys introduced by newer DirectAdmin versions.

Example:

```yaml
directadmin_packages:
  sensson:
    basic:
      bandwidth: 1000
      quota: 500
      domainptr: 5
      ftp: 10
      mysql: 5
      nemails: 25
      nsubdomains: 10
      ssl: ON
      cgi: ON
      php: ON
    pro:
      bandwidth: unlimited
      quota: unlimited
      ssl: ON
```

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: directadmin
  roles:
    - sensson.directadmin
```

We recommend using host variables to set your license. For example:

```yaml
[directadmin]
server.fqdn.com license=abcdefghijklmnopqrstuvw
```

License
-------

MIT

Author Information
------------------

Sensson is a hosting company. We try to share our work whenever possible. We
do not intend to offer commercial support on our open source modules. We do
welcome all pull requests though and will attempt to help whenever possible.
