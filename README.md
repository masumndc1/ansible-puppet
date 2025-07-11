# ansible-puppet
Ansible playbooks to install puppetserver and puppet-agent in AlmaLinux,
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
| sys-ubu22-dev1 | RUNNING | 10.187.242.99 (eth0)  |      | CONTAINER | 0         |
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
sys-ubu22-dev1

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
ansible-playbook -i inventories/hosts deployment.yml -e "env=devel"
```

# Cert
List all agent certs in puppetserver and sign them. In AlmaLinux9 or
Ubuntu22, run following.

```bash
puppetserver# /opt/puppetlabs/bin/puppetserver ca list --all

Requested Certificates:
sys-deb12-dev1 (SHA256)  22:9E:CE:92:9A:CE:E4:E5:61:8F:F5:1F:C1:A3:
               :67:D6:E2:5B:09:01:DB:7A:15:88:34:E6:F4:FB:5B:71:C7

puppetserver# /opt/puppetlabs/bin/puppetserver ca sign --all
Successfully signed certificate request for sys-deb12-dev1

```

In debian 12, puppetserver command can be found under

```bash
/opt/puppetlabs/server/bin/puppetserver ca list --all
```


# example
following command should be run as root on puppetnodes.

```bash
[root@sys-alma9-dev1]# puppet agent -t
Info: Using environment 'devel'
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Loading facts
Notice: Requesting catalog from puppetserver:8140 (10.187.242.28)
Notice: Catalog compiled by puppetserver.incus
Info: Caching catalog for sys-alma9-dev1
Info: Applying configuration version '1752186184'
Notice: f540c83f3249b8d8845b4e894f73ab48
Notice: /Stage[main]/Infra::Hostname/Notify[MD5_hash]/message: defined 'message' as 'f540c83f3249b8d8845b4e894f73ab48'
Notice:
    The MD5 hash for the node name is
    f540c83f3249b8d8845b4e894f73ab48
    and os is RedHat

Notice: /Stage[main]/Infra::Hostname/Notify[nodename_hash]/message: defined 'message' as
"\n    The MD5 hash for the node name is\n    f540c83f3249b8d8845b4e894f73ab48\n
and os is RedHat\n"
Notice: khabir added
Notice: /Stage[main]/Infra::Sudo/Notify[khabir]/message: defined 'message' as 'khabir added'
Notice: Custom message from infra!
Notice: /Stage[main]/Infra::Messages/Notify[say_something]/message: defined 'message' as
'Custom message from infra!'
Notice: Applied catalog in 0.46 seconds
[root@sys-alma9-dev1]#

```

# clear cert (careful)
From puppetserver and puppetnodes: clear pem file from following.
This is important when puppetserver or node has been re-installed.
(Be careful. At least you should know what you are doing)

```bash
find /var/lib/puppet/ssl -name '*.pem' -delete
find /var/lib/puppet/ssl/certs -name '*.pem' -delete
```
