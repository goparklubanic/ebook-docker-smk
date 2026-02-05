## 10. MAIL SERVER

### 10.1 Mengapa menggunakan mail server?

Mail server adalah aplikasi yang melayani permintaan pengiriman dan penerimaan email. Layanan yang masih sangat populer. Mail server juga dapat melayani permintaan dinamis dari aplikasi web. Layanan ini termasuk yang banyak digunakan untuk banyak keperluan seperti

1. Komunikasi Internal di sekolah
2. E-learning (moodle)
3. Perpustakaan digital, dan lain-lain

### 10.2 Cara kerja mail server

Mail server bekerja dengan cara menerima request dari client, memproses request, dan mengirimkan response ke client. Proses kerja mail server dapat dijelaskan sebagai berikut:

1. Client mengirim email menggunakan protokol SMTP (Simple Mail Transfer Protocol) ke mail server
2. Mail server menerima email melalui protokol SMTP
3. Mail server memproses dan menyimpan email di mailbox penerima
4. Client penerima mengambil email menggunakan protokol POP3 (Post Office Protocol 3) atau IMAP (Internet Message Access Protocol)
5. Client menerima email dari mail server
6. Aplikasi email menampilkan email ke user

### 10.3 Contoh Dockerized Mail Server

Terdapat beberapa pilihan server email yang dapat dibangun sebagai container docker. Beberapa diantaranya adalah

1. mailserver/docker-mailserver
2. Mailu
3. Zimbra
4. iRedMail
5. Modoboa

Semua server email di atas dapat dikategorikan menjadi 2, yaitu

1. Full Featured Mail Server, seperti: mailserver/docker-mailserver, Mailu, Zimbra, iRedMail, Modoboa
2. Simple Mail Server, seperti: Postfix, Dovecot, dan lain-lain

### 10.4 Mengapa mailserver/docker-mailserver

Dari kelima contoh _dockerized_ mail server di atas, mengapa mailserver/docker-mailserver yang digunakan? Hal ini dikarenakan mailserver/docker-mailserver sudah termasuk dalam kategori Full Featured Mail Server dan sudah teruji stabilitasnya dalam lingkungan docker. Selain itu, mailserver/docker-mailserver juga sangat mudah dikonfigurasikan dan dioperasikan. Dari segi ukuran image, mailserver/docker-mailserver juga tidak terlalu besar, hanya sekitar 100MB saja dibandingkan dengan Zimbra yang lebih dari 1GB.

### 10.5 Instalasi mailserver/docker-mailserver

### 10.5.1 Persiapan folder project

Buat struktur folder seperti berikut di /home/user/docker-project atau D:\docker-project
seperti berikut:

```text
+- mail/
| +- docker-compose.yml
| +- docker-data/
| | +- dms/
| | | +- mail-data/
| | | +- mail-state/
| | | +- mail-logs/
| | | +- config/
```

```bash
mkdir -p /home/user/docker-project/email/docker-data/dms/{mail-data,mail-state,mail-logs,config}
```

### 10.5.2 Membuat MX Record di DNS Server

Sebelum menjalankan mail server, kita harus membuat MX record di DNS server. MX record adalah record yang berisi informasi tentang mail server yang bertanggung jawab untuk menerima email untuk suatu domain. Kita akan membuat MX record untuk domain cloud-sekolah.com. Pengaturan MX record ini tidak bisa dilakukan di web interface Pi-hole. Jadi harus dikonfigurasikan dengan mengedit file konfigurasi dnsmasq di kontainer pi-hole. Berikut langkah-langkahnya.

1. Masuk ke konsole pihol melalui terminal dengan perintah

```bash
docker exec -it pihole /bin/bash
```

2. Buat file /etc/dnsmasq.d/05-mailserver.conf dengan perintah

```bash
nano /etc/dnsmasq.d/05-mailserver.conf
```

3. Tambahkan baris berikut di akhir file

```text
# A record untuk mail server
address=/mail.cloud-sekolah.com/192.168.1.100

# MX record for mail.cloud-sekolah.com
mx-host=cloud-sekolah.com,mail.cloud-sekolah.com,10
```

4. Simpan file dengan menekan Ctrl+X, kemudian tekan Y untuk menyimpan perubahan. Keluar dari konsole pihole dengan perintah `exit`.
5. Restart service dnsmasq dengan perintah

```bash
docker restart pihole
```

5. Periksa konfigurasi MX record dengan perintah

```bash
dig mx cloud-sekolah.com
```

Jika berhasil, akan muncul output sebagai berikut:

```text
cloud-sekolah.com.   MX 10 mail.cloud-sekolah.com.
```

Apabila tidak berhasil, berikut kemungkinan-kemungkinan permasalahan yang sering terjadi:
| Masalah | Penyebab |
| --------------------- | ---------------------- |
| MX tidak muncul | File `.conf` salah |
| DNS tidak jalan | Syntax error |
| Tidak resolve | A record belum ada |
| Reset setelah restart | Volume belum ter-mount |

Dari tata cara setting MX Record bisa diketaui bahwa

> Tidak semua konfigurasi bisa dilakukan lewat GUI.<br>
> Administrator jaringan harus siap mengelola file konfigurasi langsung.

**Itu semua merupakan soft skill penting di dunia kerja.**

### 10.5.3 Membuat docker-compose.yml untuk mailserver/docker-mailserver

Berikut ini adalah contoh docker-compose.yml untuk mailserver/docker-mailserver:

```yaml
services:
  mailserver:
    image: ghcr.io/docker-mailserver/docker-mailserver:latest
    container_name: mailserver
    # Provide the FQDN of your mail server here (Your DNS MX record should point to this value)
    hostname: mail.cloud-sekolah.com
    ports:
      - "25:25"
      - "465:465"
      - "587:587"
      - "993:993"
    volumes:
      - ./docker-data/dms/mail-data/:/var/mail/
      - ./docker-data/dms/mail-state/:/var/mail-state/
      - ./docker-data/dms/mail-logs/:/var/log/mail/
      - ./docker-data/dms/config/:/tmp/docker-mailserver/
      - /etc/localtime:/etc/localtime:ro
    environment:
      - ENABLE_RSPAMD=1
      - ENABLE_CLAMAV=1
      - ENABLE_FAIL2BAN=1
    cap_add:
      - NET_ADMIN # For Fail2Ban to work
    restart: always
```

### 10.5.4 Jalankan mailserver/docker-mailserver

Jalankan perintah

```bash
docker compose up -d
```

dari folder `mail/` untuk menjalankan mailserver/docker-mailserver.

### 10.5.5 Testing mailserver/docker-mailserver

Setelah menjalankan mailserver/docker-mailserver, kita dapat menguji apakah mail server sudah berjalan dengan baik. Kita dapat menggunakan tool seperti telnet untuk menguji apakah port-port yang dibutuhkan sudah terbuka. Berikut langkah-langkahnya.

1. Buka terminal di komputer lokal
2. Ketik perintah `telnet mail.cloud-sekolah.com 25` untuk menguji port 25. Jika muncul output sebagai berikut, maka port 25 sudah terbuka.

```text
Connected to mail.cloud-sekolah.com.
Escape character is '^]'.
220 mail.cloud-sekolah.com ESMTP ready
```

3. Tekan Ctrl+D untuk keluar dari telnet

Apabila tidak berhasil, maka periksa kembali konfigurasi mailserver/docker-mailserver dan pastikan port-port yang dibutuhkan sudah terbuka.

### 10.6 Menambahkan user di mailserver/docker-mailserver

Setelah mail server berhasil dijalankan, kita dapat menambahkan user di mail server. User di mail server ini akan digunakan untuk mengakses email via webmail, IMAP, SMTP, dan POP3. Berikut langkah-langkahnya.

1 . Masuk ke kontainer mailserver dengan perintah

```bash
docker exec -it mailserver /bin/bash
```

2. Tambahkan akun, misal `user01@cloud-sekolah.com` dengan password `passworD_User01`. berikut perintahnya:

```bash
setup email add user01@cloud-sekolah.com passworD_User01
```

Boleh ditambahkan alias jika diperlukan, dengan perintah

```bash
setup alias add user01@cloud-sekolah.com user01@cloud-sekolah.com
```

3. Jika diperlukan quota per user, misal 50MB, berikut perintahnya.
   Learn more about the available subcommands via: setup help

```bash
setup quota set user01@cloud-sekolah.com 50M
```

Jika user sudah tidak dibutuhkan, maka dapat dihapus dengan perintah

```bash
setup email del -y user01@cloud-sekolah.com
```

### 10.7 Konfigurai mail client

Setelah mail server bekerja dan user berhasil ditambahkan, layanan email bisa digunakan menggunakan mail client. Contoh mail client yang cukup populer di antaranya adalah

### ## 10.7.1 Mail Client Desktop

| Aplikasi                | Windows | macOS | Linux | Cocok untuk SMK | Catatan                                      |
| ----------------------- | ------- | ----- | ----- | --------------- | -------------------------------------------- |
| **Mozilla Thunderbird** | ✅      | ✅    | ✅    | ⭐⭐⭐⭐⭐      | Gratis, open source, paling direkomendasikan |
| **Microsoft Outlook**   | ✅      | ✅    | ❌    | ⭐⭐⭐          | Umum di industri, berbayar                   |
| **Evolution**           | ❌      | ❌    | ✅    | ⭐⭐⭐          | Default GNOME Linux                          |
| **Apple Mail**          | ❌      | ✅    | ❌    | ⭐⭐            | Khusus ekosistem Apple                       |
| **Mailspring**          | ✅      | ✅    | ✅    | ⭐⭐⭐          | UI modern, fitur terbatas                    |
| **Claws Mail**          | ✅      | ❌    | ✅    | ⭐⭐            | Ringan, tampilan klasik                      |

### # 10.7.2 Mail Client Mobile

| Aplikasi                     | Android | iOS | Cocok untuk SMK | Catatan                                  |
| ---------------------------- | ------- | --- | --------------- | ---------------------------------------- |
| **Gmail App**                | ✅      | ✅  | ⭐⭐⭐          | Mudah, tapi fitur server custom terbatas |
| **Microsoft Outlook Mobile** | ✅      | ✅  | ⭐⭐⭐⭐        | Stabil, cocok uji SMTP/IMAP              |
| **K-9 Mail**                 | ✅      | ❌  | ⭐⭐⭐⭐⭐      | Open source, sangat cocok lab            |
| **FairEmail**                | ✅      | ❌  | ⭐⭐⭐⭐        | Privasi tinggi                           |
| **Apple Mail**               | ❌      | ✅  | ⭐⭐            | Default iOS                              |

Untuk konfigurasi mail client, biasanya ada opsi untuk menambahkan account dengan mengisi server IMAP, SMTP, dan port-port yang digunakan. Biasanya port untuk IMAP adalah 993, SMTP adalah 587, dan POP3 adalah 995. Namun, karena dalam hal ini SSL belum dikonfigurasikan, maka port untuk IMAP adalah 143, SMTP adalah 25, dan POP3 adalah 110.

### 10.8 Mengatasi masalah umum

Masalah umum yang sering terjadi ketika menggunakan mail server adalah:

1. Mail server tidak bisa diakses.
   Solusi: Periksa konfigurasi firewall, pastikan port 25, 143, 465, 587, 993 sudah terbuka. Periksa konfigurasi DNS, pastikan MX record sudah benar. Periksa konfigurasi mail server, pastikan semua service sudah running.
2. Tidak bisa login ke webmail
   Solusi: Periksa konfigurasi mail server, pastikan user dan password sudah benar. Periksa log mail server di folder `/var/log/mail/` untuk mengetahui kesalahan apa yang terjadi.
3. Tidak bisa kirim email
   Solusi: Periksa log mail server di folder `/var/log/mail/` untuk mengetahui kesalahan apa yang terjadi. Periksa konfigurasi mail client, pastikan server dan port sudah benar.
4. Tidak bisa terima email
   Solusi: Periksa log mail server di folder `/var/log/mail/` untuk mengetahui kesalahan apa yang terjadi. Periksa konfigurasi DNS, pastikan MX record sudah benar. Periksa konfigurasi mail server, pastikan service SMTP sudah running.

### 10.9 Registrasi ke NPM

Mengapa mail server ini tidak perlu di-registrasikan ke NPM? Karena mail server ini tidak memiliki web interface. Semua operasinya dilakukan via CLI atau langsung mengakses folder konfigurasi.

> rujukan dari <br>
> https://docker-mailserver.github.io/docker-mailserver/latest/examples/tutorials/basic-installation/ <br>
> https://docker-mailserver.github.io/docker-mailserver/latest/config/account-management/provisioner/file/
