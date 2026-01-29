Ansible Role: Forgejo-docker
============================

Install Forgejo Docker Compose project.

- https://forgejo.org/
- https://codeberg.org/forgejo/forgejo

Requirements
------------

Requires the following to be installed:
- docker
- docker compose

Role Variables
--------------

Common Docker projects variables:

```yaml
# Base directory for Docker projects
docker_projects_path: # /var/apps
```

Available role variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Docker project variables

forgejo_project_name: forgejo

# Docker project dynamic vars (uses `docker_project_name` prefix, adapt if overridden)

# Port targeted by Traefik router
forgejo_traefik_loadbalancer_server_port: 3000

# Main service additional docker-compose options (ex: cpu_shares, deploy, ...)
forgejo_service_additional_options: |
  #ports:
  #  - "{{ forgejo_http_port }}:3000"
  #  - "{{ forgejo_ssh_port }}:22"
```

```yaml
# Forgejo Docker project variables

# codeberg.org/forgejo/forgejo image version
forgejo_version: 14

# UID container is running as
forgejo_puid: "{{ ansible_facts['user_uid'] }}"
# GID container is running as
forgejo_pgid: "{{ ansible_facts['user_gid'] }}"

# Port bound to host for HTTP
forgejo_http_port: 3000
# Port bound to host for SSH
forgejo_ssh_port: 8022

# Path to git repositories (mounted as a volume, if different from default)
forgejo_git_repositories: "{{ forgejo_git_repositories_default }}"

# Path to Forgejo storage directories (mounted as volumes, if different from default)
forgejo_storage_attachments:       "{{ forgejo_storage_attachments_default }}"
forgejo_storage_lfs:               "{{ forgejo_storage_lfs_default }}"
forgejo_storage_avatars:           "{{ forgejo_storage_avatars_default }}"
forgejo_storage_repo_avatars:      "{{ forgejo_storage_repo_avatars_default }}"
forgejo_storage_repo_archive:      "{{ forgejo_storage_repo_archive_default }}"
forgejo_storage_packages:          "{{ forgejo_storage_packages_default }}"
forgejo_storage_actions_log:       "{{ forgejo_storage_actions_log_default }}"
forgejo_storage_actions_artifacts: "{{ forgejo_storage_actions_artifacts_default }}"

# Path to Forgejo logs (mounted as a volume, if different from default)
forgejo_log_directory: "{{ forgejo_log_directory_default }}"


# Forgejo variables

# Forgejo database (sqlite3 | mysql | postgres)
forgejo_database: sqlite3
#forgejo_database: mysql
#forgejo_database: postgres

# Forgejo MySQL credentials (forgejo_database: mysql)
#forgejo_database_mysql_version: 8
#forgejo_database_mysql_root_password: forgejo
#forgejo_database_mysql_user:          forgejo
#forgejo_database_mysql_password:      forgejo
#forgejo_database_mysql_database:      forgejo

# Forgejo PostgreSQL credentials (forgejo_database: postgres)
#forgejo_database_postgres_version: 14
#forgejo_database_postgres_user:     forgejo
#forgejo_database_postgres_password: forgejo
#forgejo_database_postgres_db:       forgejo

# Admin user
forgejo_admin_username:        Admin
forgejo_admin_email:           admin@example.net
forgejo_admin_password:        F0rg3AN3wP455w0rD
forgejo_admin_must_change_pwd: true
```

```yaml
# Forgejo environment configuration
# See:
# - https://forgejo.org/docs/latest/admin/installation-docker/#configuration
# - https://forgejo.org/docs/latest/admin/config-cheat-sheet/
# - https://codeberg.org/forgejo/forgejo/src/branch/forgejo/custom/conf/app.example.ini
#
forgejo_env_config:

  FORGEJO____APP_NAME:   Forgejo
  FORGEJO____APP_SLOGAN: Beyond coding. We Forge.

  ## Set the domain for the server
  # FORGEJO__server__DOMAIN: forgejo.example.net

  ## Overwrite the automatically generated public URL. Necessary for proxies and docker.
  # FORGEJO__server__ROOT_URL: https://forgejo.example.net/

  ## Domain name to be exposed in clone URL
  # FORGEJO__server__SSH_DOMAIN: forgejo.example.net

  ## Port number to be exposed in clone URL
  # FORGEJO__server__SSH_PORT: "{{ forgejo_ssh_port }}"

  ## Enables git-lfs support. true or false, default is false.
  # FORGEJO__server__LFS_START_SERVER: true

  ## Whether the installer is disabled (set to true to disable the installer)
  # FORGEJO__security__INSTALL_LOCK: true

  ## List of IP addresses and networks separated by comma of trusted proxy servers. Use `*` to trust all.
  # FORGEJO__security__REVERSE_PROXY_TRUSTED_PROXIES: '*'


  ## Either "console", "file" or "conn", default is "console"
  ## Use comma to separate multiple modes, e.g. "console, file"
  # FORGEJO__log__MODE: file

  ## Either "Trace", "Debug", "Info", "Warn", "Error" or "None", default is "Info"
  # FORGEJO__log__LEVEL: info


  ## Disallow registration, only allow admins to create accounts.
  # FORGEJO__service__DISABLE_REGISTRATION: true

  ## Each new user will get the value of this setting copied into their profile
  # FORGEJO__service__DEFAULT_KEEP_EMAIL_PRIVATE: true

  ## Every new user will have rights set to create organizations depending on this setting
  # FORGEJO__service__DEFAULT_ALLOW_CREATE_ORGANIZATION: false

  ## Either "public", "limited" or "private", default is "public"
  ##   Limited is for users visible only to signed users
  ##   Private is for users visible only to members of their organizations
  ##   Public is for users visible for everyone
  # FORGEJO__service__DEFAULT_USER_VISIBILITY: limited

  ## Repositories will use timetracking by default depending on this setting
  # FORGEJO__service__DEFAULT_ENABLE_TIMETRACKING: false

  ## Value for the domain part of the user's email address in the git log if user has set KeepEmailPrivate to true. The user's email will be replaced with a
  # FORGEJO__service__NO_REPLY_ADDRESS: noreply.localhost

  ## Show Registration button
  # FORGEJO__service__SHOW_REGISTRATION_BUTTON: false

  ## Show milestones dashboard page - a view of all the user's milestones
  # FORGEJO__service__SHOW_MILESTONES_DASHBOARD_PAGE: false


  ## Whether to allow signin in via OpenID
  # FORGEJO__openid__ENABLE_OPENID_SIGNIN: false

  ## Whether to allow registering via OpenID
  ##   Do not include to rely on rhw DISABLE_REGISTRATION setting
  # FORGEJO__openid__ENABLE_OPENID_SIGNUP: false


  ## Either "memory", "file", "redis", "db", "mysql", "couchbase", "memcache" or "postgres"
  ##   Default is "memory". "db" will reuse the configuration in [database]
  # FORGEJO__session__PROVIDER: file

  ## Provider config options
  ##   memory: doesn't have any config yet
  ##   file: session file path, e.g. `data/sessions`
  ##   redis: `redis://127.0.0.1:6379/0?pool_size=100&idle_timeout=180s` (or `redis+cluster://127.0.0.1:6379/0?pool_size=100&idle_timeout=180s` for a Redis cluster)
  ##   mysql: go-sql-driver/mysql dsn config string, e.g. `root:password@/session_table`
  # FORGEJO__session__PROVIDER_CONFIG: /data/gitea/sessions

  ## Mailer
  # FORGEJO__mailer__ENABLED: true

  ## Mail server address, e.g. smtp.gmail.com.
  # FORGEJO__mailer__SMTP_ADDR: "smtp.example.net"

  ## Mail server port
  # FORGEJO__mailer__SMTP_PORT: 587

  ## Mail from address, RFC 5322. This can be just an email address, or the `"Name" <email@example.com>` format
  # FORGEJO__mailer__FROM: "user@example.net"

  ## Mailer user name and password, if required by provider.
  # FORGEJO__mailer__USER: "user@example.net"

  ## Use PASSWD = `your password` for quoting if you use special characters in the password.
  # FORGEJO__mailer__PASSWD: "************"


  ## Show version information about Gitea and Go in the footer
  # FORGEJO__other__SHOW_FOOTER_VERSION: false

  ## Show template execution time in the footer
  # FORGEJO__other__SHOW_FOOTER_TEMPLATE_LOAD_TIME: true

  ## Show the "powered by" text in the footer
  # FORGEJO__other__SHOW_FOOTER_POWERED_BY: true

  ## Generate sitemap. Defaults to `true`.
  # FORGEJO__other__ENABLE_SITEMAP: true

  ## Enable/Disable RSS/Atom feed
  # FORGEJO__other__ENABLE_FEED: true
```

Dependencies
------------

This role depends on :
- [djuuu.docker_project](https://github.com/Djuuu/ansible-role-docker-project)

Some variables allow integration with:
- [djuuu.traefik_docker](https://github.com/Djuuu/ansible-role-traefik-docker)

Example Playbook
----------------

```yaml
- hosts: all
  gather_facts: true
  gather_subset:
    - "!all"
    - "!min"
    - user_id

  roles:
    - djuuu.forgejo_docker
```

License
-------

Beerware License
