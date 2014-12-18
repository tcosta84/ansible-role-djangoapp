Ansible Role: Django APP
========================

Install minimal Django App served by Gunicorn (application server) behind Nginx (reverse proxy) 
on CentOS 6.5.

Overview:

* Install and configure Nginx, Python and Supervisor;
* Create app's user and group and add user to sudoers;
* Create virtualenv;
* Install Gunicorn and add it to Supervisor;
* Deploy app using GIT and deployment keys;
* Create .env file with environment variables to use on settings.py;
* Set up virtual host on Nginx.

Default directory structure used for app's deployment:

    /var/www
    └── sample
        ├── bin
        │   ├── activate
        │   ├── activate.csh
        │   ├── activate.fish
        │   ├── activate_this.py
        │   ├── django-admin
        │   ├── django-admin.py
        │   ├── easy_install
        │   ├── easy_install-2.7
        │   ├── gunicorn
        │   ├── gunicorn_django
        │   ├── gunicorn_paster
        │   ├── gunicorn_start
        │   ├── pip
        │   ├── pip2
        │   ├── pip2.7
        │   ├── python -> python2.7
        │   ├── python2 -> python2.7
        │   └── python2.7
        ├── include
        │   ├── python2.7 -> /usr/local/include/python2.7
        │   └── site
        ├── lib
        │   └── python2.7
        ├── logs
        │   ├── gunicorn-error.log
        │   └── gunicorn.log
        ├── media
        ├── run
        │   └── gunicorn.sock
        ├── src
        │   ├── db.sqlite3
        │   ├── __init__.py
        │   ├── manage.py
        │   ├── requirements.pip
        │   └── sample_djangoapp
        └── static
            └── admin

You can use your personal favourite directory structure by overriding some variables. See "Role 
Variables" and "Example Playbook" for further information.

Requirements
------------

None.

Role Variables
--------------

Default values:

    djangoapp_name:                 sample
    djangoapp_user:                 sample
    djangoapp_pass:                 12345
    djangoapp_group:                webapps

    djangoapp_domain:               localhost

    djangoapp_virtualenv_dir:       "/var/www/{{ djangoapp_name }}"

    djangoapp_source_dir:           "{{ djangoapp_virtualenv_dir }}/src"
    djangoapp_static_dir:           "{{ djangoapp_virtualenv_dir }}/static"
    djangoapp_media_dir:            "{{ djangoapp_virtualenv_dir }}/media"
    djangoapp_logs_dir:             "{{ djangoapp_virtualenv_dir }}/logs"

    djangoapp_repo_url:             git@github.com:tcosta84/sample_djangoapp.git
    djangoapp_repo_branch:          master

    djangoapp_base_dir:             "{{ djangoapp_source_dir }}" # where manage.py is located
    djangoapp_project_module:       sample_djangoapp
    djangoapp_settings_module:      "{{ django_project_module }}.settings"
    djangoapp_fixtures:             core/fixtures/samples.json # a space-delimited list of fixture file names

    djangoapp_requirements_file:    "{{ djangoapp_source_dir }}/requirements.pip"

    djangoapp_envvars:
      DEBUG: "True"
      SECRET_KEY: "oh8b$!qg(j5-q)0^6awq4#n$a%3he@fa69_0&4=p6r@2%a1*js"
      STATIC_ROOT: "{{ djangoapp_static_dir }}"
      MEDIA_ROOT: "{{ djangoapp_media_dir }}"

    djangoapp_enable_celery:        false
    djangoapp_enable_celerybeat:    false
    djangoapp_enable_flower:        false

    gunicorn_sock_dir:              "{{ djangoapp_virtualenv_dir }}/run"
    gunicorn_sock_file:             "{{ gunicorn_sock_dir }}/gunicorn.sock"
    gunicorn_max_requests:          1
    gunicorn_num_workers:           2
    gunicorn_stdout_logfile:        "{{ djangoapp_logs_dir }}/gunicorn.log"
    gunicorn_stderr_logfile:        "{{ djangoapp_logs_dir }}/gunicorn-error.log"

    celery_num_workers:             2
    celery_autoreload:              false
    celery_stdout_logfile:          "{{ djangoapp_logs_dir }}/celery.log"
    celery_stderr_logfile:          "{{ djangoapp_logs_dir }}/celery-error.log"

    celerybeat_schedule_dir:        "{{ djangoapp_virtualenv_dir }}/db"
    celerybeat_schedule_file:       "{{ celerybeat_schedule_dir }}/celerybeat-schedule"
    celerybeat_stdout_logfile:      "{{ djangoapp_logs_dir }}/celerybeat.log"
    celerybeat_stderr_logfile:      "{{ djangoapp_logs_dir }}/celerybeat-error.log"

    flower_db_dir:                  "{{ djangoapp_virtualenv_dir }}/db"
    flower_db_file:                 "{{ flower_db_dir }}/flower"
    flower_persistent_mode:         true
    flower_stdout_logfile:          "{{ djangoapp_logs_dir }}/flower.log"
    flower_stderr_logfile:          "{{ djangoapp_logs_dir }}/flower-error.log"
    flower_user:                    admin
    flower_pass:                    12345

You can override these values on your playbook.

Dependencies
------------

* tcosta84.nginx
* tcosta84.python
* tcosta84.supervisor

Example Playbook
----------------

To install the default app (for demonstration purposes only):

    - hosts: servers
      roles:
        - { role: tcosta84.djangoapp }

To install your own app, you should override at least 2 variables:

    * djangoapp_repo_url
    * djangoapp_repo_deploy_key

Example:
    
    - hosts: servers
      vars:
        - djangoapp_repo_url: git@github.com:yourusername/yourapprepository.git
        - djangoapp_deploy_key: ...
      roles:
        - { role: tcosta84.djangoapp }

Feel free to override the other variables described on "Role Variables" in order to suit your needs.

License
-------

BSD

Author Information
------------------

This role was created by [Thiago Costa](http://thiagocostapy.com)
