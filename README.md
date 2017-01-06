# ansible-communote

## init_ansible_setup.yml

This is a simple playbook to setup the ansible ssh key on hosts, to avoid the need to type passwords.   Use after building new instances.

```
$ ansible-playbook -k -K init_ansible_setup.yml --limit vault
SSH password: 
SUDO password[defaults to SSH password]: 
```
