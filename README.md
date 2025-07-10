# ansible-role-puppet
Ansible role to install puppetserver and puppet-agent in nodes in AlmaLinux,
Debian and Ubuntu.

# infra
The whole infra is running in incus containers but playbooks should be working
in VM or physical machines. Example of infra can be as following:

```bash
root@ubu-incus:~# incus ls
+----------------+---------+-----------------------+------+-----------+-----------+
|      NAME      |  STATE  |         IPV4          | IPV6 |   TYPE    | SNAPSHOTS |
+----------------+---------+-----------------------+------+-----------+-----------+
| puppetserver   | RUNNING | 10.187.242.28 (eth0)  |      | CONTAINER | 0         |
+----------------+---------+-----------------------+------+-----------+-----------+
| sys-alma9-dev1 | RUNNING | 10.187.242.133 (eth0) |      | CONTAINER | 0         |
+----------------+---------+-----------------------+------+-----------+-----------+
| sys-deb12-dev1 | RUNNING | 10.187.242.18 (eth0)  |      | CONTAINER | 0         |
+----------------+---------+-----------------------+------+-----------+-----------+
| sys-ubu24-dev1 | RUNNING | 10.187.242.99 (eth0)  |      | CONTAINER | 0         |
+----------------+---------+-----------------------+------+-----------+-----------+
```

# inventory hosts
Inventory host file can look like following

```bash

❯ cat inventories/hosts
[all:children]
puppetservers
puppetnodes

[puppetservers]
puppetserver

[puppetnodes]
sys-alma9-dev1
sys-deb12-dev1
sys-ubu24-dev1

[all:vars]
ansible_user = masum

```
# Usages

- Run puppet_server.yml to set up puppetserver.
```bash
ansible-playbook -i inventories/hosts puppet_server.yml
```

- Run r10k.yml to install r10k in puppetmaster.
```bash
ansible-playbook -i inventories/hosts r10k.yml
```

- Run puppet_agent.yml to install puppet-agent in puppetnodes.
```bash
ansible-playbook -i inventories/hosts puppet_agent.yml
```

- Run deployment.yml to deploy environment in puppetserver.
```bash
ansible-playbook -i inventories/hosts deployment.yml
```

# Cert
List all agent certs in puppetserver and sign them.

```bash
puppetserver# puppetserver ca list --all

Requested Certificates:
sys-deb12-dev1 (SHA256)  22:9E:CE:92:9A:CE:E4:E5:61:8F:F5:1F:C1:A3:
               :67:D6:E2:5B:09:01:DB:7A:15:88:34:E6:F4:FB:5B:71:C7

puppetserver# puppetserver ca sign --all
Successfully signed certificate request for sys-deb12-dev1

```
