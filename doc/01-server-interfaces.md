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
     Active: failed (Result: exit-code) since Tue 2026-10-06 12:28:13 UTC; 5min ago
       Docs: man:systemd-sysv-generator(8)
    Process: 2040 ExecStart=/etc/init.d/isc-dhcp-server start (code=exited, status=1/FAILURE)
        CPU: 20ms

Oct 06 12:28:11 server dhcpd[2052]: bugs on either our web page at www.isc.org or in the README file
Oct 06 12:28:11 server dhcpd[2052]: before submitting a bug.  These pages explain the proper
Oct 06 12:28:11 server dhcpd[2052]: process and the information we find helpful for debugging.
Oct 06 12:28:11 server dhcpd[2052]: 
Oct 06 12:28:11 server dhcpd[2052]: exiting.
Oct 06 12:28:13 server isc-dhcp-server[2040]: Starting ISC DHCPv4 server: dhcpdcheck syslog for diagnostics. ... failed!
Oct 06 12:28:13 server isc-dhcp-server[2040]:  failed!
Oct 06 12:28:13 server systemd[1]: isc-dhcp-server.service: Control process exited, code=exited, status=1/FAILURE
Oct 06 12:28:13 server systemd[1]: isc-dhcp-server.service: Failed with result 'exit-code'.
Oct 06 12:28:13 server systemd[1]: Failed to start isc-dhcp-server.service - LSB: DHCP server.
```

The output indicates a failure related with 1 interface but doesn't tell us why.

Command:

```bash
sudo journalctl -xeu isc-dhcp-server.service
```

Output:

```
░░ Support: https://www.debian.org/support
░░ 
░░ A start job for unit isc-dhcp-server.service has finished with a failure.
░░ 
░░ The job identifier is 574 and the job result is failed.
Oct 06 12:28:11 server systemd[1]: Starting isc-dhcp-server.service - LSB: DHCP server...
░░ Subject: A start job for unit isc-dhcp-server.service has begun execution
░░ Defined-By: systemd
░░ Support: https://www.debian.org/support
░░ 
░░ A start job for unit isc-dhcp-server.service has begun execution.
░░ 
░░ The job identifier is 638.
Oct 06 12:28:11 server isc-dhcp-server[2040]: Launching IPv4 server only.
Oct 06 12:28:11 server dhcpd[2052]: Wrote 0 leases to leases file.
Oct 06 12:28:11 server dhcpd[2052]: 
Oct 06 12:28:11 server dhcpd[2052]: No subnet declaration for eth2 (192.168.57.10).
Oct 06 12:28:11 server dhcpd[2052]: ** Ignoring requests on eth2.  If this is not what
Oct 06 12:28:11 server dhcpd[2052]:    you want, please write a subnet declaration
Oct 06 12:28:11 server dhcpd[2052]:    in your dhcpd.conf file for the network segment
Oct 06 12:28:11 server dhcpd[2052]:    to which interface eth2 is attached. **
Oct 06 12:28:11 server dhcpd[2052]: 
Oct 06 12:28:11 server dhcpd[2052]: 
Oct 06 12:28:11 server dhcpd[2052]: Not configured to listen on any interfaces!
Oct 06 12:28:11 server dhcpd[2052]: 
Oct 06 12:28:11 server dhcpd[2052]: If you think you have received this message due to a bug rather
Oct 06 12:28:11 server dhcpd[2052]: than a configuration issue please read the section on submitting
Oct 06 12:28:11 server dhcpd[2052]: bugs on either our web page at www.isc.org or in the README file
Oct 06 12:28:11 server dhcpd[2052]: before submitting a bug.  These pages explain the proper
Oct 06 12:28:11 server dhcpd[2052]: process and the information we find helpful for debugging.
Oct 06 12:28:11 server dhcpd[2052]: 
Oct 06 12:28:11 server dhcpd[2052]: exiting.
Oct 06 12:28:13 server isc-dhcp-server[2040]: Starting ISC DHCPv4 server: dhcpdcheck syslog for diagnostics. ... failed!
Oct 06 12:28:13 server isc-dhcp-server[2040]:  failed!
Oct 06 12:28:13 server systemd[1]: isc-dhcp-server.service: Control process exited, code=exited, status=1/FAILURE
░░ Subject: Unit process exited
░░ Defined-By: systemd
░░ Support: https://www.debian.org/support
░░ 
░░ An ExecStart= process belonging to unit isc-dhcp-server.service has exited.
░░ 
░░ The process' exit code is 'exited' and its exit status is 1.
Oct 06 12:28:13 server systemd[1]: isc-dhcp-server.service: Failed with result 'exit-code'.
░░ Subject: Unit failed
░░ Defined-By: systemd
░░ Support: https://www.debian.org/support
░░ 
░░ The unit isc-dhcp-server.service has entered the 'failed' state with result 'exit-code'.
Oct 06 12:28:13 server systemd[1]: Failed to start isc-dhcp-server.service - LSB: DHCP server.
░░ Subject: A start job for unit isc-dhcp-server.service has failed
░░ Defined-By: systemd
░░ Support: https://www.debian.org/support
░░ 
░░ A start job for unit isc-dhcp-server.service has finished with a failure.
░░ 
░░ The job identifier is 638 and the job result is failed.

```

Now it indicates a failure because there is no subnet declaration for the interface 'eth2' yet.