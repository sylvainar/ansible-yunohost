[![](https://img.shields.io/liberapay/receives/cchaudier.svg?logo=liberapay)](https://liberapay.com/cchaudier/donate)
[![](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/badges/main/pipeline.svg)](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/pipelines)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
[![Ansible Role](https://img.shields.io/ansible/role/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Role](https://img.shields.io/ansible/role/d/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![GitHub last commit](https://img.shields.io/github/last-commit/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Release Date](https://img.shields.io/github/release-date/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Repo stars](https://img.shields.io/github/stars/LydraFr/ansible-yunohost?style=social)](https://github.com/LydraFr/ansible-yunohost)

# Rôle Ansible : Yunohost

[🇬🇧 English version](README.md)

Déployez [Yunohost](https://yunohost.org/#/) avec Ansible !

## Prérequis

Aucun.

## Variables du rôle

Les variables par défaut sont disponibles dans `default/main.yml` cependant il est nécessaire de les surcharger selon vos besoins en termes de domaines, d'utilisateurs et d'applications sur Yunohost.

### Installation de Yunohost

```yml
# Script pour Debian 10 uniquement.
ynh_install_script_url: https://install.yunohost.org

ynh_admin_password: MYINSECUREPWD_PLZ_OVERRIDE_THIS
```

- `ynh_install_script_url` est le script d'installation des packages Yunohost, par défaut c'est le script officiel. Yunohost ne s'installe que sur Debian 10.
- `ynh_admin_password` est le mot de passe permettant d'accéder à l’interface d’administration du serveur.

### Gestion des domaines

```yml
# Liste des domaines gérés par Yunohost.
ynh_main_domain: domain.tld
ynh_extra_domains:
  - forum.domain.tld
  - wiki.domain.tld
ynh_ignore_dyndns_server: False
```

- `ynh_main_domain` correspond au domaine principal qui permet l’accès au serveur ainsi qu’au portail d’authentification des utilisateurs. On peut se contenter d'un nom de domaine qui nous appartient ou en utiliser un en .nohost.me / .noho.st / .ynh.fr (plus d'infos [ici](https://yunohost.org/fr/install/hardware:vps_debian)).
- `ynh_extra_domains` sont des sous-domaines optionnels. Ils permettent d'installer une application par sous-domaine (plus d'infos [ici](https://yunohost.org/fr/dns_subdomains)).
- `ynh_ignore_dyndns_server` permet d'enregistrer les domaines avec un service de DNS dynamique (plus d'infos [ici](https://yunohost.org/fr/dns_dynamicip)).

### Configuration d'un relais SMTP

```yml
# paramètres personnalisés du relais SMTP
ynh_smtp_relay:
    host: smtp.domain.tld
    port: 25
    user: user1
    password: Pa$$w0rd
```
Yunohost possède son propre serveur SMTP natif mais il est aussi possible de configurer Yunohost pour qu'il utilise un relais SMTP à la place. 
Pour faire cela, créez la variable `ynh_smtp_relay` et mettez vos propres valeurs. Vous pouvez en apprendre plus sur les relais SMTP [ici](https://yunohost.org/fr/administrate/specific_use_cases/email_relay).

### Gestion des utilisateurs

```yml
# Liste des utilisateurs Yunohost.
ynh_users:
   - name: user1
     pass: MYINSECUREPWD_PLZ_OVERRIDE_THIS
     firstname: Jane
     lastname: Doe
     mail_domain: domain.tld 
```

- `ynh_users` est la liste des utilisateurs à créer. Chaque champ est obligatoire. Certaines applications Yunohost nécessitent qu'un utilisateur soit administrateur de l'application. Il aura ensuite le droit de gérer l'application depuis l'interface l'administration du serveur. Vous pouvez en apprendre plus sur la gestion des utilisateurs Yunohost [ici](https://yunohost.org/fr/administrate/overview/users).

### Gestion des applications

```yml
# Liste des applications Yunohost.
ynh_apps:
  - label: WikiJS
    link: wikijs
    args:
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

- `ynh_apps` est la liste des applications à installer.
- `label` permet de donner un nom personnalisé à l'application sur l'interface utilisateur.
- `link` correspond au nom de l'application Yunohost qu'on veut installer.

Concernant les arguments :
- `domain` est indispensable. Il faut choisir un des domaines de son instance Yunohost.
- `path` est indispensable. Il faut choisir une URL pour accéder à son application comme `domain.tld/my_app`. Utilisez juste `/` si l'application doit s'installer sur un sous-domaine.
- `is_public` est  un argument qu'on retrouve souvent. Paramétré sur `yes`, l'application sera accessible à tout le monde, même sans authentification sur le portail SSO Yunohost. Paramétré sur `no`, l'application ne sera accessible qu'après authentification.

Pour les autres arguments, il faut se référer au `manifest.json` disponible dans le dépôt de l'application Yunohost qu'on installe. Vous pouvez en apprendre plus sur cette partie [ici](https://yunohost.org/fr/packaging_apps_manifest).

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
    - lydra.yunohost
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
