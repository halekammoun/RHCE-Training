<h1 align="center" style="color: red;">Les variables et ansible Facts</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment utiliser les variables suivant deux méthodes et comment utiliser les 'Facts'.


### Les variables
#### Utilisation de `vars`
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
#### Utilisation de `vars_files`
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
vim playbook.yml
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
### Facts
pour afficher les facts
```bash 

ansible node1 -m setup | less
```
pour filtrer les facts en utilisant les variables
```bash 

ansible node1 -m setup -a "filter=ansible_default_ipv4"
```
Exemple1: de playbook pour affichage de facts.
```bash 
- name: Ansible Facts Playbook
  hosts: all
  tasks:
  - name: Display ansible fact
    debug:
      var: ansible_hostname
```
Exemple2: de playbook pour affichage de facts.

```bash 
- name: Ansible Facts Playbook
  hosts: all
  tasks:
  - name: Display Facts method1
    debug:
      var: ansible_default_ipv4.address
  - name: Display Facts method2
    debug:
      var: ansible_facts['default_ipv4']['address']
```
### QUESTION:
Create a file in /home/ansible called report.yml. Using this playbook, touch a file called report.txt in node1 and node 2 . 
The file content of report.txt is  

HOST= inventory hostname  
MEMORY=total memory in mb  
BIOS=bios version  
SDA_DISK_SIZE=disk size  
SDB_DISK_SIZE=disk size  
Then edit the lines in the file to provide the real information of the hosts. 

``` bash
- name: QUESTION CORRECTION
  hosts: all
  become: true 
  tasks: 
  - name: get file to nodes
    get_url:
      url: lien 
      dest: /home/ansible/report.txt
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
   - name: change sda 
     lineinfile:
      path: /root/report.txt
      line: SDA_DISK_SIZE={{ ansible_devices.sda.size }}
      regexp: ^SDA_DISK_SIZE
      state: present
   - name: change sdb
     lineinfile:
      path: /root/report.txt
      line: SDA_DISK_SIZE={{ ansible_devices.sdb.size }}
      regexp: ^SDB_DISK_SIZE
      state: present
```

