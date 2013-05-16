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
$ git clone git@github.ugent.be:Onderwijstechnologie/ansible-piwik.git
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

PLAY [Piwik playbook] ********************* 

GATHERING FACTS ********************* 
ok: [127.0.0.1]

TASK: [Fail if no MySQL configuration] ********************* 
skipping: [127.0.0.1]

TASK: [Fail if no sites defined] ********************* 
skipping: [127.0.0.1]

TASK: [Install MySQL server] ********************* 
changed: [127.0.0.1]

TASK: [Change MySQL root password] ********************* 
changed: [127.0.0.1]

TASK: [Install MySQLdb python package] ********************* 
changed: [127.0.0.1]

TASK: [Manage Piwik db] ********************* 
changed: [127.0.0.1]

TASK: [Manage Piwik db-user] ********************* 
changed: [127.0.0.1]

TASK: [Transfer SQL file] ********************* 
changed: [127.0.0.1]

TASK: [Execute Piwik SQL files] ********************* 
changed: [127.0.0.1]

TASK: [Install Piwik dependencies] ********************* 
changed: [127.0.0.1] => (item=apache2)
changed: [127.0.0.1] => (item=php5)
ok: [127.0.0.1] => (item=libapache2-mod-php5)
changed: [127.0.0.1] => (item=php5-mysql)
changed: [127.0.0.1] => (item=php5-gd)
changed: [127.0.0.1] => (item=php5-geoip)
changed: [127.0.0.1] => (item=php5-ldap)
ok: [127.0.0.1] => (item=unzip)

TASK: [Fetch latest Piwik release] ********************* 
changed: [127.0.0.1]

TASK: [Create Piwik destination folder] ********************* 
changed: [127.0.0.1]

TASK: [Extract Piwik zipfile] ********************* 
changed: [127.0.0.1]

TASK: [Create Piwik temp folders] ********************* 
changed: [127.0.0.1] => (item=tmp)
changed: [127.0.0.1] => (item=tmp/templates_c)
changed: [127.0.0.1] => (item=tmp/assets)
changed: [127.0.0.1] => (item=tmp/tcpdf)
changed: [127.0.0.1] => (item=config)

TASK: [Create Piwik fragments folder] ********************* 
changed: [127.0.0.1]

TASK: [Add Piwik config] ********************* 
changed: [127.0.0.1]

TASK: [Add Piwik apache Alias] ********************* 
changed: [127.0.0.1]

TASK: [Restart Apache] ********************* 
changed: [127.0.0.1]

TASK: [Fetch latest GeoIP database] ********************* 
changed: [127.0.0.1]

TASK: [Extract GeoIP database] ********************* 
changed: [127.0.0.1]

TASK: [Rename GeoIP database] ********************* 
changed: [127.0.0.1]

TASK: [Fetch LDAP plugin] ********************* 
changed: [127.0.0.1]

TASK: [Extract LDAP plugin] ********************* 
changed: [127.0.0.1]

TASK: [Fix LDAP plugin dir permissions] ********************* 
changed: [127.0.0.1]

TASK: [Enable LDAP plugin] ********************* 
changed: [127.0.0.1]

TASK: [Activate LDAP plugin] ********************* 
changed: [127.0.0.1]

TASK: [Add Piwik ldap config] ********************* 
changed: [127.0.0.1]

TASK: [Create Piwik config] ********************* 
changed: [127.0.0.1]

TASK: [Generate API auth token] ********************* 
changed: [127.0.0.1]

TASK: [Check if site is present] ********************* 
failed: [127.0.0.1] => (item={'url': '127.0.0.1', 'name': 'localhost'}) => {"changed": true, "cmd": "bash -c \"[ `curl 'http://127.0.0.1/piwik/index.php?module=API&method=SitesManager.getSitesIdFromSiteUrl&url=http://127.0.0.1&format=JSON&token_auth=c0e024d9200b5705bc4804722636378a'` == '[]' ] && curl 'http://127.0.0.1/piwik/index.php?module=API&method=SitesManager.addSite&siteName=localhost&urls=127.0.0.1&format=json&token_auth=c0e024d9200b5705bc4804722636378a'\" ", "delta": "0:00:05.291130", "end": "2013-05-16 08:49:52.592105", "item": {"name": "localhost", "url": "127.0.0.1"}, "rc": 1, "start": "2013-05-16 08:49:47.300975"}
stderr:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    16  100    16    0     0      3      0  0:00:05  0:00:05 --:--:--     3
...ignoring

PLAY RECAP ********************* 
127.0.0.1                   : ok=29   changed=28   unreachable=0    failed=0    
```

## Docs and contact

Read more on the Wiki pages about how this playbook works.

http://icto.ugent.be
