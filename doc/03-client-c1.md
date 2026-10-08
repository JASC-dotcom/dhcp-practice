# Client c1: dynamic lease

## Obtained network configuration (c1)

Commands:

```bash
sudo dhclient -r eth1
sudo dhclient -v eth1
ip a
```

Output:

```
vagrant@c1:~$ sudo dhclient -r eth1
vagrant@c1:~$ sudo dhclient -v eth1
Internet Systems Consortium DHCP Client 4.4.3-P1
Copyright 2004-2022 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/eth1/08:00:27:aa:b4:92
Sending on   LPF/eth1/08:00:27:aa:b4:92
Sending on   Socket/fallback
DHCPDISCOVER on eth1 to 255.255.255.255 port 67 interval 3
DHCPOFFER of 192.168.57.21 from 192.168.57.10
DHCPREQUEST for 192.168.57.21 on eth1 to 255.255.255.255 port 67
DHCPACK of 192.168.57.21 from 192.168.57.10
bound to 192.168.57.21 -- renewal in 36411 seconds.
vagrant@c1:~$ ip a
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
       valid_lft 84229sec preferred_lft 84229sec
    inet6 fd17:625c:f037:2:a00:27ff:fe8d:c04d/64 scope global dynamic mngtmpaddr 
       valid_lft 86268sec preferred_lft 14268sec
    inet6 fe80::a00:27ff:fe8d:c04d/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:aa:b4:92 brd ff:ff:ff:ff:ff:ff
    altname enp0s8
    inet 192.168.57.20/24 brd 192.168.57.255 scope global dynamic eth1
       valid_lft 84232sec preferred_lft 84232sec
    inet 192.168.57.21/24 brd 192.168.57.255 scope global secondary dynamic eth1
       valid_lft 86394sec preferred_lft 86394sec
    inet6 fe80::a00:27ff:feaa:b492/64 scope link 
       valid_lft forever preferred_lft forever
```

c1 obtained an address inside the 192.168.57.20-192.168.57.50 range (192.168.57.21).

## DHCP messages in the server log (server)

Command:

```bash
sudo grep -a dhcpd /var/log/syslog | tail -n 20
```

Output:

```
2026-10-08T10:18:36.265300+00:00 server dhcpd[622]: PID file: /var/run/dhcpd.pid
2026-10-08T10:24:33.876305+00:00 server dhcpd[669]: Internet Systems Consortium DHCP Server 4.4.3-P1
2026-10-08T10:24:33.876470+00:00 server dhcpd[669]: Copyright 2004-2022 Internet Systems Consortium.
2026-10-08T10:24:33.876502+00:00 server dhcpd[669]: All rights reserved.
2026-10-08T10:24:33.876537+00:00 server dhcpd[669]: For info, please visit https://www.isc.org/software/dhcp/
2026-10-08T10:24:33.878711+00:00 server dhcpd[669]: Config file: /etc/dhcp/dhcpd.conf
2026-10-08T10:24:33.878767+00:00 server dhcpd[669]: Database file: /var/lib/dhcp/dhcpd.leases
2026-10-08T10:24:33.878805+00:00 server dhcpd[669]: PID file: /var/run/dhcpd.pid
2026-10-08T10:26:50.933985+00:00 server dhcpd[703]: Wrote 0 leases to leases file.
2026-10-08T10:26:50.952774+00:00 server dhcpd[703]: Server starting service.
2026-10-08T10:26:52.974334+00:00 server isc-dhcp-server[691]: Starting ISC DHCPv4 server: dhcpd.
2026-10-08T11:00:05.374812+00:00 server dhcpd[703]: DHCPDISCOVER from 08:00:27:aa:b4:92 via eth2
2026-10-08T11:00:06.391243+00:00 server dhcpd[703]: DHCPOFFER on 192.168.57.20 to 08:00:27:aa:b4:92 (c1) via eth2
2026-10-08T11:00:06.391703+00:00 server dhcpd[703]: DHCPREQUEST for 192.168.57.20 (192.168.57.10) from 08:00:27:aa:b4:92 (c1) via eth2
2026-10-08T11:00:06.392435+00:00 server dhcpd[703]: DHCPACK on 192.168.57.20 to 08:00:27:aa:b4:92 (c1) via eth2
2026-10-08T11:36:06.451918+00:00 server dhcpd[703]: DHCPDISCOVER from 08:00:27:aa:b4:92 via eth2
2026-10-08T11:36:07.536241+00:00 server dhcpd[703]: DHCPOFFER on 192.168.57.21 to 08:00:27:aa:b4:92 (c1) via eth2
2026-10-08T11:36:07.536676+00:00 server dhcpd[703]: DHCPREQUEST for 192.168.57.21 (192.168.57.10) from 08:00:27:aa:b4:92 (c1) via eth2
2026-10-08T11:36:07.537426+00:00 server dhcpd[703]: Wrote 2 leases to leases file.
2026-10-08T11:36:07.538152+00:00 server dhcpd[703]: DHCPACK on 192.168.57.21 to 08:00:27:aa:b4:92 (c1) via eth2
```

The exchange DISCOVER, OFFER, REQUEST and ACK is visible.

## Leases database (server)

Command:

```bash
sudo cat /var/lib/dhcp/dhcpd.leases
```

Output:

```
# The format of this file is documented in the dhcpd.leases(5) manual page.
# This lease file was written by isc-dhcp-4.4.3-P1

# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

lease 192.168.57.20 {
  starts 4 2026/10/08 11:00:06;
  ends 5 2026/10/09 11:00:06;
  cltt 4 2026/10/08 11:00:06;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:aa:b4:92;
  uid "\377'\252\264\222\000\001\000\0012Z45\010\000'\252\264\222";
  client-hostname "c1";
}
lease 192.168.57.21 {
  starts 4 2026/10/08 11:36:07;
  ends 5 2026/10/09 11:36:07;
  cltt 4 2026/10/08 11:36:07;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:aa:b4:92;
  client-hostname "c1";
}
```

The lease for c1 appears with `binding state active` and its MAC address.