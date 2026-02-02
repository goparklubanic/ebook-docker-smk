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

## 3. Instalasi Docker

### 3.1 Instalasi Docker di Linux (Ubuntu)

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

Logout dan login kembali.

### 3.2 Instalasi Docker Desktop di Windows

1. Download Docker Desktop
2. Aktifkan WSL2
3. Install dan jalankan Docker Desktop
4. Pastikan status Docker: **Running**

---

## 4. Perintah Dasar Docker

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

## 5. Membangun Infrastruktur Server Menggunakan Docker

### 5.1 Membuat Jaringan Virtual Docker

```bash
docker network create lab-network
```

---

## 6. Server DNS dan Nginx Proxy Manager

### 6.1 Setup Pi-hole (DNS Server & Ad Blocker)

```yaml
version: "3"
services:
  pihole:
    image: pihole/pihole
    container_name: pihole
    networks:
      - lab-network
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "8081:80"
    environment:
      TZ: Asia/Jakarta
    volumes:
      - ./pihole:/etc/pihole

networks:
  lab-network:
    external: true
```

#### Mengaktifkan Container Pi-hole

Setelah file `docker-compose.yml` dibuat, jalankan perintah berikut:

```bash
docker compose up -d
```

Pastikan container berjalan dengan perintah:

```bash
docker ps
```

Akses dashboard Pi-hole melalui browser:

```
http://IP-SERVER:8081/admin
```

#### Setup Domain di Pi-hole

- Masuk ke menu **Local DNS → DNS Records**
- Tambahkan domain lokal dan arahkan ke IP server/container tujuan

#### Manajemen Hostname

- Menu _Local DNS > DNS Records_

#### Manajemen Blokir Iklan

- Aktifkan adlist
- Tambahkan custom blacklist

---

### 6.2 Setup Nginx Proxy Manager (NPM)

```yaml
services:
  npm:
    image: jc21/nginx-proxy-manager
    ports:
      - "80:80"
      - "81:81"
      - "443:443"
    volumes:
      - ./npm/data:/data
      - ./npm/letsencrypt:/etc/letsencrypt
    networks:
      - lab-network
```

#### Management Host di NPM

- Proxy Host
- Forward ke container tujuan

#### SSL dengan Cloudflare DNS Challenge

- Gunakan API Token Cloudflare

---

## 7. Server Web

### 7.1 Web Server Apache

```yaml
apache:
  image: httpd
  volumes:
    - ./web:/usr/local/apache2/htdocs
```

### 7.2 Web Server Nginx

```yaml
nginx:
  image: nginx
  volumes:
    - ./web:/usr/share/nginx/html
```

---

## 8. Server Database

### 8.1 MariaDB

```yaml
mariadb:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: root
```

### 8.2 PostgreSQL

```yaml
postgres:
  image: postgres
  environment:
    POSTGRES_PASSWORD: postgres
```

---

## 9. Project WordPress

### Opsi 1: Menggunakan Container Terpisah

- Web server container
- Database container

### Opsi 2: Membuat Container Baru

```yaml
wordpress:
  image: wordpress
  environment:
    WORDPRESS_DB_HOST: mariadb
```

---

## 10. Server Email (Internal)

### 10.1 Modifikasi Pi-hole untuk MX Record

- Tambahkan MX record ke mail server

### 10.2 Setup Mail Server

Gunakan `mailserver/docker-mailserver`

### 10.3 Setup Webmail

- Gunakan Roundcube container

### 10.4 Setup Mail Client

- Thunderbird
- Konfigurasi IMAP & SMTP

---

## 11. Server File Management

### Setup RustFS

```yaml
rustfs:
  image: rustfs/rustfs
```

---

## 12. Server Messaging

### 12.1 Prosody (Tanpa E2EE)

- Management user
- Setup client (Gajim)

### 12.2 XMPP + OMEMO (E2EE)

- Aktifkan OMEMO
- Management user
- Setup client

---

## Penutup

Docker membekali siswa dengan keterampilan yang relevan dengan dunia industri. Dengan memahami Docker, siswa tidak hanya mampu membangun server, tetapi juga siap menghadapi ekosistem DevOps, cloud, dan microservices.

**Belajar Docker hari ini, siap kerja esok hari.**
