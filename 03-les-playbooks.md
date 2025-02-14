<h1 align="center" style="color: red;">Les Playbooks</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment écrire et exécuter les playbooks.

<p align="center">
  <img src="images/playbook.png" alt="cap" style="width: 400px;"/>
</p>  

- l'extention de playbook peut être .yml ou .yaml.
- Pour vérifier la syntaxe on fait
``` bash
 ansible-playbook --syntax-check
 ```
- Pour exécuter le playbook on fait
``` bash
 ansible-playbook playbook.yml
 ```


## Syntaxe
```bash
- name: Description du playbook
  hosts: [hôtes]
  become: [true/false]
  tasks:
    - name: Description de la tâche 1
      [module]: 
        [arguments]
    - name: Description de la tâche 2
      [module]: 
        [arguments]
```


### LAB 02
#### Q0. Utilisez un playbook Ansible pour ajouter un utilisateur nommé « alice » avec un shell /sbin/nologin et un home directory /host/alice sur toutes les machines hôtes, aussi pour modifier l'utilisateur « bob » afin que son shell soit /bin/bsh, son UID soit 2006, et son home directory soit /home/bob. 

``` bash
- name: ajouter alice te modifier bob
  hosts: all
  become: true
  tasks:
  - name: ajouter alice
    user:
      name: alice
      home: /home/alice
      shell: /bin/bash
      state: present
  - name: modifier bob
    user:
      name: bob
      uid: 2006
      home: /home/bob
      shell: /bin/sh
      state: present

```

#### Q1. Utilisez un playbook Ansible pour supprimer l'utilisateur « charlie » de toutes les machines hôtes.

``` bash

- name: supprimer charlie
  hosts: all
  become: true
  tasks:
  - name: delete
    user:
      name: charlie
      state: absent
```

#### Q2. Utilisez un playbook Ansible pour installer le package httpd sur la machine hôte web-server, vérifier l'installation du package httpd sur toutes les machines hôtes en utilisant le module command et démarrer et activer ce service sur la machine hôte target-node1.


``` bash
- hosts: web-server
  become: true
  tasks:
  - name: istaller httpd
    yum:
      name: httpd
      state: present
- hosts: all
  become: true
  tasks:
  - name: verif httpd
    command: rpm -q httpd
- hosts: target-node1
  become: true
  tasks:
  - name: enable and start httpd
    service:
      name: httpd
      enabled: yes
      state: started


```

#### Q3. Utilisez un playbook Ansible pour ajouter une ligne au fichier /etc/hosts sur toutes les machines hôtes. La ligne à ajouter est '10.0.0.1 myserver'.


``` bash
hosts: all
  become: true
  tasks:
  - name: modifier ligne
    lineinfile:
      path: /etc/hosts
      line: 10.0.0.1 myserver
```

#### Q4. Utilisez un playbook Ansible pour copier un fichier nommé config.json depuis la machine de contrôle vers le répertoire /etc/myapp/ sur la machine app-server.


``` bash
- hosts: all
  become: true
  tasks:
  - name: créer répertoire /etc/myapp
    file:
      path: /etc/myapp
      state: directory
  - name: copier fichier
    copy:
      src: config.json
      dest: /etc/myapp
```

#### Q5. Utilisez un playbook Ansible pour créer un répertoire nommé /backup sur toutes les machines hôtes avec les permissions 0755, appartenant à l'utilisateur root et au groupe root.


``` bash 
- hosts: all
  become: true
  tasks:
  - name: création répertoire
    file:
      path: /backup
      state: directory
      mode: 755
      owner: root
      group: root
```
### playbook avec plusieurs plays
Un playbook Ansible peut contenir plusieurs "plays". Chaque "play" applique un ensemble de tâches à un groupe d'hôtes. Cela permet de structurer le playbook pour réaliser différentes phases d'une configuration ou d'un déploiement.  
Exemple 1:  
- play1: ajouter un utilisateur
- play2: le modifier  
NB: on utilise plusieurs plays pour qu'on puisse exécuter les plays dans différentes hotes `hosts`
```bash
- name: play1 to add group and its user
  hosts: all
  become: true
  tasks:
  - name: add the group
    group:
      name: group1
      gid: 4900
  - name: add the user
    user:
      name: user1
      uid: 4008
      group: group1
- name: play2 to modify the user1
  hosts: all
  become: true
  tasks:
  - name: modify
    user:
      name: user1
      uid: 4007
```
Exemple 2:  
1. Premier play : Créer un site web.
- installation firewalld et httpd
- 'enable' les services httpd et firewalld
- création index.html
- 'allow' service httpd dans le firewall
2. Deuxième play : Tester le site web.
- afficher la page web
RQ: Pour installer le module firewalld on doit installer la collection ansible.posix
```bash
ansible-galaxy collection install ansible.posix
```
<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCSA-Training/blob/main/05-gestion-services.md">Lire service httpd de cours RHCSA</a>
</p>

```bash
- hosts: all  
  become: true
  tasks:      
  - name: install packages
    yum:      
      name:   
      - firewalld 
      - httpd 
      state: present
  - name: enable httpd service
    service:  
      name:  httpd  
      state: started
      enabled: yes  
  - name: enable firewalld service
    service:         
      name: firewalld
      state: started 
      enabled: yes   
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
- hosts: all
  become: false
  tasks:
  - name: webpage test
    uri:
      url: http://node1

```
