## 14. WEB CONTROL PANEL

### 14.1 Untuk apa web control panel?

Barangkali salah satu topik pembelajaran di SMK program keahlian TKJ / TKJT, adalah sistem administrasi jaringan, dan salah satu pembahasannya tentang hosting. Hosting sendiri dapat dikategorikan menjadi 2, yaitu

1. Shared Hosting
2. VPS ( Virtual Private Server )

Shared hosting adalah hosting yang dibagi-bagi menjadi beberapa akun. Setiap akun memiliki akses ke web server, database server, dan mail server. Pengguna hanya perlu mengatur web server, database server, dan mail server melalui web control panel yang sudah disediakan. Pengguna tidak perlu mengatur server secara manual.

VPS ( Virtual Private Server ) adalah server virtual yang terisolasi dari server lain. Pengguna memiliki akses penuh terhadap server. Pengguna dapat mengatur server secara manual. Biasanya, VPS digunakan untuk hosting website, namun dapat juga digunakan untuk aplikasi lainnya.

Dalam dunia industri, untuk mengatur server, terdapat 2 pendekatan, yaitu:

1. Mengatur server secara manual. Pengguna harus mengatur konfigurasi server secara manual dengan cara mengedit file konfigurasi. Cara ini lebih rumit dan tidak praktis. Di sisi lain cara ini justru lebih fleksibel, karena pengguna mendapatkan kontrol penuh.
2. Mengatur server melalui web control panel. Web control panel adalah antarmuka web yang disediakan untuk memudahkan pengguna mengatur server. Web control panel menyediakan fitur-fitur untuk mengatur server, tanpa harus mengedit file konfigurasi secara manual. Cara ini lebih mudah dan praktis, namun fleksibilitasnya kurang dibandingkan dengan mengatur server secara manual. Beberapa contoh web control panel adalah:
   1. cPanel
   2. Plesk
   3. Webmin dan virtualmin
   4. Vestacp
   5. Ajenti
   6. Zentyal
   7. aaPanel, dan lain-lain.

## 14.2 Mengapa aaPanel?

aaPanel adalah web control panel yang dikembangkan oleh Alibaba Cloud. aaPanel bersifat free, meskipun tidak sepenuhnya open source. aaPanel mendukung multiple platform, yaitu:

1. Debian, dan distro turunannya, termasuk Ubuntu, Linux Mint, dan sebagainya.
2. Red Hat, dan distro turunannya, termasuk CentOS, Fedora, dan sebagainya.
3. SUSE, dan distro turunannya, termasuk openSUSE, dan sebagainya.
4. Arch Linux, dan distro turunannya, termasuk Manjaro, dan sebagainya.
5. Windows
6. FreeBSD
7. OpenBSD
8. Solaris
9. MacOS, dan lainnya.

aaPanel versi _free_ sendiri memiliki fitur-fitur yang menarik, yaitu:

1. Unlimited WebSites. Jumlah website tidak dibatasi.
2. WP Toolkit. aaPanel menyediakan fitur-fitur untuk mengatur WordPress, seperti auto install, auto update, one-click plugin install, dan sebagainya.
3. Cloudflare Integration. Memungkinkan pengguna menghubungkan situs web mereka dengan layanan Cloudflare untuk meningkatkan keamanan dan performa. Pengguna dapat mengelola pengaturan DNS, mengaktifkan SSL, dan mengatur pengaturan caching langsung dari antarmuka aaPanel, sehingga memudahkan pengelolaan dan optimasi situs.
4. Docker manager. Fitur yang memungkinkan pengguna untuk mengelola kontainer Docker secara langsung dari antarmuka aaPanel. Dengan Docker Manager, pengguna dapat membuat, menjalankan, menghentikan, dan menghapus kontainer, serta mengelola image Docker tanpa menggunakan command line. Fitur ini mempermudah pengelolaan aplikasi berbasis kontainer dalam lingkungan hosting.
5. Unlimited SSL certificate. Jumlah sertifikat ssl tidak dibatasi.
6. Basic security protection, meliputi: Firewall, SSL, password kuat, update rutin, backup dan monitoring.
7. Full-featured file manager. Antarmuka intuitif dan mudah digunakan. Mendukung operasi dasar seperti upload, download, edit, dan mengelola file dan folder, serta terintegrasi dengan FTP. Mudah mengelola hak akses. Menyediakan fitur pencarian, kompresi, dan ekstraksi, dan pratinjau.
8. File online editor. Mengedit file langsung dari browser.
9. Use all free plugins. Bebas menggunakan semua plugin yang tersedia.
10. Git Support. Memungkinkan pengguna untuk mengelola repositori Git langsung dari antarmuka aaPanel. Fitur ini mempermudah pengelolaan proyek dan integrasi dengan platform pengembangan lainnya.

## 14.3 Instalasi aaPanel menggunakan Docker

Salah satu keuntungan instalasi aaPanel menggunakan docker adalah memungkinkan reset lingkungan belajar dengan cepat jika terjadi kesalahan konfigurasi. Dalam project ini, disarankan aaPanel tetap diinstall pada dedicated host, tidak digabung dengan kontainer-kontainer lain dengan pertimbangan untuk memaksimalkan resource hardware. Selain itu juga karena aaPanel versi free tidak mendukung multi-user, sehingga strategi yang diterapkan dalam project ini satu container berlaku untuk satu user. Satu user boleh menjadi perwakilan satu siswa atau kelompok siswa.

Untuk keperluan tersebut, berikut saran spesifikasi perangkat keras yang dibutuhkan untuk menyediakan 60 container:

| Komponen       | Spesifikasi Minimum | Spesifikasi Rekomendasi               |
| -------------- | ------------------- | ------------------------------------- |
| CPU            | 8 Cores (shared)    | 16 Cores atau lebih (dedicated)       |
| RAM            | 32 GB               | 64 GB                                 |
| Penyimpanan    | 250 GB SSD          | 500 GB NVMe SSD                       |
| Sistem Operasi | Ubuntu 24.04 LTS    | Proxmox atau Debian (Minimal Install) |

Hal lain yang patut diingat, sampai ebook ini ditulis, aaPanel versi docker memiliki sejumlah kekurangan, antara lain:

1. Update image terakhir sekitar tahun 2022.
2. Versi nginx 1.22
3. Versi apache 2.4
4. Versi MySQL tertinggi 5.7
5. Versi PHP maksimal 7.4.

Untuk melakukan update versi PHP dan MySQL, bisa dilakukan dengan 2 cara, yaitu:

1. Melalui menu AppStore di dashboard aaPanel.
2. Melalui command line di dalam container aaPanel.

### 14.3.1 Persiapan folder

1. Persiapkan sebuah folder `/home/user/docker-project/aapanel` atau `D:\docker-project\aapanel` dengan struktur sebagai berikut:

```text
aapanel/
  +- website_data/
  +- mysql_data/
  +- vhost/
  +- docker-compose.yml
```

### 14.3.2 docker-compose.yml

```yaml
services:
  aapanel:
    image: aapanel/aapanel:latest
    container_name: aapanel-siswa
    ports:
      - "78xx:7800" # Port Panel
      - "22xx:21" # Port SSH
      - "443xx:443" # Port HTTPS
      - "80xx:80" # Port HTTP
      - "88xx:888" # Port PHPMyAdmin
      # sesuaikan xx dengan nomor container yang diberikan
    volumes:
      - ./www_data:/www/wwwroot
      - ./mysql_data:/www/server/data
      - ./vhost:/www/server/panel/vhost
    restart: unless-stopped
    privileged: true # Dibutuhkan untuk manajemen resource sistem
```

### 14.3.3 Jalankan aaPanel

Jalankan perintah

```bash
docker compose up -d
```

dari folder `aapanel/` untuk menjalankan aaPanel.

Selanjutnya akses aaPanel di `http://192.168.1.100:8886`. Berikut beberapa kredensial default aapanel:

- Installatione entry: aapanel
- Username: aapanel
- Password: aapanel123
- root password: aapanel123

Setelah instalasi selesai, segera ganti username dan password aapanel.

### 14.4 Registrasi ke NPM

Setelah aaPanel untuk hostname siswaxx.cloud-sekolah.com sudah berjalan, maka langkah selanjutnya adalah registrasikan aaPanel ke NPM. Lagnkah-langkanya dapat merujuk ke [04-nginx-proxy-manager.md](04-nginx-proxy-manager.md#751-proxy-host).
