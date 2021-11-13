# Jarkom-Modul-3-C01-2021

## Anggota Kelompok
- Melanchthon Bonifacio Butarbutar (05111940000097)
- Mu
- Mu

## Soal 1
## Soal 2
## Soal 3
## Soal 4
## Soal 5
## Soal 6
## Soal 7
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
