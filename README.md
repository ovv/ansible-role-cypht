ovv.cypht
=========

[![Build Status](https://travis-ci.org/ovv/cypht.svg?branch=master)](https://travis-ci.org/ovv/cypht)

Ansible role to install and configure [cypht](https://cypht.org/).

Requirements
------------

A PHP, Nginx and PostgreSQL installation are required. We recommend using [ovv.php7](https://github.com/ovv/ansible-role-php7),
[pyslackers.nginx](https://github.com/pyslackers/ansible-role-nginx) and [pyslackers.postgres](https://github.com/pyslackers/ansible-role-postgres).

Installation
------------

To install this roles clone it into your roles directory.

```bash
$ git clone https://github.com/ovv/ansible-role-cypht.git ovv.cypht
```

If your playbook already reside inside a git repository you can clone it by using git submodules.

```bash
$ git submodule add -b master https://github.com/ovv/ansible-role-cypht.git ovv.cypht
```

Role Variables
--------------

* `cypht_user`: Cypht admin username.
* `cypht_password`: Cypht admin password.
* `cypht_db_user`: User used to connect to database (default to `cypht`).
* `cypht_db_password`: Password used to connect to database (required).
* `cypht_cookie_domain`: Cypht cookie domain name (default to autodetect).


Example Playbook
----------------

```yml
- hosts: cypht
  tags:
    - cypht
  roles:
    - pyslackers.nginx
    - pyslackers.postgres
    - ovv.php7
    - ovv.cypht
  vars:
    cypht_cookie_domain: cypht.example.com
    cypht_user: admin
    cypht_password: password
    cypht_db_password: differentpassword

    # ovv.php7 variables
    custom_php_packages:
      - php7.0-pgsql

    php_pools:
      cypht:
        socket: /var/run/php7.0-fpm-cypht.sock
        user: cypht
        working_dir: /opt/cypht-master/site

    # pyslackers.postgres variables
    postgres_users:
      cypht:
        password: differentpassword

    # pyslackers.nginx variables
    nginx_sites:
      cypht:
        directory: /opt/cypht-master/site
        locations:
          root:
            location: /
            custom: |
              try_files $uri $uri/ =404;
          php:
            location: ~ \.php$
            custom: |
              fastcgi_split_path_info ^(.+\.php)(/.+)$;
              fastcgi_pass unix:{{ php_pools['cypht']['socket'] }};
              fastcgi_index index.php;
              include fastcgi.conf;
```

License
-------

MIT

