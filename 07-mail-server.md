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
to be continued...
rujukan::
https://docker-mailserver.github.io/docker-mailserver/latest/examples/tutorials/basic-installation/
::
