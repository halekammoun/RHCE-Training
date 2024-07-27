<h1 align="center" style="color: red;">Task handlers et Error handlers</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment utiliser les variables ignore_errors, ignore_unreachable aussi l'utilisation de block, rescue et always.

### ignore_errors et ignore_unreachable dans Ansible
`ignore_errors` est utilisé pour ignorer les erreurs sur une tâche spécifique et continuer l'exécution des tâches suivantes.
```bash
- hosts: all
  tasks:
    - name: This task will fail but continue
      command: /bin/false
      ignore_errors: yes

    - name: This task will still run
      command: /bin/echo "Hello, World!"

```
Dans cet exemple, même si la première tâche échoue, Ansible continue et exécute la deuxième tâche.  
`ignore_unreachable` est utilisé pour ignorer les hôtes inaccessibles et continuer à exécuter des tâches sur les autres hôtes. Vous l'utilisez généralement au niveau du bloc de lecture ou du bloc de tâches.

```bash
- hosts: all
  ignore_unreachable: yes
  tasks:
    - name: This task will run even if some hosts are unreachable
      command: /bin/echo "Running on reachable hosts"

```
Dans cet exemple, si certains hôtes sont inaccessibles, Ansible continue à exécuter les tâches sur les hôtes accessibles.
### Utilisation des blocs (block)
Les blocs (`block`) sont utilisés pour regrouper plusieurs tâches et leur appliquer des directives ou des conditions communes.  
Exemple de bloc avec une condition :  
```bash
- hosts: all
  tasks:
    - block:
        - name: Install package
          yum:
            name: httpd
            state: present

        - name: Start service
          service:
            name: httpd
            state: started
      when: ansible_os_family == "RedHat"

```
NB: on peut utiliser block pour une condition contenant plusieurs tâches. 
### Utilisation de block et rescue
Les blocs peuvent être utilisés pour gérer les erreurs en utilisant `rescue`, qui définit les tâches à exécuter en cas d'erreur dans le `block`.  

Exemple simple avec block et rescue :


```bash
- hosts: localhost
  tasks:
    - block:
        - name: Run a command that may fail
          command: /bin/false

        - name: This task will be skipped if the above fails
          command: /bin/echo "This won't run"
      rescue:
        - name: Handle the error
          debug:
            msg: "There was an error, handling it now"

```
Dans cet exemple :  

Les tâches à l'intérieur du block sont exécutées en premier.  
Si une tâche dans le block échoue, les tâches dans le rescue sont exécutées pour gérer l'erreur.  
### Utilisation de block et always
Les blocs peuvent également être utilisés pour exécuter des tâches finales, indépendamment de la réussite ou de l'échec des tâches précédentes, en utilisant `always`.  

Exemple simple avec block et always :


```bash
- hosts: localhost
  tasks:
    - block:
        - name: Run a command that may fail
          command: /bin/false

        - name: This task will be skipped if the above fails
          command: /bin/echo "This won't run"
      always:
        - name: Always run this task
          debug:
            msg: "This task always runs, regardless of the previous results"

```
Dans cet exemple :  

Les tâches à l'intérieur du `block` sont exécutées en premier.  
Les tâches à l'intérieur du `always` sont exécutées quelles que soient les erreurs dans le block.  
### Utilisation de block, rescue et always
Les blocs peuvent également être utilisés pour gérer les erreurs avec `rescue` et pour exécuter des tâches finales avec `always`.  
Exemple simple avec rescue et always :


```bash
- hosts: localhost
  tasks:
    - block:
        - name: Run a command that may fail
          command: /bin/false

        - name: This task will be skipped if the above fails
          command: /bin/echo "This won't run"
      rescue:
        - name: Handle the error
          debug:
            msg: "There was an error"
      always:
        - name: Always run this task
          debug:
            msg: "This always runs"

```
Dans cet exemple :  

- Les tâches à l'intérieur du block sont exécutées en premier.  
- Si une tâche dans le block échoue, les tâches dans le rescue sont exécutées pour gérer l'erreur.
- Les tâches dans le always sont exécutées, que les tâches du block ou du rescue aient échoué ou réussi.
# lien meet à 9h
# https://meet.google.com/ckv-kayq-cxs