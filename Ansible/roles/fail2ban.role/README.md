DESCRIPTION
====

Installs and configures fail2ban


USAGE
===

* add role like a submodule to you playbook
* override default.yml variables

VARIABLES
===

```fail2ban_whitelist``` - set fail2ban whitelist.

```fail2ban_services```  - set services for monitoring



# Example


```
fail2ban_services:
  - name: "ssh"
    comment: "SSH service"
    enabled: True
    port: 'ssh'
    filter: 'sshd'
    logpath: '/var/log/auth.log'
    maxretry: 10

```
