Ansible Notes
---

#### Run ansible-playbook with default private key and vault password
```
alias ansops='ansible-playbook --private-key /path/to/keypair.pem --vault-password-file /path/to/ansible-vault.pass --become'
```

#### Run ansible-console with default private key and vault password
```
alias ansrun='ansible-console --private-key /path/to/keypair.pem --vault-password-file /path/to/ansible-vault.pass --become'
```

#### Run ansible-playbook with --limit and --tags
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

#### Ansible Jinja Loop Variable

```yaml
---
people:
  - name: Mike
    fav_colour: Blue
  - name: Kyle
    fav_colour: Yellow
  - name: Shea
    fav_colour: Blue
  - name: Aly
    fav_colour: Yellow
  - name: Daniyal
    fav_colour: Yellow
  - name: Tim
    fav_colour: Orange

colours:
  - name: Blue
    things:
      - Sky
      - Sea
      - Jeans
  - name: Yellow
    things:
      - Egg yolk
      - Taxi
      - Banana
      - Lemon
      - Su
  - name: Orange
    things:
      - Pumpkin
      - Basketball
      - Carrots
      - Oranges
```

```jinja
{% for colour in colours %}
{% set colour_count = {'count': 0} %}
Colour number {{ loop.index }} is {{ colour.name }}.
{% for person in people if person.fav_colour == colour.name %}
{% if colour_count.update({'count': colour_count.count + 1}) %}{% endif %}
{% endfor %}
Currently {{ colour_count.count }} people call {{ colour.name }} their favourite.
And the following are examples of things that are {{ colour.name }}:
{% for item in colour.things %}
- {{ item }}
{% endfor %}

{% endfor %}
```

```text
Colour number 1 is Blue.
Currently 2 people call Blue their favourite.
And the following are examples of things that are Blue:
  - Sky
  - Sea
  - Jeans

Colour number 2 is Yellow.
Currently 3 people call Yellow their favourite.
And the following are examples of things that are Yellow:
  - Egg yolk
  - Taxi
  - Banana
  - Lemon
  - Sun

Colour number 3 is Orange.
Currently 1 people call Orange their favourite.
And the following are examples of things that are Orange:
  - Pumpkin
  - Basketball
  - Carrots
  - Oranges
```
