# otus-pacemaker
Virtual sandbox for 2-node pacemaker installation

## How to run
```bash
ansible-galaxy install -r requirements.yml
vagrant up
ansible-playbook site.yml
```
## Add Resources
```bash
pcs property set stonith-enabled=false
pcs resource create ClusterIP ocf:heartbeat:IPaddr2  ip=192.168.56.120 cidr_netmask=32 op monitor interval=30s
pcs resource create ClusterNginx ocf:heartbeat:nginx configfile=/etc/nginx/nginx.conf op monitor timeout="5s" interval="5s"
pcs constraint colocation add ClusterNginx ClusterIP INFINITY
pcs constraint order ClusterIP then ClusterNginx
```

## Useful commands
```bash
pacemakerd --features
pcs status cluster
corosync-cfgtool -s
corosync-cmapctl | grep members
crm_verify -L -V
pcs status
pcs cluster cib
pcs resource standards
pcs resource providers
pcs resource agents ocf:heartbeat
```
