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
- name: install and strat service httpd
  hosts: all
  become: true
  tasks:
  - name: install httpd
    yum:
      name: httpd
      state: latest
  - name: start httpd
    service:
      name: httpd
      state: started
      enabled: yes

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
