## 14. MONITORING TOOL NETDATA

### 14.1 Mengapa menggunakan Netdata?

Netdata adalah aplikasi yang membantu sistem administrator memantau kinerja komputer, server, atau aplikasi secara langsung—seperti speedometer pada kendaraan yang menunjukkan kecepatan secara real-time. Setiap detik, Netdata mencatat informasi penting seperti penggunaan CPU, memori, jaringan, dan aktivitas aplikasi.

### 14.2 Cara kerja Netdata

- Netdata dipasang langsung di setiap komputer atau server yang ingin dipantau (disebut Agent).
- Data tidak dikirim ke satu tempat pusat, tetapi tetap disimpan di komputer masing-masing. Meski begitu, kita tetap bisa melihat semua data dari satu layar terpusat—seperti mengawasi beberapa CCTV dari satu ruang kontrol.
- Karena datanya tidak perlu dikirim jauh-jauh, pemantauan jadi lebih cepat, hemat biaya, dan bisa menangani ribuan bahkan jutaan data per detik.

### 14.3 Fitur Netdata

- Langsung jadi pakai—tidak perlu setting rumit. Begitu diinstal, Netdata otomatis menampilkan dashboard grafik yang menarik.
- Bisa mendeteksi masalah sendiri menggunakan kecerdasan buatan (AI), misalnya memberi tahu jika server tiba-tiba lemot atau kehabisan memori.
- Cocok untuk siswa, teknisi IT, atau tim kecil yang ingin memantau sistem tanpa harus jadi ahli jaringan terlebih dahulu.

### 14.4 Komponen Utama Netdata

1. Netdata Agent → Software yang diinstal di tiap komputer/server untuk mengumpulkan data.
2. Netdata Parent → Komputer pusat (opsional) yang mengumpulkan data dari beberapa Agent sekaligus—berguna jika kita punya banyak server.
3. Netdata Cloud → Layanan online yang memungkinkan kita memantau semua server dari mana saja lewat browser, lengkap dengan notifikasi (misalnya lewat email atau WhatsApp) jika terjadi masalah.

> Analogi: Netdata seperti halnya jam tangan pintar ( smartwatch ) yang bisa mendeteksi kadar oksigen darah, jantung, dan suhu tubuh setiap saat.

### 14.5 Instalasi Netdata

#### 14.5.1 Persiapan folder project

1. Persiapkan sebuah folder `/home/user/docker-project/netdata` atau `D:\docker-project\netdata` dengan struktur sebagai berikut:

```text
netdata/
  +- netdataconfig/
  +- netdatalib/
  +- netdatacache/
  +- docker-compose.yml
```

#### 14.5.2 docker-compose.yml

Dalam project ini, akan digunakan contoh docker-compose dari website resmi Netdata. Berikut adalah contoh docker-compose.yml untuk menginstall Netdata Agent:

```yaml
version: "3"
services:
  netdata:
    image: netdata/netdata
    container_name: netdata
    pid: host
    network_mode: host
    restart: unless-stopped
    cap_add:
      - SYS_PTRACE
      - SYS_ADMIN
    security_opt:
      - apparmor:unconfined
    volumes:
      - netdataconfig:/etc/netdata
      - netdatalib:/var/lib/netdata
      - netdatacache:/var/cache/netdata
      - /:/host/root:ro,rslave
      - /etc/passwd:/host/etc/passwd:ro
      - /etc/group:/host/etc/group:ro
      - /etc/localtime:/etc/localtime:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /etc/os-release:/host/etc/os-release:ro
      - /var/log:/host/var/log:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /run/dbus:/run/dbus:ro

volumes:
  netdataconfig:
  netdatalib:
  netdatacache:
```

> https://learn.netdata.cloud/docs/netdata-agent/installation/docker

[cek qwen grovero](https://chat.qwen.ai/c/5b0ed098-75e1-4e9a-9c62-7d90f895a4e1)
