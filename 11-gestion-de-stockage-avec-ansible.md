<h1 align="center" style="color: red;">Gestion de stockage</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment gérer les partitions et les volumes logiques.

### Création de partition
On a besoin de la collection community.general pour les modules lvm
``` bash
ansible-galaxy collection install community.general
```

Pour créer une partition, nous utilisons le module `parted`.
``` bash
ansible-doc parted
```

``` bash
---
- name: Create a new partition
  hosts: all
  become: yes
  tasks:
    - name: Create partition
      parted:
        device: /dev/sda
        number: 1
        state: present
        part_end: 1GiB

```
### Formater la Partition 
Ensuite, nous formatons cette partition nouvellement créée en ext4 en utilisant le module `filesystem`.
``` bash
ansible-doc filesystem
```
``` bash
---
- name: Format the new partition
  hosts: all
  become: yes
  tasks:
    - name: Format partition to ext4
      filesystem:
        fstype: ext4
        dev: /dev/sda1

```
### Monter la Partition et l'Ajouter à fstab
``` bash
ansible-doc mount
```
``` bash
- name: Mount the new partition
  hosts: all
  become: yes
  tasks:
    - name: Create mount point directory
      file:
        path: /mnt/data
        state: directory

    - name: Mount partition
      mount:
        path: /mnt/data
        src: /dev/sda1
        fstype: ext4
        state: mounted

```
## QUESTION 

Create /home/ansible/partition.yml, which will create partitions on all the managed nodes:  
a) After sdb creating a 1200M primary partition, partition number 1, and format it into ext4 filesystem and mount it under /srv  
c) If there is not enough disk space, give prompt information "Could not create partition of that size" and create a 800M partition.  
d) If sdb does not exist, a prompt message will be given "this disk does not exist."  
e) format the partition with the ext4 filesystem if it is created  

``` bash
- name: Partition Playbook
  hosts: all
  tasks:
  - name: check block stroage availablity.
    block: 
      - name: If sdb does not exist
        debug:
          msg: "this disk does not exist."
        when: "'sdb' not in ansible_devices"
      - name: Creating the 1200m partition
        parted:
          device: /dev/sdb
          number: 1
          part_end: 1200MiB
          state: present
        when: "'sdb' in ansible_devices"  
   rescue:
      - name: If there is not enough disk space
        debug:                            
          msg: "Could not create partition of that size"
        when: "'sdb1' not in ansible_devices.sdb"
      - name: Creating the smaller partition
        parted:
          device: /dev/sdb
          number: 1
          part_end: 800MiB
          state: present
        when: "'sdb1' not in ansible_devices.sdb"
    always:
      - name: Creating the ext4 filesystem
        filesystem:
          fstype: ext4
          dev: /dev/sdb1
        when: "'sdb' in ansible _devices"
      - name: Create mount point directory
        file:
          path: /srv
          state: directory
        when: "'sdb' in ansible_devices"
      - name: Mount partition
        mount:
          path: /srv
          src: /dev/sdb1
          fstype: ext4
          state: mounted
        when: "'sdb' in ansible_devices"
``` 

### Création d'un Groupe de Volumes avec lvg
``` bash
ansible-doc lvg
```
``` bash
---
- name: Create a Volume Group
  hosts: all
  become: yes
  tasks:
    - name: Create volume group
      lvg:
        vg: vg_data  # Nom du groupe de volumes
        pvs: /dev/sdb1  # La partition physique à inclure dans le groupe de volumes

```
### Créer un Volume Logique avec lvol
``` bash
ansible-doc lvol
```
``` bash
---
- name: Create a Logical Volume
  hosts: all
  become: yes
  tasks:
    - name: Create logical volume
      lvol:
        vg: vg_data  # Le groupe de volumes auquel appartient le LV
        lv: lv_data  # Nom du volume logique
        size: 500M  # Taille du volume logique

```
### Formater le Volume Logique avec filesystem
``` bash
ansible-doc filesystem
```
``` bash
---
- name: Format the Logical Volume
  hosts: all
  become: yes
  tasks:
    - name: Format logical volume
      filesystem:
        fstype: ext4  # Type de système de fichiers (ex: ext4, xfs)
        dev: /dev/vg_data/lv_data  # Dispositif à formater
```
### Monter le Volume en Utilisant le Module mount
``` bash
ansible-doc mount
```
``` bash
---
- name: Mount the Logical Volume
  hosts: all
  become: yes
  tasks:
    - name: Create mount point directory
      file:
        path: /mnt/data1  # Chemin du point de montage
        state: directory  # État souhaité (directory pour créer un répertoire)
    - name: Mount logical volume
      mount:
        path: /mnt/data1  # Chemin du point de montage
        src: /dev/vg_data/lv_data  # Source du dispositif à monter
        fstype: ext4  # Type de système de fichiers
        state: mounted  # État souhaité (mounted pour monter le volume)
```
## QUESTION
Create a playbook called /home/ansible/lvm.yml that runs on all the managed nodes and does the following:  
a) Creates a logical volume with the following requirements:
i.The logical volume is created in the volume group.  
ii.The logical volume name is data.  
ili.The logical volume size is 1200 Mib.  
iv. Format the logical volume with the ext4 file-system.  
v. if the requested logical volume size cannot be created, the error message "could not create logical volume of that size" should be displayed and size 800 MiB should be used instead.  
vi. if the volume research does not exist, the error message "volume group does not exist"
should be displayed.  
vii. Don't mount the logical volume in any way.  
<!--
``` bash
- name: lvm playbook
  hosts: all
  tasks:
  - name: checking details
    block:
      - name: if the volume research does not exist
        debug:
          msg: "volume group does not exist"
        when: "'research' not in ansible_lvm.vgs"
  - name: creating the 1200m lvm
    lvol:
      vg: research     
      Lv: data   
      size: 1200m
    when: "'research' in ansible_lvm.vgs"  
  rescue:
    - name: if the requested logical volume size cannot be cretaed
      debug:
        msg: "Could not create logical volume of that size"
      when: "'research' in ansible_lvm.vgs"
    - name: creating the logical volume of 800m
      Lvol:
        vg : research
        lv: data
        size: 800m
      when: "'research' in ansible_lvm.vgs"
  always:
    - name: format filesystem
      filesystem:
        fstype: ext4
        dev: /dev/research/data
      when: "'research' in ansible_lvm.lvs"
```
-->