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
  # The main domain, then a list of other domains.
  domain: example.com
  extra_domains:
    - example2.com
    - example3.com
  # Yunohost admin password
  password: MYINSECUREPWD_PLZ_OVERRIDE_THIS
  # If you don't want to use a noho.st url
  ignore_dyndns: False
  # The list of apps you want to install.
  apps:
    - label: Tiny Tiny RSS # Label is important, it's a reference for the Playbook.
      link: ttrss # It can be the name of an official app or a github link
      args: # Provide here args. Path and domain are mandatory, other args depend of the app (cf manifest.json of app).
        path: /ttrss
        domain: example.com
  # The list of users.
  users:
    - name: admin
      pass: p@ssw0rd
      firstname: admin
      lastname: admin
      mail: admin@example.com
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
     - { role: sylvainar.yunohost }
```

License
-------

GPL-3.0
