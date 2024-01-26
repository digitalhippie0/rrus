# rrus
really really useful scripts

here's a few scripts I have written over the years:

* **spamtrainer** will assist in training spamassassin bayes filter
* **beautify** will remove those annoying comments in conf files and present you with the bare content
* **highlight** is a beauty in itself. It is an ouput-highlighter for sylog tails and alike
```
    Usage: highlight RegEx [color] [RegEx color] [RegEx color] [... ...]
       color can be in WHITE RED GREEN YELLOW BLUE PURPLE CYAN
       default is RED. Order of RegEx is relevant.
```
***
* **sasl-auth-failures** will add IPs and/or ASNs to an ever growing ipset, useful for blocking those script kiddies
* **ipset_blocker**, **ipset.conf**, **ipset_blocker.service** is a systemd version of **sasl-auth-failures** listening to a named pipe where (r)syslog writes to
> At the end of your iptables set (after all allow rules!), you will want
```
    -A INPUT -m ... -j ALLOW
    ...
    -A INPUT -m set --match-set by-syslog src -j DROP
    -A INPUT -m set --match-set by-syslog-nets src -j DROP
```
> same applies for IPv6:
```
    -A INPUT -m ... -j ALLOW
    ...
    -A INPUT -m set --match-set by-syslog-ipv6 src -j DROP
    -A INPUT -m set --match-set by-syslog-nets-ipv6 src -j DROP
```
**ipset.conf** is a rsyslog conf file to be placed in ``/etc/rsyslog.d/`` - if you use syslog-ng, it can do the same but you will have to write a similar file. Note that all filter conditions are in this file.

**ipset_blocker.service** is the systemd unit file to be placed in ``/etc/systemd/system/``. You will want to 
```
    # systemctl daemon-reload
    # systemctl enable ipset_blocker.service
    # systemctl start ipset_blocker.service
```
