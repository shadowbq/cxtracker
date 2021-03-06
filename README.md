# CxTracker

[![Build Status](https://travis-ci.org/shadowbq/cxtracker.svg)](https://travis-ci.org/shadowbq/cxtracker)

Connection Tracker - is a passive network connection tracker 

## About

CxTracker (Connection Tracker) is a passive network connection tracker 
for profiling, history, auditing and network discovery. It can be used 
as an replacement for sancp in the sguil setup. It handles VLANs (2 layers)
and IPv6 out of the box.

## Install

See INSTALL.md

## USE 

### cxtracker (The C version)
I use it now instead of sancp in my sguil setup.
See '$ cxtracker --help' for updated info

```shell
cxtracker --help
Usage:
     $ cxtracker [options]

     OPTIONS:

     -?             Help
     -V             Version and compiled in options.
     -v             Verbose output.
     -i <iface>     Interface to sniff from.
     -f <format>    Output format line. See Format options.
     -b <bfp>       Berkley packet filter.
     -d <dir>       Directory to write session files to.
     -D             Enable daemon mode.
     -u <user>      User to drop priveleges to after daemonising.
     -g <group>     Group to drop priveleges to after daemonising.
     -T <dir>       Direct to chroot into.
     -P <path>      Path to PID file (/var/run).
     -p <file>      Name of pidfile (cxtracker.pid).
     -r <pcap>      PCAP file to read.
     -w <name>      Dump PCAP to file with specified prefix.
     -F             Flush output after every write to dump file.
     -s <bytes>     Roll over dump file based on size.
     -t <interval>  Roll over dump file based on time intervals.
     -x <bytes>     Amount of space to leave free on disk.
     -A             Write packets to directories by date (YYYY-MM-DD)
```

### Example

Running cxtracker in foreground:
 Only IPv4 traffic:
 `./cxtracker -d /nsm_data/sensor1/sancp -u nsm -g nsm -i eth1 -b 'ip'`

 Only VLAN and IPv4 traffic:
 `./cxtracker -d /nsm_data/sensor1/sancp -u nsm -g nsm -i eth1 -b 'vlan and ip'`

 vlan and IPv4 and IPv6:
 `./cxtracker -d /nsm_data/sensor1/sancp -u nsm -g nsm -i eth1`

## LICENSE

This program is served 'as is'. We take no responsibility for anything :)