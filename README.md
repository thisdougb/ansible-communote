# ansible-communote

This high level playbooks briefly described, and in the order they should be run in if you want to try this out.

## init_ansible_setup.yml

This is a simple playbook to setup the ansible ssh key on hosts, to avoid the need to type passwords.   Use after building new instances.

```
$(ansible-host) ansible-playbook -k -K init_ansible_setup.yml --limit vault
SSH password: 
SUDO password[defaults to SSH password]: 
```

## build_vault_instance.yml

This playbook will build, init and unseal an instance of HashiCorp Vault.   It will drop the vault keys into ./roles/.hashicorp_vault_keys.json (you need these keys to work on the vault).   Put the root_token k/v pair into a local Ansible Vault file under ./group_vars, this will allow subsequent playbooks to connect to HashiCorp Vault.

The gist, showing the steps and finally logging into the vault instance proving the status is unsealed and ready to use:
```
$(ansible-host) ansible-playbook build_vault_instance.yml

$(ansible-host) cat roles/.hashicorp_vault_keys.json 
{"keys": ["1804799548be7594b78f1f76a558bbf3588f2caa2114e98235aa57333a93c3a701", "eeb8f226827b73ea9307974392fef3c23b2b40ba59cb8d1c5ec4212f8316511902"],
 "keys_base64": ["GAR5lUi+dZS3jx92pVi781iPLKohFOmCNapXMzqTw6cB", "7rjyJoJ7c+qTB5dDkv7zwjsrQLpZy40cXsQhL4MWURkC"], "root_token": "97a9e11e-49fc-1434-dafe-9be0eccce519"}

$(ansible-host) mkdir -p group_vars/all
$(ansible-host) ansible-vault create group_vars/all/hashicorp_token.yml
root_token: "97a9e11e-49fc-1434-dafe-9be0eccce519"

$(ansible-host) vagrant ssh vault
Last login: Fri Jan  6 14:12:15 2017 from 172.28.128.4
[vagrant@vault ~]$ su -
Password: 
Last login: Tue Dec  6 13:39:33 GMT 2016 on tty1
[root@vault ~]# vault status
Sealed: false
Key Shares: 2
Key Threshold: 2
Unseal Progress: 0
Version: 0.6.2
Cluster Name: vault-cluster-abf8df0e
Cluster ID: e62f290c-02e1-ac0a-5995-60d6bf5ce88c

High-Availability Enabled: false
```
