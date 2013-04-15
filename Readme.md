# Readme

## Description

*ansible-galidasher* is an [Ansible](http://ansible.cc) playbook.
The playbook contains tasks to install the Matterhorn web dashboard interface (MH dashboard).

## Requirements

### Ansible

Everything is documented on the [Ansible](http://ansible.cc/docs/gettingstarted.html) site...

### Supported platforms

Playbook tested on *Debian-7.0-b4-amd64*, probably works on other Debian versions too. Heavily depends on *apt*, sorry CentOS users...

## Usage

### Get the code

```bash
$ git clone git@github.com:ICTO/ansible-galidasher.git
```

### Create a host file

Following example make ansible aware of the Vagrant box reachable on localhost port 2222.

```bash
$ vi ansible.host
```

with

```ini
[galidasher]
127.0.0.1 ansible_ssh_port=2222
```

### Create group specific variables

Create the group_vars directory where *ansible.host* file is located.

```bash
$ mkdir group_vars
```

Create a file in the newly created directory matching your group.

```bash
$ cd group_vars
$ vi galidasher
```

with

```yaml
---
matterhorn:
  server: 'http://matterhorn.server.com:8080' 	# Matterhorn URL
  user: 'matterhorn_system_account'				# Matterhorn user		
  pass: 'matterhorn_password'					# Matterhorn password

vnc_local_dir_pass: '/home/user/vnc_pass_dir'	# Local directory containing VNC password files

galicaster_agents:
  - name: 'Galicaster agent 1 name'
    hostname: 'Galicaster agent 1 hostname'
    vnc:
      passfile: 'galicaster1_vnc_password_file'
      port: '8080'

  - name: 'Galicaster agent 2 name'
    hostname: 'Galicaster agent 2 hostname'
	vnc:
      passfile: 'galicaster2_vnc_password_file'
      port: '8000'

apache_ldap:
  msg: 'LOGIN MESSAGE'
  url: 'LDAP_URL'
  bind: 'LDAP_BIND'
  password: 'LDAP_PASS'
  users:
    - userA
    - userB
```

Omit *apache_ldap* to skip LDAP authentication on the galidasher webroot directory.

### Run the playbook

Use *ansible.host* as inventory. Run the playbook only for the remote host *galidasher*. Use *vagrant* as the SSH user to connect to the remote host. *-k* enables the SSH password prompt.

```bash
$ ansible-playbook -k -i ansible.host ansible-galidasher/setup.yml --extra-vars="hosts=galidasher user=vagrant"
```

### Example output

```
SSH password: 

PLAY [Galidasher playbook] ********************* 

GATHERING FACTS ********************* 
ok: [127.0.0.1]

TASK: [Fail if no agents are defined] ********************* 
skipping: [127.0.0.1]

TASK: [Fail when Matterhorn settings are absent] ********************* 
skipping: [127.0.0.1]

TASK: [Fail when local directory with VNC passfiles is not defined] ********************* 
skipping: [127.0.0.1]

TASK: [Install Galidasher dependencies] ********************* 
ok: [127.0.0.1] => (item=git,apache2,python-pycurl,imagemagick,vncsnapshot)

TASK: [Enable Apache LDAP Module] ********************* 
changed: [127.0.0.1]

TASK: [Install Apache config Galidasher: /etc/apache2/conf.d/galidasher.conf] ********************* 
ok: [127.0.0.1]

TASK: [Create Galidasher destination directory: /opt/mh_dashboard] ********************* 
ok: [127.0.0.1]

TASK: [Fetch MH dashboard: https://github.com/flyapen/dashboard.git] ********************* 
changed: [127.0.0.1]

TASK: [Create Galidasher VNC directory: /opt/mh_dashboard/etc/mh-dashboard/vnc] ********************* 
ok: [127.0.0.1]

TASK: [Link /opt/mh_dashboard/web to /var/www/galidasher] ********************* 
ok: [127.0.0.1]

TASK: [Install Galidasher config file: /opt/mh_dashboard/etc/mh-dashboard/dashboard.conf] ********************* 
changed: [127.0.0.1]

TASK: [Cronjob agents.json updater: /opt/mh_dashboard/script/dashboard.py] ********************* 
ok: [127.0.0.1]

TASK: [Install Galidasher logrotate file: /etc/logrotate.d/galidasher] ********************* 
ok: [127.0.0.1]

TASK: [Transfer VNC passfiles: ${vnc_local_dir_pass}] ********************* 
ok: [127.0.0.1] => (item=/home/user/vnc_pass_dir/galicaster1_vnc_password_file)
ok: [127.0.0.1] => (item=/home/user/vnc_pass_dir/galicaster2_vnc_password_file)

TASK: [Create agents.conf file: add vncpasswdFile entry] ********************* 
ok: [127.0.0.1] => (item={'hostname': 'Galicaster agent 1 hostname', 'vnc': {'passfile': 'galicaster1_vnc_password_file', 'port': '8080'}, 'name': 'Galicaster agent 1 name'})

TASK: [Create agents.conf file: add VNCPort entry] ********************* 
ok: [127.0.0.1] => (item={'hostname': 'Galicaster agent 2 hostname', 'vnc': {'passfile': 'galicaster2_vnc_password_file', 'port': '8000'}, 'name': 'Galicaster agent 2 name'})

TASK: [Execute updater: /opt/mh_dashboard/script/dashboard.py] ********************* 
changed: [127.0.0.1]

PLAY RECAP ********************* 
127.0.0.1                   : ok=15   changed=4    unreachable=0    failed=0    
```

## Docs and contact

Read more on the Wiki pages about how this playbook works.

http://icto.ugent.be

[setup.yml](setup.yml)
[agents.yml](vars/agents.yml)
