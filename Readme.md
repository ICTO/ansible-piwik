# Readme

## Description

*ansible-piwik* is an [Ansible](http://ansible.cc) role.
The playbook contains tasks to provide a Piwik installation (with MySQL backend) running on Apache.

## Provides

1. MySQL
2. Apache
3. PHP
4. Latest Piwik codebase
5. GeoIP database

## Requires

1. Ansible 1.4 or higher
2. Debian 7.3 (other deb-based distros should work too)
3. Vagrant (optional)

Note: Tested on Fedora 20 but requires some custom host_vars see below:

```
---
piwik:
  mysql:
    service: 'mysqld'
  apache:
    user: 'apache'
    group: 'apache'
    service: 'httpd'
```

(If necessary adjust firewall rules to allow http...)

## Usage

### Get the code

```bash
$ git clone git@github.com:ICTO/ansible-piwik.git
```

### Custom settings


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

### Create the playbook file

```
---
- name: Piwik
  hosts: piwik
  roles:
    - ansible-piwik
```

### Create host or group specific variables

Create a *host_vars* or *group_vars* directory where the *ansible.host* file is located.

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
piwik:
  mysql:
    host: 127.0.0.1
    admin_user: root
    admin_pass: NEW
    piwik_user: piwik
    piwik_pass: piwik
    piwik_db: piwik

  settings:
    ip_range: '192.168.33.1'
```

If empty the role will load default variables from *defaults/main.yml*

Make sure to use the Ansible setting *hash_behaviour=merge* in *ansible.cfg*, i.e.:

```
[defaults]
hash_behaviour=merge
```

### Run the playbook

Use *ansible.host* as inventory. Run the playbook only for the remote host *piwik*. Use *vagrant* as the SSH user to connect to the remote host. *-k* enables the SSH password prompt.

```bash
$ ansible-playbook -k -i ansible.host piwik.yml -u vagrant
```

### Example output

```
SSH password: 

PLAY [Piwik] ****************************************************************** 

GATHERING FACTS *************************************************************** 
ok: [127.0.0.1]

TASK: [ansible-piwik | Install MySQL dependencies] **************************** 
changed: [127.0.0.1] => (item=mysql-server)
changed: [127.0.0.1] => (item=python-mysqldb)

TASK: [ansible-piwik | Install PHP dependencies] ****************************** 
changed: [127.0.0.1] => (item=apache2)
changed: [127.0.0.1] => (item=php5)
ok: [127.0.0.1] => (item=libapache2-mod-php5)
changed: [127.0.0.1] => (item=php5-mysql)
changed: [127.0.0.1] => (item=php5-gd)
changed: [127.0.0.1] => (item=php5-geoip)
changed: [127.0.0.1] => (item=php5-ldap)

TASK: [ansible-piwik | Install general dependencies] ************************** 
changed: [127.0.0.1] => (item=unzip)

TASK: [ansible-piwik | Check if empty MySQL admin password] ******************* 
changed: [127.0.0.1]

TASK: [ansible-piwik | Change MySQL root password] **************************** 
changed: [127.0.0.1]

TASK: [ansible-piwik | Manage Piwik db] *************************************** 
changed: [127.0.0.1]

TASK: [ansible-piwik | Manage Piwik db-user] ********************************** 
changed: [127.0.0.1]

TASK: [ansible-piwik | Fetch latest Piwik release] **************************** 
changed: [127.0.0.1]

TASK: [ansible-piwik | Create Piwik destination folder] *********************** 
changed: [127.0.0.1]

TASK: [ansible-piwik | Extract Piwik zipfile] ********************************* 
changed: [127.0.0.1]

TASK: [ansible-piwik | Create Piwik temp folders] ***************************** 
changed: [127.0.0.1] => (item=tmp)
changed: [127.0.0.1] => (item=config)

TASK: [ansible-piwik | Add Piwik Apache Alias] ******************************** 
changed: [127.0.0.1]

TASK: [ansible-piwik | Fetch latest GeoIP database] *************************** 
changed: [127.0.0.1]

TASK: [ansible-piwik | Extract GeoIP database] ******************************** 
changed: [127.0.0.1]

NOTIFIED: [ansible-piwik | Restart Apache] ************************************ 
changed: [127.0.0.1]

PLAY RECAP ******************************************************************** 
127.0.0.1                  : ok=16   changed=15   unreachable=0    failed=0   
```
