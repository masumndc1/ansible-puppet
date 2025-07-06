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

- Run deployment.yml to deploy environment in agent nodes.
```bash
ansible-playbook -i inventories/hosts deployment.yml
```
