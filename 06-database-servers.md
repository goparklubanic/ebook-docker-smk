## 09. DATABASE SERVERS

### 9.1 Mengapa menggunakan database server?

Dewasa ini banyak aplikasi yang menggunakan database server sebagai backend-nya. Contohnya adalah wordpress, moodle, e-learning, blog, forum, wiki, framework modern seperti laravel, codeigniter, django, dan framework mobile seperti react native, flutter, dan lain-lain.
Database server sendiri dapat dikategorikan menjadi 2, yaitu

1. Relational Database Management System (RDBMS)
2. NoSQL Database

RDBMS menggunakan model data relasional yang direpresentasikan dalam bentuk tabel, baris, dan kolom. Manajemen data dilakukan dengan menggunakan bahasa pemrograman SQL.
NoSQL Database menggunakan model data yang tidak relasional. NoSQL database tidak menggunakan bahasa pemrograman SQL. Namun, NoSQL database memiliki bahasa pemrograman tersendiri untuk manajemen data.

Dalam project ini hanya akan dibahas 2 jenis database server, yaitu MariaDB dan PostgreSQL. Kedua database server ini termasuk dalam kategori RDBMS.

### 9.2 Cara kerja database server?

Database server bekerja dengan cara menerima request dari client, memproses request, dan mengirimkan response ke client. Proses kerja database server dapat dijelaskan sebagai berikut:

1. Client mengirim request ke database server
2. Database server menerima request berupa query SQL
3. Database server memproses request
4. Database server mengirimkan response ke client
5. Client menerima response dari database server
6. Aplikasi menampilkan response ke user

### 9.3 Contoh database server?

#### 9.3.1 MariaDB

MariaDB adalah fork dari database server MySQL. MariaDB dikembangkan oleh tim pengembang asli dari MySQL setelah MySQL diakuisisi oleh Oracle. MariaDB sepenuhnya open source dan didistribusikan di bawah lisensi GNU GPL.

#### 9.3.2 PostgreSQL

PostgreSQL adalah database server open source yang dikembangkan oleh PostgreSQL Global Development Group. PostgreSQL didistribusikan di bawah lisensi PostgreSQL License.

### 9.3.3 Perbandingan MariaDB dan PostgreSQL

| Aspek                  | **MariaDB**                    | **PostgreSQL**                   |
| ---------------------- | ------------------------------ | -------------------------------- |
| Jenis Database         | Relational Database (RDBMS)    | Relational Database (RDBMS)      |
| Asal                   | Fork dari MySQL                | Dikembangkan mandiri             |
| Lisensi                | Open Source (GPL)              | Open Source (PostgreSQL License) |
| Tingkat Kesulitan      | ⭐ Mudah                       | ⭐⭐⭐ Lebih kompleks            |
| Cocok untuk Pemula     | ✅ Sangat cocok                | ⚠️ Perlu pembiasaan              |
| Performa Web App       | Sangat baik                    | Baik                             |
| Konsistensi Data       | Baik                           | Sangat tinggi                    |
| Dukungan Transaksi     | Standar                        | Sangat kuat (ACID ketat)         |
| Tipe Data              | Umum & sederhana               | Sangat lengkap & fleksibel       |
| JSON Support           | Ada (cukup)                    | Sangat kuat                      |
| Skalabilitas           | Baik                           | Sangat baik                      |
| Replikasi              | Mudah                          | Lebih kompleks                   |
| Kompatibilitas         | Sangat kompatibel dengan MySQL | Tidak kompatibel MySQL           |
| Digunakan oleh         | Web hosting, CMS               | Sistem besar & enterprise        |
| Contoh Penggunaan      | WordPress, Laravel             | Sistem keuangan, GIS             |
| Cocok untuk WordPress  | ✅ Ya (default)                | ❌ Tidak direkomendasikan        |
| Cocok untuk Docker Lab | ✅ Sangat cocok                | ✅ Cocok (lanjutan)              |

Secara singkat, keduanya dapat dianalogikan seperti ini.

> MariaDB itu seperti motor matic — mudah dipakai, irit, dan cukup untuk kebutuhan sehari-hari.
> PostgreSQL itu seperti mobil manual — lebih ribet, tapi kontrol dan tenaganya lebih besar.

MariaDB Mudah dipelajari, Banyak dipakai di web server, Hampir semua CMS (WordPress, Joomla) pakai ini, Cocok untuk kelas X dan XI. PostgreSQL Lebih ketat dan kompleks, Cocok untuk data besar & kritis, Banyak dipakai di perusahaan besar, Cocok untuk kelas XI dan XII (lanjutan)

### 9.4 Instalasi Server Database

### 9.4.1 Instalasi MariaDB

#### 9.4.1.1 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/mariadb` atau `D:\docker-project\mariadb` dengan struktur sebagai berikut:

```text
mariadb/
  +- data/
  +- docker-compose.yml
```

2. Siapkan isi file docker-compose.yml sebagai berikut:

```yaml
version: "3.8"
services:
  mariadb:
    image: mariadb:latest
    container_name: mariadb
    ports:
      - "3306:3306"
    volumes:
      - ./data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=secret
    networks:
      - lab-network
networks:
  lab-network:
    external: true
```

#### 9.4.1.2 Jalankan MariaDB

Jalankan perintah

```bash
docker compose up -d
```

dari folder `mariadb/` untuk menjalankan MariaDB.

### 9.4.2 Instalasi PostgreSQL

#### 9.4.2.1 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/postgres` atau `D:\docker-project\postgres` dengan struktur sebagai berikut:

```text
postgres/
  +- data/
  +- docker-compose.yml
```

2. Siapkan isi file docker-compose.yml sebagai berikut:

```yaml
version: "3.8"
services:
  postgres:
    image: postgres:latest
    container_name: postgres
    ports:
      - "5432:5432"
    volumes:
      - ./data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=secret
    networks:
      - lab-network
networks:
  lab-network:
    external: true
```

#### 9.4.2.2 Jalankan PostgreSQL

Jalankan perintah

```bash
docker compose up -d
```

dari folder `postgres/` untuk menjalankan PostgreSQL.

### 9.5 Instalasi database manager

Untuk memudahkan pengelolaan database, baik MariaDB maupun PostgreSQL, umumnya digunakan aplikasi database manager. Untuk database MariaDB bisa digunakan PHPMyAdmin, sedangkan untuk PostgreSQL bisa menggunakan Adminer.

### 9.5.1 Instalasi phpMyAdmin Untuk MariaDB

#### 9.5.1.1 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/phpmyadmin` atau `D:\docker-project\phpmyadmin` dengan struktur sebagai berikut:

```text
phpmyadmin/
  +- data/
  +- docker-compose.yml
```

2. Siapkan isi file docker-compose.yml sebagai berikut:

```yaml
version: "3.8"
services:
  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    ports:
      - "8085:80"
    environment:
      - PMA_HOST=mariadb # nama container MariaDB
      - PMA_PORT=3306
    networks:
      - lab-network
networks:
  lab-network:
    external: true
```

#### 9.5.1.2 Jalankan phpMyAdmin

Jalankan perintah

```bash
docker compose up -d
```

dari folder `phpmyadmin/` untuk menjalankan phpMyAdmin. Kemudian buka broser, arahkan ke 192.168.1.100:8085. Login dengan username `root` dan password `secret` untuk memastikan phpmyadmin berjalan dengan baik.



### 9.5.2 Instalasi Adminer untuk PostgreSQL

#### 9.5.2.1 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/adminer` atau `D:\docker-project\adminer` dengan struktur sebagai berikut:

```text
adminer/
  +- data/
  +- docker-compose.yml
```

2. Siapkan isi file docker-compose.yml sebagai berikut:

```yaml
version: "3.8"
services:
  adminer:
    image: adminer:latest
    container_name: adminer
    ports:
      - "8086:8080"
    environment:
      - ADMINER_DEFAULT_SERVER=postgres # nama container PostgreSQL
    networks:
      - lab-network
networks:
  lab-network:
    external: true
```

#### 9.5.2.2 Jalankan Adminer

Jalankan perintah

```bash
docker compose up -d
```

dari folder `adminer/` untuk menjalankan Adminer.

### 9.6 Mengatasi masalah umum?

Masalah umum yang sering terjadi ketika menggunakan database server adalah:

1. Database server tidak dapat diakses dari luar jaringan
2. Database server tidak dapat mengakses internet
3. Database server tidak dapat membuka website
4. Database server lambat membuka website
5. Database server tidak bisa diakses dari container lain

Berikut ini adalah solusi untuk masalah-masalah tersebut:

1. Database server tidak dapat diakses dari luar jaringan
   Solusi: Pastikan port database server sudah dimapping ke host dan port host sudah dibuka di firewall. Jika menggunakan router, pastikan port database server sudah dibuka di router.
2. Database server tidak dapat mengakses internet
   Solusi: Pastikan konfigurasi DNS server di host sudah benar. Pastikan port 53 sudah dimapping ke host dan port 53 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 53 sudah dibuka di router.
3. Database server tidak dapat membuka website
   Solusi: Pastikan konfigurasi database server sudah benar. Pastikan file konfigurasi database server sudah benar. Pastikan file index.html atau index.php sudah benar.
4. Database server lambat membuka website
   Solusi: Pastikan konfigurasi database server sudah benar. Pastikan file konfigurasi database server sudah benar. Pastikan file index.html atau index.php sudah benar.
5. Database server tidak bisa diakses dari container lain
   Solusi: Pastikan database server sudah dijalankan. Pastikan port database server sudah dimapping ke host. Pastikan konfigurasi firewall sudah benar. Periksa komponen environment variabel di docker-compose.yml.

### 9.7 Registrasi database server ke NPM

Setelah database server untuk hostname db.cloud-sekolah.com, pg.cloud-sekolah.com, pma.cloud-sekolah.com (phpmyadmin), dan adminer.cloud-sekolah.com sudah berjalan, maka langkah selanjutnya adalah registrasikan database server ke NPM. Lagnkah-langkanya dapat merujuk ke [04-nginx-proxy-manager.md](04-nginx-proxy-manager.md#751-proxy-host)
