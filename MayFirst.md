These are instructions on how to use this role with MayFirst's shared hosting.

# Control Panel

There are a number of things to set up in the control panel before applying this role.

## Web Configuration
Create a Web Configuration for this service.

- Encryption: https enabled
- Show Advanced Settings
  - Settings (enter the block below)

```
ProxyPass / http://localhost:8000/ upgrade=websocket
ProxyPreserveHost On
RequestHeader set X-Real-IP %{REMOTE_ADDR}s
RequestHeader setifempty Connection "Upgrade"
RequestHeader setifempty Upgrade "websocket"
```

Note: In the above example it uses port 8000. If someone else is using this port, vaultwarden will not start up and you will need to choose another port in your ansible variables as well as update the setting above to match.

## Server Access
Add a new item and paste in your public SSH key here so you will be able to SSH into the web environment. Record the username, as it will be needed in the playbook.

## PostgreSQL database and user
Add a database which you can name whatever you want. Record the username and password, as they'll be needed in the playbook.

# Ansible

## Playbook
You can start with the example playbook for a shared server in the main
[README](README.md) file.

You will need to change the `remote_user` in the playbook to match your username (from the Server Access section).

## Variables

At a minimum,  and the following variables:

- vaultwarden_admin_hash
- vaultwarden_smtp_username
- vaultwarden_smtp_password
- vaultwarden_database_username
- vaultwarden_database_password
- vaultwarden_domain

These are typically stored in [host vars](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html) and protected with [ansible vault](https://docs.ansible.com/ansible/latest/cli/ansible-vault.html).

## ansible.cfg
In the same directory as your playbook, you will also need an ansible.cfg with some specific settings required to be compatible with MayFirst's environment. The easiest way to get these settings is to just copy and paste the block below into your ansible.cfg file.

```ini
[defaults]
# Found via the error message if you don't have this in here
remote_tmp=$HOME/.ansible/tmp

# Use pipelining to work around this bug:
# https://github.com/ansible/ansible/issues/57542
# found via this forum post
# https://forum.ansible.com/t/ansible-2-9-failed-to-transfer-ansiballz-setup-py-when-gathering-facts/34253
pipelining=True

# Use stdout_callback of "debug" to avoid escaped quotes in debug messages
# found via https://stackoverflow.com/a/54943100
stdout_callback=debug

[ssh_connection]
# Found via the warning messages if you don't have this in here
# Use SCP only (as opposed to using SFTP)
transfer_method=scp
```

# Running the playbook
Follow the instructions in the main [README](README.md) to run the playbook.

# Scheduled job
At the end of execution of the playbook, it will print out: a comand, a directory and string of environment variables to enter into your scheduled job. In the MayFirst control panel, go to Scheduled job and fill these values in.

After the scheduled job is active, you should be able to go to /admin on your vaultwarden instance. If this is not the case, you'll want to SSH into your instance and run `systemctl --user status red-item-366487.service` substituting your Scheduled job ID for 366487.

If it can't start because there is already something listening on the port you're trying to use, you will need to change the `vaultwarden_rocket_port` variable and update your Web Configuations settings to match the new port.
