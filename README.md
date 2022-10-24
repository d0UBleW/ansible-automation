# ansible-automation-mbb

## Features

### Bootstrap

The `bootstrap` role prepares the target machine by installing `python3` and `pip3`, which are then used to install `pexpect` python library which is necessary to utilize [`ansible.builtin.expect`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/expect_module.html) module

After that, set ansible to use `/usr/bin/python3` as the default interpreter and start gathering facts.

### Server Setup

List of components:

- /etc/hosts
- Deep Security agent
- TripWire
- Splunk
- FireEye
- OCS agent
- CyberArk
- Nagios

### OS Hardening

List of components:

- sshd configuration
- auditd configuration
- audit rules
- PAM
- grub configuration
- inactive password lockout
- /var/log/ permissions

### OS Patching

List of components:

- `yum update` and reboot when necessary

## Configuration

### ./main.yml

To enable proxy, uncomment this part inside `./main.yml` and fill in the value:

```yaml
environment:
  http_proxy: http://localhost:1337
  https_proxy: https://localhost:1337
```

### Server Setup

- `pkgs_dir` variable in `main.yml` specifies where packages that are required to be installed on remote machines should be placed locally

#### /etc/hosts

`./roles/server_setup/vars/vars_etc_hosts.yml`:

- `entries` variable contains sets of IP address and aliases to be included in `/etc/hosts`
- `entries['ip']` specifies the IP address
- `entries['d_names']` specifies the list of aliases to `entries['ip']`

#### Deep Security Agent

`./roles/server_setup/vars/vars_ds_agent.yml`:

- `pkgs_dict` is a dictionary with `Distro_MajorVersion` as the key and path to package locally as the value

#### Splunk

`./roles/server_setup/vars/vars_splunk.yml`:

- `splunk_tgz` specifies the path to splunk file locally
- `splunk_base_dir` specifies where splunk would be installed on remote machines
- `splunk_username` specifies the username to be created initially
- `splunk_password` specifies the password to be created initially

### OS Hardening

#### sshd configuration

`./roles/os_hardening/vars/vars_sshd_conf.yml`:

- `sshd_conf_path` specifies the path to remote config file
- `sshd_conf` is a dictionary used to describe the necessary configurations

#### auditd configuration

`./roles/os_hardening/vars/vars_auditd_conf.yml`:

- `auditd_conf_path` specifies the path to remote config file
- `auditd_conf` is a dictionary used to describe the necessary configurations

#### audit rules

`./roles/os_hardening/vars/vars_audit_rules.yml`:

- `audit_rules_path` specifies the path to remote file
- `rules_b64` specifies the rule set for `x86_64` machines
- `rules_b32` specifies the rule set for `x86` machines

#### /var/log/

`./roles/os_hardening/vars/vars_var_log.yml`:

- `files` contains list of dictionaries that describe file name, owner, group, and mode
- `directories` contains list of dictionaries that describe directory name, owner, group, and mode

## Usage

For general usage

```sh
$ ansible-playbook main.yml
```

To only run specific features, please edit the `vars` in `./main.yml` and toggle the boolean values for `server_setup`, `os_hardening`, and `os_patching`.

To skip the `bootstrap` features, comment `tags: always`.

To run specific unit, e.g. /etc/hosts, sshd configuration, etc., please use `--tags <tag1>,<tag2>,<tag3>` when executing through command line.

Example:

```sh
# Only run /etc/hosts and sshd configuration
$ ansible-playbook main.yml --tags etc_hosts,sshd_conf

# Only run splunk and os hardening
$ ansible-playbook main.yml --tags splunk,os_hardening
```
