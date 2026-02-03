## 6. Setup Pi-hole (DNS Server & Ad Blocker)

### 6.1 Mengapa menggunakan Pi-hole?

Pi-hole adalah DNS server yang mampu memblokir iklan dan tracking. Pi-hole dapat diinstall di server lokal dan dapat digunakan untuk seluruh jaringan lokal. Pi-hole dapat bekerja dengan sangat baik dengan Docker.

### 6.2 Cara kerja Pi-hole

### 6.2.1 Sebagai DNS Server

Pi-hole pada dasarnya menggunakan paket `dnsmasq` untuk bekerja sebagai DNS server. Secara default, Pi-hole akan mengarahkan request DNS dari client ke DNS server upstream yang ditentukan. Biasanya, DNS server upstream adalah DNS server dari ISP (Internet Service Provider). Namun, kita juga dapat mengatur DNS server upstream kita sendiri.

### 6.2.2 Sebagai Ad Blocker

Pi-hole bekerja dengan cara mengintercept request DNS dari client. Pi-hole akan memeriksa daftar domain iklan yang telah ditentukan, dan jika domain tersebut terdaftar sebagai iklan, maka Pi-hole akan mengembalikan response DNS dengan alamat IP yang ditentukan untuk menghindari load balancer. Jika domain tersebut bukan iklan, maka Pi-hole akan mengembalikan response DNS dengan alamat IP asli dari domain tersebut.

### 6.3 Instalasi Pi-hole

Pi-hole dapat diinstalasi menggunakan container Docker. Cara instalasi Pi-hole dapat dilihat di https://pi-hole.net/docs/guides/docker/. Berikut ini contoh docker-compose.yml untuk menginstall Pi-hole:

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
      - ./pihole-data:/etc/pihole # volume untuk menyimpan data pihole

networks:
  lab-network:
    external: true
```

simpan file di atas dengan nama `docker-compose.yml` di folder `~/docker-project/pihole`. Jalankan perintah

```bash
docker compose up -d
```

untuk menjalankan Pi-hole. Pastikan container berjalan dengan perintah

```bash
docker ps
```

### 6.4 Konfigurasi Pi-hole

1. Akses web interface Pi-hole di `http://192.168.1.100:8081/admin`
2. Login dengan username `admin` dan password yang ditentukan saat instalasi. Jika lupa, cari di folder volume pihole di `~/docker-volume/pihole/pihole-setup.log`. Ganti password dengan yang mudah diingat namun sulit ditebak.
3. Konfigurasi DNS server upstream
   3.1 Pada menu `Settings`, pilih `DNS`
   3.2 Pada bagian `Upstream DNS Servers`, ganti dengan DNS server sesuai kebutuhan. Contoh: 1.1.1.1, 1.0.0.1, 8.8.8.8, 8.8.4.4

4. Konfigurasi adlist
   4.1 Pada menu `Settings`, pilih `Blocklists`
   4.2 Pada bagian `Blocklists`, tambahkan URL untuk daftar domain iklan. Contoh: https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
   4.3 Pada menu `Settings`, pilih `Query Log`
   4.4 Pada bagian `Query Log`, aktifkan `Log All Queries` dan `Log Adlists` untuk mengetahui domain-domain yang diblokir oleh Pi-hole.
5. Konfigurasi client untuk menggunakan Pi-hole sebagai DNS server
   5.1 Atur DNS server di router atau komputer client ke IP address Pi-hole. Contoh: 192.168.1.100
   5.2 Test dengan perintah `nslookup` di command prompt atau terminal. Contoh: `nslookup doubleclick.net 192.168.1.100`

### 6.4.1 Membuat domain lokal

Domain lokal adalah domain yang tidak terdaftar di DNS root. Domain lokal biasanya diakhiri dengan `.local`, `.lan`, atau `.home`. Dalam project ini, kita akan menggunakan domain lokal `cloud-sekolah.com`.

Langkah-langkah membuat domain lokal:

1. Pastikan Pi-hole sudah berjalan
2. Akses web interface Pi-hole di `http://192.168.1.100:8081/admin`
3. Login dengan username `admin` dan password yang ditentukan saat instalasi.
4. Pada menu `Settings`, pilih `DNS`
5. Pada bagian `Local DNS Records`, tambahkan domain lokal. Contoh: `cloud-sekolah.com` dengan IP address `192.168.1.100`.
6. Simpan perubahan
   Lakukan langkah 5 dan 6 untuk setiap host yang lain yaitu:

- npm.cloud-sekolah.com
- web.cloud-sekolah.com
- mail.cloud-sekolah.com
- rustfs.cloud-sekolah.com
- chat.cloud-sekolah.com
- blog.cloud-sekolah.com
- monitor.cloud-sekolah.com
  dengan ip address 192.168.1.100

### 6.5 Mengatasi masalah umum

Masalah umum yang sering terjadi ketika menggunakan Pi-hole adalah:

1. Pi-hole tidak dapat diakses dari luar jaringan
2. Pi-hole tidak dapat mengakses internet
3. Pi-hole tidak dapat membuka website
4. Pi-hole lambat membuka website

Berikut ini adalah solusi untuk masalah-masalah tersebut:

1. Pi-hole tidak dapat diakses dari luar jaringan
   Solusi: Pastikan port 8081 sudah dimapping ke host dan port 8081 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 8081 sudah dibuka di router.
2. Pi-hole tidak dapat mengakses internet
   Solusi: Pastikan DNS server upstream sudah dikonfigurasi dengan benar. Pastikan port 53 sudah dimapping ke host dan port 53 host sudah dibuka di firewall. Jika menggunakan router, pastikan port 53 sudah dibuka di router.
3. Pi-hole tidak dapat membuka website
   Solusi: Pastikan konfigurasi DNS server di client sudah benar. Pastikan Pi-hole sudah diatur sebagai DNS server di client.
   Jika sudah benar, coba flush DNS cache di client dengan perintah `ipconfig /flushdns` di command prompt atau terminal.
   Jika masih tidak dapat membuka website, coba cek di menu `Query Log` apakah domain yang dicari ada di list. Jika tidak ada, coba cek kembali konfigurasi DNS server upstream.
4. Pi-hole lambat membuka website
   Solusi: Pastikan konfigurasi DNS server upstream sudah benar. Jika menggunakan DNS server lokal, coba tambahkan konfigurasi DNS forwarder ke DNS server upstream.
