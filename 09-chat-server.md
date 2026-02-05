## 12. CHAT SERVER (Still Conceptual)

### 12.1 Mengapa menggunakan chat server?

Obrolan, atau chat merupakan layanan yang populer. Digunakan oleh banyak orang. Contoh layanan ini adalah WhatsApp, Telegram, Signal, dan lain-lain. Chat server adalah aplikasi yang melayani permintaan pengiriman dan penerimaan pesan chat. Chat server juga dapat melayani permintaan dinamis dari aplikasi. Contoh nyata, Facebook, Instagram, Threads dan sejumlah e-commerce memiliki fitur chat.

### 12.2 End-to-end Encryption

Salah satu fitur yang menarik dari chat server adalah end-to-end encryption. End-to-end encryption adalah fitur yang memungkinkan pengguna untuk mengirim pesan dengan aman. Hanya pengirim dan penerima yang dapat membuka pesan. Tidak ada pihak lain, termasuk admin server yang dapat membuka pesan. Fitur ini sangat penting untuk menjaga privasi pengguna. Itu sebabnya, chat server dibangun dalam project ini harus mendukung fitur end-to-end encryption.

a. Struktur folder

```text
chat/
  +- certs/
  +- data/
  +- docker-compose.yml
```

b. Working docker-compose.yml

```yaml
version: "3.7"

services:
  server:
    image: sarasmiseth/prosody:latest
    restart: unless-stopped
    ports:
      - "5000:5000"
      - "5222:5222"
      - "5223:5223"
      - "5269:5269"
      - "5281:5281"
    environment:
      DOMAIN: chat.cloud-sekolah.com
    volumes:
      - ./certs:/usr/local/etc/prosody/certs # volume untuk menyimpan sertifikat ssl, gunakan sertifikat dari mkcert
      - ./data:/usr/local/var/lib/prosody
```

c. Client

- Desktop: Gajim
- Mobile: Conversations
- Web: Briar Project

poin-poin registrasi:

- domain
- username
- password
- proxy

> https://github.com/SaraSmiseth/prosody?tab=readme-ov-file
