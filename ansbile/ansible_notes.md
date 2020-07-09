Ansible Notes
---

#### Run ansible with default private key and vault password
```
alias ansops='ansible-playbook --private-key /path/to/keypair.pem --vault-password-file /path/to/ansible-vault.pass --become'
```

#### Run ansible-console with default private key and vault password
```
alias ansrun='ansible-console --private-key /path/to/keypair.pem --vault-password-file /path/to/ansible-vault.pass --become'
```

#### Run Ansible playbook with --limit and --tags
```
ansops heylinux-all.yml -i hosts.heylinux --limit heylinux-devops-1 --tags common-etc
```

#### Encrypt a string with the vault password
```
ansible-vault encrypt_string --vault-password-file /path/to/ansible-vault.pass 'real_app_pass' --name 'app_pass'
```

#### Encrypt a file with the vault password
```
ansible-vault encrypt --vault-password-file /path/to/ansible-vault.pass protected.key
```

#### View a file with the vault password
```
ansible-vault view --vault-password-file /path/to/ansible-vault.pass protected.key
```

#### Decrypt a string app_pass from file encrypted.yml
```
ansible localhost -m debug -a var=app_pass -e @encrypted.yml --vault-password-file /path/to/ansible-vault.pass
```

#### Run ad-hoc command on local machine
```
ansible localhost --connection=local -m debug -a "msg={{ 'appname.conf.j2' | basename | regex_replace('.j2') }}"
```

#### Run ad-hoc command to get ansible_facts
```
ansible heylinux-devops-1 -i hosts.heylinux --private-key /path/to/keypair.pem -m setup
```
