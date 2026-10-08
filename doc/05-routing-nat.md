# Routing and NAT

## Server: forwarding and NAT rule

Commands:

```bash
cat /proc/sys/net/ipv4/ip_forward
sudo iptables -t nat -L POSTROUTING -n -v
ip r
```

Output:

```
vagrant@server:~$ cat /proc/sys/net/ipv4/ip_forward
0
vagrant@server:~$ sudo iptables -t nat -L POSTROUTING -n -v
Chain POSTROUTING (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
vagrant@server:~$ ip r
default via 10.0.2.2 dev eth0 
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 
10.209.0.0/16 dev eth1 proto kernel scope link src 10.209.68.1 
192.168.57.0/24 dev eth2 proto kernel scope link src 192.168.57.10
```

## Client: default route

Command (on c1 and printer):

```bash
ip r
```

Output:

```
vagrant@c1:~$ ip r
default via 192.168.57.10 dev eth1 
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 
192.168.57.0/24 dev eth1 proto kernel scope link src 192.168.57.20
vagrant@printer:~$ ip r
default via 192.168.57.10 dev eth1 
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 
192.168.57.0/24 dev eth1 proto kernel scope link src 192.168.57.111
```

The default route points to 192.168.57.10.

## Connectivity test

Commands (on c1 and printer):

```bash
ping -c 3 8.8.8.8
ping -c 3 google.com
```

Output:

```

```
