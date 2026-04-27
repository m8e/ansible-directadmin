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
* `directadmin_accounts`: Resellers and admins to create. Existing accounts
  (detected via `/usr/local/directadmin/data/users/<name>/`) are left
  untouched. Authentication uses an API URL minted on the server with
  `da api-url`, so no credentials are needed.
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
directadmin_accounts:
  sensson:
    type: reseller
    email: hostmaster@sensson.net
    domain: sensson.net
    package: pro
    ip: 1.2.3.4
    password: "{{ vault_sensson_password }}"
  superadmin:
    type: admin
    email: admin@example.com
    password: "{{ vault_superadmin_password }}"

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

Storing passwords with ansible-vault
------------------------------------

Account passwords should not be committed to git in plain text. Use
`ansible-vault` to encrypt them.

Create an encrypted file alongside your group vars, for example
`group_vars/directadmin/vault.yml`:

```bash
ansible-vault create group_vars/directadmin/vault.yml
```

Put the secret values in there, prefixed with `vault_` by convention:

```yaml
vault_sensson_password: "AbcXyz123!"
vault_superadmin_password: "Sup3rS3cret"
```

In the regular (unencrypted) `group_vars/directadmin/vars.yml` you reference
those vault variables:

```yaml
directadmin_accounts:
  sensson:
    type: reseller
    password: "{{ vault_sensson_password }}"
    ...
```

Run the playbook with one of:

```bash
ansible-playbook site.yml --ask-vault-pass
ansible-playbook site.yml --vault-password-file ~/.vault_pass
```

Make sure the password file is in `.gitignore`. Edit existing secrets with
`ansible-vault edit group_vars/directadmin/vault.yml`.

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
