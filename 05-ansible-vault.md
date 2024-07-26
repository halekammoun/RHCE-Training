<h1 align="center" style="color: red;">Ansible-Vault</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment utiliser l'outil Ansible-vault.
### Ansible-vault
`Ansible Vault` est un outil utilisé pour chiffrer des fichiers sensibles, comme des fichiers contenant des mots de passe ou des clés API. Cela permet de sécuriser les informations sensibles dans les playbooks et autres fichiers de configuration. Voici une explication détaillée de l'utilisation d'Ansible Vault et des commandes associées.
### Commandes Ansible Vault
```bash
ansible-vault --help
```
`ansible-vault create`  
créer un nouveau fichier chiffré avec Ansible Vault:
```bash
ansible-vault create protected_file.yml
```
Après avoir exécuté cette commande, vous serez invité à entrer un mot de passe. Ce mot de passe sera utilisé pour chiffrer le fichier. Une fois le fichier créé, vous pouvez le consulter pour vérifier qu'il est bien chiffré.
```bash
cat protected_file.yml
```
`ansible-vault view`  
Cette commande permet de visualiser le contenu d'un fichier chiffré sans le modifier.
```bash
ansible-vault view protected_file.yml
```
Vous serez invité à entrer le mot de passe pour déchiffrer et afficher le contenu du fichier.
`ansible-vault edit`  
Cette commande permet d'éditer un fichier chiffré.
```bash
ansible-vault edit protected_file.yml
```
Vous serez invité à entrer le mot de passe, puis le fichier s'ouvrira dans votre éditeur de texte par défaut.
`ansible-vault rekey` 
Cette commande change le mot de passe d'un fichier chiffré.
```bash
ansible-vault rekey protected_file.yml
```
Vous serez invité à entrer l'ancien mot de passe, puis un nouveau mot de passe.
### Exécution d'un playbook avec des fichiers chiffrés
Pour exécuter un playbook contenant des fichiers chiffrés, vous pouvez utiliser les options `--ask-vault-password` ou `--vault-password-file`.
#### Option `--ask-vault-password`
Cette option vous invite à entrer le mot de passe pour déchiffrer le fichier.
```bash
ansible-playbook playbook.yml --ask-vault-password
```
#### Option `--vault-password-file`
Cette option utilise un fichier contenant le mot de passe pour déchiffrer le fichier.
```bash
ansible-playbook playbook.yml --vault-password-file vault_password
```
Exemple:
1. Utilisez Ansible Vault pour créer un fichier de mots de passe chiffré contenant la variable `password` et sa valeur.
2. Créez un fichier non chiffré contenant la variable `username` et sa valeur.
3. Écrivez un playbook Ansible qui utilise ces deux fichiers pour créer un utilisateur avec le nom d'utilisateur et le mot de passe spécifiés.

```bash
ansible-vault create password.yml

```

```bash
password: "Password"

```
```bash
vim user.yml
```
```bash
username: "alice"

```
```bash
vim playbook.yml
```

```bash
- name: Créer un utilisateur avec un mot de passe chiffré
  hosts: all
  become: true
  vars_files:
    - user.yml
    - password.yml
  tasks:
  - name: Créer l'utilisateur
    user:
      name: "{{ username }}"
      password: "{{ password | password_hash('sha512') }}"
      state: present

```
```bash
ansible-playbook create_user.yml --ask-vault-password

```
RQ: `{{ password | password_hash('sha512') }}` permet de s'assurer que le mot de passe stocké et utilisé dans le système est sécurisé et haché, plutôt que de stocker et utiliser le mot de passe en clair. 
Vérifier avec `/etc/shadow`
### QUESTION:
1. Create an ansible vault password file called lock.yml with the password reallysafepw in the /home/ansible directory. In the lock.yml file define two variables. One is pw_dev and the password is ‘dev’ and the other is pw_mgr and the password is ‘mgr’. Create a regular file called secret.txt which contains the password for lock.yml.

2. Create the users in the file users_list.yml file provided. Do this in a playbook called users.yml located at /home/ansible. The passwords for these users should be set using the lock.yml file. When running the playbook, the lock.yml file should be unlocked with secret.txt file.
users_list.yml
```bash
users:
 - username: bill
   job: developer
 - username: chris
   job: manager
 - username: dave
   job: test
 - username: ethan
   job: developer  
```
3. All users with the job of ‘developer’ should be created on the dev hosts, add them to the group devops, their password should be set using the pw_dev variable. Likewise create users with the job of ‘manager’ on the proxy host and add the users to the group ‘managers’, their password should be set using the pw_mgr variable.