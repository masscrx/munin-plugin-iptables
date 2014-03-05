munin-plugin + firewall (iptables) - 0.2
============

Munin plugin for counting dropped packets, and iptables rules for protect syn flood and restrict incoming traffic.

### Releases:
0.1 - Basic ruleset with munin plugin

0.2 - Added limits for logging, before without limits file growed up very fast (after 1 month - 300MB), I also added simple syn flood protection and new gauge to munin plugin

### This packet include:

1.Munin plugin written in bash

2.Iptables rules

### Specification
File 'fw' contains simple iptables rules for allow tcp/udp services, in addition there are rules for logging all dropped packets.
You can define which ports do you want to open, please don't change rules about logging dropped packets, munin plugin works on them.

Munin plugin and all configuration was tested only on Debian with rsyslog, I assume that your paths are:

Shared munin plugins: **/usr/share/munin/plugins/**

Rsyslog folder with configuration files: **/etc/rsyslog.d/**

Munin directory: **/etc/munin/**



# How it works?

#### IPtables
- Firewall rules write to (specified in rsyslog's configuration) file, default to /var/log/iptables.log all dropped events

#### Munin plugin
- Munin plugin read iptables counters and count all dropped packets (appropriate rules are commented)

# Configuration:

#### Rsyslog
1.Add two lines below to /etc/rsyslog.d/iptables.conf

:msg, contains, "IPTables" /var/log/iptables.log

& ~

2.Restart rsyslog service - /etc/init.d/rsyslog restart

#### Iptables
1.Change file settings.conf for your needs

2.Execute fw file

#### Munin-node
1.Copy fw_dropped to /usr/share/munin/plugins

2.Make symbolic link to **fw_dropped** file by *ln -s /usr/share/munin/plugins/fw_dropped /etc/munin/plugins/fw_dropped*

3.Add to file **/etc/munin/plugin-conf.d/munin-node**

[fw_dropped]

user root

env.in_warning some_digit

env.out_warning some_digit

env.in_invalid_warning some_digit

env.apache_warning some_digit

env.synflood_warning some_digit



* some_digit could be decimal or normal, for example 0.6 or 5, default this all warning values are set to 2


# Benchmark:
Simple statistics with use of this ruleset

* siege -b -c 10 192.168.1.110/

Normal - normal system resources usage

1 - Under test, without rule set

2 - Under test, with ruleset

      |   Normal  |    1   |    2   |
|:---:|:---------:|:------:|:------:|
| CPU | 0.5 - 1 % |   50%  |   2%   |
| SYS |   1 - 6%  | 35-50% |   7%   |
| MEM |   290 MB  | 440 MB | 312 MB |

* hping3 -S --flood 192.168.1.110

Normal - normal system resources usage

1 - Under test, without rule set

2 - Under test, with ruleset

      |   Normal  |    1   |    2   |
|:---:|:---------:|:------:|:------:|
| CPU | 0.5 - 1 % | 30-50% |  3-5%  |
| SYS |   1 - 6%  | 15-40% |   7%   |
| MEM |   290 MB  | 312 MB | 312 MB |

So as you see, this ruleset protect against 'GET/HTTP attack', in new release I'll try to improve them to more protect against medium SYN attack.









