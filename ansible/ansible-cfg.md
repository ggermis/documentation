# Ansible.cfg

The easiest way to configure ansible is by creating an `ansible.cfg` file. The search order for the location of this configuration file is as follows:

  * ANSIBLE_CONFIG (environment variable if set)
  * ansible.cfg (in the current directory)
  * ~/.ansible.cfg (in the home directory)
  * /etc/ansible/ansible.cfg

If you have multiple projects, the easiest setup I have found was to just put an `ansible.cfg` file inside the root of your project folder where you execute your `ansible` and `ansible-playbook` commands from (2nd option from the list above)

## Example configuration

```
[defaults]
stdout_callback = skippy
display_skipped_hosts = False
retry_files_enabled = False
vault_identity_list = .vault-password
unparsed_is_failed = True

[ssh_connection]
pipelining = True
```