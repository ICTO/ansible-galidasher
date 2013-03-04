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
[vagrant]
127.0.0.1 ansible_ssh_port=2222

[vagrant:vars]
vnc_pass='local_vnc_passfile'
vnc_port='8080'
mh_server='http://your.matterhorn.server:8080'
mh_user='matterhorn_user'
mh_pass='matterhorn_password'
```

### Run the playbook

Use *ansible.host* as inventory. Run the playbook only for the remote host *vagrant*. Use *vagrant* as the SSH user to connect to the remote host. *-k* enables the SSH password prompt.

```bash
$ ansible-playbook -k -i ansible.host ansible-galidasher/setup.yml --extra-vars="hosts=vagrant user=vagrant"
```

### Example output

```
SSH password: 

PLAY [Galidasher playbook] ********************* 

GATHERING FACTS ********************* 
ok: [127.0.0.1]

TASK: [Install Galidasher dependencies] ********************* 
ok: [127.0.0.1] => (item=git,apache2,python-pycurl)

TASK: [Create Galidasher destination directory: /opt/mh_dashboard] ********************* 
ok: [127.0.0.1]

TASK: [Fetch MH dashboard: https://github.com/flyapen/dashboard.git] ********************* 
changed: [127.0.0.1]

TASK: [Link /opt/mh_dashboard/web to /var/www/galidasher] ********************* 
ok: [127.0.0.1]

TASK: [Install Galidasher config file: /opt/mh_dashboard/etc/mh-dashboard/dashboard.conf] ********************* 
changed: [127.0.0.1]

TASK: [Cronjob agents.json updater: /opt/mh_dashboard/script/dashboard.py] ********************* 
ok: [127.0.0.1]

TASK: [Install Galidasher logrotate file: /etc/logrotate.d/galidasher] ********************* 
ok: [127.0.0.1]

TASK: [Execute updater: /opt/mh_dashboard/script/dashboard.py] ********************* 
changed: [127.0.0.1]

TASK: [Transfer VNC passfile: /opt/mh_dashboard/etc/mh-dashboard/vnc_passfile] ********************* 
ok: [127.0.0.1]

PLAY RECAP ********************* 
127.0.0.1                   : ok=10   changed=3    unreachable=0    failed=0    
```

## Docs and contact

Read more on the Wiki pages about how this playbook works.

http://icto.ugent.be