## 13. PORTAL WEBSITE DENGAN WORDPRESS

### 13.1 Mengapa menggunakan WordPress?

WordPress adalah aplikasi web yang dapat digunakan untuk membuat website statis maupun blog. WordPress juga dapat digunakan untuk membuat website dinamis dengan menggunakan plugin-plugin tambahan. WordPress dapat diinstall di server lokal maupun di server komputasi awan. Wordpress bisa digunakan sebagai portal website sekolah, dokumentasi ekstrakurikuler, dokumentasi kegiatan sekolah, dan lain-lain.

### 13.2 Kelebihan WordPress

WordPress memiliki banyak kelebihan, di antaranya:

1. Mudah digunakan
2. Banyak plugin, theme, dan template
3. Banyak sumber belajar
4. Banyak komunitas
5. Banyak integrasi dengan aplikasi lain
6. Bisa dihosting sendiri maupun dihosting di komputasi awan
7. Mendukung multi user dan multi role. Admin, editor, author, contributor, dan subscriber.
8. Bisa dikustomisasi sesuai kebutuhan


### 13.3 Manfaat WordPress Untuk Pelajar

Manfaat utama menggunakan WordPress untuk pelajar adalah sebagai tempat untuk belajar `menulis`. Beberapa manfaat aktifitas menulis diantaranya adalah:

1. Mempertajam Kemampuan Kognitif & Memori. 
Menulis membantu otak memproses informasi dengan lebih mendalam dibandingkan hanya membaca atau mendengarkan.

- Retensi Informasi: Proses merangkai kalimat membantu memindahkan informasi dari memori jangka pendek ke memori jangka panjang.

- Sintesis Ide: Menulis memaksa pelajar untuk menghubungkan konsep-konsep yang berbeda menjadi satu pemahaman yang utuh.

2. Sarana Katarsis & Kesehatan Mental
Bagi pelajar, menulis adalah alat regulasi emosi yang sangat efektif.

- Melepaskan Stres: Menuliskan kecemasan atau tekanan pelajaran (journaling) dapat mengurangi beban mental.

- Mengenal Diri: Membantu pelajar memahami pola pikir dan perasaan mereka sendiri terhadap suatu masalah.

3. Melatih Logika & Struktur Berpikir
Tulisan yang baik lahir dari pikiran yang teratur. Saat menulis, seorang pelajar belajar untuk:

- Berargumen: Menyusun premis dan kesimpulan secara logis.

- Sistematis: Belajar mendahulukan apa yang penting (prioritas) agar pembaca paham alur ceritanya.

4. Meningkatkan Kemampuan Komunikasi Verbal
Seringkali, orang yang mahir menulis juga akan lebih lancar saat berbicara.

- Kekayaan Kosakata: Menulis memaksa kita mencari diksi yang tepat, yang nantinya akan terbawa ke percakapan sehari-hari.

- Kepercayaan Diri: Karena sudah terbiasa menyusun ide di kepala, mereka cenderung lebih tenang saat harus menyampaikan pendapat di depan kelas.

Menulis menggunakan media wordpress memiliki manfaat tambahan, diantaranya:

1. Belajar kerja sama tim, misal dalam mengelola `e-mading` ( majalah dinding elektronik )
2. Belajar presentasi hasil kerja
3. Belajar mendokumentasikan pembelajaran
4. Meningkatkan Literasi Digital & Teknis
5. Membangun Portofolio Digital
6. Mengasah Berpikir Kritis & Terstruktur
7. Media Ekspresi Diri yang Aman

### 13.4 Instalasi WordPress

Wordpress adalah content management system ( CMS ) yang membutuhkan layanan database untuk menyimpan data. Secara default, WordPress menggunakan MySQL sebagai database server. Namun, WordPress juga dapat menggunakan MariaDB, PostgreSQL, SQLite, dan Oracle Database. Untuk menggunakan MariaDB, kita akan menggunakan container database yang sudah ada.

Dalam project ini, wordpress akan menggunakan MariaDB sebagai database server memanfaatkan container database yang sudah ada. Berikut ini langkah-langkahnya.

#### 13.4.1 Persiapan database

1. Buka web interface phpMyAdmin di `http://192.168.1.100:8085`
2. Login dengan username `root` dan password `secret`
3. Klik menu `Databases` di bagian kiri atas
4. Masukkan nama database, misal `wordpress` dan klik tombol `Create`
5. Tambahkan credential untuk database wordpress. Misal username 'wpuser' dan password 'wppass'. Klik nama database `wordpress` yang baru dibuat, kemudian klik tab `sql`. Ketikkan perintah sql berikut untuk membuat user dan memberikan akses ke database `wordpress`

```sql
CREATE USER 'wpuser'@'%' IDENTIFIED BY 'wppass';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'%';
FLUSH PRIVILEGES;
```

6. Klik tombol `Go` untuk mengeksekusi perintah sql di atas.

#### 13.4.2 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/wordpress` atau `D:\docker-project\wordpress` dengan struktur sebagai berikut:

```text
wordpress/
  +- data/
  +- docker-compose.yml
```

#### 13.4.3 docker-compose.yml

```yaml
version: "3.8"
services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    ports:
      - "8084:80"
    volumes:
      - ./data:/var/www/html
    environment:
      - WORDPRESS_DB_HOST=mariadb
      - WORDPRESS_DB_NAME=wordpress
      - WORDPRESS_DB_USER=wpuser
      - WORDPRESS_DB_PASSWORD=wppass
    networks:
      - lab-network
networks:
  lab-network:
    external: true
```

#### 13.4.4 Jalankan WordPress

Jalankan perintah

```bash
docker compose up -d
```

dari folder `wordpress/` untuk menjalankan WordPress.

Setelah berhasil, buka browser dan arahkan ke `http://192.168.1.100:8084` untuk memulai instalasi wordpress.

### 13.5 Mengatasi masalah umum

Masalah umum yang sering terjadi ketika menggunakan WordPress adalah:

1. WordPress tidak dapat diakses dari luar jaringan
2. WordPress tidak dapat mengakses internet
3. WordPress tidak dapat membuka website
4. WordPress lambat membuka website

Berikut ini adalah solusi untuk masalah-masalah tersebut:

1. WordPress tidak dapat diakses dari luar jaringan
   Solusi: Pastikan port 8084 sudah dimapping ke host dan port 8084 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 8084 sudah dibuka di router.
2. WordPress tidak dapat mengakses internet
   Solusi: Pastikan konfigurasi DNS server di host sudah benar. Pastikan port 53 sudah dimapping ke host dan port 53 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 53 sudah dibuka di router.
3. WordPress tidak dapat membuka website
   Solusi: Pastikan konfigurasi WordPress sudah benar. Pastikan file konfigurasi WordPress sudah benar. Pastikan file index.html atau index.php sudah benar.
4. WordPress lambat membuka website
   Solusi: Pastikan konfigurasi WordPress sudah benar. Pastikan file konfigurasi WordPress sudah benar. Pastikan file index.html atau index.php sudah benar.

### 13.6 Registrasi WordPress ke NPM

Setelah WordPress untuk hostname blog.cloud-sekolah.com sudah berjalan, maka langkah selanjutnya adalah registrasikan WordPress ke NPM. Lagnkah-langkanya dapat merujuk ke [04-nginx-proxy-manager.md](04-nginx-proxy-manager.md#751-proxy-host)
