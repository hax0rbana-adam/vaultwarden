A simple role to install Vaultwarden.

This was initially written to be used in a shared hosting environment, where
you do not have root access. This means the playbook will not install any
system software, nor create any databases (these may be optional features in
the future).

# Post installation
After applying this role, you will need to configure your web server to proxy
requests to Vaultwarden and create a service to run it (both of which will be
done differently depending on your hosting provider). After that, you can log
into /admin and invite users to sign up from the Users tab
(/admin/users/overview).

# Variables

See defaults/main.yml for the variables and an explanation as to what they do.

# Examples
## Playbook

```yaml
- hosts: all
  remote_user: user8391
  vars:
    vaultwarden_admin_hash: '$argon2id$v=19$m=65540,t=3,p=4$hpiewbOU3H/iY6WvPoQJCvx9CY7DFmXvUWm9T9b3Z3k$tUhBc7/ucfquUJtUy43iXvceqZtdASGqPHNDEbHkflQ'
    vaultwarden_smtp_username: bilbo
    vaultwarden_smtp_password: hunter2
    vaultwarden_database_url: postgresql://vaultwarden:hunter2@psql002.mayfirst.cx/vaultwarden
  roles:
    - role: hax0rbana-adam.vaultwarden
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
