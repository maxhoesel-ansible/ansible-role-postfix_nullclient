maxhoesel.postfix_nullclient
=========

A very minimal role to install and configure postfix as a nullclient (send mails from localhost to a remote server).
Supports SSL/TLS auth and multi-instance operation as the primary instance.

Requirements
------------

- A recent Ansible version. This role supports the 2 most recent major Ansible releases.
  Older versions might still work, but are not supported
- A host running:
  - Ubuntu 20.04 LTS or newer
  - Debian 11 or newer
  - Other distributions may work, but are not supported (Feel free to add support with a PR!)

Role Variables
--------------

### Relayhost and Authentication

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `postfix_relayhost` | Hostname or IP address of the host to send mails to | undefined | X |
| `postfix_port` | Optionally overwrite the automatically selected port | see below | |
| `postfix_relayhost_auth` | Set to yes if you need to login with a username and password | `true` | |
| `postfix_relayhost_username` | Username used for login on the relayhost | undefined | If `postfix_relayhost_auth` is `true` |
| `postfix_relayhost_password` | Password used for login on the relayhost | undefined | If `postfix_relayhost_auth` is `true` |

### Domain Management

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `postfix_masquerade_domains` | Truncate subdomains when sending mail for these domains | `[]` | |

#### Port Auto-Selection

This role will automatically set the remote port to connect to based on the connection method used:

- If `postfix_tls` is `false` => Port 25
- If `postfix_tls` is `true` and `postfix_tls_mode` is:
  - `SSL/TLS` (implicit TLS) => Port 465
  - `STARTTLS` (explicit TLS) => Port 587

### TLS

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `postfix_tls` | Whether to use TLS when connecting to the relayhost | `true` | |
| `postfix_tls_mode` | Whether to use an implicit (SSL/TLS) or explicit/upgrade (STARTTLS) TLS connetion. | `SSL/TLS` | |
| `postfix_tls_verify_cert` | Whether to verify the remote TLS cert. Only turn this off if you want to use a self-signed certificate and know what you are doing | `true` | |
| `postfix_tls_custom_cafile` | Optionally provide a custom CA cert for the relayhost to postfix. If unset, will use the systems default CA certstore | `""` | |

### Multi-Instance

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `postfix_multi_instance_enable` | Whether to enable support for multi-instance operations | `true` | |
| `postfix_multi_instance_list` | A list of postfix instances as paths to their configuration directory. Overrides `postfix_multi_instance_search` | `[]` | |
| `postfix_multi_instance_search_path` |Search for postfix multi-instance direcories in this path (all dirs matching `postfix-*`). `postfix_multi_instance_list` overrides this | `"/etc"` | |


Example Playbook
----------------

```yaml
- hosts: all
  tasks:
    - name: Install postfix
      include_role:
        name: maxhoesel.postfix_nullclient
      vars:
        postfix_relayhost: smtp.gmail.com
        postfix_relayhost_username: youraddress@gmail.com
        postfix_relayhost_password: your gmail password
```

License
-------

GPL 3 or later

Author Information
------------------

Created and maintained by Max Hösel (@maxhoesel)
