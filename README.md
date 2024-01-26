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
***

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
**ipset.conf** is a rsyslog conf file to be placed in ``/etc/rsyslog.d/`` - if you use syslog-ng, it can do the same but you will have to write a similar file. Note that all filter conditions are in this file. Restart rsyslog after placing the file and ```# mkfifo /var/spool/rsyslog/ipset-blocker``` before you do so.

**ipset_blocker.service** is the systemd unit file to be placed in ``/etc/systemd/system/``. You will want to 
```
    # systemctl daemon-reload
    # systemctl enable ipset_blocker.service
    # systemctl start ipset_blocker.service
```

You will need ```logger``` from [util-linux](https://github.com/util-linux/util-linux), obviously ```ipset``` from [netfilter.org](https://ipset.netfilter.org/) and some version of perl with installed ```Regexp::IPv4``` and ```Regexp::IPv6``` - most distributions come with all of these pre-installed anyway.

Be very careful! Anything which looks like an IP, either v4 or v6, which is piped into ```/var/spool/rsyslog/ipset-blocker``` will be blocked by the firewall within a second or two. Be sure to adjust ```WHITELIST``` to contain at least your home country. Adjust ```BLACKLIST``` to your liking. An IP originating in one of the blacklisted countries will result in its entire ASN being blocked instead of just the IP itself. This cuts down on attacs dramatically.

The ipsets are created automatically. They have a timeout of 24 days, so no blocking will ocurr for ever.

If you don't want to use systemd or have a different init system (System V init or upstart or ...) you may add an ampersand (```&```) to the ```main``` in the very last line of the script. This will detach it from the calling terminal.
