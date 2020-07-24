# WIP: Main cloud

Inspired by

- <https://gitlab.com/NickBusey/HomelabOS>


## Goals

- only docker
- server provision and deploy with ansible
- backup just files, don't think about export data implementation
- auth with authelia ant second factor

## Provisioning

### Step 0 - Setup ssh port and common settings

if you need change ssh port for common securiry reasons (if you have no VPN network setup or else) you must run command below

This command change your ssh port from default (22) to your inventory file port

```bash
ansible-playbook -i playbook.provision.yml --tags setup_ssh -e 'ansible_user=root'
```

Notes:

- On a fresh server, most likely there is nothing configured, and it is logical to change the port before the next setup
- So i use `ansible_user=root`
- If you need change ports back or to antother port, you need setup variable `default_ssh_port` to your current SSH port and inventory file to new ssh port

### Step 1 - Create main admin and main ansible users with keys

```bash
 ansible-playbook playbook.provision.yml --tags setup_users -e 'ansible_user=root'
```

Notes:

- No users on server - so use `ansible_user=root`

### Step 2 - Install packgages and configure server

```bash
ansible-playbook playbook.provision.yml --tags setup_common
```

## Deploy

```bash
ansible-playbook playbook.provision.yml --tags setup_deploy
```

### Step 1 - Install "System" services

#### Traefic

#### Authelia

### Step 2 - Install other services


## Notes

### Fsync

https://github.com/mozilla-services/syncserver/issues/208

### Wallabag

```bash

docker-compose exec -T wallabag_db sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < wallabag_dump.sql

```
### Nextcloud

-  try to migrate to linuxserver/nextcloud cause of php-fmp and nginx. apache fpm too Sloooooooooooooooooooooooooooow
- nextcloud always autogenerate confix.php, but respects database content
  - so for mitration you need 
    - or update config php to new creads/paths
    - or 
      - delete old config.php
      - visit web
      - create new admin user
      - edit new config php and CHANGE PASSWORD SALT, secrets and etc

```bash
docker-compose exec nextcloud_db sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"'
docker-compose exec -u 1001 nextcloud_app php occ 

```