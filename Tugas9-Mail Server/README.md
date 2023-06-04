# 🚀 Tugas Kelompok Mail Server
![](https://www.seekpng.com/png/detail/416-4164571_logo-pens-png-electronic-engineering-polytechnic-institute-of.png)

<p align = center>
LAPORAN RESMI <br>
WORKSHOP ADMINISTRASI JARINGAN </br>

<p align = center>
Dosen Pengampu <br>
Dr. Ferry Astika Saputra ST, M.Sc<br><br>

<p align = center>
Disusun Oleh<br>
Muhammad Fajrul Falah Subakti3121600035<br>
Andriana Wahyu Hapsari3121600040<br>
Alan Tri Arbani Hidayat 3121600056<br>
2 D4 IT B<br><br>

### **Requirement**
#
1.  Mail server adalah sebuah sistem yang membantu dalam pendistribusian email, baik dalam proses menerima atau mengirim. Secara sederhana, mail server adalah perantara dalam proses pengiriman dan penerimaan surat. Email yang dikirim akan disimpan pada mail server, kemudian selanjutnya diforward oleh mail server ke penerima.
   
2.  Postfix adalah mail transfer agent free dan open source. Postfix merupakan mail transfer agent default untuk sejumlah sistem operasi bertipe Unix. Postfix didistribusikan menggunakan Lisensi Umum IBM 1.0 yang merupakan lisensi perangkat lunak bebas tetapi tidak kompatibel dengan GPL. Salah satu ketangguhan Postfix adalah kemampuannya menahan “buffer overflow”. Ketangguhan lainnya adalah kesanggupan Postfix memproses surat elektronik dalam jumlah banyak.

3.  Dovecot adalah server email IMAP dan POP3 open source untuk sistem Linux / UNIX, yang ditulis dengan mengutamakan keamanan. Dovecot adalah pilihan yang sangat baik untuk instalasi kecil dan besar. Cepat, mudah diatur, tidak memerlukan administrasi khusus dan hanya menggunakan sedikit RAM/memori.
   
4.  Roundcube adalah email client IMAP berbasis web. Fitur Roundcube yang paling menonjol adalah penggunaan teknologi Ajax. Salah satu software open source yang berlisensi GNU General Public License (GPL).


### **Konfigurasi Postfix dan Dovecot**
#
Sebelum memulai install mail server, ada baiknya siapkan domain khusus yang akan digunakan untuk konfigurasi mail server. Dalam contoh konfigurasi kali ini akan menggunakan nama domain mail.kampus-02.takehome.com yang dibuat menggunakan bind9 secara lokal.
1. Update repository dan install package postfix.
    ```
    apt update
    apt install postfix dovecot-imapd dovecot-pop3d
    ```
2. **Konfigurasi Postfix** <br>
    Setelah installasi selesai akan muncul message box, kemudian pilih internet site agar komunikasi email menggunakan protokol SMTP secara langsung.
    <img src="./gambar/slave.png"/><br>
    Selanjutnya masukkan nama domain yang akan digunakan.
    <img src="./gambar/slave.png"/><br>
    Setelah itu, postfix akan menyelesaikan installasinya. Setelah Installasi selesai, edit file di /etc/postfix/main.cf dan tambahkan home_mailbox = Maildir/ pada baris paling bawah.
    ```
    sudo nano /etc/postfix/main.cf
    ```

    ```
    inet_interfaces = all
    inet_protocols = all

    #tambahkan baris berikut pada baris paling bawah
    home_mailbox = Maildir/
    ```

    buat mail directory di directory /etc/skel
    ```
    maildirmake.dovecot /etc/skel/Maildir
    ```

    Setelah itu masukkan perintah berikut
    ```
    dpkg-reconfigure postfix
    ```
    Pilih beberapa pilihan dan isikan beberapa input yang akan muncul, sesuaikan dengan topology/konfigurasi sistem dan kebutuhan.
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>
    <img src="./gambar/slave.png"/><br>

    Restart postfix service.
    ```
    systemctl restart postfix
    ```

3. **Konfigurasi Dovecot** <br>
   Edit file konfigurasi /etc/dovecot/dovecot.conf.
   ```
    sudo nano /etc/dovecot/dovecot.conf
    ```

    Uncomment dan edit baris berikut.
    ```
    ...
    # If you want to specify non-default ports or anything more complex,
    # edit conf.d/master.conf.
    listen = *
    ...
    ```
    Edit file konfigurasi /etc/dovecot/conf.d/10-auth.conf.
    ```
    sudo nano /etc/dovecot/conf.d/10-auth.conf
    ```
    Uncomment dan ganti dari yes ke no.
     ```
    ...
    # connection is considered secure and plaintext authentication is allowed.
    # See also ssl=required setting.
    disable_plaintext_auth = no
    ...
    ```

    Edit file konfigurasi /etc/dovecot/conf.d/10-mail.conf.
     ```
    sudo nano /etc/dovecot/conf.d/10-mail.conf
    ```
    Uncomment pada baris berikut.
     ```
    ...
    mail_location = maildir:~/Maildir
    ...
    ```
    Beri comment pada baris berikut.
     ```
    ...
    # mail_location = mbox:~/mail:INBOX=/var/mail/%u
    ...
    ```

    Restart dovecot service.
     ```
    systemctl restart dovecot
    ```


    
### **Config DNS Slave**
#
-  Buka direktori `/etc/bind/named.conf.default-zones` untuk konfigurasi DNS slave
    <img src="./gambar/slave.png"/>
- jangan lupa untuk restart service dns master
    ```
    sudo systemctl reload named
    ```
- untuk melihat apakah slave berhasil transfer zone masternya gunakan command berikut
    ```
    sudo grep "transfer" /var/log/syslog
    ```
- maka akan keluar output berikut jika berhasil transfer `transfer status : success`
    <img src="./gambar/transfer success.png"/>

- cara lain untuk memastikan bahwa berhasil terhubung adalah dengan mengecek di direktori `/var/cache/bind` terdapat file zone masters
    <img src="./gambar/transfer success2.png"/>

### **Install Composer**
#
- karena sudah menginstall apache, php, dan mysql maka langsung menginstall composernya & laravel saja
- install composer
    ```
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer
    ```
- cek apakah composer sudah terinstall
    ```
    composer
    ```
- jika berhasil akan keluar output seperti berikut
    <img src="./gambar/test composer.png"/>

### **Install Laravel**
#
- pindah ke direktori `/var/www/html/` sebagi direktori project laravel
- Buat project laravel
    ```
    composer create-project laravel/laravel kelompok2
    ```
    <img src="./gambar/create_laravel_project.png"/>
- ubah permission direktori project
    ```
    chown -R kel1:kel1 .
    chmod -R 775 storage/
    ```
    <img src="./gambar/change_chown.png"/>

- testing jalankan project laravel dengan port 8000
    ```
    php artisan serve --host 192.168.2.21 --port 8000
    ```
- coba di pc client apakah laravel sudah berjalan dan dapat dibuka
    ```
    192.168.2.21
    ```
    <img src="./gambar/my web.png"/>