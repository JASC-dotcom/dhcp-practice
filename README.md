# DHCP Practice A

DHCP server (ISC `isc-dhcp-server`) on a Linux VM connected to a public
network and to an internal network (`intnet`, 192.168.57.0/24), with two
clients: `c1` (dynamic lease) and `printer` (fixed IP by MAC).

## Network layout

| Machine | Role | Address |
|---|---|---|
| server | DHCP server, router and NAT | `eth1`: public network (DHCP), `eth2`: 192.168.57.10/24 |
| c1 | Dynamic client | Lease from 192.168.57.20-192.168.57.50 |
| printer | Client with reservation | Fixed 192.168.57.111 (MAC 08:00:27:AA:BB:01) |

## DHCP configuration summary

- Default lease time: 1 day. Maximum: 8 days.
- Domain: `yourname.test`
- Name servers: 10.0.0.2 and 4.4.4.4
- Dynamic range: 192.168.57.20-192.168.57.50
- Reservation for `printer` with a 2-hour lease time

## Repository structure

```
.
├── Vagrantfile # server, c1 and printer
├── server.sh # provisioning of the server
├── client.sh # provisioning of the clients
├── config/ # copies of the configuration files used in the VM
│ ├── dhcpd.conf
│ ├── dhcpd.conf.bak
│ └── isc-dhcp-server
└── doc/ # documentation of each checkpoint, with commands and outputs
```

## How to run it

```bash
vagrant up server
vagrant up c1
vagrant up printer
```

Before running, set in the `Vagrantfile` the name of your host network
adapter in `bridge:`. The clients request their address with `dhclient`.

## Documentation

| Checkpoint | Document |
|---|---|
| 1. Server and DHCP installation | [doc/01-server-interfaces.md](doc/01-server-interfaces.md) |
| 2. DHCP service configuration | [doc/02-dhcp-config.md](doc/02-dhcp-config.md) |
| 3. Client c1 | [doc/03-client-c1.md](doc/03-client-c1.md) |
| 4. Fixed IP for printer | [doc/04-printer-fixed-ip.md](doc/04-printer-fixed-ip.md) |
| 5. Routing and NAT | [doc/05-routing-nat.md](doc/05-routing-nat.md) |

## Status

- Checkpoints 1 to 4: completed.
- Checkpoint 5: IP forwarding and the NAT rule are configured. The default
  route through the public network gateway and the client ping tests are
  pending.

## Notes

- Routes, the forwarding flag and the `iptables` rules are not persistent
  across reboots.
- The versioned configuration files in `config/` are copies of the files
  inside the VM (`/etc/dhcp/dhcpd.conf`, `/etc/default/isc-dhcp-server`).