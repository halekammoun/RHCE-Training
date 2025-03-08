<h1 align="center" style="color: red;">Gestion des Cron Jobs avec Ansible</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment gérer des tâches planifiées (cron jobs) en utilisant Ansible.
### Utilisation du module cron
Ansible fournit le module cron qui permet d'ajouter, de modifier ou de supprimer des tâches planifiées dans le fichier crontab d'un utilisateur.
#### Exemple de création d'un cron job

Le playbook suivant crée un cron job qui exécute la commande logger "EX294 in progress" toutes les 2 minutes sous l'utilisateur student.
Utilisez le module `ansible.builtin.cron` pour créer le cronjob.
#### Exercice
Écrire une tâche Ansible qui crée un cronjob pour un utilisateur nommé student. Ce cronjob doit s'exécuter toutes les 2 minutes et exécuter la commande logger "EX294 in progress" pour ajouter le message "EX294 in progress" dans les logs du système.
``` bash
vim crontab.yml
```
``` bash
- name: Create a cronjob
  hosts: all
  tasks:
    - name: Cronjob for logger
      ansible.builtin.cron:
        name: Create logger
        user: student
        minute: "*/2"
        job: logger "EX294 in progress"
        state: present
```
