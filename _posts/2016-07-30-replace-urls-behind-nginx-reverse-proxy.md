---
layout: post
subtitle: Replacing URLs in content served behind nginx reverse proxy
title: Replace URLs with nginx sub_filter
bigimg: /img/tropicbeach.jpg
disqus: marsbard
comments: true
---

I've got a bunch of servers behind an nginx reverse proxy, one in particular, 
[Jenkins](https://jenkins.io) publishes the results of its builds to an 
[Artifactory](https://www.jfrog.com/artifactory/). To do so, in its config
I had to put the URL of the artifactory server, naturally this is in the 
private IP range 192.x.x.x behind the nginx proxy.

However, Jenkins publishes a link to the published artifacts within artifactory as part of its
record of the build data. Obviously, because Jenkins thinks that the address of 
the artifactory server is `http://192.x.x.x:8081/artifactory` it was putting this
into the build information, with the result that there is a broken link, since clients
cannot get to that IP address.

To the rescue comes `sub_filter`, but my first try didn't work, despite having set up
a rewriting filter, the content wasn't being changed. Turns out you have to explicitly 
tell the proxy to send an empty `Accept-Encoding` header, so that all compression is 
disabled.

Here's the final setting I used, this is inside a `location` block after all the other 
proxy commands like `proxy_pass`, `proxy_set_header` etc.:

```
      proxy_set_header Accept-Encoding ""; # no compression allowed or next won't work
      sub_filter "http://192.x.x.x:8081/" "https://artifacts.domain.org/";
      sub_filter_once off;

```
