# ansible-automation

## Roles

### bootstrap

The `bootstrap` role prepares the target machine by installing `python3` and `pip3`, which are then used to install `pexpect` python library which is necessary to utilize [`ansible.builtin.expect`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/expect_module.html) module

### server_setup

Contains dependencies, defined in [`./roles/server_setup/meta/main.yml`](https://github.com/d0UBleW/ansible-automation/blob/main/roles/server_setup/meta/main.yml):

- [etc_hosts](https://github.com/d0UBleW/ansible-automation/tree/main/roles/etc_hosts)
- [ds_agent](https://github.com/d0UBleW/ansible-automation/tree/main/roles/ds_agent)
- [tripwire](https://github.com/d0UBleW/ansible-automation/tree/main/roles/tripwire)
- [splunk](https://github.com/d0UBleW/ansible-automation/tree/main/roles/splunk)
- [fireeye](https://github.com/d0UBleW/ansible-automation/tree/main/roles/fireeye)
- [ocs_agent](https://github.com/d0UBleW/ansible-automation/tree/main/roles/ocs_agent)
- [nagios](https://github.com/d0UBleW/ansible-automation/tree/main/roles/nagios)

### os_hardening

Contains dependencies, defined in [`./roles/os_hardening/meta/main.yml`](https://github.com/d0UBleW/ansible-automation/blob/main/roles/os_hardening/meta/main.yml):

- [sshd](https://github.com/d0UBleW/ansible-automation/tree/main/roles/sshd)
- [auditd](https://github.com/d0UBleW/ansible-automation/tree/main/roles/auditd)
- [grub](https://github.com/d0UBleW/ansible-automation/tree/main/roles/grub)
- [inactive_password_lock](https://github.com/d0UBleW/ansible-automation/tree/main/roles/inactive_password_lock)
- [var_log](https://github.com/d0UBleW/ansible-automation/tree/main/roles/var_log)

### os_patching

Performs:

- `yum update` and reboot when necessary

### etc_hosts

Performs:

- Using provided entries of IP address and domain names:
  - Comment out faulty entries
  - Insert missing comment headers
  - Add missing entries

Overridable default variables:

- `etc_hosts_path`: path to `/etc/hosts`
- `<name>_header`: comment header regex pattern for `<name>` section
- `header_entries`: list of dictionaries to match header pattern and `line` to replace with
- `entries`: list of dictionaries which contains IP address, list of domain names (`d_names`), and where to insert the entry

### ds_agent

Performs:

- `ds_agent` installation check
- Copy installation package (located on [`./roles/ds_agent/files/`](https://github.com/d0UBleW/ansible-automation/tree/main/roles/ds_agent/files)) to remote machine
- Install/upgrade `ds_agent`
- Reset config
- Activate to DSM
- Clean up package file(s)

Overridable default variables:

- `ds_pkgs_dict`: dictionary with key (`<os family>_<major version>`, e.g., `RedHat_7`) and value of the corresponding `rpm` file name
- `ds_pkg_dst_path`: path to package file in remote machine

### tripwire

Performs:

- Config files existence
- Extract TripWire archive file from local ([`./roles/tripwire/files/`](https://github.com/d0UBleW/ansible-automation/tree/main/roles/tripwire/files)) to remote machine
- Install TripWire config files if not present
- Packages installation check
- Installation missing packages
- Enabling and starting services

Overridable default variables:

- `tripwire_tgz`: archive file name (archive need to include a directory), located in role's `files` directory
- `tripwire_base_dir`: directory where tripwire would be installed on the remote machine (must not end with forward slash)
- `tripwire_files`: list of TripWire config files
- `tripwire_pkgs`: list of package name and its rpm file name
- `tripwire_services`: list of TripWire services

### splunk

Performs:

- Installation checking
- Splunk installation
- Initial account creation
- Enable splunk to start on boot
- Start splunk service
- Set deploy poll
- Verify service status is running

Overridable default variables:

- `splunk_tgz`: archive file name located in role's `files` directory
- `splunk_base_dir`: directory where splunk would be installed remotely (must not end with forward slash)
- `splunk_bin`: splunk binary file path
- `splunk_username`: username for initial account creation
- `splunk_password`: password for initial account creation

### fireeye

Performs:

- Copy package to remote machine
- Install rpm package
- Clean up rpm file
- Copy json config file to remote machine
- Import config file to FireEye
- Start service and verify its status

Overridable default variables:

- `fireeye_conf`: json config file
- `fireeye_pkg`: rpm package file name
- `fireeye_pkg_dst_path`: path to where package file would be copied to
- `fireeye_base_dir`: path to where fireeye would be installed remotely (must not end with forward slash)
- `fireeye_bin`: path to fireeye binary folder

### ocs_agent

Performs:

- Create `crontab` job if not exists

Overridable default variables:

- `ocs_bin`: dictionary with key (`<os family>_<major version>`, e.g., `RedHat_7`) and value of the corresponding binary path
- `ocs_crontab`: list of dictionaries correspoinding to [`ansible.builtin.cron`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/cron_module.html) module specification

### nagios

Performs:

- Extract tarball to specified destination on remote machine

Overridable default variables:

- `nagios_tarball`: tarball file name located in [`./roles/nagios/files/`](https://github.com/d0UBleW/ansible-automation/tree/main/roles/nagios/files)
- `nagios_dest`: path to where nagios would be installed on remote machine (must not end with forward slash)

### sshd

Performs:

- Commenting faulty configuration based on supplied entries
- Add missing configurations

Overridable default variables:

- `sshd_conf_path`: path to `sshd_config` file
- `sshd_conf`: desired configurations in list of dictionaries format

### auditd

Performs:

- Commenting faulty configuration based on supplied entries
- Add missing configurations
- Overwriting `rules.d/audit.rules` file with desired rules based on remote machine architecture

Overridable default variables:

- `auditd_conf_path`: path to `auditd.conf` file
- `auditd_conf`: desired configurations in list of dictionaries format
- `audit_rules_path`: path to `rules.d/audit.rules` file
- `rules_b64`: ruleset for `x86_64` machine
- `rules_b32`: ruleset for `x86` machine

### grub

Performs:

- Include `audit=1` on all kernel lines

Overridable default variables:

- `grub_conf_path`: path to `grub.conf` file

### inactive_password_lock

Performs:

- Ensure password lock inactive period is set to desired value

Overridable default variables:

- `period`: number of days of inactivity

### var_log

Performs:

- Ensure correct permissions set to specified files and directories

Overridable default variables:

- `files`: list of dictionary with these keys, name, owner, group, mode
- `directory`: list of dictionary with these keys, name, owner, group, mode

## Usage

For general usage

```sh
$ ansible-playbook main.yml
```

To run specific role(s), e.g. /etc/hosts, sshd configuration, server setup, etc., please use `--tags <tag1>,<tag2>,<tag3>` when executing through command line.

Example:

```sh
# Only run /etc/hosts and sshd configuration
$ ansible-playbook main.yml --tags etc_hosts,sshd_conf

# Only run splunk and os hardening
$ ansible-playbook main.yml --tags splunk,os_hardening
```
