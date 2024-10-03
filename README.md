# Jarkom-Modul-2-IT44-2024

**Nusantara** <br />
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.85.1.2
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.85.2.2
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.85.3.2
	netmask 255.255.255.0
```
Jalankan script bash
```
apt install iptables-persistent


iptables -L -n -v

iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
iptables -A FORWARD -i eth0 -o eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT

```

**Sriwijaya (DNS Master)**
```
auto eth0
iface eth0 inet static
    address 10.85.3.3
    netmask 255.255.255.0
    gateway 10.85.3.2
```
**Majapahit (DNS Slave)**
```
auto eth0
iface eth0 inet static
    address 10.85.1.3
    netmask 255.255.255.0
    gateway 10.85.1.2 
```
**Hayamwuruk**
```
auto eth0
iface eth0 inet static
    address 10.85.1.4
    netmask 255.255.255.0
    gateway 10.85.1.2  
```
