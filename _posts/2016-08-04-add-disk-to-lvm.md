---
layout: post
subtitle: An aide-memoire for me with KVM/qemu
title: Adding disks with lvm
bigimg: /img/tropicbeach.jpg
disqus: marsbard
comments: true
---

This is one of those posts that's just a placeholder for me to remember how to do something, 
if it helped you too, that's great.

[Original thread on AskUbuntu](http://askubuntu.com/a/459176/33804)

-   Assign a new disk to the VM in virt-manager, or using virsh, as you prefer - choose 
    VirtIO for the disk type if you have no other preference. Might also be a good idea
		to take a snapshot at this point.
-   Enter the VM - handy things to try at this point would be `pvdisplay` to list the
    physical volume(s) and show the volume group name(s), then you could try `vgdisplay trusty-vg` for example
-   Run `sudo pvcreate /dev/vdb` (or whatever device name is used by the new disk you added).
-   Add the physical volume to the volume group: `sudo vgextend trusty-vg /dev/vdb`
-   Allocate the physical volume to a logical volume: `sudo lvextend -l +100%FREE /dev/trusty-vg/root`
-   Resize the file system on the logical volume: `sudo resize2fs /dev/trusty-vg/root`

Yeah it's simple, but I had it written down in a notebook and forgot which one `>.<`
