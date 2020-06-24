Ansible Notes
---

#### Create an alias to run ansible with default root key
```
$ vim ~/.bashrc
alias ansops='ansible-playbook --private-key ~/workspace/credentials/heylinux.pem --vault-password-file ~/workspace/credentials/ansible_vault.pass --become'
```

#### Run Ansible playbook with --limit and --tags
```
$ ansops heylinux-all.yml -i hosts.heylinux --limit heylinux-devops-1 --tags common-etc
```

#### Encrypt a string with the vault password
```
$ ansible-vault encrypt_string --vault-password-file ~/workspace/credentials/ansible_vault.pass 'real_nexus_pass' --name 'nexus_pass'
```

#### Encrypt a file with the vault password
```
$ ansible-vault encrypt --vault-password-file ~/workspace/credentials/ansible_vault.pass protect_me.pem
```

#### View a file with the vault password
```
$ ansible-vault view --vault-password-file ~/workspace/credentials/ansible_vault.pass protect_me.pem
```

#### Decrypt a string nexus_pass from file encrypted.yml
```
$ ansible localhost -m debug -a var=nexus_pass -e @encrypted.yml --vault-password-file ~/workspace/credentials/ansible_vault.pass
```

#### Run ad-hoc command on local machine
```
$ ansible localhost --connection=local -m debug -a "msg={{ 'appname.conf.j2' | basename | regex_replace('.j2') }}"
```
