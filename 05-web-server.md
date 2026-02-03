## 08. WEB SERVER

### 8.1 Mengapa menggunakan web server?

Web server adalah aplikasi yang melayani permintaan klien (biasanya web browser) dan mengirimkan file-file web (HTML, CSS, JavaScript, gambar, dll.) ke klien. Web server juga dapat melayani permintaan dinamis dari aplikasi web. Layanan ini termasuk yang banyak digunakan untuk banyak keperluan seperti

1. Web portal sekolah
2. Blog sekolah
3. E-learning (moodle)
4. Perpustakaan digital, dan lain-lain

### 8.2 Cara kerja web server

Web server bekerja dengan cara menerima request dari client, memproses request, dan mengirimkan response ke client. Proses kerja web server dapat dijelaskan sebagai berikut:

1. Client mengirim request ke web server
2. Web server menerima request
3. Web server memproses request
4. Web server mengirimkan response ke client
5. Client menerima response dari web server
6. Browser menampilkan response ke user

### 8.3 Contoh Web server

Selama ini dalam praktikum sekolah, kita menggunakan XAMPP untuk menjalankan web server. XAMPP sendiri sudah termasuk Apache HTTP Server dan PHP. Namun dalam lingkungan industri, banyak web server lain yang digunakan, antara lain

1. Apache HTTP Server
2. Nginx
3. Lighttpd, dan
4. Microsoft IIS

Semua web server di atas dibangun menggunakan bahasa c.

Ada pula aplikasi berbasis web yang tidak menggunakan web server di atas, melainkan menggunakan web server yang disediakan oleh aplikasi tersebut, antara lain

1. Caddy ( berbasis golang )
2. Flask, Django, FastAPI ( berbasis python )
3. Gunicorn ( berbasis python )
4. uWSGI ( berbasis python )
5. Jetty ( berbasis java )
6. express (berbasis node.js). Digunakan oleh framework Vue.js, React, dan Angular.

Masing-masing memiliki keunggulan dan kekurangannya sendiri. Dalam project ini, hanya Nginx dan Apache yang akan dibahas.

### 8.4 Ilustrasi Kasus Setup Web Server

Dalam project ini, akan dibuat layanan web sebagai Berikut:

1. Web untuk halaman statis web.cloud-sekolah.com, menggunakan nginx ( 1 instance )
2. Web untuk ujicoba load balancer, menggunakan nginx dan apache. ( 2 instance )

### 8.4.1 Persiapan Folder Project

1. Persiapkan sebuah folder di`/home/user/docker-project/web` atau `D:\docker-project\web` dengan struktur sebagai berikut:

```text
web/
  +- nginx1/
  |   +- html/
  |   |   +- index.html
  |   +- docker-compose.yml
  +- nginx2/
  |   +- html/
  |   |   +- index.html
  |   +- docker-compose.yml
  +- apache/
  |   +- public_html/
  |   |   +- index.html
  |   +- docker-compose.yml
```

2. Siapkan isi dari masing-masing file sebagai berikut:

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Hello World</title>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

Sesuaikan konten tag &lt;h1&gt; untuk membedakan instance nginx dan apache. Misal. nginx1, nginx2, dan apache.

### 8.5 Instalasi web server

instalasi dilakukan dengan menggunakan docker-compose. Berikut adalah contoh docker-compose.yml untuk masing-masing web server.

nginx1/docker-compose.yml

```yaml
name: nginx1
services:
  nginx:
    container_name: nginx1
    volumes:
      - ./html:/usr/share/nginx/html:rw
    ports:
      - "8084:80"
    networks:
      - lab-network
    image: nginx
networks:
  lab-network:
    external: true
```

nginx2/docker-compose.yml

```yaml
name: nginx2
services:
  nginx:
    container_name: nginx2
    volumes:
      - ./html:/usr/share/nginx/html:rw
    ports:
      - "8091:80"
    networks:
      - lab-network
    image: nginx
networks:
  lab-network:
    external: true
```

apache/docker-compose.yml

```yaml
name: apache
services:
  apache:
    container_name: apache
    volumes:
      - ./public_html:/var/www/html:rw
    ports:
      - "8092:80"
    networks:
      - lab-network
    image: httpd
networks:
  lab-network:
    external: true
```

Untuk menjalankan web server, jalankan perintah

```bash
docker compose up -d
```

dari masing-masing folder project.

### 8.6 Penyederhanaan setup project

Struktur folder dan docker compose di atas dapat disederhanakan cukup dengan struktur folder dan docker compose berikut:

### 8.6.1 Struktur Folder

```text
web/
├── docker-compose.yml
├── nginx1/
│   └── html/
│       └── index.html
├── nginx2/
│   └── html/
│       └── index.html
└── apache/
    └── public_html/
        └── index.html
```

### 8.6.2 Docker Compose

```yaml
version: "3.8"

services:
  nginx1:
    image: nginx:latest
    container_name: nginx1
    ports:
      - "8084:80"
    volumes:
      - ./nginx1/html:/usr/share/nginx/html
    networks:
      - lab-network

  nginx2:
    image: nginx:latest
    container_name: nginx2
    ports:
      - "8091:80"
    volumes:
      - ./nginx2/html:/usr/share/nginx/html
    networks:
      - lab-network

  apache:
    image: httpd:latest
    container_name: apache
    ports:
      - "8092:80"
    volumes:
      - ./apache/public_html:/usr/local/apache2/htdocs
    networks:
      - lab-network

networks:
  lab-network:
    external: true
```

### 8.6.3 Menjalankan Web server sekaligus

Dari folder `web/`, jalankan perintah

```bash
docker compose up -d
```

### 8.6 Web Server Nginx dengan File Konfigurasi

Web server nginx juga bisa menggunakan file konfigurasi sendiri. File konfigurasi nginx terletak di `/etc/nginx/nginx.conf` dan `/etc/nginx/conf.d/*.conf` di dalam container. Kita bisa membuat file konfigurasi sendiri dan menggunakannya dengan mengubah `Dockerfile` dan `docker-compose.yml`.

#### 8.6.1 Struktur Folder

```text
web/
├── docker-compose.yml
├── nginx/
│   ├── conf.d/
│   │   └── default.conf
│   ├── html/
│   │   └── index.html
│   └── Dockerfile

```

#### 8.6.2 Dockerfile

```Dockerfile
FROM nginx:latest
COPY ./html /var/www/html
COPY ./conf.d/default.conf /etc/nginx/conf.d/default.conf
```

#### 8.6.3 default.conf

```nginx
server {
    listen 80;
    listen [::]:80;
    root /var/www/html;
    index index.html;
    server_name _;
}
```

#### 8.6.4 docker-compose.yml

```yaml
version: "3.8"

services:
  nginx:
    container_name: nginx3
    build: ./nginx
    ports:
      - "8084:80"
    networks:
      - lab-network

networks:
  lab-network:
    external: true
```

ports dan container_name bisa diubah sesuai kebutuhan.

#### 8.6.5 Menjalankan Web server dengan file konfigurasi sendiri

Dari folder `web/`, jalankan perintah

```bash
docker compose up -d
```

### 8.7 Nginx dengan PHP-FPM

Framework modern seperti laravel dan codeigniter menggunakan PHP sebagai backend-nya. PHP sendiri tidak direkomendasikan untuk dijalankan sebagai web server karena tidak stabil. Sebaiknya PHP dijalankan dengan menggunakan PHP-FPM (FastCGI Process Manager). PHP-FPM akan menjalankan PHP sebagai service di background dan menerima request dari web server. Web server akan berfungsi sebagai reverse proxy ke PHP-FPM.

#### 8.7.1 Struktur Folder

```text
webcgi/
├── docker-compose.yml
├── nginx/
│   ├── conf.d/
│   │   └── default.conf
│   ├── html/
│   │   └── index.html
│   └── Dockerfile
└── php/
    └── index.php
```

#### 8.7.2 Dockerfile

```Dockerfile
FROM php:8.1-fpm
COPY ./php /var/www/html
```

#### 8.7.3 default.conf

```nginx
server {
    listen 80;
    listen [::]:80;
    root /var/www/html;
    index index.html index.htm index.php;
    server_name _;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass php:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

#### 8.7.4 docker-compose.yml

```yaml
version: "3.8"

services:
  nginx:
    container_name: nginx4
    build: ./nginx
    ports:
      - "8084:80"
    networks:
      - lab-network

  php:
    container_name: php
    build: ./php
    networks:
      - lab-network

networks:
  lab-network:
    external: true
```

Sesuaikan ports supaya tidak tabrakan dengan port lain. Jalankan perintah `docker compose up -d` dari folder `webcgi/`.

### 8.8 Mengatasi masalah umum

Masalah umum yang sering terjadi ketika menggunakan web server adalah:

1. Web server tidak dapat diakses dari luar jaringan
2. Web server tidak dapat mengakses internet
3. Web server tidak dapat membuka website
4. Web server lambat membuka website

Berikut ini adalah solusi untuk masalah-masalah tersebut:

1. Web server tidak dapat diakses dari luar jaringan
   Solusi: Pastikan port web server sudah dimapping ke host dan port host sudah dibuka di firewall. Jika menggunakan router, pastikan port web server sudah dibuka di router.
2. Web server tidak dapat mengakses internet
   Solusi: Pastikan konfigurasi DNS server di host sudah benar. Pastikan port 53 sudah dimapping ke host dan port 53 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 53 sudah dibuka di router.
3. Web server tidak dapat atau lambat membuka website
   Solusi: Pastikan konfigurasi web server sudah benar. Pastikan file konfigurasi web server sudah benar. Pastikan file index.html atau index.php sudah benar.

### 8.9 Docker untuk laravel

Apabila dibutuhkan container untuk development web menggunakan framework laravel, file docker-compose.yml sebagai template sudah tersedia di [docs.docker.com](https://docs.docker.com/guides/frameworks/laravel/development-setup/)
