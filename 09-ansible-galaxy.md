<h1 align="center" style="color: red;">Ansible Galaxy</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment utiliser ansible-galaxy et utiliser les rôles préconfigurés.  


`ansible-galaxy` est un outil en ligne de commande pour gérer les rôles et les collections Ansible. Il permet de télécharger, installer et gérer des rôles depuis des dépôts publics ou privés.  

#### installation de Rôle
``` bash
ansible-config dump | grep -i galaxy_server
```

Pour installer un rôle depuis Ansible Galaxy, utilisez la commande ansible-galaxy install.  
Par exemple, pour installer le rôle geerlingguy.apache, vous pouvez exécuter :
``` bash
ansible-galaxy install geerlingguy.apache
```
NB:  
Par défaut, les rôles installés avec ansible-galaxy sont stockés dans le répertoire .ansible/roles. (ansible --version)  
#### Changer l'Emplacement par Défaut des Rôles
``` bash
ansible-config dump | grep -i roles
```

Vous pouvez changer l'emplacement par défaut où ansible-galaxy installe les rôles en utilisant le fichier de configuration `ansible.cfg`.  
Ajoutez cette ligne dans le fichier ansible.cfg :
``` bash
roles_path = /path/to/your/custom/roles
```
Dans cet exemple, les rôles seront installés dans /path/to/your/custom/roles au lieu du répertoire roles par défaut. 
``` bash
ansible-config dump | grep -i roles
``` 
#### Appeler un Rôle dans un Playbook
``` bash
- name: Configure web server
  hosts: all
  roles:
    - geerlingguy.apache
```
Dans ce cas, le rôle geerlingguy.apache est utilisé pour configurer un serveur web Apache.
#### Utilisation de requirements.yml pour Installer Plusieurs Rôles
`requirements.yml` est un fichier utilisé pour définir une liste de rôles et de collections à installer via ansible-galaxy. Ce fichier permet de gérer plusieurs rôles de manière centralisée et cohérente.  
Exemple de requirements.yml :


``` bash 
---
- name: geerlingguy.apache
  src: geerlingguy.apache

- name: geerlingguy.mysql
  src: geerlingguy.mysql

```
#### Installer les Rôles à partir de requirements.yml

``` bash 
ansible-galaxy install -r requirements.yml
```
Cette commande télécharge et installe tous les rôles listés dans le fichier requirements.yml et les place dans le répertoire roles par défaut ou dans le répertoire spécifié dans ansible.cfg.
#### Exemple de Playbook Utilisant Plusieurs Rôles
``` bash 
---
- name: Configure web and database servers
  hosts: all
  become: true
  roles:
    - geerlingguy.apache
    - geerlingguy.mysql
```
## QUESTION
Create a file called requirements.yml in /home/ansible/roles to install two roles. The source for the first role is geerlingguy.haproxy and geerlingguy.php. Name the first haproxy-role and the second php-role. The roles should be installed in /home/ansible/roles1.