## 11. FILE SERVER

### 11.1 Mengapa menggunakan file server?

Di lingkungan sekolah, pasti banyak aktifitas yang melibatkan file, seperti:

1. Dokumen surat menyurat Tata Usaha
2. Dokumen kegiatan belajar mengajar ( RPP, SILABUS, LKS, dll )
3. Dokumentasi kegiatan sekolah ( foto, video, dll )
4. Kerjasama dengan lembaga luar sekolah ( file-file kerjasama, hasil rapat, dll )

File server menyediakan fasilitas untuk menyimpan, mengakses, dan mengelola file-file tersebut, baik sebagai penyimpanan utama ataupun sebagai backup (pencadangan). File server juga dapat digunakan untuk mengelola file-file pribadi guru dan siswa.

### 11.2 Mengapa Bukan FTP Server?

FTP (File Transfer Protocol) adalah protokol yang cukup tua dan tidak aman untuk digunakan. FTP tidak mengenkripsi data yang dikirimkan, sehingga mudah diketahui oleh pihak ketiga. Selain itu, FTP juga tidak aman untuk digunakan di lingkungan sekolah karena tidak memiliki fitur-fitur yang dibutuhkan, seperti autentikasi, otorisasi, dan enkripsi

### 11.3 Mengapa RustFS?

RustFS adalah S3 (Simple Storage Service) server yang dibangun menggunakan bahasa pemrograman Rust. Layanan S3 sendiri sudah diimplementasikan oleh Amazon Web Services (AWS) dan Google Drive untuk menyimpan file-file di cloud. RustFS adalah file server yang dibangun menggunakan bahasa pemrograman Rust. RustFS memiliki kelebihan dibandingkan dengan file server lainnya, yaitu:

1. Mudah dikonfigurasikan
2. Ringan
3. Aman

Selain itu, RustFS juga memiliki fitur-fitur yang menarik, seperti:

1. Enkripsi end-to-end
2. Kompatibel dengan Windows, Linux, dan MacOS
3. Dapat diakses melalui web interface, mobile app, dan desktop app
4. Cara kerja mirip dengan Google Drive

### 11.4 Instalasi RustFS

#### 11.4.1 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/rustfs` atau `D:\docker-project\rustfs` dengan struktur sebagai berikut:

```text
rustfs/
  +- data/
  +- docker-compose.yml
```

Set permission ke folder `data` menjadi writable, readable dan executable.

```bash
chmod 7770 data/
```

#### 11.4.2 docker-compose.yml

```yaml
version: "3.8"
services:
  localrustfs:
    image: rustfs/rustfs:latest
    container_name: localrustfs
    ports:
      - "9000:9000"
      - "9001:9001"
    volumes:
      - ./data:/data
    environment:
      - RUSTFS_ACCESS_KEY=adminaccess
      - RUSTFS_SECRET_KEY=adminsecret
      - RUSTFS_CONSOLE_ENABLE=true
    networks:
      - lab-network
    command: >
      --address :9000
      --console-enable
      --access-key adminaccess
      --secret-key adminsecret
      /data

networks:
  lab-network:
    external: true
```

#### 11.4.3 Jalankan RustFS

Jalankan perintah

```bash
docker compose up -d
```

dari folder `rustfs/` untuk menjalankan RustFS.

### 11.5 Mengelola File Server RustFS

### 11.5.1 Membuat bucket

Bucket adalah tempat untuk menyimpan file seperti halnya folder. Setiap bucket memiliki nama yang unik dan case-sensitive, huruf besar dan huruf kecil dibedakan. Nama bucket tidak boleh mengandung spasi, tab, atau karakter ilegal lainnya. Langkah-langkah membuat bucket:

1. Buka web interface RustFS di `http://192.168.1.100:9001`
2. Login dengan account `adminaccess` dan key `adminsecret`
3. Klik tombol `+ Create Bucket`
4. Isi nama bucket, misal `folderuser01` dan klik tombol `Create`

### 11.5.2 Membuat Policy

Policy adalah aturan yang mengatur akses ke bucket. Setiap policy memiliki nama yang unik dan case-sensitive, huruf besar dan huruf kecil dibedakan. Sekarang akan dibuat policy untuk membatasi akses hanya ke bucket `folderuser01` saja. Langkah-langkah membuat policy:

1. Klik menu `Policies` di sisi kiri
2. Klik tombol `+ Create Policy`
3. Isi nama policy, misal `user01`
4. Isi `Policy Original` dengan kode JSON sebagai berikut:

```text
{
  "ID": "",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "NotAction": [],
      "Resource": [
        "arn:aws:s3:::folderuser01/*"
      ],
      "NotResource": [],
      "Condition": {}
    },
    {
      "Sid": "",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "NotAction": [],
      "Resource": [
        "arn:aws:s3:::folderuser01/*"
      ],
      "NotResource": [],
      "Condition": {}
    }
  ]
}
```

7. Klik tombol `Submit`

### 11.5.3 Membuat User

User adalah entitas yang dapat mengakses file server. Setiap user memiliki nama yang unik dan case-sensitive, huruf besar dan huruf kecil dibedakan. Nama user tidak boleh mengandung spasi, tab, atau karakter ilegal lainnya. Setiap user juga memiliki key yang digunakan untuk autentikasi. Langkah-langkah membuat user:

1. Klik menu `Users` di sisi kiri
2. Klik tombol `+ Create User`
3. Isi Access Key, misal `user01`
4. Isi Secret Key, misal `user01key`
5. Isi policy, pilih `user01` yang baru dibuat
6. Klik tombol `Submit`

### 11.5.4 Menambahkan Akun Untuk Users

1. Login ke rustfs
2. Klik menu `Users` di sisi kiri
3. Pilih user yang akan ditambahkan akun, misal user01
4. Klik tab `Accounts`
5. Klik tombol `+ Add Account`
6. Isikan `Access Key`,`Secret Key`, `Name`, `Description` dan `Expiration` jika diperlukan.
7. Klik tombol `Submit`

### 11.5.5 Membuat Group User

1. Login
2. Klik menu `User Groups` di sisi kiri
3. Klik tombol `+ User Group`
4. Isi nama group, misal `group01`
5. Pilih user-user yang akan ditambahkan ke dalam group
6. Klik tombol `Submit`

### 11.6 menggunakan File Server RustFS

#### 11.6.1 Mengakses File Server RustFS

Umumnnya layanan RustFS dapat diakses melalui web browser. Berikut langkah-langkahnya:

1. Buka web browser dan akses `http://192.168.1.100:9001`
2. Login dengan username dan password ( account dan secret key ) yang sudah dibuat. Misal `user01` dan `user01key`

Setelah itu, akan muncul halaman dashboard dengan isi sesuai dengan policy yang dimiliki. Sebagai contoh, user user01 hanya dapat mengakses bucket `folderuser01` saja. Selanjutnya, user dapat mengupload, mengunduh, dan menghapus file, membuat folder, dan menghapus folder di dalam bucket `folderuser01` tersebut.

#### 11.6.2 Aplikasi Client RustFS (S3)

Mobile App

- BucketAnywhere for S3 (Android)
- FileBrowser (Android)
- FileBrowser (iOS)

Desktop App

- FileBrowser (Windows, Linux, MacOS)
- Cyberduck (Windows, MacOS)
- Forklift (MacOS)
- Transmit (MacOS)
- CommanderOne (Windows)

Semua aplikasi di atas gratis dan open source. Anda bisa memilih salah satu sesuai kebutuhan. Penggunaan aplikasi di atas hampir mirip dengan penggunaan aplikasi FTP client. Anda dapat mengakses file server RustFS melalui aplikasi di atas dengan menggunakan endpoint `http://192.168.1.100:9001` dan credentials ( account dan secret key ) yang

### 11.7 Mengatasi masalah umum

Masalah umum yang sering terjadi ketika menggunakan file server adalah:

1. Container gagal dijalankan
2. File server tidak dapat diakses dari dalam jaringan.

Berikut ini adalah solusi untuk masalah-masalah tersebut:

1. Container gagal dijalankan
   Solusi: Periksa log container dengan perintah `docker logs <container_name>` atau `docker logs <container_id>`. Periksa pemission folder `data` yang dijadikan volume bind. Nonaktifkan Selinux jika diaktifkan.
2. File server tidak dapat diakses dari dalam jaringan.
   Solusi: Periksa kembali konfigurasi docker-compose.yml, pastikan port sudah benar. Periksa firewall di host dan router jika diperlukan.
