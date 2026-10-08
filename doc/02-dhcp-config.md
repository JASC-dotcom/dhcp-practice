# DHCP Service configuration

## Configuration file

Command:

```bash
cat /etc/dhcp/dhcpd.conf
```

Output:

```
# Globals
default-lease-time 86400; # 1 día
max-lease-time 691200; # 8 días
option domain-name "tunombre.test";
option domain-name-servers 10.0.0.2, 4.4.4.4;
authoritative;

# Subnet
subnet 192.168.57.0 netmask 255.255.255.0 {
  range 192.168.57.20 192.168.57.50;
  option routers 192.168.57.10;
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.57.255;
}
```

Global lease times (1 day / 8 days), domain name (joseantonio.test), name servers and the
dynamic range 192.168.57.20-192.168.57.50

## Syntax check

Command:

```bash
sudo dhcpd -t
```

Output:

```
Internet Systems Consortium DHCP Server 4.4.3-P1
Copyright 2004-2022 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/
Config file: /etc/dhcp/dhcpd.conf
Database file: /var/lib/dhcp/dhcpd.leases
PID file: /var/run/dhcpd.pid
```

## Service status

Command:

```bash
sudo systemctl restart isc-dhcp-server.service
sudo systemctl status isc-dhcp-server.service
```

Output:

```
● isc-dhcp-server.service - LSB: DHCP server
     Loaded: loaded (/etc/init.d/isc-dhcp-server; generated)
     Active: active (running) since Thu 2026-10-08 10:26:52 UTC; 7s ago
       Docs: man:systemd-sysv-generator(8)
    Process: 691 ExecStart=/etc/init.d/isc-dhcp-server start (code=exited, stat>
      Tasks: 1 (limit: 496)
     Memory: 4.3M
        CPU: 33ms
     CGroup: /system.slice/isc-dhcp-server.service
             └─703 /usr/sbin/dhcpd -4 -q -cf /etc/dhcp/dhcpd.conf eth2

Oct 08 10:26:50 server systemd[1]: Starting isc-dhcp-server.service - LSB: DHCP>
Oct 08 10:26:50 server isc-dhcp-server[691]: Launching IPv4 server only.
Oct 08 10:26:50 server dhcpd[703]: Wrote 0 leases to leases file.
Oct 08 10:26:50 server dhcpd[703]: Server starting service.
Oct 08 10:26:52 server isc-dhcp-server[691]: Starting ISC DHCPv4 server: dhcpd.
Oct 08 10:26:52 server systemd[1]: Started isc-dhcp-server.service - LSB: DHCP 
```

## Listening UDP port

Command:

```bash
sudo ss -lun
```

Output:

```
State    Recv-Q   Send-Q     Local Address:Port     Peer Address:Port  Process  
UNCONN   0        0                0.0.0.0:67            0.0.0.0:*              
UNCONN   0        0              127.0.0.1:323           0.0.0.0:*              
UNCONN   0        0                0.0.0.0:68            0.0.0.0:*              
UNCONN   0        0                0.0.0.0:68            0.0.0.0:*              
UNCONN   0        0                  [::1]:323              [::]:*
```

The service is active (running) and listens on UDP 67