ansible-yunohost
=========

Deploy Yunohost with Ansible !

Requirements
------------

None.

Role Variables
--------------

Example of Variables:
```yml
yunohost:
  # Link to the install script
  install_script_url: https://raw.githubusercontent.com/YunoHost/install_script/master/install_yunohost
  # The main domain
  domain: example.com
  # Yunohost admin password
  password: MYINSECUREPWD_PLZ_OVERRIDE_THIS
  # If you don't want to use a noho.st url
  ignore_dyndns: False
  # The list of apps you want to install.
  apps:
    - link: ttrss # It can be the name of an official app or a github link
      args: # Provide here args. Path and domain are mandatory, other args depend of the app.
        path: /var/www/ttrss
        domain: example.com
```

Dependencies
------------

None.

Example Playbook
----------------
```yml
- name: Provision servers
  hosts: all
  remote_user: root
  pre_tasks:
    - name: Update all packages and index
      apt:
        upgrade: dist
        update_cache: yes

  roles:
     - { role: ansible-yunohost }
```

License
-------

GPL-3.0
