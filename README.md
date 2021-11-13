# Jarkom-Modul-3-C01-2021

## Anggota Kelompok
- Muhammad Arifiansyah (05111940000027)
- Melanchthon Bonifacio Butarbutar (05111940000097)
- Mu

## Soal 1
Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server (1)
## Soal 2
Foosha sebagai DHCP Relay (2).

## Pembahasan
install dhcp relay terlebih dahulu dengan command dibawah:
```bash
apt-get install isc-dhcp-relay -y
```
kemudian ubah isi  /etc/default/isc-dhcp-relay
dengan
```bash
SERVERS="192.184.2.4"
INTERFACES="eth1 eth2 eth3"
OPTIONS=””
```
dimana interface diisi eth1 eth2 eth3(mengarah ke 3 switch dan Server diset menuju DHCP server <b>Jipangu</b>

## Soal 3
Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169 (3)


## Pembahasan
Ubah konfigruasi client terlebih dahulu
dengan melakukan vim /etc/network/interfaces isi dengan

```bash
auto eth0
iface eth0 inet dhcp
```
kemduian melakukan konfigurasi ke jipangu
install dhcp relay terlebih dahulu dengan command dibawah:
```bash
apt-get install isc-dhcp-server -y
```
kemudian ubah isi /etc/default/isc-dhcp-server
dengan
```bash
INTERFACES="eth0"
```
dimana interface diisi eth0(mengarah ke foosha)
kemudian dilakukan konfigurasi DCH dengan mengedit /etc/dhcp/dhcpd.conf
dengan
```bash
subnet 192.184.1.0 netmask 255.255.255.0 {
    range 192.184.1.20 192.184.1.99; //nomer 3
    range 192.184.1.150 192.184.1.169; //nomer 3
    option routers 192.184.1.1;
    option broadcast-address 192.184.1.255;
    option domain-name-servers 192.184.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}
```
sehingga kita dapat mengatur range alokasi IP yang kita sediakan(yang diminta soal)

## Soal 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50 (4)
## Pembahasan
pertama ubah isi /etc/default/isc-dhcp-server
dengan
```bash
INTERFACES="eth0"
```
dimana interface diisi eth0(mengarah ke foosha)
kemudian dilakukan konfigurasi DCH dengan mengedit /etc/dhcp/dhcpd.conf
dengan
```bash
subnet 192.184.3.0 netmask 255.255.255.0 {
    range 192.184.3.30 192.184.3.50;
    option routers 192.184.3.1;
    option broadcast-address 192.184.3.255;
    option domain-name-servers 192.184.2.2;
    default-lease-time 720;
    max-lease-time 7200;
}
```
sehingga kita dapat mengatur range alokasi IP yang kita sediakan(yang diminta soal)
## Soal 5
Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut. (5)
## Pembahasan
Untuk mengerjakan soal ini pertama kita buat forwardernya dahulu
pertama konfigurasi dhcpd.conf. di EniesLobby dengan vim
```bash
option domain-name-servers 192.184.2.2;
```
kemudian pada EniesLobby edit /etc/bind/named.conf.options lalu tambah dengan
```bash
forwarders {
  192.168.122.1;
};
```
Kemudian comment pada bagian
```bash
dnssec-validation auto;
```
Dan tambahkan
```bash
allow-query{any;};
```
kemudian restart bindnya
## Soal 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit. (6)

## Pembahasan
Bisa dilihat dari configurasi yang ada default lease time dan max lease time
```bash
subnet 192.184.1.0 netmask 255.255.255.0 {
    range 192.184.1.20 192.184.1.99;
    range 192.184.1.150 192.184.1.169;
    option routers 192.184.1.1;
    option broadcast-address 192.184.1.255;
    option domain-name-servers 192.184.2.2;
    default-lease-time 360; //no 6
    max-lease-time 7200; //no 6
}
subnet 192.184.3.0 netmask 255.255.255.0 {
    range 192.184.3.30 192.184.3.50;
    option routers 192.184.3.1;
    option broadcast-address 192.184.3.255;
    option domain-name-servers 192.184.2.2;
    default-lease-time 720; //no 6
    max-lease-time 7200; //no 6
}
```
## Soal 7
Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69 (7).

##Pembahasan
## Soal 8
## Soal 9
Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy.  

## Pembahasan
Pertama install apache2-utils dengan command `apt-get install apache2-utils -y`  

Kemudian gunakan command `htpasswd -cm /etc/squid/passwd luffybelikapalc01` untuk membuat file passwd.  
Di sini parameter `c` untuk membuat file baru dan `m` untuk menggunakan enkripsi MD5.  
Setelah menjalankan command tersebut masukkan password dan confirm password.  
Kemudian jalankan command sekali lagi tanpa parameter dan dengan username zorobelikapalc01.  

Apabila password pada file `/etc/squid/passwd` berawal dengan `$apr1$` maka enkripsi dengan MD5 telah berhasil.  
![image](https://user-images.githubusercontent.com/57700613/141644855-5979010e-6c11-4e77-85fd-13b2881893a6.png)

Kemudian pada file `/etc/squid/squid.conf` tambahkan
```
http_port 5000
visible_hostname jualbelikapal.c01.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS
http_access deny all
```
Jangan lupa restart squid `service squid restart`

Kemudian untuk mengecek atur proxy pada client Loguetown dengan command `export http_proxy="http://jualbelikapal.c01.com:5000"`
Setelah itu `lynx its.ac.id`

![image](https://user-images.githubusercontent.com/57700613/141645073-e6d2a2f5-aed4-4c12-82eb-7f22925f9f69.png)

## Soal 10
Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00).  

## Pembahasan
Untuk membatasi waktu pengaksesan pada proxy maka buat file `/etc/squid/acl.conf` yang isinya
```
acl AVAILABLE_WORKING time MTWH 07:00-11:00
acl AVAILABLE_WORKING time TWHF 15:00-24:00
acl AVAILABLE_WORKING time WHFA 00:00-03:00
```
Di sini karena mulai hari rabu dapat di akses pada jam 3 pagi maka harus menggunakan `WHFA 00:00-03:00`

Kemudian pada file `/etc/squid/squid.conf` tambahkan  
```

```

## Soal 11
## Soal 12
## Soal 13
