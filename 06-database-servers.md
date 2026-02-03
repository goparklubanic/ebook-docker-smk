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

### 9.4.2 Instalasi PostgreSQL

### 9.5 Instalasi database manager

### 9.5.1 Instalasi phpMyAdmin Untuk MariaDB

### 9.5.2 Instalasi Adminer untuk PostgreSQL

### 9.6 Mengatasi masalah umum?
