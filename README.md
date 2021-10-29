# Jarkom-Modul-2-D10-2021
# Prefix IP
Prefix ip untuk kelompok kami adalah `10.26`

# Soal No. 1
## Soal
EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet
![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903631674171072542/unknown.png)
## Jawaban
1. Buat susunan topologi seperti gambar diatas
2. Kemudian setting network setiap node yang ada
   * Foosha
    ```
    auto eth0
    iface eth0 inet dhcp

    auto eth1
    iface eth1 inet static
        address 10.26.1.1
        netmask 255.255.255.0

    auto eth2
    iface eth2 inet static
        address 10.26.2.1
        netmask 255.255.255.0
    ```
    * Longuetown
    ```
    auto eth0
    iface eth0 inet static
        address 10.26.1.2
        netmask 255.255.255.0
        gateway 10.26.1.1
    ```
    * Alabasta
    ```
    auto eth0
    iface eth0 inet static
	      address 10.26.1.3
	      netmask 255.255.255.0
	      gateway 10.26.1.1
    ```
    * EniesLobby
    ```
    auto eth0
    iface eth0 inet static
	      address 10.26.2.2
	      netmask 255.255.255.0
	      gateway 10.26.2.1
    ```
    * Water7
    ```
    auto eth0
    iface eth0 inet static
	      address 10.26.2.3
	      netmask 255.255.255.0
	      gateway 10.26.2.1
    ```
    * Skypie
    ```
    auto eth0
    iface eth0 inet static
	      address 10.26.2.4
	      netmask 255.255.255.0
	      gateway 10.26.2.1
    ```
3. Restart semua node
4. Kemudian, jalankan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.26.0.0/16` pada Foosha
5. Lalu, jalankan echo `nameserver 192.168.122.1 > /etc/resolv.conf` pada semua node yang ada
6. Terakhir, tes menggunakan ping google pada client, di sini clientnya adalah Alabasta dan Longuetown
   * LogueTown

    ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903637158210990120/unknown.png)
   * Alabasta

    ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903638006940962867/unknown.png)
    
# Soal No. 2
## Soal
Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.e14.com dengan alias www.franky.d10.com pada folder kaizoku.
## Jawaban
1. Lakukan update pada node EniesLobby dengan command
    ```
    apt-get update
    ```
2. Lalu, lakukan instalasi bind9 di node yang sama dengan command
    ```
    apt-get install bind9 -y
    ```
3. Lalu buka `/etc/bind/named.conf.local` dengan command
   ```
    nano /etc/bind/named.conf.local
   ```
4. Kemudian tambahkan beberapa line berikut pada file yang sudah dibuka tedi
   ```
   zone "franky.d10.com" {
      type master;
      file "/etc/bind/kaizoku/franky.d10.com";
   };
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903640749118226452/unknown.png)
5. Buat folder `kaizoku` pada `/etc/bind` dengan command
   ```
   mkdir /etc/bind/kaizoku
   ```
6. Kemudian, salin file `/etc/bind/db.local` ke folder yang baru dibuat dengan nama `franky.d10.com` menggunakan command
   ```
   cp /etc/bind/db.local /etc/bind/kaizoku/franky.d10.com
   ```
7. Kemudian buka file tersebut dan ubah sehingga menjadi seperti gambar di bawah
   ```
   nano /etc/bind/kaizoku/franky.d10.com
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903642601939411024/Screenshot_2021-10-24_200257.png)
8. Lalu, restart bind9 dengan command
   ```
   service bind9 restart
   ```
9. Buka konsol pada client dan tambahkan nameserver EniesLobby pada `/etc/resolv.conf`
     ```
     #nameserver 192.168.122.1
     nameserver 10.26.2.2
     ```
11. Terakhir, lakukan test ping ke `franky.d10.com` dan `www.franky.d10.com` pada client
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903644454769025094/unknown.png)

# Soal No. 3
## Soal
Setelah itu buat subdomain super.franky.d10.com dengan alias www.super.franky.d10.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie
## Jawaban
1. Ubah file `/etc/bind/kaizoku/franky.d10.com` dengan menambahkan subdomain yang mengarah ke **IP Skypie**
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903645954220109834/Screenshot_2021-10-24_202139.png)
2. Restart bind9 dengan command
   ```
   service bind9 restart
   ```
3. Lakukan test ping ke `super.franky.d10.com` dan `www.super.franky.d10.com` pada client
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903646793408069652/unknown.png)

# Soal No. 4
## Soal
Buat juga reverse domain untuk domain utama
## Jawaban
1. Buka `/etc/bind/named.conf.local` pada EniesLobby dengan command
   ```
   nano /etc/bind/named.conf.local
   ```
2. Kemudian tambahkan beberapa line di bawah sehingga seperti gambar
   ```
   zone "2.26.10.in-addr.arpa" {
   type master;
   file "/etc/bind/kaizoku/2.26.10.in-addr.arpa";
   };
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903647975635574784/unknown.png)
3. Kemudian, salin file `/etc/bind/db.local` ke folder yang baru dibuat dengan nama `2.26.10.in-addr.arpa` menggunakan command
   ```
   cp /etc/bind/db.local /etc/bind/kaizoku/2.26.10.in-addr.arpa
   ```
4. Ubah file yang baru dibuat dengan command di bawah sehingga seperti gambar
   ```
   nano /etc/bind/kaizoku/2.26.10.in-addr.arpa
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903649514899005450/unknown.png)
5. Kemudian, lakukan beberapa langkah-langkah berikut untuk checking
   * Ubah nameserver kembali ke Foosha agar bisa mengakses internet
     ```
     nameserver 192.168.122.1
     #nameserver 10.26.2.2
     ```
   * Install paket dnsutils
     ```
     apt-get update
     apt-get install dnsutils
     ```
   * Kembalikan nameserver ke EniesLobby
     ```
     #nameserver 192.168.122.1
     nameserver 10.26.2.2
     ```
   * Jalankan command berikut
     ```
     host -t PTR 10.26.2.2
     ```
   * Hasilnya sebagai berikut

     ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903651456022880286/unknown.png)
# Soal No. 5
## Soal
Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama
## Jawaban
1. Buka `/etc/bind/named.conf.local` pada EniesLobby dengan command
   ```
   nano /etc/bind/named.conf.local
   ```
2. Edit dengan menambahkan line di bawah ini
   ```
   zone "franky.d10.com" {
      type master;
      notify yes;
      also-notify { 10.26.2.3; };
      allow-transfer { 10.26.2.3; };
      file "/etc/bind/kaizoku/franky.d10.com";
   };
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903653208117895178/unknown.png)
3. Restart bind9 dengan command
   ```
   service bind9 restart
   ```
4. Buka konsol Water7 kemudian lakukan update dan install bind9
   ```
   apt-get update
   apt-get install bind9 -y
   ```
5. Lalu buka `/etc/bind/named.conf.local` dengan command
   ```
    nano /etc/bind/named.conf.local
   ```
6. Kemudian tambahkan beberapa line berikut pada file yang sudah dibuka tedi
   ```
   zone "franky.e14.com" {
      type slave;
      masters { 10.26.2.2; };
      file "/var/lib/bind/franky.d10.com";
   };
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903654572562718781/unknown.png)
7. Restart bind9 dengan command
   ```
   service bind9 restart
   ```
8. Untuk melakukan testing, lakukan beberapa langkah berikut
   * Matikan bind9 pada EniesLobby
     ```
     service bind9 stop
     ```
   * Buka konsol pada client dan tambahkan nameserver Water7 pada `/etc/resolv.conf`
     ```
     #nameserver 192.168.122.1
     nameserver 10.26.2.2
     nameserver 10.26.2.3
     ```
   * Lakukan ping ke `franky.d10.com`
     ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903658144935342130/unknown.png)
# Soal No. 6
## Soal
Setelah itu terdapat subdomain mecha.franky.e14.com dengan alias www.mecha.franky.e14.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo
## Jawaban
1. Buka file /etc/bind/kaizoku/franky.d10.com pada EniesLobby dengan command
   ```
   nano /etc/bind/kaizoku/franky.d10.com
   ```
2. Ubah hingga menjadi seperti gambar di bawah
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903659655551655996/unknown.png)
3. Kemudian buka file `/etc/bind/named.conf.options` dengan command
   ```
   nano /etc/bind/named.conf.options
   ```
4. Ubah sehingga seperti gambar di bawah
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903660201025110046/unknown.png)
5. Lalu buka `/etc/bind/named.conf.local` dengan command
   ```
    nano /etc/bind/named.conf.local
   ```
6. Kemudian ubah seperti gambar di bawah ini
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903660817105449010/unknown.png)
7. Restart bind9 dengan command
   ```
   service bind9 restart
   ```
8. Pada Water7, buka file `/etc/bind/named.conf.options` dengan command
   ```
   nano /etc/bind/named.conf.options
   ```
9. Ubah sehingga seperti gambar di bawah ini
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903661359697379418/unknown.png)
10. Buat direktori dengan nama `sunnygo` pada **Water7** dengan ccommand
    ```
    mkdir /etc/bind/sunnygo
    ```
11.  Kemudian, salin file `/etc/bind/db.local` ke folder yang baru dibuat dengan nama `mecha.franky.d10.com` menggunakan command
   ```
   cp /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.d10.com
   ```
12. Kemudian buka file tersebut dan ubah sehingga menjadi seperti gambar di bawah
   ```
   nano /etc/bind/sunnygo/mecha.franky.d10.com
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903662648166580244/unknown.png)
13. Restart bind9 pada Water9
    ```
    service bind9 restart
    ```
14. Lakukan ping ke `mecha.franky.d10.com` dan `www.mecha.franky.d10.com` pada client
    ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903663477867040879/unknown.png)
# Soal No.7
## Soal
Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.e14.com dengan alias www.general.mecha.franky.e14.com yang mengarah ke Skypie
## Jawaban
1. Buka file `/etc/bind/sunnygo/mecha.franky.d10.com` pada **Water7**, lalu tambahkan subdomain untuk general.mecha.franky.d10.com yang mengarah ke IP Skypie.
   ```
   nano /etc/bind/sunnygo/mecha.franky.d10.com
   ```
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903664744672673813/unknown.png)
2. Restart bind9 dengan command
   ```
   service bind9 restart
   ```
3. Test menggunakan ping ke `general.mecha.franky.d10.com` dan `www.general.mecha.franky.d10.com` pada client
   ![text alt](https://cdn.discordapp.com/attachments/848199470025801749/903665673564205126/unknown.png)
