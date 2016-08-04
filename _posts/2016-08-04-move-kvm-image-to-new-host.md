---
layout: post
title: Move KVM image to a new host
#subtitle: An aide-memoire for me with KVM/qemu
bigimg: /img/tropicbeach.jpg
disqus: marsbard
comments: true
---

Another placeholder just to jog my memory (copied straight from Server Fault, see below)


1. copy the VM's disks from /var/lib/libvirt/images on src host to the same dir on destination host
2. on the source host run `virsh dumpxml VMNAME > domxml.xml` and copy this xml to the dest. host
3. on the destination host run `virsh define domxml.xml`

start thew VM.

- If the disk location differs, you need to edit the xml's devices/disk node to point to the image on the destination host
- If the VM is attached to custom defined networks, you'll need to either edit them out of the xml on the destination host or redefine them as well (`virsh net-dumpxml > netxml.xml` and the `virsh net-define netxml.xml && virsh net-start NETNAME & virsh net-autostart NETNAME`)


[Original answer at Server Fault](http://serverfault.com/a/434070/319703)


