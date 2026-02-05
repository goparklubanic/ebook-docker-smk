## 5. Membangun Infrastruktur Server Menggunakan Docker

### 5.1 Project Overview

Dalam project ini, docker akan digunakan untuk membangun infrastruktur server. Infrastruktur server yang akan dibangun adalah sebagai berikut:

- DNS server dan penangkal iklan menggunakan Pi-hole
- Reverse proxy dan load balancer menggunakan Nginx Proxy Manager
- Web server menggunakan Nginx dan Apache
- Database server menggunakan MariaDB
- Database server menggunakan PostgreSQL
- Mail server menggunakan mailserver/docker-mailserver
- File server menggunakan RustFS
- Chat server menggunakan XMPP + OMEMO
- Web portal menggunakan WordPress
- Monitoring server menggunakan Netdata

### 5.2 Port Mapping

Port mapping adalah mekanisme untuk menghubungkan port pada host (komputer/server fisik) dengan port pada container Docker. Hal ini diperlukan karena:

1. **Isolasi Container**: Setiap container Docker berjalan dalam lingkungan yang terisolasi dengan network stack sendiri. Tanpa port mapping, layanan di dalam container tidak dapat diakses dari luar container.

2. **Akses dari Host/Jaringan**: Port mapping memungkinkan kita mengakses layanan yang berjalan di dalam container melalui IP address host. Misalnya, web server di container port 80 dapat diakses melalui `http://localhost:8080` jika dimapping ke port 8080 host.

3. **Menghindari Konflik Port**: Beberapa container mungkin menggunakan port yang sama (misalnya port 80 untuk web server). Dengan port mapping, kita dapat menjalankan multiple container dengan port internal yang sama, namun dimapping ke port host yang berbeda.

4. **Fleksibilitas Deployment**: Port mapping memberikan fleksibilitas dalam mengatur akses ke layanan tanpa harus mengubah konfigurasi aplikasi di dalam container.

Format port mapping: `HOST_PORT:CONTAINER_PORT`
Contoh: `-p 8080:80` artinya port 80 di container dapat diakses melalui port 8080 di host.

Berikut adalah daftar port mapping yang akan digunakan dalam project ini:

| Service                      | Host Port              | Container Port         | Keterangan                                     |
| ---------------------------- | ---------------------- | ---------------------- | ---------------------------------------------- |
| Pi-hole                      | 53, 8081               | 53, 80                 | DNS server dan web interface                   |
| Nginx Proxy Manager          | 80, 81, 443            | 80, 81, 443            | Web interface dan proxy                        |
| Nginx Web Server             | 8082                   | 80                     | Web server                                     |
| MariaDB                      | 3306                   | 3306                   | Database server                                |
| PostgreSQL                   | 5432                   | 5432                   | Database server                                |
| mailserver/docker-mailserver | 25, 143, 465, 587, 993 | 25, 143, 465, 587, 993 | SMTP, IMAP, POP3, SMTPS, IMAPS                 |
| RustFS                       | 9000,9001              | 9000,9001              | File server                                    |
| XMPP + OMEMO                 | 5222, 5269, 5443       | 5222, 5269, 5443       | XMPP client-to-server, server-to-server, HTTPS |
| WordPress                    | 8084                   | 80                     | Web server                                     |
| Netdata                      | 19999                  | 19999                  | Monitoring server                              |

## 5.3 Domain Mapping

Domain mapping adalah mekanisme untuk menghubungkan domain dengan container Docker. Hal ini diperlukan karena:

1. **Akses melalui nama domain**: Dengan domain mapping, kita dapat mengakses layanan melalui nama domain yang lebih mudah diingat dan diingat oleh manusia.
2. **Menghindari konflik port**: Dengan domain mapping, kita dapat menghindari konflik port karena akses ke layanan dilakukan melalui nama domain, bukan port.
3. **Fleksibilitas deployment**: Dengan domain mapping, kita dapat dengan mudah mengubah port mapping tanpa harus mengubah konfigurasi aplikasi di dalam container.

Berikut adalah daftar domain mapping yang akan digunakan dalam project ini:

| Service                      | Domain                    | Keterangan                   |
| ---------------------------- | ------------------------- | ---------------------------- |
| Pi-hole                      | pihole.cloud-sekolah.com  | DNS server dan web interface |
| Nginx Proxy Manager          | npm.cloud-sekolah.com     | Web interface dan proxy      |
| Nginx Web Server             | web.cloud-sekolah.com     | Web server                   |
| MariaDB                      | db.cloud-sekolah.com      | Database server              |
| PostgreSQL                   | pg.cloud-sekolah.com      | Database server              |
| mailserver/docker-mailserver | mail.cloud-sekolah.com    | Mail server                  |
| RustFS                       | rustfs.cloud-sekolah.com  | File server                  |
| XMPP                         | chat.cloud-sekolah.com    | Chat server                  |
| WordPress                    | blog.cloud-sekolah.com    | Web server                   |
| Netdata                      | monitor.cloud-sekolah.com | Monitoring server            |

## 5.4 Docker Network

Docker network adalah jaringan virtual yang digunakan untuk menghubungkan container Docker. Hal ini diperlukan karena:

1. **Isolasi container**: Setiap container berjalan dalam lingkungan yang terisolasi. Docker network memungkinkan kita menghubungkan container-container yang perlu berkomunikasi.
2. **Menghindari konflik IP**: Dengan Docker network, kita dapat menghindari konflik IP address karena setiap container memiliki IP address virtual yang unik dalam network-nya sendiri.
3. **Fleksibilitas deployment**: Dengan Docker network, kita dapat dengan mudah mengubah konfigurasi jaringan tanpa harus mengubah konfigurasi aplikasi di dalam container.

Diasumsikan subnet jaringan lokal untuk project ini adalah 192.168.1.0/24. IP host yang menjalankan container Docker adalah 192.168.1.100. Docker network akan dibuat dengan subnet 172.20.0.0/16, dengan nama `lab-network`. Setiap service akan diberikan IP address statis dalam range 172.20.0.0/16. Berikut adalah daftar IP address yang akan digunakan:

| Service                      | IP Address  | Keterangan                   |
| ---------------------------- | ----------- | ---------------------------- |
| Pi-hole                      | 172.20.0.2  | DNS server dan web interface |
| Nginx Proxy Manager          | 172.20.0.3  | Web interface dan proxy      |
| Nginx Web Server             | 172.20.0.4  | Web server                   |
| MariaDB                      | 172.20.0.5  | Database server              |
| PostgreSQL                   | 172.20.0.6  | Database server              |
| mailserver/docker-mailserver | 172.20.0.7  | Mail server                  |
| RustFS                       | 172.20.0.8  | File server                  |
| XMPP                         | 172.20.0.9  | Chat server                  |
| WordPress                    | 172.20.0.10 | Web server                   |
| Netdata                      | 172.20.0.11 | Monitoring server            |

## 5.5 Docker Volume

Docker volume adalah mekanisme untuk menyimpan data di luar container. Hal ini diperlukan karena:

1. **Data persistensi**: Data dalam container tidak akan bertahan jika container dihapus. Docker volume memungkinkan kita untuk menyimpan data di luar container, sehingga data akan bertahan walaupun container dihapus.
2. **Mudah dibackup**: Data dalam volume mudah dibackup karena berada di luar container.
3. **Mudah dikonfigurasi**: Konfigurasi volume dapat dilakukan di luar container, sehingga tidak perlu mengubah konfigurasi aplikasi di dalam container.

## 5.6 Setup Network

Berikut ini adalah langkah-langkah untuk membuat network untuk project ini:

```bash
docker network create lab-network
```

## 5.7 Struktur Folder Project

Project ini akan dikelola di folder `/home/user/docker-project` pada sistem operasi linux, atau di `D:\docker-project` pada sistem operasi windows. Di dalamnya akan dibuat sub-folder untuk nama project yang berisi docker-compose.yml dan folder untuk volume. Pengertian volume dalam docker adalah folder di host yang akan di-mount ke dalam container untuk menyimpan data-data statis seperti database, konfigurasi, dan lain-lain. Berikut adalah struktur folder yang direkomendasikan:

```text
docker-project/
  +- pihole/
  +- npm/
  +- web/
  +- mariadb/
  +- postgres/
  +- mail/
  +- rustfs/
  +- chat/
  +- wordpress/
  +- netdata/
```

Berikut perintah linux untuk membuat folder:

```bash
mkdir -p /home/user/docker-project/{pihole,npm,web,mariadb,postgres,mail,rustfs,chat,wordpress,netdata}
```

Berikut perintah windows untuk membuat folder. Pastikan terminal yang digunakan adalah `Git Bash` atau `WSL` dan sudah diarahkan ke drive `D:\`:

```bash
mkdir .\docker-project\{pihole,npm,web,mariadb,postgres,mail,rustfs,chat,wordpress,netdata}
```
