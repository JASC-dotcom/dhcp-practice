# Linux server and DHCP Installation

## Network interfaces

Command:

```bash
ip a
```

Output:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:8d:c0:4d brd ff:ff:ff:ff:ff:ff
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 86298sec preferred_lft 86298sec
    inet6 fd17:625c:f037:2:a00:27ff:fe8d:c04d/64 scope global dynamic mngtmpaddr 
       valid_lft 86295sec preferred_lft 14295sec
    inet6 fe80::a00:27ff:fe8d:c04d/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:7c:2c:0a brd ff:ff:ff:ff:ff:ff
    altname enp0s8
    inet 10.209.68.1/16 brd 10.209.255.255 scope global dynamic eth1
       valid_lft 21502sec preferred_lft 21502sec
    inet6 fe80::a00:27ff:fe7c:2c0a/64 scope link 
       valid_lft forever preferred_lft forever
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:c6:32:5f brd ff:ff:ff:ff:ff:ff
    altname enp0s9
    inet 192.168.57.10/24 brd 192.168.57.255 scope global eth2
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fec6:325f/64 scope link 
       valid_lft forever preferred_lft forever
```

The 'eth2' interface has the IP '192.168.57.10/24' (internal network 'intnet') and
'eth1' is in the public network.

## Service installation

Command:

```bash
dpkg -l | grep isc-dhcp-server
```

Output:

```
ii  isc-dhcp-server               4.4.3-P1-2                     amd64        ISC DHCP server for automatic IP address assignment
```

### Service status (expected error)

Command:

```bash
sudo systemctl status isc-dhcp-server.service
```

Output:

```
× isc-dhcp-server.service - LSB: DHCP server
     Loaded: loaded (/etc/init.d/isc-dhcp-server; generated)
     Active: failed (Result: exit-code) since Tue 2026-10-06 11:31:27 UTC; 13mi>
       Docs: man:systemd-sysv-generator(8)
    Process: 1924 ExecStart=/etc/init.d/isc-dhcp-server start (code=exited, sta>
        CPU: 18ms

Oct 06 11:31:25 server dhcpd[1936]: bugs on either our web page at www.isc.org >
Oct 06 11:31:25 server dhcpd[1936]: before submitting a bug.  These pages expla>
Oct 06 11:31:25 server dhcpd[1936]: process and the information we find helpful>
Oct 06 11:31:25 server dhcpd[1936]: 
Oct 06 11:31:25 server dhcpd[1936]: exiting.
Oct 06 11:31:27 server isc-dhcp-server[1924]: Starting ISC DHCPv4 server: dhcpd>
Oct 06 11:31:27 server isc-dhcp-server[1924]:  failed!
Oct 06 11:31:27 server systemd[1]: isc-dhcp-server.service: Control process exi>
Oct 06 11:31:27 server systemd[1]: isc-dhcp-server.service: Failed with result >
Oct 06 11:31:27 server systemd[1]: Failed to start isc-dhcp-server.service - LS>
~
```

The service fails because there isn't any subnet configured yet.