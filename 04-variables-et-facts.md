## Les variables
### Utilisation de `vars`
Dans cette méthode, nous définissons les variables directement dans le playbook sous la section `vars`
Exemple 1 :
```bash
- name: Ajouter utilisateur avec des variables définies
  hosts: all
  become: true
  vars:
    username: "alice"
    home_directory: "/home/alice"
    user_shell: "/bin/bash"
  tasks:
    - name: Ajouter utilisateur
      user:
        name: "{{ username }}"
        home: "{{ home_directory }}"
        shell: "{{ user_shell }}"
        state: present

```
### Utilisation de `vars_files`
Dans cette méthode, nous stockons les variables dans un fichier séparé puis on fait l'appel de fichier dans la section `vars_files`
```bash
vim variables.yml
```
```bash
username: "alice"
home_directory: "/home/alice"
user_shell: "/bin/bash"

```
```bash
vim plaubook.yml
```
```bash
- name: Ajouter utilisateur avec un fichier de variables
  hosts: all
  become: true
  vars_files:
    - variables.yml
  tasks:
    - name: Ajouter utilisateur
      user:
        name: "{{ username }}"
        home: "{{ home_directory }}"
        shell: "{{ user_shell }}"
        state: present
```

ansible node1 -m setup | less
ansible node1 -m setup -a "filter=ansible_default_ipv4"

nane: Ansible Facts Playbook
hosts: all
tasks:
name: Display ansible fact
debug:
var: ansible hostname



nane: Ansible Facts Playbook
hosts: all
tasks:
nane: Display Facts in old way
debug:
var:
ansible_default_1pv4.address
nane: Display Facts in new way
debug:
var: ansible_facts['default ipv4']['address']
