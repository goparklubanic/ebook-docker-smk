# Buku Panduan Pemanfaatan Docker

## Untuk Siswa SMK Jurusan Teknik Komputer dan Jaringan Telekomunikasi

---

## Pendahuluan

Perkembangan teknologi jaringan dan server saat ini menuntut efisiensi, skalabilitas, dan kecepatan dalam deployment layanan. Selama ini, praktikum server di SMK umumnya menggunakan **VirtualBox** dengan mesin virtual penuh. Pendekatan tersebut memang baik untuk memahami konsep sistem operasi, namun kurang relevan dengan kebutuhan industri modern.

**Docker** hadir sebagai solusi berbasis _container_ yang lebih ringan, cepat, dan banyak digunakan di dunia kerja. Buku panduan ini bertujuan menanamkan **mindset baru** kepada siswa bahwa membangun server tidak selalu harus menggunakan mesin virtual penuh, tetapi dapat dilakukan dengan container.

---

## 1. Overview tentang Docker

Docker adalah platform terbuka untuk mengembangkan, mendistribusikan, dan menjalankan aplikasi. Docker memungkinkan Anda untuk memisahkan aplikasi Anda dari infrastruktur Anda sehingga Anda dapat mengirimkan perangkat lunak dengan cepat. Dengan Docker, Anda dapat mengelola infrastruktur Anda dengan cara yang sama seperti Anda mengelola aplikasi Anda. Dengan memanfaatkan keunggulan metodologi docker dalam mendistribusikan, menguji dan menerapkan kode, Anda dapat mengurangi jeda waktu antara penulisan kode dan menjalankannya di lingkungan produksi secara signifikan

### Platform Docker

Docker menyediakan kemampuan untuk mengemas dan menjalankan aplikasi dalam lingkungan yang terisolasi secara longgar dalamlingkungan yang disebut kontainer. Isolasi dan keamanannya memungkinkan Anda menjalankan banyak hal
kontainer secara bersamaan pada host yang sama. Kontainer merupkan objek yang ringan dan berisi semua yang dibutuhkan untuk menjalankan aplikasi, jadi Anda tidak perlu bergantung pada apa yang sudah terinstal di host. Anda dapat berbagi kontainer saat bekerja, dan pastikan bahwa setiap orang yang Anda ajak berbagi mendapatkan kontainer yang sama fungsinya.

Docker menyediakan perangkat dan platform untuk mengelola siklus hidup kontainer Anda:

- Kembangkan aplikasi Anda dan komponen pendukungnya menggunakan kontainer.
- Kontainer tersebut menjadi unit untuk mendistribusikan dan menguji aplikasi Anda.
- Saat semua siap, sebarkan aplikasi Anda ke lingkungan produksi Anda,
  sebagai kontainer atau layanan terorkestrasi. Ini bekerja dengan cara yang sama terlepas dari apakah Anda
  Lingkungan produksi dapat berupa pusat data lokal, penyedia cloud, atau hibrida.
  dari keduanya.

### Docker Bisa Apa?

- Pengiriman aplikasi Anda yang cepat dan konsisten
- Penerapan dan penskalaan responsif
- Menjalankan lebih banyak beban kerja pada perangkat keras yang sama
- Mengurangi biaya operasional

### Arsitektur Docker

Docker menggunakan arsitektur klien-server. Arsitektur Docker terdiri dari 3 komponen utama, yaitu Docker Daemon, Docker Client,dan Docker Registry.

#### Docker Daemon
Docker daemon (`dockerd`) mendengarkan permintaan API (Appliction Programming Interface) dan mengelola objek-objek docker seperti; _image_, _container_, _network_, dan _volume_. Docker daemon berjalan di host dan dapat berkomunikasi dengan daemon lain untuk mengelola docker. Daemon dokcer bisa diinstall sebagai doker desktop.

#### Docker Client

Docker client (`docker`) adalah cara utama para pengguna dalam berinteraksi dengan Docker. Saat Anda menggunakan perintah seperti `docker run`, klien mengirimkan ini perintah ke `dockerd`, yang menjalankannya. Perintah `docker` menggunakan API Docker. Klien Docker dapat berkomunikasi dengan lebih dari satu daemon.

#### Docker Registry

Docker registry menyimpan image Docker. Docker Hub adalah platform registri publik yang dapat digunakan siapa saja, dan Docker secara default akan mencari image di Docker Hub. Anda bahkan dapat menjalankan registry pribadi Anda sendiri.

Saat Anda menggunakan perintah `docker pull` atau `docker run`, Docker akan menarik image yang dibutuhkan dari registry yang telah Anda konfigurasi. Saat Anda menggunakan perintah `docker push`, Docker akan mengunggah image tersebut ke registri yang telah Anda konfigurasi.

### Objek Docker

- **Image**:
  Sebuah image adalah template hanya bisa dibaca yang berisi petunjuk untuk membuat kontainer Docker. Sebuah image bisa dibuat dari image lain, dengan beberapa kustomisasi
  
  Anda bisa membuat image sendiri atau hanya menggunakan imabe buatan orang lain dan dipublikasikan di dalam sebuah registry. Untuk membuat image Anda sendiri, Anda membuat `Dockerfile` dengan sintaks sederhana untuk mendefinisikan langkah-langkah yang diperlukan untuk membuat dan menjalankan image tersebut. Setiap instruksi dalam Dockerfile menciptakan sebuah layer dalam image. Ketika Anda ubah Dockerfile dan membangun ulang image, hanya layer-layer image yang memiliki perubahan akan dibangun kembali. Inilah sebagian dari alasan mengapa image menjadi sangat ringan dan kecil dan cepat, jika dibandingkan dengan teknologi virtualisasi lainnya.

- **Container**: 
  Kontainer adalah instance image yang dapat dijalankan. Sebuah kontainer didefinisikan oleh image yang digunakan, serta diberi konfigurasi tambahan saat kontainer dibuat atau mulai dijalankan. Ketika container dihapus, semua perubahan yang tidak tersimpan secara persisten akan hilang.
  
  Secara default, sebuah kontainer relatif terisolasi dengan baik dari kontainer lain dan mesin host-nya. Anda dapat mengontrol seberapa terisolasi jaringan, penyimpanan, dan atau subsistem-subsistem yang mendasarinya berasal dari kontainer lain atau dari mesin host.

- **Dockerfile**: 
  File konfigurasi untuk membangun image. Dockerfile merupakan berkas teks berisi instruksi untuk membangun image Docker secara otomatis. Di dalamnya terdapat perintah seperti FROM, RUN, COPY, dan CMD. Dockerfile memastikan aplikasi dapat dikemas dengan konfigurasi yang konsisten dan dapat dijalankan di berbagai lingkungan.
  Untuk membangun image dari dockerfile, digunakan perinah `docker build`.

- **Docker Compose**: 
  Docker Compose adalah alat untuk menjalankan dan mengelola beberapa container sekaligus menggunakan satu file konfigurasi `docker-compose.yml`. Dengan Compose, layanan seperti aplikasi, database, dan cache dapat diatur dalam satu konfigurasi terstruktur dan dijalankan bersama menggunakan satu perintah.
  untuk menjalankan docker compose, digunakan perintah `docker compose up -d`.

- **Docker Network**: 
  Jaringan virtual antar container. Docker Network adalah fitur yang memungkinkan container saling terhubung dan berkomunikasi dalam jaringan virtual. Network dapat bersifat bridge, host, atau overlay. Dengan Docker Network, container dapat saling mengenali melalui nama service tanpa perlu konfigurasi IP manual.
  untuk membuat network, digunakan perintah `docker network create <nama_network>`.

- **Volume**: 
  Media penyimpanan data persisten. Docker Volume adalah mekanisme penyimpanan data persisten di Docker. Volume memungkinkan data tetap tersimpan meskipun container dihentikan atau dihapus. Biasanya digunakan untuk menyimpan database, file konfigurasi, atau data aplikasi agar tidak hilang saat container diperbarui.
  untuk membuat volume, digunakan perintah `docker volume create <nama_volume>`, atau melalui mekanisme volume mount binding dalam file `docker-compose.yml`.

### Keunggulan Docker

- Lebih ringan dari virtual machine
- Startup sangat cepat
- Mudah direplikasi
- Cocok untuk DevOps dan cloud

---

## 2. Perbandingan Docker dengan VirtualBox

| Aspek             | Docker            | VirtualBox |
| ----------------- | ----------------- | ---------- |
| Virtualisasi      | Container         | Full VM    |
| Konsumsi Resource | Rendah            | Tinggi     |
| Booting           | Detik             | Menit      |
| Skalabilitas      | Sangat baik       | Terbatas   |
| Kebutuhan OS      | Share kernel host | OS sendiri |

**Mindset baru**: Fokus pada layanan (service), bukan OS.

---

## 3. Docker dan Komputasi Awan ( Cloud Computing )

Komputasi awan adalah cara menjalankan server, aplikasi, dan layanan melalui internet, tanpa harus memiliki server fisik sendiri. Contoh layanan komputasi awan adalah Amazon Web Services (AWS), Google Cloud Platform (GCP), Microsoft Azure, dan DigitalOcean. User bisa menyewa server ( virtual machine ), menyimpan data, dan menjalankan aplikasi tanpa harus mengatur infrastruktur server secara manual.

Docker berperan dalam komputasi awan sebagai cara untuk mengemas aplikasi beserta seluruh dependensinya dalam container. Container ini dapat dijalankan di berbagai platform, termasuk di dalam virtual machine di komputasi awan. Dengan demikian, aplikasi yang dikembangkan dapat dengan mudah dijalankan di lingkungan lokal maupun di lingkungan komputasi awan.

Docker dan Cloud merupakan **standar industri** yang tidak dapat dihindari. Di dunia kerja, aplikasi hampir selalu dijalankan dalam container. Para penyedia cloud pun secara native sudah mendukung docker. Jadi tidak ada alasan untuk tidak menggunakan docker.

## 4. Instalasi Docker

### 4.1 Instalasi Docker di Linux (Ubuntu)

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

Logout dan login kembali.

### 4.2 Instalasi Docker Desktop di Windows

1. Download Docker Desktop
2. Aktifkan WSL2
3. Install dan jalankan Docker Desktop
4. Pastikan status Docker: **Running**

---

## 5. Perintah Dasar Docker

```bash
docker ps
docker ps -a
docker images
docker pull nginx
docker run -d -p 80:80 nginx
docker stop <container>
docker rm <container>
docker logs <container>
```

### Operasional Dasar Docker Desktop

- Start / Stop container
- Monitoring resource
- Melihat logs
- Manajemen volume dan network

---
