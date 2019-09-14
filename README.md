# Ansible Role Install NetData

The use of the role of ansible is the installation of NetData for many Linux distributions

## Supported distributions:

  * Debian
  * Ubuntu
  * CentOS
  * Fedora

## Install role:

### From Ansible Galaxy:

1. Put in to file `galaxy.yml:

```yaml
- name: netdata
  src: karolcode.netdata
  version: "v0.1.2"
```

2. Run command: `ansible-galaxy install -r galaxy.yml`


### From GitHub:

1. Put in to file `galaxy.yml:


```yaml
- name: netdata
  src: https://github.com/KarolCode/Ansible-Role-NetData
  version: "v0.1.2"
```

2. Run command: `ansible-galaxy install -r galaxy.yml`

## Application example:

```yaml
  tasks:

    - name: Install NetData.
      import_role:
        name: netdata
      vars:
        netdata_service_is: started   # started / stopped
        netdata_when_boot:  disabled  # enabled / disabled
      tags:
        - netdata
```

## Multi Linux distribution:

### Variable import for many distributions:

This Role of Ansible have support for many Linux distribution and many release,
thanks to cascading inporting variables for Linux distributions.

```yaml
- name: Load the variable tree for distribution
  include_vars: "{{ item }}"
  ignore_errors: yes
  failed_when: false
  with_items:
    - "families/{{ ansible_os_family }}.yml"
    - "distributions/{{ ansible_distribution }}.yml"
    - "releases/{{ ansible_distribution }}-{{ ansible_distribution_major_version}}.yml"
```
The import order of variables:
  1. For the distribution family.
  2. For system distribution.
  3. For major distribution release

Variables are imported sequentially and in case any existing exists, it is replace with a new one.
An attempt to import a non-existent file is skipped and does not return an error.

For example:

```text
├── distributions
│   └── Fedora.yml
├── families
│   ├── Debian.yml
│   └── RedHat.yml
└── releases

```

### Service management:

Using the ansible role variables `netdata_service_is` i `netdata_when_boot`, Example:

```yaml
- name: Install NetData.
  import_role:
    name: netdata
  vars:
    netdata_service_is: started   # started / stopped
    netdata_when_boot:  disabled  # enabled / disabled
```

You can decide whether the service should be started after installation.
You can set whether the service should start the with system start.

### Available tasks in the Eole of Ansible:

The ansible role supports additional tasks, by default it is the installation of NetData.

  * `main` - Install NetData daemon (default).
  * `reload` - Reload NetData daemon
  * `enable` - Enable the NetData daemon during the operating system startup.
  * `disable` - Disable the NetData daemon during the operating system startup.
  * `start` - Start NetData daemon
  * `stop` - Stop NetData daemon
  * `restart` - Restart NetData daemon

For example:

```yaml
  - name: Install NetData
      import_role:
        name: netdata
        tasks_from: enable
```

### Available tags:

  - `netdata_install` - Performs only the installation.
  - `netdata_service` - Performs only service operations
  - `netdata_check`   - Performs a version check

Example of use:

```bash
ansible-playbook playbooks/stack.yml --tags netdata_service
```
