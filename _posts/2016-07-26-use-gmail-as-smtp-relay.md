---
comments: true
layout: post
title: How to use Gmail as an SMTP relay with Postfix
subtitle: If your mails are getting blackholed you need this!
bigimg: /img/tropicbeach.jpg
disqus: marsbard
---

Many times you want to be able to send emails from a server that you've set up, but unless
you happen to be lucky to have your own IP block, and instead you need to rely on the IP 
address assigned to you by some virtual host provider, you'll probably find that your
mails end up getting [blackholed](https://en.wikipedia.org/wiki/DNSBL) at least some of the
time.

What you can do to get round this is to use Gmail as an SMTP forwarder. It's not against 
the [terms of service](https://www.google.com/intl/en/policies/terms/) as I found out recently
when a misconfiguration in my setup meant that the gmail account I was using for forwarding 
got banned! I filled out the form they presented me asking what I was doing with the account
and I truthfully told them I was trying to use it as an SMTP forwarder. I guess they checked 
the account and found a few test emails from [Jenkins](https://jenkins.io) and decided I wasn't
a bulk emailer after all (now that *is* against the ToS). In any case the account was re-enabled
within fewer than 20 minutes.

This setup uses postfix on the local server but if you have a bunch of servers that need 
email services you might consider setting up a dedicated relay server and pointing each server
application to that for mail services. In this case however, when the application asks
for mail server configuration, we'll use `localhost` without any authentication. The port
will be the standard SMTP port, `25`.

First you need a dedicated Gmail account to use for forwarding. Head over to [Gmail](https://gmail.com), 
sign out if you're logged in, and then select 'Add account' to create a new email account.
I suggest something like `<projectname>.smtp@gmail.com` so you can be clear about where the email
is coming from. Make a note of the password (of course!).

Next you'll need to install postfix. On Debian based systems this can be done with 
`apt-get install postfix`. Choose 'Internet Site' in the first configuration dialog and set the
server address in the next. Probably the default is fine.

Now you'll need to edit the postfix configuration file, `/etc/postfix/main.cf`. You'll probably 
want to edit `myhostname` to reflect the domain name of the server that you set in the postfix 
configuration dialog. Next, set `relayhost` to `[smtp.gmail.com]:587`. 

Add the following lines if they aren't there. If they are there, just change `smtp_sasl_password_maps`
to match the line here:

```
smtp_use_tls = yes 
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/relay_passwd
smtp_sasl_security_options =
```

Ensure that `mydestination` has the name of this machine, not just
as localhost, and if you have a fully qualified domain name, e.g. `application.my.domain` then
be sure to list the unqualified `application` as well. If you want to accept mail from other 
hosts, add their netmasks to `mynetworks`, for example if you might use `192.168.100.0/24` if 
your machines are in that network range.

Now create the file `/etc/postfix/relay_passwd` with the following content, replacing `[[USER]]`
with the Gmail user you created above, and `[[PASSWORD]]` with the password.

```
[smtp.gmail.com]:587 [[USER]]@gmail.com:[[PASSWORD]]
```

Finally run `postmap /etc/postfix/relay_passwd`. This will create a file `relay_passwd.db`
which postfix can use directly.

That's it, should be all good to go now. Reload the postfix service with `systemctl reload postfix`
(if you're using an older OS you might need `/etc/init.d/postfix reload`) and try to send an 
email from your application (remember, set the email server to `localhost` with no authentication).
If you keep an eye on `/var/log/mail.log` you should see the mail get accepted locally and 
forwarded on to gmail. If anything went wrong you should get a good clue about it here too.

