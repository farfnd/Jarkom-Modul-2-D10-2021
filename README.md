# Laporan Resmi Soal Shift Modul 2 Jarkom 2021

## Anggota Kelompok
- Mohammad Faderik I H (05111940000023)
- Farhan Arifandi (05111940000061)
- Yusril Zubaydi (05111940000160)


## Prefix IP
Prefix IP untuk kelompok kami adalah `10.26`.

## Soal 1
> EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet
> 
>  ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903631674171072542/unknown.png)

### Jawaban:
1. Buat susunan topologi seperti gambar di atas
2. Kemudian setting network setiap node yang ada
   - Foosha
   
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
    
    - Loguetown
    
    ```
    auto eth0
    iface eth0 inet static
        address 10.26.1.2
        netmask 255.255.255.0
        gateway 10.26.1.1
    ```
    
    - Alabasta
    
    ```
    auto eth0
    iface eth0 inet static
	      address 10.26.1.3
	      netmask 255.255.255.0
	      gateway 10.26.1.1
    ```
    
    - EniesLobby
    ```
    auto eth0
    iface eth0 inet static
	      address 10.26.2.2
	      netmask 255.255.255.0
	      gateway 10.26.2.1
    ```
    
    - Water7
    
    ```
    auto eth0
    iface eth0 inet static
	      address 10.26.2.3
	      netmask 255.255.255.0
	      gateway 10.26.2.1
    ```
    
    - Skypie
    
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
6. Terakhir, tes menggunakan ping google pada client, di sini clientnya adalah Alabasta dan Loguetown
   - Loguetown
   
    ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903637158210990120/unknown.png)
    
   - Alabasta

    ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903638006940962867/unknown.png)
    
## Soal 2
> Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses **franky.yyy.com** dengan alias www.franky.yyy.com pada folder kaizoku.

### Jawaban:
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
10. Terakhir, lakukan test ping ke `franky.d10.com` dan `www.franky.d10.com` pada client
   
    ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903644454769025094/unknown.png)

## Soal 3
> Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS-nya di EniesLobby dan mengarah ke Skypie

**Jawaban:**
1. Ubah file `/etc/bind/kaizoku/franky.d10.com` dengan menambahkan subdomain yang mengarah ke **IP Skypie**
   
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903645954220109834/Screenshot_2021-10-24_202139.png)
   
2. Restart bind9 dengan command
   ```
   service bind9 restart
   ```
   
3. Lakukan test ping ke `super.franky.d10.com` dan `www.super.franky.d10.com` pada client
   
   ![alt text](https://cdn.discordapp.com/attachments/848199470025801749/903646793408069652/unknown.png)

## Soal 4
> Buat juga reverse domain untuk domain utama

### Jawaban:
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

## Soal 5
> Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama

### Jawaban:
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
   zone "franky.d10.com" {
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

## Soal 6
> Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

### Jawaban:
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
   
   ![image](https://user-images.githubusercontent.com/70105993/139464084-dad42aba-2a99-4b14-8c99-5445026d6828.png)
   
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

## Soal 7
> Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie

### Jawaban:
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

## Soal 8
> Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.

### Jawaban:
**EniesLobby**
1. Edit file **/etc/bind/kaizoku/franky.d10.com** seperti berikut

	```vim
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     franky.d10.com. root.franky.d10.com. (
					4       ; Serial
				 604800         ; Refresh
				  86400         ; Retry
				2419200         ; Expire
				 604800 )       ; Negative Cache TTL
	;
	@               IN      NS      franky.d10.com.
	@               IN      A       10.26.2.4
	www             IN      CNAME   franky.d10.com.
	super           IN      A       10.26.2.4
	www.super       IN      CNAME   super.franky.d10.com.
	ns1             IN      A       10.26.2.3
	mecha           IN      NS      ns1
	```

2. Restart bind9 dengan perintah `service bind9 restart`.

**Skypie**
1. Update library yang sudah terinstall, lalu install Apache dan librarynya, lalu install php dan menjalankan service Apache yang telah ter-install dengan perintah berikut.
	```bash
	apt-get install apache2 -y
	apt-get install php -y
	apt-get install libapache2-mod-php7.0 -y

	service apache2 start
	```
2. Buat folder **/var/www/franky.d10.com** dengan perintah `mkdir /var/www/franky.d10.com`.
3. Install **wget** dan **unzip** dengan perintah:
	```bash
	apt-get install wget -y
	apt-get install unzip -y
	```
4. Download file zip yang diperlukan untuk web dengan perintah
	```bash
	wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/franky.zip
	```
5. Unzip file yang sudah didownload dengan perintah `unzip -j franky.zip -d /var/www/franky.d10.com`
6. Buat file baru pada direktori **/etc/apache2/sites-available** dengan nama **franky.d10.com.conf** dan isi sebagai berikut
	```vim
	<VirtualHost *:80>
		# The ServerName directive sets the request scheme, hostname and port that
		# the server uses to identify itself. This is used when creating
		# redirection URLs. In the context of virtual hosts, the ServerName
		# specifies what hostname must appear in the request's Host: header to
		# match this virtual host. For the default virtual host (this file) this
		# value is not decisive as it is used as a last resort host regardless.
		# However, you must set it for any further virtual host explicitly.
		ServerName franky.d10.com
		ServerAlias www.franky.d10.com

		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/franky.d10.com

		# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
		# error, crit, alert, emerg.

		# It is also possible to configure the loglevel for particular
		# modules, e.g.
		#LogLevel info ssl:warn

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		# For most configuration files from conf-available/, which are
		# enabled or disabled at a global level, it is possible to
		# include a line for only one particular virtual host. For example the
		# following line enables the CGI configuration for this host only
		# after it has been globally disabled with "a2disconf".
		#Include conf-available/serve-cgi-bin.conf
	</VirtualHost>

	# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
	```
7. Jalankan konfigurasi webserver yang telah dibuat dengan perintah `a2ensite franky.d10.com` lalu restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**
1. Install Lynx dengan command `apt-get install lynx -y`
2. Akses www.franky.d10.com dengan command `lynx www.franky.d10.com`

![image](https://user-images.githubusercontent.com/70105993/139464873-9cf38926-1a1b-45c9-b0c9-b7782e1c452d.png)

## Soal 9
> Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home.

### Jawaban:
**Skypie**
1. Aktifkan modul rewrite pada Apache dengan perintah `a2enmod rewrite`.
2. Restart service Apache dengan perintah `service apache2 restart`.
3. Edit file **.htaccess** pada folder **/var/www/franky.d10.com** sebagai berikut
	```
	RewriteEngine On
	RewriteBase /
	RewriteCond %{HTTP_HOST} ^10\.26\.2\.4$
	RewriteRule ^(.*)$ http://www.franky.d10.com/$1 [L,R=301]
	```
4. Edit file **franky.d10.com.conf** pada folder **/etc/apache2/sites-available** sebagai berikut
	```vim
	<VirtualHost *:80>
		# The ServerName directive sets the request scheme, hostname and port that
		# the server uses to identify itself. This is used when creating
		# redirection URLs. In the context of virtual hosts, the ServerName
		# specifies what hostname must appear in the request's Host: header to
		# match this virtual host. For the default virtual host (this file) this
		# value is not decisive as it is used as a last resort host regardless.
		# However, you must set it for any further virtual host explicitly.
		ServerName franky.d10.com
		ServerAlias www.franky.d10.com

		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/franky.d10.com

		Alias "/home" "/var/www/franky.d10.com/index.php/home"

		# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
		# error, crit, alert, emerg.

		# It is also possible to configure the loglevel for particular
		# modules, e.g.
		#LogLevel info ssl:warn

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		# For most configuration files from conf-available/, which are
		# enabled or disabled at a global level, it is possible to
		# include a line for only one particular virtual host. For example the
		# following line enables the CGI configuration for this host only
		# after it has been globally disabled with "a2disconf".
		#Include conf-available/serve-cgi-bin.conf
	</VirtualHost>

	# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
	```
5. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Cek dengan mengakses www.franky.d10.com/index.php/home dengan command `lynx www.franky.d10.com/index.php/home` maupun www.franky.d10.com/home dengan command `lynx www.franky.d10.com/home`, akan ditampilkan halaman yang sama.

![image](https://user-images.githubusercontent.com/70105993/139464873-9cf38926-1a1b-45c9-b0c9-b7782e1c452d.png)

## Soal 10
> Setelah itu, pada subdomain `www.super.franky.yyy.com`, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada **/var/www/super.franky.yyy.com**.

### Jawaban:
**Skypie**
1. Buat folder document root untuk subdomain **super.franky.d10.com** dengan command `mkdir /var/www/super.franky.d10.com`.
2. Download file yang diperlukan, lalu unzip pada folder document root subdomain dengan command
	```
	wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip
	unzip -j super.franky.zip -d /var/www/super.franky.d10.com
	```
3. Salin file **000-default.conf** sebagai template file konfigurasi subdomain **super.franky.d10.com** dengan command 
	```bash
	cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/super.franky.d10.com.conf
	```
4. Edit file **super.franky.d10.com.conf** sebagai berikut
	```vim
	<VirtualHost *:80>
		# The ServerName directive sets the request scheme, hostname and port that
		# the server uses to identify itself. This is used when creating
		# redirection URLs. In the context of virtual hosts, the ServerName
		# specifies what hostname must appear in the request's Host: header to
		# match this virtual host. For the default virtual host (this file) this
		# value is not decisive as it is used as a last resort host regardless.
		# However, you must set it for any further virtual host explicitly.
		ServerName super.franky.d10.com
		ServerAlias www.super.franky.d10.com

		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/super.franky.d10.com

		# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
		# error, crit, alert, emerg.

		# It is also possible to configure the loglevel for particular
		# modules, e.g.
		#LogLevel info ssl:warn

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		# For most configuration files from conf-available/, which are
		# enabled or disabled at a global level, it is possible to
		# include a line for only one particular virtual host. For example the
		# following line enables the CGI configuration for this host only
		# after it has been globally disabled with "a2disconf".
		#Include conf-available/serve-cgi-bin.conf
	</VirtualHost>
	```
5. Aktifkan konfigurasi subdomain **super.franky.d10.com** dengan command `a2ensite super.franky.d10.com`.
6. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Cek subdomain yang sudah dibuat menggunakan lynx dengan command `lynx super.franky.d10.com`

![image](https://user-images.githubusercontent.com/70105993/139468221-c6e21810-2ddb-42e8-99ff-1088734370d9.png)


## Soal 11
> Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.

### Jawaban:
**Skypie**
1. Tambahkan beberapa baris pada file **/etc/apache2/sites-available/super.franky.d10.com.conf** sebagai berikut
	```vim
	<VirtualHost *:80>
		# The ServerName directive sets the request scheme, hostname and port that
		# the server uses to identify itself. This is used when creating
		# redirection URLs. In the context of virtual hosts, the ServerName
		# specifies what hostname must appear in the request's Host: header to
		# match this virtual host. For the default virtual host (this file) this
		# value is not decisive as it is used as a last resort host regardless.
		# However, you must set it for any further virtual host explicitly.
		ServerName super.franky.d10.com
		ServerAlias www.super.franky.d10.com

		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/super.franky.d10.com

		<Directory /var/www/super.franky.d10.com>
			Options +Indexes
		</Directory>

		<Directory /var/www/super.franky.d10.com/public>
			Options +Indexes
		</Directory>

		...
	```
2. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Akses super.franky.d10.com/public menggunakan lynx dengan command `lynx super.franky.d10.com/public`

![image](https://user-images.githubusercontent.com/70105993/139469558-814dedcc-ebae-46f2-b9e6-3f28f51ee08d.png)


## Soal 12
> Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache.

### Jawaban:
**Skypie**
1. Tambahkan baris pada file **/etc/apache2/sites-available/super.franky.d10.com.conf** untuk mengatur error page 404 sebagai berikut
	```vim
		...

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		ErrorDocument 404 /error/404.html

		# For most configuration files from conf-available/, which are
		# enabled or disabled at a global level, it is possible to
		# include a line for only one particular virtual host. For example the
		# following line enables the CGI configuration for this host only
		# after it has been globally disabled with "a2disconf".
		#Include conf-available/serve-cgi-bin.conf
	</VirtualHost>
	```
2. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Coba tampilkan halaman 404 dengan memasukkan sembarang path setelah super.franky.d10.com/, contohnya super.franky.d10.com/asdfg menggunakan lynx dengan command `lynx super.franky.d10.com/asdfg`

![image](https://user-images.githubusercontent.com/70105993/139470382-b847e513-1fab-4c99-906e-31018a81f4f5.png)

## Soal 13
> Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset `www.super.franky.yyy.com/public/js` menjadi `www.super.franky.yyy.com/js`.

### Jawaban:
**Skypie**
1. Tambahkan baris pada file **/etc/apache2/sites-available/super.franky.d10.com.conf** untuk alias path `/js` ke `/public/js` sebagai berikut
	```vim
	<VirtualHost *:80>
		# The ServerName directive sets the request scheme, hostname and port that
		# the server uses to identify itself. This is used when creating
		# redirection URLs. In the context of virtual hosts, the ServerName
		# specifies what hostname must appear in the request's Host: header to
		# match this virtual host. For the default virtual host (this file) this
		# value is not decisive as it is used as a last resort host regardless.
		# However, you must set it for any further virtual host explicitly.
		ServerName super.franky.d10.com
		ServerAlias www.super.franky.d10.com

		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/super.franky.d10.com

		<Directory /var/www/super.franky.d10.com>
			Options +Indexes
		</Directory>

		<Directory /var/www/super.franky.d10.com/public>
			Options +Indexes
		</Directory>

		Alias "/js" "/var/www/super.franky.d10.com/public/js"
		...
	```
2. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Cek dengan mengakses www.franky.d10.com/js dengan command `lynx www.franky.d10.com/js` maupun www.franky.d10.com/public/js dengan command `lynx www.franky.d10.com/public/js`, akan ditampilkan halaman yang sama.

![image](https://user-images.githubusercontent.com/70105993/139470809-41f7893c-1d14-40b9-bc9f-ed2bbfcdddb9.png)

## Soal 14
> Dan Luffy meminta untuk web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

### Jawaban:
**Skypie**
1. Buat folder document root untuk subdomain **general.mecha.franky.d10.com** dengan command `mkdir /var/www/general.mecha.franky.d10.com`.
2. Download file yang diperlukan, lalu unzip pada folder document root subdomain dengan command
 
	```
	wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/general.mecha.franky.zip
	unzip -j general.mecha.franky.zip -d /var/www/general.mecha.franky.d10.com
	```

3. Salin file **000-default.conf** sebagai template file konfigurasi subdomain **general.mecha.franky.d10.com** untuk masing-masing port 15000 dan 15500 dengan command 
 
	```bash
	cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/general.mecha.franky.d10.com-15000.conf
	cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/general.mecha.franky.d10.com-15500.conf
	``` 

4. Edit file **general.mecha.franky.d10.com-15000.conf** sebagai berikut
 
	```vim
	<VirtualHost *:15000>
	ServerName general.mecha.franky.d10.com
	ServerAlias www.general.mecha.franky.d10.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/general.mecha.franky.d10.com

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>
	```

5. Lakukan pula hal yang sama pada file **general.mecha.franky.d10.com-15500.conf**, hanya saja port pada baris paling atas diganti 15500.
6. Aktifkan konfigurasi subdomain **general.mecha.franky.d10.com** pada port 15000 dan 15500 dengan command
 
	```
	a2ensite general.mecha.franky.d10.com-15000
	a2ensite general.mecha.franky.d10.com-15500
	```

7. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Cek subdomain yang sudah dibuat pada port 15000 dan 15500 menggunakan lynx dengan command lynx general.mecha.franky.d10.com:15000 dan `lynx general.mecha.franky.d10.com:15500`, akan ditampilkan halaman yang sama

![image](https://user-images.githubusercontent.com/70105993/139472896-a58b2420-3896-43f5-b1b5-f7b8c9a8418d.png)

## Soal 15
> dengan autentikasi username luffy dan password onepiece dan file di /var/www/general.mecha.franky.yyy

### Jawaban:
**Skypie**
1. Buat file **.htpasswd** pada folder **/etc/apache2** untuk menyimpan informasi autentikasi dengan command `htpasswd -cb /etc/apache2/.htpasswd luffy onepiece`.
2. Tambahkan beberapa baris pada file **general.mecha.franky.d10.com-15000.conf** dan **general.mecha.franky.d10.com-15500.conf** sebagai berikut
 
	```vim
		...
		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		<Directory "var/www/general.mecha.franky.d10.com">
			AuthType Basic
			AuthName "Restricted Content"
			AuthUserFile /etc/apache2/.htpasswd
			Require valid-user
		</Directory>
	</VirtualHost>
	``` 

3. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Cek general.mecha.franky.d10.com pada port 15000 dan 15500 menggunakan lynx dengan command lynx general.mecha.franky.d10.com:15000 dan `lynx general.mecha.franky.d10.com:15500`, akan ditampilkan pesan berikut.

![image](https://user-images.githubusercontent.com/70105993/139474069-5084f24f-496c-4c0e-b427-2f9cf9c6432b.png)

Tunggu sejenak, maka akan tampil halaman untuk memasukkan username dan password.

![image](https://user-images.githubusercontent.com/70105993/139473127-bd809dca-f861-4b74-aa64-00b6327526fb.png)
![image](https://user-images.githubusercontent.com/70105993/139473189-176e1bb6-604f-4f61-9577-fa5ba752ab9c.png)

Setelah memasukkan username dan password yang benar, maka akan muncul halaman berikut.

![image](https://user-images.githubusercontent.com/70105993/139472896-a58b2420-3896-43f5-b1b5-f7b8c9a8418d.png)


## Soal 16
> Dan setiap kali mengakses IP Skypie akan dialihkan secara otomatis ke www.franky.yyy.com

### Jawaban:
**Skypie**
1. Edit file **/var/www/franky.d10.com/.htaccess** sebagai berikut untuk mengalihkan akses dari IP Skypie (10.26.2.4) ke www.franky.d10.com.

	```vim
	RewriteEngine On
	RewriteBase /
	RewriteCond %{HTTP_HOST} ^10\.26\.2\.4$
	RewriteRule ^(.*)$ http://www.franky.d10.com/$1 [L,R=301]
	```

2. Edit file **/etc/apache2/sites-available/000-default.conf** sebagai berikut.
 
	```vim
	<VirtualHost *:80>
		ServerAdmin webmaster@localhost
		DocumentRoot "/var/www/franky.d10.com"

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>
	```

3. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Akses IP Skypie menggunakan lynx dengan command lynx 10.26.2.4 , akan ditampilkan halaman home dari franky.d10.com.

![image](https://user-images.githubusercontent.com/70105993/139475098-49074d47-3dde-407b-a19e-e19e4b18dcee.png)


## Soal 17
> Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website www.super.franky.yyy.com, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring “franky” akan diarahkan menuju franky.png.

### Jawaban:
**Skypie**
1. Edit file **/var/www/super.franky.d10.com/.htaccess** sebagai berikut untuk mengalihkan akses dari URL yang pathnya mengandung substring 'franky' ke super.franky.d10.com/public/images/franky.png.
 
	```vim
	RewriteEngine On
	RewriteBase /
	RewriteCond %{REQUEST_URI} !\bfranky.png\b
	RewriteRule franky http://super.franky.d10.com/public/images/franky.png$1 [L,R=301]
	```

2. Edit file **/etc/apache2/sites-available/super.franky.d10.com.conf** sebagai berikut.
 
	```vim
	 <VirtualHost *:80>
		ServerName super.franky.d10.com
		ServerAlias www.super.franky.d10.com

		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/super.franky.d10.com

		<Directory /var/www/super.franky.d10.com>
			   Options +Indexes
			   AllowOverride All
		</Directory>
	  	...
	``` 

3. Restart service Apache dengan perintah `service apache2 restart`.

**Loguetown**

Cek dengan memasukkan sembarang path yang mengandung substring 'franky' setelah super.franky.d10.com/, contohnya super.franky.d10.com/franky1234 menggunakan lynx dengan command `lynx super.franky.d10.com/franky1234`, maka akan diarahkan ke super.franky.d10.com/public/images/franky.png.

![image](https://user-images.githubusercontent.com/70105993/139475929-9dfd7257-3eaa-40bb-aadb-e5bf3915d267.png)
