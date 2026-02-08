## 12. CHAT SERVER

### 12.1 Mengapa menggunakan chat server?

Obrolan, atau chat merupakan layanan yang populer. Digunakan oleh banyak orang. Contoh layanan ini adalah WhatsApp, Telegram, Signal, dan lain-lain. Chat server adalah aplikasi yang melayani permintaan pengiriman dan penerimaan pesan chat. Chat server juga dapat melayani permintaan dinamis dari aplikasi. Contoh nyata, Facebook, Instagram, Threads dan sejumlah e-commerce memiliki fitur chat.

### 12.2 End-to-end Encryption

Salah satu fitur yang menarik dari chat server adalah end-to-end encryption. End-to-end encryption adalah fitur yang memungkinkan pengguna untuk mengirim pesan dengan aman. Hanya pengirim dan penerima yang dapat membuka pesan. Tidak ada pihak lain, termasuk admin server yang dapat membuka pesan. Fitur ini sangat penting untuk menjaga privasi pengguna. Itu sebabnya, chat server dibangun dalam project ini harus mendukung fitur end-to-end encryption.

### 12.3 Prinsip Kerja Chat Server

Chat server bekerja chat server dengan end to end encryption dapat dijelaskan kurang lebih sebagai berikut:

1. Pertukaran Public Key. User A dan User B saling bertukar public key. Biasanya lewat server (aman)

2. Enkripsi di Sisi Pengirim. User A menulis pesan. Aplikasi chat Mengenkripsi pesan sebelum dikirim dan menggunakan public key milik User B

3. Server Meneruskan Pesan pesan yang diterima tanpa bisa membaca.

4. User B menerima pesan. Aplikasi chat membuka pesan menggunakan private key sehingga pesan kembali terbaca

### 12.4 Instalasi Chat Server Menggunakan XMPP + OMEMO

#### 12.4.1 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/chat` atau `D:\docker-project\chat` dengan struktur sebagai berikut:

```text
chat/
  +- certs/
  |   +- cloud-sekolah.com/
  |   |   +- fullchain.pem
  |   |   +- privkey.pem
  |   +- conference.cloud-sekolah.com/
  |   |   +- fullchain.pem
  |   |   +- privkey.pem
  |   +- proxy.cloud-sekolah.com/
  |   |   +- fullchain.pem
  |   |   +- privkey.pem
  |   +- upload.cloud-sekolah.com/
  |       +- fullchain.pem
  |       +- privkey.pem
  +- config/
  |   +- prosody.cfg.lua
  +- data/
  |   +- prosody/
  +- logs/
  +- docker-compose.yml
```

#### 12.4.2 Membuat Sertifikat SSL

Penamaan sertifikat pada ilustrasi di atas merujuk pada dokumentasi resmi dari [sara-smiseth/prosody](https://github.com/SaraSmiseth/prosody#ssl-certificate). Berikut ini langkah-langkah untuk membuat sertifikat dengan mkcert untuk subdomain conference.cloud-sekolah.com. Untuk subdomain lainnya, ikuti langkah yang sama.

Pastikan mkcert sudah terinstall dan local CA juga terinstall di dalam `system trust store`. Jika belum, bisa jalankan perintah berikut 

```bash
mkcert -install
```

untuk install local CA ke dalam system trust store.


```bash
# Masuk ke folder certs
cd certs/
# Membuat folder untuk subdomain conference.cloud-sekolah.com
mkdir conference.cloud-sekolah.com
# Masuk ke folder tersebut
cd conference.cloud-sekolah.com
# Membuat sertifikat
mkcert conference.cloud-sekolah.com
# Rename file sertifikat
mv conference.cloud-sekolah.com.pem fullchain.pem
# Rename file key
mv conference.cloud-sekolah.com-key.pem privkey.pem
# kembali ke folder project
cd ../../
```

ulangi langkah-langkah di atas untuk subdomain lainnya.

#### 12.4.3 Mengatur Hak Akses dan Ownership

Dari dokumentasi resmi, user yang menjalankan prosody di dalam container bernama `prosody` dengan user id 999 dan group id 999. Oleh karena itu, folder dan file yang dibuat harus memiliki hak akses dan ownership yang sesuai. Folder certs, data, dan logs harus menjadi milik user dan group dengan id 999. Jalankan perintah berikut dari root folder project `~/docker-project/chat/` untuk mengatur hak akses dan ownership.

```bash
# Mengatur hak akses dan ownership
sudo chown -R 999:999 certs/ data/ logs/
```

#### 12.4.4 Membuat docker-compose.yml

```yaml
services:
  prosody:
    image: sarasmiseth/prosody:latest
    container_name: prosody-chat-server
    hostname: chat.cloud-sekolah.com
    restart: unless-stopped
    ports:
      - "5222:5222"
      - "5269:5269"
      - "5280:5280"
      - "5281:5281"
    volumes:
      - ./config:/usr/local/etc/prosody/
      - ./data:/usr/local/var/lib/prosody
      - ./certs:/usr/local/etc/prosody/certs
      - ./logs:/usr/local/var/log/prosody
    environment:
      - TZ=Asia/Jakarta
      - PROSODY_ADMINS=admin@chat.cloud-sekolah.com
      - DOMAIN=chat.cloud-sekolah.com
      - DB_DATABASE=prosody
      - DB_USER=prosody
      - DB_PASSWORD=prosody
    networks:
      - lab-network

networks:
  lab-network:
    external: true
```
#### 12.4.5 Membuat prosody.cfg.lua

Buat file `prosody.cfg.lua` di folder `config/` dengan isi sebagai berikut:

```lua
-- Server settings
admins = { "admin@cloud-sekolah.com" }
use_libevent = true

-- Domain settings
VirtualHost "cloud-sekolah.com"
    ssl = {
        key = "/etc/prosody/certs/cloud-sekolah.com/privkey.pem";
        certificate = "/etc/prosody/certs/cloud-sekolah.com/fullchain.pem";
    }
    
    -- Disable in-band registration (admin only)
    allow_registration = false
    
    -- Authentication
    authentication = "internal_hashed"
    
    -- Storage
    storage = "internal"
    
    -- Archive settings for 500 users
    archive_expires_after = "1w"
    default_archive_policy = false
    
    -- Resource constraints for 500 users
    c2s_ping_timeout = 300
    c2s_ping_interval = 120

-- Component for MUC (Multi-User Chat)
Component "conference.cloud-sekolah.com" "muc"
    name = "Chat Server Cloud Sekolah"
    restrict_room_creation = "local"
    max_history_messages = 50

-- Component for proxy (file transfer, etc)
Component "proxy.cloud-sekolah.com" "proxy65"
    proxy65_address = "chat.cloud-sekolah.com"

-- Component for PubSub
Component "pubsub.cloud-sekolah.com" "pubsub"

-- Logging
log = {
    { levels = { "error" }, to = "console" };
    { levels = { "warn" }, to = "console" };
    { levels = { "info" }, to = "console" };
    { levels = { "debug" }, to = "file", filename = "/var/log/prosody/prosody.log" };
}

-- Performance tuning for 500 users
c2s_require_encryption = false
s2s_require_encryption = true

-- Limits
limits = {
    c2s = {
        max_items = 5000;
        max_stanzas_per_second = 100;
    };
}

-- Modules
modules_enabled = {
    -- Core
    "roster";
    "saslauth";
    "tls";
    "dialback";
    "disco";
    "carbons";
    "private";
    "blocklist";
    "vcard";
    "version";
    "uptime";
    "time";
    "ping";
    "pep";
    "register";
    
    -- MUC
    "muc";
    
    -- File transfer
    "http_files";
    
    -- HTTP
    "bosh";
    "websocket";
    "http";
    
    -- Security
    "limits";
    "watchregistrations";
    "smacks";
    
    -- Privacy
    "privacy";
    "default_privacy";
}

modules_disabled = {
    "register_web";  -- Disable web registration
}

-- BOSH and WebSocket
cross_domain_bosh = true
consider_bosh_secure = true
cross_domain_websocket = true
consider_websocket_secure = true

-- HTTP ports
http_ports = { 5280 }
https_ports = { 5281 }

-- Stats
statistics = "internal"

```

#### 12.4.6 Jalankan Chat Server

Jalankan perintah

```bash
docker compose up -d
```

dari folder `chat/` untuk menjalankan Chat Server. Untuk memeriksa log, jalankan perintah

```bash
docker compose logs -f
```

dari folder `chat/`.

### 15.5 Membuat User

Secara default, prosody memiliki environment variable `ALLOW_REGISTRATION` dengan nilai `True` yang berarti user bisa melakukan registrasi melalui client. Meski begitu, disarankan untuk membuat user melalui container langsung. Berikut langkah-langkah membuat user admin@cloud-sekolah.com seperti tertera di environment variable `PROSODY_ADMINS` di docker-compose.yml.

1. Masuk ke dalam container dengan perintah

```bash
docker exec -it prosody-chat-server /bin/bash
```

2. Buat user dengan perintah

```bash
prosodyctl adduser admin@cloud-sekolah.com
```

3. Masukkan password untuk user tersebut.
4. Ulangi langkah 2 dan 3 untuk membuat user lainnya.
5. Keluar dari container dengan perintah `exit`

### 12.6 Setup Client Chat

Berikut ini beberapa contoh aplikasi klien chat untuk XMPP yang sudah built in fitur end-to-end encryption OMEMO.

- Desktop Linux: Dino
- Desktop Windows: Psi+
- Android: ATalk, Briar

Poin-poin yang perlu diisikan pada waktu mengkonfigurasikan klien adalah sebagai berikut:

- Domain: chat.cloud-sekolah.com
- Username: user@cloud-sekolah.com
- Password: password
- Proxy: proxy.cloud-sekolah.com:5269

Untuk bisa melakukan komunikasi, selain user sudah terdaftar di server, pengguna juga perlu mengundang (invite) user lainnya baik untuk chat pribadi maupun masuk ke dalam group chat.

### 12.7 Mengatasi masalah umum

1. Tidak bisa registrasi user. Solusi: Periksa bind mount folder `data` dan `config`. Pastikan mapping dan permission sudah sudah benar. Periksa environment variable `ALLOW_REGISTRATION` sudah benar. Periksa log container dengan perintah `docker compose logs -f` dari folder project.
2. Tidak bisa login ke chat server. Solusi: Periksa kembali username dan password. Periksa apakah user sudah terdaftar di server. Periksa log container dengan perintah `docker compose logs -f` dari folder project.
3. Tidak bisa mengirim pesan. Solusi: Periksa koneksi internet. Periksa apakah user sudah login. Periksa log container dengan perintah `docker compose logs -f` dari folder project. Periksa pula kompatibilitas klien. Beberapa klien harus menambahkan plugin omemo agar bisa mengirim pesan.


### 12.8 Registrasi ke NPM

Langkah terakhir adalah registrasi chat server ke NPM. Langkah-langkahnya dapat merujuk ke [04-nginx-proxy-manager.md](04-nginx-proxy-manager.md#751-proxy-host)Chatt

### 12.9 Alternatif Chat Server

Chat server prosody termasuk chat server yang open source dan gratis, juga sederhana. Butuh konfigurasi lebih lanjut agar bisa menyerupai WhatsApp. Jika dikehendaki chat server dengan fitur-fitur yang lebih lengkap, maka dapat mempertimbangkan chat server alternatif lainnya, yaitu:
1. Matrix + Element [Matrix](https://hub.docker.com/r/matrixdotorg/synapse)
2. Wire [wire.com](https://wire.com/en/deployment-options)



> Sumber rujukan: https://github.com/SaraSmiseth/prosody?tab=readme-ov-file
