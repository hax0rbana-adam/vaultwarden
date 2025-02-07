A simple role to install Vaultwarden.

This was initially written to be used in a shared hosting environment, where
you do not have root access. This means the playbook will not install any
system software, nor create any databases (these may be optional features in
the future).

# Post installation

There are some additional steps to set this up on a shared server after
applying the role before you'll be able to go to the website and start
configuring the webapp.

## Shared server
After applying this role this on a shared server, you will need to configure
your web server to proxy requests to Vaultwarden and create a service to run it
(both of which will be done differently depending on your hosting provider).

## All cases
After that, you can log into /admin and invite users to sign up from the Users
tab (/admin/users/overview).

# Variables

See defaults/main.yml for the variables and an explanation as to what they do.

# Examples
## Playbook
### Shared server
Here's an example playbook to set up Vaultwarden on a shared server.

```yaml
# This file is vaultwarden.yml
- hosts: all
  remote_user: user8391
  vars:
    vaultwarden_admin_hash: '$argon2id$v=19$m=65540,t=3,p=4$hpiewbOU3H/iY6WvPoQJCvx9CY7DFmXvUWm9T9b3Z3k$tUhBc7/ucfquUJtUy43iXvceqZtdASGqPHNDEbHkflQ'
    vaultwarden_smtp_username: bilbo
    vaultwarden_smtp_password: hunter2
    vaultwarden_database_url: postgresql://vaultwarden:hunter2@psql002.mayfirst.cx/vaultwarden
  roles:
    - hax0rbana-adam.vaultwarden
```

Running the playbook will look something like this:

```sh
ansible-playbook -ishell.mayfirst.org, vaultwarden.yml
```

### Dedicated server
And if you're going to run this on a machine where you have root and are able
to install and configure a web proxy and a systemd service, this role will
look very similar in the playbook to the above, but you will need some
additional roles in the playbook as well.

Here's a sample playbook for a standalone server:

```yaml
# This file is vaultwarden.yml
- hosts: all
  remote_user: root
  vars:
    vaultwarden_shared_server: false
    vaultwarden_admin_hash: '$argon2id$v=19$m=65540,t=3,p=4$hpiewbOU3H/iY6WvPoQJCvx9CY7DFmXvUWm9T9b3Z3k$tUhBc7/ucfquUJtUy43iXvceqZtdASGqPHNDEbHkflQ'
    vaultwarden_smtp_username: bilbo
    vaultwarden_smtp_password: hunter2
    vaultwarden_database_password: hunter3
    vaultwarden_database_name: vaultwarden
    vaultwarden_database_server: localhost
  roles:
    # ansible-galaxy role install ANXS.postgresql,v1.16.0
    - role: ANXS.postgresql
      postgresql_databases:
        - name: "{{vaultwarden_database_name}}"
      postgresql_users:
        - name: "{{vaultwarden_database_username}}"
          pass: "{{vaultwarden_database_password}}"
      postgresql_database_schemas:
        - database: "{{vaultwarden_database_name}}"
          schema: "public"
          state: present
      postgresql_user_privileges:
        - name: "{{vaultwarden_database_username}}"
          db: "{{vaultwarden_database_name}}"
          priv: "ALL"
      postgresql_apt_dependencies: ["python3-psycopg2", "locales"]
    - hax0rbana-adam.vaultwarden
    # You'll also need to get TLS certificates and set up nginx/apache/haproxy
    #- nginxinc.nginx
    #- nginxinc.nginx_config
```

If you don't already have an existing inventory file, running the playbook will
look something like this:

```sh
ansible-playbook  -ivault.example.org, vaultwarden.yml
```

# Official repo location
All activity takes place on the official GitLab instance:
[https://gitlab.hax0rbana.org/public-repos/ansible/vaultwarden](https://gitlab.hax0rbana.org/public-repos/ansible/vaultwarden)

Any other hosting providers, such as GitHub.com and GitLab.com, are just mirrors
and we do not monitor the issue trackers over there.

# Support
## Matrix channel
You can also join our Matrix channel: #ansible:hax0rbana.org

This is a good place to ask questions or make requests without having to sign
up for another account.

# Contributing
See [contributor guidelines](CONTRIBUTING.md).

# License
This project is licensed under MIT License. See [LICENSE](LICENSE) for more details.
