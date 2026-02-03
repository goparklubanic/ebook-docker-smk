# Buku Panduan Pemanfaatan Docker

## Untuk Siswa SMK Jurusan Teknik Komputer dan Jaringan Telekomunikasi

---

## Pendahuluan

Perkembangan teknologi jaringan dan server saat ini menuntut efisiensi, skalabilitas, dan kecepatan dalam deployment layanan. Selama ini, praktikum server di SMK umumnya menggunakan **VirtualBox** dengan mesin virtual penuh. Pendekatan tersebut memang baik untuk memahami konsep sistem operasi, namun kurang relevan dengan kebutuhan industri modern.

**Docker** hadir sebagai solusi berbasis _container_ yang lebih ringan, cepat, dan banyak digunakan di dunia kerja. Buku panduan ini bertujuan menanamkan **mindset baru** kepada siswa bahwa membangun server tidak selalu harus menggunakan mesin virtual penuh, tetapi dapat dilakukan dengan container.

---

## 1. Overview tentang Docker

Docker adalah platform untuk menjalankan aplikasi di dalam _container_. Container berisi aplikasi beserta seluruh dependensinya, sehingga aplikasi dapat berjalan konsisten di berbagai lingkungan.

### Konsep Dasar Docker

- **Image**: Template untuk membuat container
- **Container**: Instance berjalan dari image
- **Dockerfile**: File konfigurasi untuk membangun image
- **Docker Compose**: Alat untuk menjalankan banyak container sekaligus
- **Docker Network**: Jaringan virtual antar container
- **Volume**: Media penyimpanan data persisten

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
