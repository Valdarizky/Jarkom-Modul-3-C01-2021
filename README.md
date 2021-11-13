# Jarkom-Modul-3-C01-2021

## Anggota Kelompok dan Pembagian Tugas
- Muhammad Arifiansyah (05111940000027) **1-5**
- Melanchthon Bonifacio Butarbutar (05111940000097) **6-9**
- Muhammad Valda Rizky Nur Firdaus (05111940000115) **10-13**

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
hasilnya akan seperti ini
![image](https://user-images.githubusercontent.com/73706605/141647763-30a81983-aa55-4afd-a948-315ce395bb88.png) <br>
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
kemudian melakukan konfigurasi ke jipangu
install dhcp relay terlebih dahulu dengan command dibawah:
```bash
apt-get install isc-dhcp-server -y
```
kemudian ubah isi /etc/default/isc-dhcp-server
dengan
```bash
INTERFACES="eth0"
```
sepeeti gambar berikut:
![image](https://user-images.githubusercontent.com/73706605/141647852-5328a6c0-5755-4cce-b5c5-4db853211167.png)  <br>
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
seperti gambar berikut: 
![image](https://user-images.githubusercontent.com/73706605/141647934-0344f371-d6bc-4a32-91ee-425b04db5ff9.png) <br>
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
seperti gambar berikut: 
![image](https://user-images.githubusercontent.com/73706605/141647934-0344f371-d6bc-4a32-91ee-425b04db5ff9.png) <br>
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
maka hasilnya seperti ini:
![image](https://user-images.githubusercontent.com/73706605/141648062-1c7f42fe-78e9-4044-a440-c913848738e5.png)  <br>
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
seperti gambar berikut: 
![image](https://user-images.githubusercontent.com/73706605/141647934-0344f371-d6bc-4a32-91ee-425b04db5ff9.png)  <br>
## Soal 7
Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69 (7).

## Pembahasan
Dari perintah soalnya bisa diketahui bahwa kita harus buat fixed address IP untuk Skypie
pertama dilakukan konfigurasi dhcp dengan mengedit /etc/dhcp/dhcpd.conf di <b>Jipangu</b>
dengan menambahkan
```bash
host Skypie {
    hardware ethernet 72:16:31:c1:56:f3;
    fixed-address 192.184.3.69;
}
```
![image](https://user-images.githubusercontent.com/73706605/141648179-1c4819da-5944-41e7-90c6-842fcff8c5c4.png)  <br>
Yang tentunya hardware internet didapat dari mengecek IP di Skypie untuk hardware addressnya
kemudian pada skypie,edit networnya dikonfigurasi dengan
```bash
auto eth0
iface eth0 inet dhcp
hwaddress ether 72:16:31:c1:56:f3
```
![image](https://user-images.githubusercontent.com/73706605/141648207-a36363b9-b50c-4c3d-bf74-bf170be54a69.png)

Kemudian restart dhcp server pada Jipangu,kemudian bisa di cek apakah ip skypie muncul atau tidak fixed addressnya sesuai perintah soal.


## Soal 8
Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi.
Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000 (8).

## Pembahasan

Sebelumnya pertama buat DNS pada EniesLobby dengan nama jualbelikapal.c01.com.  
Buatlah file konfigurasi `/etc/bind/jualbelikapal.c01.com` yang isinya  

![image](https://user-images.githubusercontent.com/57700613/141647773-a628f36c-67eb-4d78-8a43-c450f962a872.png)

Pastikan PTR mengarah pada ip Water 7  

Kemudian pada file `/etc/bind/named.conf.local` tambahkan zone `jualbelikapal.c01.com` : 

![image](https://user-images.githubusercontent.com/57700613/141647850-8340f4e5-ec02-41fc-a367-98dad2fc4f6c.png)

Lakukan restart bind.  

Dan yang terakhir pastikan nameserver Water 7 mengarah pada EniesLobby :  

![image](https://user-images.githubusercontent.com/57700613/141647893-b742b7ca-68a7-4faf-a547-15bd0e57477b.png)

Untuk membuat proxy pertama lakukan instalasi squid 
```
apt-get update
apt-get install squid -y
```

Kemudian pada file `/etc/squid/squid.conf`

```
http port 5000
visible_hostname jualbelikapal.c01.com

http_access allow all

```

Kemudian lakukan restart squid  

Untuk mengakses jualbelikapal.c01.com dengan proxy pertama set proxy dengan command `export http_proxy="http://jualbelikapal.c01.com:5000"`

![image](https://user-images.githubusercontent.com/57700613/141648055-b07583b4-4227-4719-870e-3f3fe620a2d4.png)


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
include /etc/squid/acl.conf
http_port 5000
visible_hostname jualbelikapal.c01.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED


http_access allow AVAILABLE_WORKING USERS
http_access deny all
```
Jangan lupa restart squid `service squid restart`  

Kemudian untuk mengecek atur proxy pada client Loguetown dengan command `export http_proxy="http://jualbelikapal.c01.com:5000"`
Jika tanggal pada Loguetown di set menjadi senin jam 10 `date -s "8 nov 2021 10:00:00"`
Setelah itu `lynx its.ac.id`  

![image](https://user-images.githubusercontent.com/57700613/141646501-c3a33436-39f5-4942-b9a7-c5bda31a6c7f.png)

Jika tanggal di set menjadi senin jam 18.00 `date -s "8 nov 2021 18:00:00"`

![image](https://user-images.githubusercontent.com/57700613/141646594-51d05478-d0cb-4237-9e52-4ed8ea1fac3e.png)

## Soal 11
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie  

## Pembahasan

#### Untuk soal ini menggunakan konfigurasi webserver yang sama seperti modul sebelumnya, dengan DNS franky.c01.com mengarah pada Skypie. DNS jualbelikapal.c01.com hanya dibuat agar dapat diakses dengan proxy jualbelikapal.c01.com:5000

Agar saat mengakses google.com dengan proxy dapat diredirect maka harus ditambahkan pada file `/etc/squid/squid.conf`  

```
acl BLACKLIST dstdomain .google.com
deny_info http://super.franky.c01.com/ BLACKLIST
.
.
.
http_access deny AVAILABLE_WORKING BLACKLIST
```
`deny_info` berfungsi sebagai "error page" di mana kita akan di redirect apabila mencoba untuk mengakses situs yang diblacklist yaitu google.com.

![image](https://user-images.githubusercontent.com/57700613/141647176-105b40ff-e289-4374-a55f-27e56e4f417e.png)  

![image](https://user-images.githubusercontent.com/57700613/141647186-00a38421-2b8e-4676-8428-7d4b03806da2.png)


## Soal 12 dan Soal 13

Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps (12). Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya (13).

## Pembahasan

Untuk soal ini pertama buat file `/etc/squid/extension.txt` yang isinya

```
\.jpg$
\.png$
```
File ini akan berfungsi sebagai pembatasan file yang dapat diakses luffy.  

Kemudian pada file squid tambahkan

```
.
.
.
acl USER2 proxy_auth zorobelikapalc01
acl extension url_regex "/etc/squid/extension.txt"
    
delay_pools 2
delay_class 1 1
delay_access 1 allow USER2 
delay_parameters 1 -1/-1
delay_class 2 1
delay_access 2 allow extension
delay_parameters 2 10000/10000
.
.
.

```

Pertama untuk pembatasan bandwith membuat 2 pool, yang pertama untuk user zoro, dan yang kedua untuk user lainnya.  
User zoro akan diperbolehkan untuk mendownload dengan speed tidak terbatas `delay_parameters 1 -1/-1`  
Dan untuk pool ke dua, sisa pengguna (luffy dan pengguna lain) akan diperbolehkan untuk mendownload jpg dan png `delay_access 2 allow extension`  
dengan speed 10 kbps `delay_parameters 2 10000/10000` 

Mendownload dengan user **luffy**:  

![image](https://user-images.githubusercontent.com/57700613/141647409-6f479935-e9cf-4fe3-aba5-5f3398f40167.png)

Dengan user **zoro** download langsung selesai:  

![image](https://user-images.githubusercontent.com/57700613/141647446-e8dedef1-b88e-483e-85b6-b899a730a705.png)
