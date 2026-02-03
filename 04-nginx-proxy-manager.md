## 07. NGINX PROXY MANAGER

### 7.1 Mengapa menggunakan Nginx Proxy Manager?

Nginx Proxy Manager, selanjutnya disebut NPM, adalah aplikasi berbasis web untuk mengatur proxy dan load balancer. Nginx Proxy Manager dapat diinstall di server lokal dan dapat digunakan untuk seluruh jaringan lokal. Nginx Proxy Manager dapat bekerja dengan sangat baik dengan Docker.

### 7.2 Cara kerja Nginx Proxy Manager

Nginx Proxy Manager bekerja dengan cara meneruskan request dari client ke server tujuan. Nginx Proxy Manager dapat mengatur proxy untuk multiple server dengan multiple port. Nginx Proxy Manager juga dapat mengatur load balancer untuk multiple server.

### 7.3 Contoh penggunaan NPM

### 7.3.1 Contoh penggunaan NPM sebagai load balancer

Misalkan kita memiliki 2 web server, yaitu web1 dan web2. Kita ingin mengatur proxy untuk kedua web server tersebut. Kita ingin mengatur proxy dengan port 8082 untuk web1 dan port 8083 untuk web2. Kita ingin mengatur load balancer untuk kedua web server tersebut dengan port 8084. Dengan begitu, request ke `http://192.168.1.100:8084` akan di-load balance ke `http://192.168.1.100:8082` dan `http://192.168.1.100:8083`. Cara ini sangat berguna jika traffic ke web server terlalu tinggi dan kita ingin membaginya ke multiple server.

### 7.3.2 Contoh penggunaan NPM sebagai proxy

Misalkan server pi-hole yang sebelumnya disetup bisa diakses melalui `http://192.168.1.100:8081`. Walaupun server pi-hole sudah terdaftar di domain lokal `pihole.cloud-sekolah.com` dengan IP address `192.168.1.100`, kita masih harus mengetikkan port 8081 untuk mengakses web interface Pi-hole.Jadi server pi-hole harus diakses dengan alamat `http://pihole.cloud-sekolah.com:8081`. Dengan NPM, kita dapat mengatur proxy untuk server pi-hole dengan port 8081 dan mengatur domain `pihole.cloud-sekolah.com` untuk diakses tanpa port.

### 7.4 Instalasi Nginx Proxy Manager

Nginx Proxy Manager dapat diinstalasi menggunakan container Docker. Cara instalasi Nginx Proxy Manager dapat dilihat di https://nginxproxymanager.com/setup/. Berikut ini contoh docker-compose.yml untuk menginstall Nginx Proxy Manager:

```yaml
version: "3"
services:
  npm:
    image: "jc21/nginx-proxy-manager:latest"
    container_name: npm
    networks:
      - lab-network
    ports:
      - "80:80"
      - "81:81"
      - "443:443"
    volumes:
      - ./npm-data:/data
      - ./npm-letsencrypt:/etc/letsencrypt
networks:
  lab-network:
    external: true
```

simpan file di atas dengan nama `docker-compose.yml` di folder `~/docker-project/npm`, atau `D:\docker-project\npm` untuk windows. Jalankan perintah

```bash
docker compose up -d
```

untuk menjalankan Nginx Proxy Manager. Pastikan container berjalan dengan perintah

```bash
docker ps
```

atau dengan membuka web interface Nginx Proxy Manager di `http://192.168.1.100:81` dengan username `admin` dan password `changeme`. Ganti password dengan yang mudah diingat namun sulit ditebak.

### 7.5 Konfigurasi Nginx Proxy Manager

NPM akan dikonfigurasikan untuk keperluan project lab ini. Fitur-fitur NPM yang akan digunakan adalah sebagai berikut:

1. Proxy Host
2. Load Balancer
3. SSL Certificate

#### 7.5.1 Proxy Host

Untuk konfigurasi proxy host, ikuti langkah-langkah di bawah ini:

1. Buka web interface Nginx Proxy Manager di `http://192.168.1.100:81`
2. Login dengan username `admin` dan password yang ditentukan saat instalasi.
3. Klik menu `Proxy Hosts` di bagian kiri atas
4. Klik tombol `Add Proxy Host`
5. Isi form dengan konfigurasi sebagai berikut:
   - `Name`: pihole
   - `Domain(s)`: pihole.cloud-sekolah.com
   - `Scheme`: http
   - `Remote Hostname/IP`: 192.168.1.100
   - `Forward Port`: 8081
   - `Use SSL`: tidak dicentang
   - `Block Common Exploits`: tidak dicentang
   - `Advanced Settings`: tidak diubah
6. Klik tombol `Save` di bagian bawah kanan
   Langkah-langkah ini akan mengatur proxy untuk server pi-hole dengan port 8081 dan mengatur domain `pihole.cloud-sekolah.com` untuk diakses tanpa port. Maka untuk mengakses web interface Pi-hole, kita hanya perlu mengakses `http://pihole.cloud-sekolah.com` saja. Lakukan langkah di atas untuk setiap service yang akan diakses tanpa port. Berikut tabel rujukan konfigurasi untuk servis-servis lainnya

| Service             | Domain                    | Port  |
| ------------------- | ------------------------- | ----- |
| Pi-hole             | pihole.cloud-sekolah.com  | 8081  |
| Nginx Proxy Manager | npm.cloud-sekolah.com     | 81    |
| Nginx Web Server    | web.cloud-sekolah.com     | 8082  |
| MariaDB             | db.cloud-sekolah.com      | 3306  |
| PostgreSQL          | pg.cloud-sekolah.com      | 5432  |
| RustFS              | rustfs.cloud-sekolah.com  | 8083  |
| XMPP                | chat.cloud-sekolah.com    | 5443  |
| WordPress           | blog.cloud-sekolah.com    | 8084  |
| Netdata             | monitor.cloud-sekolah.com | 19999 |

_Mengapa layanan mail tidak ditambahkan dan hanya port 5443 pada layanan XMPP yang ditambahkan?_
NPM sebagai host proxy hanya digunakan untuk layanan yang akan diakses dengan web browser. Sementara itu port-port pada layanan email dan sebagain XMPP digunakan oleh protokol tertentu yang tidak diakses dengan web browser.
Layanan email akan diakses dengan aplikasi mail client, dan layanan chatting XMPP, kita akan mengaksesnya dengan web browser di port 5443.

#### 7.5.2 Load Balancer

Ilustrasi kasus. Akses ke http://web.cloud-sekolah.com akan di-load balance ke http://192.168.1.100:8091 dan http://192.168.1.100:8093.

Untuk konfigurasi load balancer, ikuti langkah-langkah di bawah ini:

1. Buka web interface Nginx Proxy Manager di `http://192.168.1.100:81`
2. Login dengan username `admin` dan password yang ditentukan saat instalasi.
3. Klik tombol `Add Proxy Host`
4. Isi form dengan konfigurasi sebagai berikut:
   - `Name`: web
   - `Domain(s)`: web.cloud-sekolah.com
   - `Scheme`: http
   - `Remote Hostname/IP`: _backendservers_
   - `Forward Port`: 8084
   - `Use SSL`: tidak dicentang
   - `Block Common Exploits`: tidak dicentang
5. Klik tombol `Advanced` di bagian atas kanan
6. Pada bagian **Custom Nginx Configuration** ditambahkan konfigurasi sebagai berikut:

   ```nginx
   # Mendefinisikan grup server backend
   upstream backendservers {
    server 192.168.1.100:8091;
    server 192.168.1.100:8092;
   }

   # Memberitahu Nginx untuk menggunakan upstream tersebut

    location / {
    proxy_pass http://backendservers;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
   }
   ```

7. Klik tombol `Save` di bagian bawah kanan

> nama backend-server merupakan nama alias yang akan didefinisikan kemudian.

### 7.6 Menguji hasil konfigurasi

Pastikan setting dns di client sudah menggunkan dns lokal di `192.168.1.100`.
Untuk menguji konfigurasi proxy host, Buka web browser dan akses `http://pihole.cloud-sekolah.com`. Konfigurasi berhasil bila anda dapat mengakses web interface Pi-hole.

> Disclaimer: Langkah-langkah di bawah ini diasumsikan anda sudah memiliki 2 buah web server yang berjalan di port 8082 dan 8083. Jika anda belum memiliki, silakan buat terlebih dahulu.

Untuk menguji konfigurasi load balancer, dibutuhkan dua buah web server tambahan. Dalam contoh kasus pada bab ini menggunakan web server nginx port 8082 dan 8083. Masing-masing memiliki halaman index yang berbeda. arahkan browser ke alamat utama (jika sudah dibuat) dan refresh browser. Anda akan melihat halaman index yang berbeda. Coba refresh kembali dan lihat hasilnya.

### 7.7 Troubleshooting

1. Nginx Proxy Manager tidak dapat diakses
   Solusi: Pastikan port 80 dan 81 sudah dimapping ke host dan port 80 dan 81 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 80 dan 81 sudah dibuka di router.
2. Nginx Proxy Manager tidak dapat mengakses internet
   Solusi: Pastikan DNS server upstream sudah dikonfigurasi dengan benar. Pastikan port 53 sudah dimapping ke host dan port 53 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 53 sudah dibuka di router.
