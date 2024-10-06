### Anggota Kelompok
| Nama                     | NRP         |
| -------------------------| ----------- |
| Diandra Naufal Abror     | 5027231004  |
| Acintya Edria Sudarsono  | 5027231020  |

### Topologi
![Screenshot 2024-10-06 083904](https://github.com/user-attachments/assets/57d6e767-eb37-414b-b72d-a519160f7651)

### Inisialisasi
- Nusantara
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.85.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.85.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.85.3.1
	netmask 255.255.255.0

up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.85.0.0/16
```
- Majapahit (DNS Slave)
```
auto eth0
iface eth0 inet static
	address 10.85.1.2
	netmask 255.255.255.0
	gateway 10.85.1.1
up echo nameserver 192.168.122.1 > /etc/resolv.conf`
```
- HayamWuruk (Client)
```
auto eth0
iface eth0 inet static
	address 10.85.1.3
	netmask 255.255.255.0
	gateway 10.85.1.1
```
- Solok (Load Balancer)
```
auto eth0
iface eth0 inet static
	address 10.85.1.4
	netmask 255.255.255.0
	gateway 10.85.1.1
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
- Srikandi (Client)
```
auto eth0
iface eth0 inet static
	address 10.85.1.5
	netmask 255.255.255.0
	gateway 10.85.1.1
```
- Sriwijaya
```
auto eth0
iface eth0 inet static
	address 10.85.3.2
	netmask 255.255.255.0
	gateway 10.85.3.1
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
- AlbertEinstein (Client)
```
auto eth0
iface eth0 inet static
	address 10.85.2.5
	netmask 255.255.255.0
	gateway 10.85.2.1
```
- Tanjungkulai (Web Server)
```
auto eth0
iface eth0 inet static
	address 10.85.2.4
	netmask 255.255.255.0
	gateway 10.85.2.1
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
- Bedahulu (Web Server)
```
auto eth0
iface eth0 inet static
	address 10.85.2.3
	netmask 255.255.255.0
	gateway 10.85.2.1
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
- Kotalingga (Web Server)
```
auto eth0
iface eth0 inet static
	address 10.85.2.2
	netmask 255.255.255.0
	gateway 10.85.2.1
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Soal
Sebuah kerajaan besar di Indonesia sedang mengalami pertempuran dengan penjajah. Kerajaan tersebut adalah **Sriwijaya**. Karena merasa terdesak **Sriwijaya** meminta bantuan pada **Majapahit** untuk mempertahankan wilayahnya. Pertempuran besar tersebut berada di **Nusantara**. Untuk topologi lihat pada link ini.

1. Untuk mempersiapkan peperangan World War MMXXIV (Iya sebanyak itu), Sriwijaya membuat dua kotanya menjadi web server yaitu Tanjungkulai, dan Bedahulu, serta Sriwijaya sendiri akan menjadi DNS Master. Kemudian karena merasa terdesak, Majapahit memberikan bantuan dan menjadikan kerajaannya (Majapahit) menjadi DNS Slave.

*Kami memulai dengan menginstall bind9 pada node Majapahit dan Sriwijaya:*
```
#!/bin/bash

apt-get update
apt-get install bind9 -y
service bind9 start
```
*Sedangkan untuk node Srikandi, HayamWuruk, dan AlbertEinstein kami menginstall dns dan lynx:*
```
echo -e 'nameserver 10.85.3.2
nameserver 10.85.1.2
nameserver 192.168.122.1' > /etc/resolv.conf

apt-get update
apt-get install dnsutils -y
apt-get install lynx -y
```

2. Karena para pasukan membutuhkan koordinasi untuk melancarkan serangannya, maka buatlah sebuah domain yang mengarah ke **Solok** dengan alamat **sudarsana.xxxx.com** dengan alias **www.sudarsana.xxxx.com**, dimana **xxxx** merupakan kode kelompok. Contoh: **sudarsana.it01.com**.

*Lalu kami mengisi sudarsana dengan detail sebagai berikut untuk node Sriwijaya yang mengarah ke Solok:*
```
apt-get update
apt-get install bind9 -y

echo 'zone "sudarsana.it44.com" { 
    type master; 
    file "/etc/bind/sudarsana/sudarsana.it44.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/sudarsana

cp /etc/bind/db.local /etc/bind/sudarsana/sudarsana.it44.com

echo ';
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     sudarsana.it44.com. root.sudarsana.it44.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      sudarsana.it44.com.
@       IN      A       10.85.1.4     ;
www     IN      CNAME   sudarsana.it44.com.' > /etc/bind/sudarsana/sudarsana.it44.com

service bind9 restart
```

3. Para pasukan juga perlu mengetahui mana titik yang akan diserang, sehingga dibutuhkan domain lain yaitu **pasopati.xxxx.com** dengan alias **www.pasopati.xxxx.com** yang **mengarah ke Kotalingga**.

*Di sisi lain kami juga memasukkan konfigurasi pasopati yang mengarah ke Kotalingga sebagai berikut:*
```
echo 'zone "pasopati.it44.com" {
    type master; 
    file "/etc/bind/pasopati/pasopati.it44.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/pasopati

cp /etc/bind/db.local /etc/bind/pasopati/pasopati.it44.com

echo ';
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pasopati.it44.com. root.pasopati.it44.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      pasopati.it44.com.
@       IN      A       10.85.2.2     ;
www     IN      CNAME   pasopati.it44.com.' > /etc/bind/pasopati/pasopati.it44.com

service bind9 restart
```

4. Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi dan suplai meme terbaru tersebut **mengarah ke Tanjungkulai** dan domain yang ingin digunakan adalah **rujapala.xxxx.com** dengan alias **www.rujapala.xxxx.com**.

*Selain itu, berikut adalah konfigurasi rujapala kami yang mengarah ke Tanjungkulai:*
```
echo 'zone "rujapala.it44.com" {
    type master; 
    file "/etc/bind/rujapala/rujapala.it44.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/rujapala

cp /etc/bind/db.local /etc/bind/rujapala/rujapala.it44.com

echo ';
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     rujapala.it44.com. root.rujapala.it44.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      rujapala.it44.com.
@       IN      A       10.85.2.4     ;
www     IN      CNAME   rujapala.it44.com.' > /etc/bind/rujapala/rujapala.it44.com

service bind9 restart
```

5. Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Nusantara.
