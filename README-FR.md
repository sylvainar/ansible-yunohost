[![](https://img.shields.io/liberapay/receives/cchaudier.svg?logo=liberapay)](https://liberapay.com/cchaudier/donate)
[![](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/badges/main/pipeline.svg)](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/pipelines)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
[![Ansible Role](https://img.shields.io/ansible/role/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Role](https://img.shields.io/ansible/role/d/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![GitHub last commit](https://img.shields.io/github/last-commit/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Release Date](https://img.shields.io/github/release-date/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Repo stars](https://img.shields.io/github/stars/LydraFr/ansible-yunohost?style=social)](https://github.com/LydraFr/ansible-yunohost)

# ansible-yunohost
[🇬🇧 English version](README.md)

Deployez Yunohost avec Ansible !

## Prérequis

Aucun.

## Role Variables
Les variables par défaut sont disponibles dans `default/main.yml` cependant il est nécessaire de les surcharger selon vos besoins en termes de domaines, d'utilisateurs et d'applications sur Yunohost.

## Exemple de Variables
```yml
---
# Debian 10 script only.
ynh_install_script_url: https://install.yunohost.org

ynh_admin_password: MYINSECUREPWD_PLZ_OVERRIDE_THIS

# The list of domains.
ynh_main_domain: domain.tld
ynh_extra_domains: 
  - forum.domain.tld
  - wiki.domain.tld
ynh_ignore_dyndns_server: False

# The list of Yunohost users.
ynh_users: 
   - name: user1
     pass: MYINSECUREPWD_PLZ_OVERRIDE_THIS
     firstname: Jane
     lastname: Doe 
     mail_domain: domain.tld 

# The list of Yunohost apps.
ynh_apps: 
  - label: WikiJS # Label is important, it's a reference for the Playbook.
    link: wikijs # It can be the name of an official app or a git repo link.
    args: # Provide args. Domain and pah are mandatory, for other args read manifest.json of app.
      domain: wiki.domain.tld
      path: /
      admin: user1 
      is_public: no
  - label: Discourse
    link: discourse 
    args:
      domain: forum.domain.tld
      path: /
      admin: user1 
      is_public: yes
```
## Dépendances

Aucune.

## Exemple de Playbook
```yml
---
- name: Install Yunohost on Debian Server
  hosts: all
  become: True
  pre_tasks:
    - name: Update all packages and index
      ansible.builtin.apt:
        upgrade: dist
        update_cache: yes
    
  roles:
    - ansible-yunohost
```

## License

GPL-3.0
