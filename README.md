# Ansible role [bareos_sd](https://galaxy.ansible.com/ui/standalone/roles/buluma/bareos_sd/documentation)

Install and configure [Bareos](https://www.bareos.com/) Storage Daemon.

|GitHub|Version|Issues|Pull Requests|Downloads|
|------|-------|------|-------------|---------|
|[![github](https://github.com/buluma/ansible-role-bareos_sd/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bareos_sd/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bareos_sd.svg)](https://github.com/buluma/ansible-role-bareos_sd/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-bareos_sd.svg)](https://github.com/buluma/ansible-role-bareos_sd/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-bareos_sd.svg)](https://github.com/buluma/ansible-role-bareos_sd/pulls/)|[![Ansible Role](https://img.shields.io/ansible/role/d/buluma/bareos_sd)](https://galaxy.ansible.com/ui/standalone/roles/buluma/bareos_sd/documentation)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-bareos_sd/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true

  roles:
    - role: buluma.bareos_sd
      bareos_sd_backup_configurations: true
      bareos_sd_install_debug_packages: true
      bareos_sd_devices:
        - name: "FileStorage"
          description: "File device. A connecting Director must have the same Name and MediaType."
          media_type: "File"
          archive_device: "/var/lib/bareos/storage"
          label_media: true
          random_access: true
          automatic_mount: true
          removable_media: false
          always_open: false
          maximum_concurrent_jobs: 1
        - name: "disabled-device"
          enabled: false
      bareos_sd_directors:
        - name: bareos-dir
          password: "somepassword"
        - name: "disabled-director"
          enabled: false
      bareos_sd_messages:
        - name: "Standard"
          description: "Send relevant messages to the Director."
          director:
            server: bareos-dir
            messages:
              - all
              - "!skipped"
              - "!restored"
          append:
            file: "/var/log/bareos/bareos.log"
            messages:
              - all
              - "!skipped"
              - "!terminate"
          console:
            - all
            - "!skipped"
            - "!saved"
        - name: "disabled-message"
          enabled: false
      bareos_sd_s3_profiles:
        - name: exoscale
          host: "sos.exo.io:443"
          use_https: true
          access_key: "SomeAPIKey"
          secret_key: "SomeSecret"
          pricing_dir: ""
          backend: "s3"
          aws_auth_sign_version: 4
          aws_region: "ch-gva-2"
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-bareos_sd/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: false

  roles:
    - role: buluma.bootstrap
    - role: buluma.bareos_repository
      bareos_repository_enable_tracebacks: true
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-bareos_sd/blob/master/defaults/main.yml):

```yaml
---
# defaults file for bareos_sd

# The Storage Daemon has these configuration parameters.

# Backup the configuration files.
bareos_sd_backup_configurations: false

# Install debug packages. This requires the debug repositories to be enabled.
bareos_sd_install_debug_packages: false

# The hostname of the Storage Daemon.
bareos_sd_hostname: "{{ inventory_hostname }}"

# The messages configuration to use.
bareos_sd_message: "Standard"

# The amount of jobs to run concurrently.
bareos_sd_max_concurrent_jobs: 20

# Enable TLS.
bareos_sd_tls_enable: true

# Verify the peer.
bareos_sd_tls_verify_peer: false

# A list of devices to configure.
bareos_sd_devices: []

# A list of directors to connect to.
bareos_sd_directors: []

# A list of messages to send to the director.
bareos_sd_messages: []

# A list of S3 profiles to configure.
bareos_sd_s3_profiles: []
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-bareos_sd/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | Version |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bootstrap.svg)](https://github.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.bareos_repository](https://galaxy.ansible.com/buluma/bareos_repository)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bareos_repository/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bareos_repository/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bareos_repository.svg)](https://github.com/shadowwalker/ansible-role-bareos_repository)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-bareos_sd/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[Debian](https://hub.docker.com/r/buluma/debian)|bookworm, bullseye, buster|
|[EL](https://hub.docker.com/r/buluma/enterpriselinux)|8, 9|
|[Fedora](https://hub.docker.com/r/buluma/fedora)|38, 39|
|[Ubuntu](https://hub.docker.com/r/buluma/ubuntu)|jammy|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-bareos_sd/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-bareos_sd/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-bareos_sd/blob/master/LICENSE)

## [Author Information](#author-information)

[Shadow Walker](https://buluma.github.io/)
