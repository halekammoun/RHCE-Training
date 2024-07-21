<h1 align="center" style="color: red;">Introduction à Ansible et Setup de l'Environnement</h1>

## Introduction
👋 Dans cette section, nous allons vous guider à travers les étapes nécessaires pour installer Ansible et configurer votre environnement.

## Qu'est-ce qu'Ansible ?
Ansible est un outil d'automatisation qui facilite la gestion des serveurs et des applications. Il est "sans agent", ce qui signifie qu'il n'a pas besoin d'un logiciel spécial installé sur les machines que vous gérez. Il est populaire parce qu'il est simple à utiliser, efficace, et utilise des fichiers YAML faciles à comprendre.
<p align="center">
  <img src="images/Capture.JPG" alt="cap" style="width: 400px;"/>
</p>  

## Architecture d'Ansible
- `Serveur Ansible` :  
C'est l'ordinateur (un laptop, un PC ou un serveur) où Ansible est installé. Il contient :
- `Playbooks` :  
Ce sont des fichiers qui définissent ce que vous voulez faire (comme installer des logiciels ou configurer des paramètres).
- `Inventaire` :  
Une liste des serveurs que vous gérez, appelée "hôtes".

## Playbooks :

- `Rôles` :  
Groupes de tâches organisées pour une fonction spécifique (par exemple, installer un serveur web).
- `Tâches` :  
 Ce sont les commandes individuelles que vous voulez exécuter (comme "installer nginx").

## SSH sans mot de passe : 
Ansible communique avec les serveurs cibles en utilisant SSH, sans nécessiter de mot de passe à chaque fois.

## Comment ça fonctionne ?
- Vous créez des playbooks et un inventaire sur votre machine locale.
- Vous établissez une connexion SSH sans mot de passe aux serveurs cibles.
- Ansible recueille des informations (gather facts) sur les serveurs cibles pour connaître leur état.
- Ansible envoie les instructions (playbooks) aux serveurs.
- Les serveurs exécutent les tâches définies dans les playbooks.

## Étapes de Configuration
<p align="center">
  <img src="images/ansible.png" alt="cap" style="width: 600px;"/>
</p>  

### 1. Prérequis
Avant de commencer, assurez-vous d'avoir :
- Un système Red Hat Enterprise Linux installé.
- Un accès root ou sudo sur votre système.

### 2. Installer Ansible
Pour installer Ansible sur votre système Red Hat, suivez ces étapes :
- Ouvrez un terminal et exécutez les commandes suivantes pour installer le dépôt EPEL et Ansible :
```bash