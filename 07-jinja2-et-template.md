<h1 align="center" style="color: red;">Les conditions & les boucles</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment utiliser la syntaxe `jinja2` et le module `template`.

### Syntaxe Jinja2 et Module template
Jinja2 est un moteur de template utilisé dans Ansible pour générer des fichiers de manière dynamique. Il permet d'incorporer des variables, des boucles et des conditions dans des fichiers de configuration en utilisant des fichiers ayant l'extension `.j2`.  

#### Syntaxe Jinja2 de base :
- `Variables` : Utilisées pour insérer des valeurs dynamiques.
```bash 
{{ variable_name }}

```
- `Boucles` : Utilisées pour répéter une section de code.

```bash 
{% for item in list %}
{{ item }}
{% endfor %}

```
- `Conditions` : Utilisées pour insérer des sections de code conditionnelles.

```bash 
{% if condition %}
Some content
{% endif %}

```
#### Module template : 
Le module `template` d'Ansible utilise Jinja2 pour créer des fichiers basés sur des templates. Il permet de copier un fichier template vers un emplacement sur l'hôte distant, en remplaçant les variables par leurs valeurs actuelles.  
Exemple de module template :


```bash 
- name: Render a configuration file from template
  template:
    src: /path/to/template.j2
    dest: /path/to/destination/file

```
## QUESTION:
0. create this file called /root/myhosts.j2 in /home/ansible/.
```bash 
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
```
1. create the jinja template in this file, Then create a playbook in /home/ansible called hosts.yml on dev node so that /root/myhosts content will be like below.  
The order of the nodes doesn’t matter.
```bash 
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
10.0.2.1 node1.example.com node1
10.0.2.2 node2.example.com node2
10.0.2.3 node3.example.com node3
10.0.2.4 node4.example.com node4
10.0.2.5 node5.example.com node5
```
NB: `hostvars` est une variable spéciale dans Ansible qui contient des informations sur tous les hôtes de l'inventaire. C'est un dictionnaire qui vous permet d'accéder aux variables d'un hôte spécifique en utilisant son nom (ou son alias) comme clé.
## ANSWER:  


```bash 
vim /root/myhosts.j2
```

```bash 
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
{% for host in groups.all %}
{{ hostvars[host].ansible_default_ipv4.address }} {{ hostvars[host].ansible_fqdn }} {{ hostvars[host].ansible_hostname }}
{% endfor %}

```

```bash 
vim hosts.yml
```

```bash 
---
- name: Use hosts.j2 template
  hosts: all
  tasks:
    - name: Template a file to dev hosts
      template:
        src: /home/ismat/ansible/hosts.j2
        dest: /root/myhosts
      when: "'dev' in group_names"

```