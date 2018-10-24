# otus-pacemaker
Virtual sandbox for 2-node pacemaker installation

##How to run
```bash
ansible-galaxy install -r requirements.yml
vagrant up
ansible-playbook site.yml
```

```
pcs cluster start --all
pcs property set stonith-enabled=false
pcs property set no-quorum-policy=freeze
pcs resource create dlm systemd:dlm op monitor interval=30s on-fail=ignore clone interleave=true ordered=true
pcs resource create clvmd ocf:heartbeat:clvm op monitor interval=30s on-fail=ignore clone interleave=true ordered=true
pcs constraint order start dlm-clone then clvmd-clone
pcs status resources

pvcreate /dev/mapper/mpatha
vgcreate -Ay -cy cluster_vg /dev/mapper/mpatha  
lvcreate -L900M -n cluster_lv cluster_vg
mkfs.gfs2 -j3 -p lock_dlm -t otusha:gfs2 /dev/cluster_vg/cluster_lv
pcs resource create clusterfs Filesystem device="/dev/cluster_vg/cluster_lv" directory="/mnt/gfs2"  fstype="gfs2" "options=noatime" op monitor interval=10s  on-fail=ignore clone interleave=true
pcs constraint order start clvmd-clone then clusterfs-clone
pcs constraint colocation add clusterfs-clone with clvmd-clone
```
