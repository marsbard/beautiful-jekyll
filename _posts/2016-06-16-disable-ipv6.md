---
layout: post
title: Disabling IPv6 on Linux
subtitle: Those inexplicable network timeouts.... >.<
bigimg: /img/path.jpg
---

When you're looking at a seemingly stuck network process and you realise
there's some kind of IPv6 problem, here's a command that will shut the IPv6
stack down straight away and hand control back to the IPv4 stack allowing 
your application to continue: 

```
echo 1 > /proc/sys/net/ipv6/conf/all/disable_ipv6 

```
You can replace `all` with a particular interface if you like, like `eth0`
or `br0` for example.

To make the change permanent, edit `/etc/sysctl.conf` and add these lines:

```
# to disable IPv6 on all interfaces system wide
net.ipv6.conf.all.disable_ipv6 = 1

# to disable IPv6 on a specific interface (e.g., eth0, lo)
net.ipv6.conf.lo.disable_ipv6 = 1
net.ipv6.conf.eth0.disable_ipv6 = 1
```

eek

_(Source)[http://ask.xmodulo.com/disable-ipv6-linux.html]_
