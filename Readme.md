# Readme

## Description

*ansible-piwik* is an [Ansible](http://ansible.cc) playbook.
The playbook contains tasks to install Piwik (with MySQL backend) running on Apache.

## Requirements

### Ansible

Everything you should know about Ansible is documented on the [Ansible](http://ansible.cc/docs/gettingstarted.html) site...

### Supported platforms

#### Debian wheezy

Playbook tested on *Debian-7.0-rc1-amd64*, probably works on other Debian versions too. Heavily depends on *apt*, sorry CentOS users...

#### Ansible >= 1.0

Any Ansible version >= 1.0 should work. If not, please use the issue tracker to report any bugs.

## Usage

### Get the code

```bash
$ git clone git@github.ugent.be:tberton/ansible-piwik.git
```

### Create an Ansible inventory file

Following example makes Ansible aware of a box reachable through SSH on 127.0.0.1, port 2222.

```bash
$ vi ansible.host
```

with

```
[piwik]
127.0.0.1 ansible_ssh_port=2222
```

The *ansible-piwik* playbook is only executed for the host/group *piwik*.

### Create group specific variables

Create a *group_vars* directory where the *ansible.host* file is located.

```bash
$ mkdir group_vars
```

Create the file *piwik* in the newly created directory.

```bash
$ cd group_vars
$ vi piwik
```

with

```yaml
---
mysql:
  admin_user: root
  admin_password: NEW
  host: 127.0.0.1

piwik_settings:
  contact: 'your@email.com'
  trustedhost: 'ip running piwik'
  superuser: 'admin'
  password: 'md5sum of password'
  salt: '991c3720d8762830ddeeea1201aaa22a'
  ip_range: 'All'

piwik_sites:
  - name: 'example1'
    url: 'www.example1.com'
  - name: 'example2'
    url: 'www.example2.com'

ldap_settings:
  server: 'ldaps://ldapserver'
  basedn: 'base_dn'
  user: 'user'
  pass: 'pass'
  filter: '(objectClass=person)'
```

### Run the playbook

Use *ansible.host* as inventory. Run the playbook only for the remote host *piwik*. Use *vagrant* as the SSH user to connect to the remote host. *-k* enables the SSH password prompt.

```bash
$ ansible-playbook -k -i ansible.host ansible-piwik/setup.yml --extra-vars="user=vagrant"
```

### Example output

```
SSH password: 

```

## Docs and contact

Read more on the Wiki pages about how this playbook works.

http://icto.ugent.be
