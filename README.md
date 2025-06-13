Ansible: Certbot Role for OpenCast
==============================

[![lint](https://github.com/elan-ev/opencast_certbot/actions/workflows/lint.yml/badge.svg)](https://github.com/elan-ev/opencast_certbot/actions/workflows/lint.yml)

This Ansible role configures TLS certificate renewal via certbot for Opencast.

Role Variables
--------------

- `elan_certbot_letsencrypt_email`
  - Email address for Let's Encrypt account (_required_)
  - This is used by Let's Encrypt to send certificate expiration warnings if necessary.
- `opencast_certbot_deploy_hook`
  - Command to run after certbot has updated certificate. The value is optional.
    Already created certificates will not be updated.
- `opencast_certbot_enable_epel`
  - On RedHat system certbot package is living in the EPEL repository. Here you can enable (value: `true`)
    or disable (value: `false`) installation of the `epel-release` package (default: `true`).
    On RedHat installation with Satellite this property can be handy.
  - On Debian based systems this property do nothing.
- `elan_certbot_domains`: A list specifying the domains for which the certificate should be valid. Defaults to ["{{ inventory_hostname }}"].
- `elan_certbot_expand_existing`: A boolean flag that you can use e.g. as extra variable when running a playbook, to force certbot to expand already existing certificates. You should not set this to true as default, but only when you actually need it.
- `elan_certbot_ca`: You can specify if you want to use letsencrypt (the default) or use sectigo with eab for DFN ACME. You then also need to define `elan_certbot_eab_kid` and `elan_certbot_eab_hmac`.

Dependencies
------------

Certbot runs an ACME Webroot challenge to obtain a certificate. This requires a web server that
exposes the path `/var/lib/nginx/` (especially the subdirectory `.well-known/acme-challenge`)
over HTTP. This can be done using the Ansible role `elan.opencast_nginx` or others.
The `elan.opencast_nginx` role is no longer a hard dependency.

Example Playbook
----------------

Example of how to configure and use the role:

```yaml
- hosts: servers
  become: true
  roles:
    - role: elan.certbot
      elan_certbot_letsencrypt_email: admin@example.com
      opencast_certbot_deploy_hook: /usr/bin/systemctl reload nginx
```
