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

RustFS adalah file server yang dibangun menggunakan bahasa pemrograman Rust. RustFS memiliki kelebihan dibandingkan dengan file server lainnya, yaitu:

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

#### 11.4.2 docker-compose.yml

```yaml
version: "3.8"
services:
  rustfs:
    image: rustfs/rustfs
    command: --address:9000 --console-enable --server-domain rustfs.cloud-sekolah.com --access-key rustfsadmin --secret-key rustfsadmin /data
    container_name: rustfs
    ports:
      - "9000:9000"
      - "9001:9001"
    volumes:
      - ./data:/data
    environment:
      - RUSTFS_ACCESS_KEY=rustfsadmin
      - RUSTFS_SECRET_KEY=rustfsadmin
      - RUSTFS_CONSOLE_ENABLE=true
      - RUSTFS_SERVER_DOMAIN=rustfs.cloud-sekolah.com
    networks:
      - lab-network
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

1. Buka web interface RustFS di `http://192.168.1.100:8083`
2. Login dengan account `rustfsadmin` dan key `rustfsadmin`
3. Klik tombol `+ Create Bucket`
4. Isi nama bucket, misal `folderuser01` dan klik tombol `Create`

### 11.5.2 Membuat Policy

Policy adalah aturan yang mengatur akses ke bucket. Setiap policy memiliki nama yang unik dan case-sensitive, huruf besar dan huruf kecil dibedakan. Sekarang akan dibuat policy untuk membatasi akses hanya ke bucket `folderuser01` saja. Langkah-langkah membuat policy:

1. Klik menu `Policies` di sisi kanan
2. Klik tombol `+ New Policy`
3. Isi nama policy, misal `user01`
4. Isi Policy Original dengan kode JSON sebagai berikut:

```text
{
  "ID": "",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Action": [
        "s3:*"
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

1. Klik menu `Users` di sisi kanan
2. Klik tombol `+ New User`
3. Isi Access Key, misal `user01`
4. Isi Secret Key, misal `user01key`
5. Isi policy, pilih `user01` yang baru dibuat
6. Klik tombol `Submit`
