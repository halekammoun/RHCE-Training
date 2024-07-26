<h1 align="center" style="color: red;">Les conditions & les boucles</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment utiliser les Loops et les Conditions en Ansible.

### Loop en Ansible
Ansible permet d'itérer sur une liste d'éléments en utilisant des boucles. Cela simplifie les tâches répétitives. Voici une explication avec des exemples concrets.
- `Loop simple avec liste intégrée` : Itère directement sur une liste définie dans le playbook.
- `Loop simple avec fichier de variables` : Itère sur une liste définie dans un fichier de variables externe.
- `Loop avec dictionnaire` : Itère sur une liste de dictionnaires, permettant d'accéder à plusieurs valeurs associées à chaque élément.
#### Exemple 1 : Boucle simple avec une liste intégrée
```bash
- hosts: all
  tasks:
    - name: afficher la liste
      shell: echo "{{ item }}"
      loop:
        - "one"
        - "two"
        - "three"

```
NB: 
`item` : C'est une variable spéciale utilisée à l'intérieur de la boucle pour représenter l'élément actuel de la liste.
`loop`: Contient la liste des éléments sur lesquels itérer.
#### Exemple 2 : Boucle simple avec un fichier de variables

```bash
vim simple_loop.yml
```

```bash
numbers:
  - "one"
  - "two"
  - "three"

```

```bash
vim playbook.yml

```

```bash
- hosts: all
  vars_files:
    - simple_loop.yml
  tasks:
    - name: afficher la liste
      shell: echo "{{ item }}"
      loop: "{{ numbers }}"

```
NB: le playbook utilise la liste numbers définie dans `simple_loop.yml`.
#### Exemple 3 : Boucle avec un dictionnaire

```bash
vim dict_loop.yml
```
```bash
users:
  - username: user1
    his_group: group1
  - username: user2
    his_group: group2
  - username: user3
    his_group: group3

```
```bash
vim playbook.yml
```
```bash
- hosts: all
  vars_files:
    - dict_loop.yml
  tasks:
    - name: afficher la liste
      debug:
        msg: this is the user "{{ item.username }}" and his group is "{{ item.his_group }}"
      loop: "{{ users }}"

```
NB: 
- `users`: Contient une liste de dictionnaires, chaque dictionnaire ayant les clés `username` et `his_group`.
- loop: "{{ users }}" : Utilise la liste `users` définie dans `dict_loop.yml`.
- `item.username` et `item.his_group` : Accède aux valeurs du dictionnaire actuel dans la boucle.  
On peut maintenant utiliser les loops pour l'exercice de httpd 
<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCE-Training/blob/master/03-les-playbooks.md"> dans la fin de cette section </a>
</p>

comme suivant:
```bash
vim services.yml
```
```bash
services:
  - httpd
  - firewalld
```
```bash
vim httpd.yml
```
```bash
- hosts: all
  become: true
  vars_files:
     - services.yml
  tasks:
  - name: install packages
    yum:
      name: "{{item}}"
      state: present
    loop: "{{services}}"
  - name: enable httpd service
    service:
      name: "{{item}}"
      state: started
      enabled: yes
    loop: "{{services}}"
  - name: create index
    copy:
      content: "hello"
      dest: /var/www/html/index.html
      setype: httpd_sys_content_t
  - name: allow httpd service
    firewalld:
      service: http
      permanent: yes
      state: enabled
      immediate: yes

```
### When en Ansible
La condition when en Ansible permet d'exécuter des tâches uniquement si certaines conditions sont remplies. Si la condition n'est pas remplie, la tâche est ignorée, et Ansible indique cette action dans la sortie `skipping`
#### Exemple de Playbook avec when:
```bash
- name: Install apache
  hosts: web
  tasks:
    - name: Install vim on Centos
      yum:
        name: vim
        state: present
      when: ansible_facts['distribution'] == "CentOS"

    - name: Install vim on Ubuntu
      apt:
        name: vim
        state: present
      when: ansible_facts['distribution'] == "Ubuntu"

```
On peut maintenant utiliser les conditions pour la question
<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCE-Training/blob/master/04-variable-et-facts.md"> dans la fin de cette section </a>
</p>

tel que "If a disk does not exist then write NONE" comme suivant:
``` bash

- name: QUESTION CORRECTION
  hosts: all
  become: true 
  tasks: 
  - name: change hostname
    lineinfile:
      path: /root/report.txt
      line: HOST={{ ansible_hostname }}
      regexp: ^HOST 
      state: present 
   - name: chahge memory
     lineinfile:
      path: /root/report.txt
      line: MEMORY={{ ansible_memtotal_mb }}
      regexp: ^MEMORY
      state: present 
   - name: change bios version
     lineinfile:
      path: /root/report.txt  
      line: BIOS={{ ansible_bios_version }}
      regexp: ^BIOS
      state: present 
   - name: change sda if there is sda
     lineinfile:
      path: /root/report.txt
      line: SDA_DISK_SIZE={{ ansible_devices.sda.size }}
      regexp: ^SDA_DISK_SIZE
      state: present
     when: "'sda' in  ansible_devices "
   - name: change sda if there is no sda
     lineinfile:
      path: /root/report.txt
      line: SDA_DISK_SIZE= NONE
      regexp: ^SDA_DISK_SIZE
      state: present
     when: "'sda' not in  ansible_devices "
   - name: change sdb if there is sdb
     lineinfile:
      path: /root/report.txt
      line: SDA_DISK_SIZE={{ ansible_devices.sdb.size }}
      regexp: ^SDB_DISK_SIZE
      state: present
     when: "'sdb' in ansible_devices "
   - name: change sda if there is no sdb
     lineinfile:
      path: /root/report.txt
      line: SDB_DISK_SIZE= NONE
      regexp: ^SDB_DISK_SIZE
      state: present
     when: "'sdb' not in  ansible_devices "

```