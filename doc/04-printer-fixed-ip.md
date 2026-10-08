# Printer: fixed IP by MAC

## Host reservation in dhcpd.conf

Command:

```bash
sudo grep -A4 "host printer" /etc/dhcp/dhcpd.conf
```

Output:

```
host printer {
  hardware ethernet 08:00:27:AA:BB:01;
  fixed-address 192.168.57.111;
  default-lease-time 7200;
}
```

## Syntax check and restart

Commands:

```bash
sudo dhcpd -t
sudo systemctl restart isc-dhcp-server.service
sudo systemctl status isc-dhcp-server.service --no-pager -l
sudo ss -lun
```

Output:

```
vagrant@server:~$ sudo dhcpd -t
Internet Systems Consortium DHCP Server 4.4.3-P1
Copyright 2004-2022 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/
Config file: /etc/dhcp/dhcpd.conf
Database file: /var/lib/dhcp/dhcpd.leases
PID file: /var/run/dhcpd.pid
vagrant@server:~$ sudo systemctl restart isc-dhcp-server.service
vagrant@server:~$ sudo systemctl status isc-dhcp-server.service
● isc-dhcp-server.service - LSB: DHCP server
     Loaded: loaded (/etc/init.d/isc-dhcp-server; generated)
     Active: active (running) since Thu 2026-10-08 12:00:15 UTC; 12s ago
       Docs: man:systemd-sysv-generator(8)
    Process: 79963 ExecStart=/etc/init.d/isc-dhcp-server start (code=exited, st>
      Tasks: 1 (limit: 496)
     Memory: 6.2M
        CPU: 30ms
     CGroup: /system.slice/isc-dhcp-server.service
             └─79975 /usr/sbin/dhcpd -4 -q -cf /etc/dhcp/dhcpd.conf eth2

Oct 08 12:00:13 server systemd[1]: Starting isc-dhcp-server.service - LSB: DHCP>
Oct 08 12:00:13 server isc-dhcp-server[79963]: Launching IPv4 server only.
Oct 08 12:00:13 server dhcpd[79975]: Wrote 0 deleted host decls to leases file.
Oct 08 12:00:13 server dhcpd[79975]: Wrote 0 new dynamic host decls to leases f>
Oct 08 12:00:13 server dhcpd[79975]: Wrote 3 leases to leases file.
Oct 08 12:00:13 server dhcpd[79975]: Server starting service.
Oct 08 12:00:15 server isc-dhcp-server[79963]: Starting ISC DHCPv4 server: dhcp>
Oct 08 12:00:15 server systemd[1]: Started isc-dhcp-server.service - LSB: DHCP >
vagrant@server:~$ sudo ss -lun
State    Recv-Q   Send-Q     Local Address:Port     Peer Address:Port  Process  
UNCONN   0        0                0.0.0.0:67            0.0.0.0:*              
UNCONN   0        0              127.0.0.1:323           0.0.0.0:*              
UNCONN   0        0                0.0.0.0:68            0.0.0.0:*              
UNCONN   0        0                0.0.0.0:68            0.0.0.0:*              
UNCONN   0        0                  [::1]:323              [::]:*
```

## Release and renew (printer)

Commands:

```bash
sudo dhclient -r eth1
sudo dhclient -v eth1
ip a
```

Output:

```
vagrant@printer:~$ sudo dhclient -r eth1
vagrant@printer:~$ sudo dhclient -v eth1
Internet Systems Consortium DHCP Client 4.4.3-P1
Copyright 2004-2022 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/eth1/08:00:27:aa:bb:01
Sending on   LPF/eth1/08:00:27:aa:bb:01
Sending on   Socket/fallback
DHCPDISCOVER on eth1 to 255.255.255.255 port 67 interval 3
DHCPOFFER of 192.168.57.111 from 192.168.57.10
DHCPREQUEST for 192.168.57.111 on eth1 to 255.255.255.255 port 67
DHCPACK of 192.168.57.111 from 192.168.57.10
RTNETLINK answers: File exists
bound to 192.168.57.111 -- renewal in 2941 seconds.
vagrant@printer:~$ ip a
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
       valid_lft 85978sec preferred_lft 85978sec
    inet6 fd17:625c:f037:2:a00:27ff:fe8d:c04d/64 scope global dynamic mngtmpaddr 
       valid_lft 86354sec preferred_lft 14354sec
    inet6 fe80::a00:27ff:fe8d:c04d/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:aa:bb:01 brd ff:ff:ff:ff:ff:ff
    altname enp0s8
    inet 192.168.57.111/24 brd 192.168.57.255 scope global dynamic eth1
       valid_lft 6779sec preferred_lft 6779sec
    inet6 fe80::a00:27ff:feaa:bb01/64 scope link 
       valid_lft forever preferred_lft forever
```

printer obtained 192.168.57.111, matching its MAC address.

## Lease in the server

Command:

```bash
sudo cat /var/lib/dhcp/dhcpd.leases
```

Output:

```
vagrant@server:~$ sudo cat /var/lib/dhcp/dhcpd.leases
# The format of this file is documented in the dhcpd.leases(5) manual page.
# This lease file was written by isc-dhcp-4.4.3-P1

# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

lease 192.168.57.20 {
  starts 4 2026/10/08 11:00:06;
  ends 5 2026/10/09 11:00:06;
  tstp 5 2026/10/09 11:00:06;
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
  tstp 5 2026/10/09 11:36:07;
  cltt 4 2026/10/08 11:36:07;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:aa:b4:92;
  client-hostname "c1";
}
lease 192.168.57.22 {
  starts 4 2026/10/08 11:53:57;
  ends 5 2026/10/09 11:53:57;
  tstp 5 2026/10/09 11:53:57;
  cltt 4 2026/10/08 11:53:57;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:aa:bb:01;
  uid "\377'\252\273\001\000\001\000\0012Z@\324\010\000'\252\273\001";
  client-hostname "printer";
}
server-duid "\000\001\000\0012ZBM\010\000'\3062_";
```

## c1 is unaffected

Command (on c1, after release and renew):

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
       valid_lft 81908sec preferred_lft 81908sec
    inet6 fd17:625c:f037:2:a00:27ff:fe8d:c04d/64 scope global dynamic mngtmpaddr 
       valid_lft 86354sec preferred_lft 14354sec
    inet6 fe80::a00:27ff:fe8d:c04d/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:aa:b4:92 brd ff:ff:ff:ff:ff:ff
    altname enp0s8
    inet 192.168.57.20/24 brd 192.168.57.255 scope global dynamic eth1
       valid_lft 81911sec preferred_lft 81911sec
    inet 192.168.57.21/24 brd 192.168.57.255 scope global secondary dynamic eth1
       valid_lft 84072sec preferred_lft 84072sec
    inet6 fe80::a00:27ff:feaa:b492/64 scope link 
       valid_lft forever preferred_lft forever
```

c1 still receives an address from the dynamic range.