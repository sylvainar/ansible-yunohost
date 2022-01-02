[![](https://img.shields.io/liberapay/receives/cchaudier.svg?logo=liberapay)](https://liberapay.com/cchaudier/donate)
[![](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/badges/main/pipeline.svg)](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/pipelines)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
[![Ansible Role](https://img.shields.io/ansible/role/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Role](https://img.shields.io/ansible/role/d/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![GitHub last commit](https://img.shields.io/github/last-commit/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Release Date](https://img.shields.io/github/release-date/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Repo stars](https://img.shields.io/github/stars/LydraFr/ansible-yunohost?style=social)](https://github.com/LydraFr/ansible-yunohost)

# Ansible Role: Yunohost

[🇫🇷 French version](README-FR.md)

Deploy [Yunohost](https://yunohost.org/#/) with Ansible!

## Requirements

None.

## Role Variables

Default variables are available in `default/main.yml` however it is necessary to override them according to your needs for Yunohost domains, users and apps.

### Yunohost Installation

```yml
# Debian 10 script only.
ynh_install_script_url: https://install.yunohost.org

ynh_admin_password: MYINSECUREPWD_PLZ_OVERRIDE_THIS
```

- `ynh_install_script_url` downloads official Yunohost script for installing Yunohost packages. Yunohost is only available on Debian 10.
- `ynh_admin_password` is the password used to access to the server's administration interface.

### Domain management

```yml
# The list of Yunohost domains.
ynh_main_domain: domain.tld
ynh_extra_domains:
  - forum.domain.tld
  - wiki.domain.tld
ynh_ignore_dyndns_server: False
```

- `ynh_main_domain` is the main domain used by the server's users to access the authentication portal. If you already own a domain name, you probably want to use it here. You can also use a domain in .nohost.me / .noho.st / .ynh.fr (more info [here](https://yunohost.org/en/install/hardware:vps_debian)).
- `ynh_extra_domains` are optional and allow you to install one app per subdomain (more info [here](https://yunohost.org/en/administrate/specific_use_cases/domains/dns_subdomains)).
- `ynh_ignore_dyndns_server` allow to register domains with a Dynamic DNS service (more info [here](https://yunohost.org/en/dns_dynamicip)).

### SMTP relay configuration

```yml
# SMTP custom settings
ynh_smtp_relay:
    host: smtp.domain.tld
    port: 25
    user: user1
    password: Pa$$w0rd
```

There is a built-in SMTP server on Yunohost but you can also set up Yunohost to use a SMTP relay instead.
In order to do so, create the `ynh_smtp_relay` variable and provide your own values. You can learn more about SMTP relay [here](https://yunohost.org/en/administrate/specific_use_cases/email_relay).

### User management

```yml
# The list of Yunohost users.
ynh_users:
   - name: user1
     pass: MYINSECUREPWD_PLZ_OVERRIDE_THIS
     firstname: Jane
     lastname: Doe
     mail_domain: domain.tld
```
- `ynh_users` is the list of users to create. Each field is mandatory. Some Yunohost applications require that a user be the app administrator. He will then have the right to manage the application from the server administration interface. You can learn more about Yunohost user management [here](https://yunohost.org/en/users).

### App management

```yml
# The list of Yunohost apps.
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
    post_install:
      - src: "templates/site_settings.yml.j2"
        dest: "/var/www/discourse/config/site_settings.yml"
        type: "config"

      - src: "templates/configure_discourse.sh.j2"
        dest: "/tmp/configure_discourse.sh"
        type: "script"
        owner: root
        group: root
```

- `ynh_apps` is the list of applications to install.
- `label` allows you to give a custom name to the application on the user interface.
- `link` is the name of the Yunohost application to install.

#### About the arguments:
- `domain` is essential. You have to choose one of the domains of your Yunohost instance.
- `path` is required. You have to choose a URL to access your application like `domain.tld/my_app`. Just use `/` if the application is to be installed on a subdomain.
- `is_public` argument is a common one. Set to `yes`, the application will be accessible to everyone, even without authentication to the Yunohost SSO portal. Set to `no`, the application will be accessible only after authentication.

For the other arguments, you have to refer to the `manifest.json` available in the repository of the Yunohost application you install. You can learn more about this part [here](https://yunohost.org/fr/packaging_apps_manifest). 

#### About the post-installation:
It is possible to complete the installation of applications by adding jinja template configuration files or scripts written by yourself.
To enable this feature, define the `post_install` variable which corresponds to the list of post-installation files of your applications.
Because this task uses the template module, you can use your own variables and call them in your template files. To know more about this module, click [here](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html).

- `src` is mandatory. This is the directory where the template file is located on the machine running Ansible.
- `dest` is mandatory. This is the directory where the template file will be stored.
- `type` is mandatory:
  - If you specify `script` as the value, then the template file will have 740 rights. It will be executed after it is transferred to the Yunohost server (usually in `/tmp/`) and then deleted.
  - If you specify `config` as the value, then the template file will have 660 rights. It will be transferred to the Yunohost server (usually in `/var/www/AppName/`) and after you could import it with a shell script on the side for example.

For `owner` and `group`, by default the file will take as owner the name of the application and as owner www-data(NGINX group). You can change them by specifying different values.

## Dependencies

None.

## Example Playbook

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

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
