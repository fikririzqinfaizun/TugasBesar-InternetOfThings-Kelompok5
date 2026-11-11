<img width="224" height="225" alt="unsoed2" src="https://github.com/user-attachments/assets/38f78619-5246-496c-a2b5-c667f1d38bf1" />
<div align="center">

<img src="unsoed2.png" alt="Logo Universitas Jenderal Soedirman" width="160"/>

# LAPORAN & DOKUMENTASI PROYEK TUGAS BESAR IOT
## **Smart Fashion Inventory**
### **Konveyor Mini dengan Kontrol PID, Indikator Rak LED, dan Web Dashboard Laravel**

*"Produksi Terkontrol, Stok Terpantau, Bisnis Lebih Efisien"*

![ESP32](https://img.shields.io/badge/ESP32-DevKit%20V1-blue)
![Laravel](https://img.shields.io/badge/Laravel-10-red)
![MQTT](https://img.shields.io/badge/Protocol-MQTT-green)
![Control](https://img.shields.io/badge/Control-PID-orange)

---

**PROGRAM STUDI TEKNIK KOMPUTER**  
**FAKULTAS TEKNIK DAN SAINS**  
**UNIVERSITAS JENDERAL SOEDIRMAN**  
**2026**

---

</div>

## 👥 Identitas Kelompok & Pembagian Kerja

| No | Nama Anggota | NIM | Peran / Pembagian Kerja |
|---|---|---|---|
| 1 | **Finda Wulan Febrianti** | H1H024055 | **IoT & Embedded Engineer**<br>- Merancang rangkaian fisik ESP32, sensor IR, encoder HC-020K, driver motor L298N, dan LED rak.<br>- Mengimplementasikan algoritma kontroler PID pada ESP32.<br>- Menguji akurasi pencacahan sensor IR dan pengiriman telemetri. |
| 2 | **Reva Aura Ramadhani** | H1H024059 | **Frontend & UI/UX Developer**<br>- Merancang antarmuka dashboard web responsif.<br>- Menampilkan statistik real-time (Total Produksi, Stok, Terjual, Kapasitas Rak).<br>- Mengintegrasikan tampilan log konveyor dan visualisasi QR Code. |
| 3 | **Fikri Rizqin Faizun** | H1H024068 | **Backend & Network Developer**<br>- Membangun RESTful API Laravel & perantara pesan MQTT.<br>- Mengelola database master & transaksi log (SQLite/MySQL).<br>- Mengatur logika alokasi rak otomatis dan generator QR Code. |

---

## 📌 Gambaran Umum Sistem

**Smart Fashion Inventory** adalah sistem inventori dan manajemen pergudangan pakaian pintar berbasis Internet of Things (IoT). Sistem ini memadukan konveyor mini, pemrosesan sensor real-time, kontrol PID, alokasi rak otomatis berindikator LED, serta dashboard web Laravel.

### Masalah & Solusi
* **Masalah:** Pencatatan pakaian hasil produksi secara manual rawan salah hitung, stok tidak real-time, dan pencarian rak kosong memakan waktu.
* **Solusi:** Konveyor mini menghitung tiap pakaian via sensor IR, menunjuk rak tujuan dengan LED (Rak A, B, C), lalu mengirim data telemetri ke dashboard Laravel melalui Wi-Fi/MQTT.
* **Hasil yang dituju:** Produksi tercatat otomatis, lokasi stok jelas, dan penjualan langsung mengurangi stok.

### Tiga Bagian Sistem
| Bagian | Fungsi | Komponen |
|---|---|---|
| **IoT** | Mendeteksi dan memutuskan | ESP32, sensor IR, LED rak, Wi-Fi |
| **Sistem Kendali** | Menjaga kecepatan belt | Motor DC (TT), L298N, encoder HC-020K, PID |
| **Dashboard Web** | Memantau | Statistik, QR Code, log konveyor, RPM PID |

---

## 🛠️ Komponen & Pinout Wiring

### 1. Hardware

| Komponen | Fungsi Utama | Sambungan ESP32 |
|---|---|---|
| **ESP32 DevKit V1** | Mikrokontroler utama, PID, Wi-Fi/MQTT | Micro USB 5V |
| **Driver Motor L298N** | Mengatur daya dan arah putar motor | ENA → GPIO25, IN1 → GPIO26, IN2 → GPIO27 |
| **Motor DC (TT)** | Memutar roller dan belt konveyor | OUT1 & OUT2 L298N |
| **Encoder HC-020K** | Pulsa RPM aktual (umpan balik PID) | OUT → GPIO34, VCC → 3.3V |
| **Sensor IR** | Pencacah pakaian otomatis | OUT → GPIO32, VCC → 3.3V |
| **LED Rak A, B, C** | Menunjuk rak tujuan | GPIO16, GPIO17, GPIO18 (+ resistor 220 Ω) |
| **Catu Daya Motor** | Sumber daya L298N & motor | 4x AA (6V) / adaptor 7.5V |
| **Rangka Konveyor** | Miniatur konveyor kardus (25 × 10 × 7 cm) | Roller Ø 2 cm, belt 7 cm |

> **Catatan:** Seluruh GND (ESP32, L298N, sensor, catu daya eksternal) harus disatukan (Common GND).

### 2. Software
* **Firmware:** Arduino IDE (C/C++), library `WiFi.h`, `HTTPClient.h` / `PubSubClient`.
* **Backend:** Laravel 10 (PHP), RESTful API, MQTT Broker Gateway.
* **Database:** SQLite / MySQL (master data & transaction logs).
* **Frontend:** Blade, HTML5, Tailwind CSS, JavaScript.

---

## 📐 Arsitektur & Flowchart

### 1. Arsitektur IoT (4 Layer)
```
+-----------------------------------------------------------------------+
| LAYER 4: APLIKASI (Web Dashboard Laravel, Traceability QR Code)       |
+-----------------------------------------------------------------------+
                                   ▲ (HTTP / REST API)
                                   ▼
+-----------------------------------------------------------------------+
| LAYER 3: KOMUNIKASI & DATA (Broker MQTT, Laravel API, Database)      |
+-----------------------------------------------------------------------+
                                   ▲ (Wi-Fi / MQTT Payload)
                                   ▼
+-----------------------------------------------------------------------+
| LAYER 2: PEMROSESAN LOKAL (ESP32: PID Diskrit, IR Counter)            |
+-----------------------------------------------------------------------+
                                   ▲ (Signal & Pulse Feedback)
                                   ▼
+-----------------------------------------------------------------------+
| LAYER 1: SENSOR & AKTUATOR (IR, Encoder HC-020K, L298N, Motor, LED)  |
+-----------------------------------------------------------------------+
```

### 2. Alur Komunikasi Antar Layer
| Alur | Data | Cara |
|---|---|---|
| Layer 1 → ESP32 | Pulsa sensor IR dan encoder | Sinyal digital (GPIO) |
| ESP32 → Layer 1 | PWM & arah ke L298N, nyala LED rak | GPIO & PWM |
| ESP32 → Layer 3 | Jumlah IR, RPM, rak tujuan | Wi-Fi & MQTT |
| MQTT → Database | Pesan diterima lalu disimpan | RESTful API Laravel (HTTP) |
| Layer 4 ↔ Layer 3 | Statistik & log ke dashboard; konfirmasi simpan & status terjual ke database | Web Laravel |

> PID berjalan lokal di ESP32. Dashboard hanya untuk monitoring dan pengelolaan stok, **tidak** mengendalikan aktuator.

### 3. Flowchart Logika IoT & Alokasi Rak
```
[ Pakaian Diletakkan di Belt ]
              │
              ▼
    [ Sensor IR Mendeteksi ] ───► [ Counter IR +1 ]
              │
              ▼
    [ ESP32 Memproses PID ] ───► [ Menjaga Kecepatan Motor 50 RPM ]
              │
              ▼
[ Cek Kapasitas Rak A, B, C ]
              │
              ▼
  [ Pilih Rak Paling Kosong ] ───► [ LED Rak Tujuan Menyala (1,5 s) ]
              │
              ▼
[ Kirim Payload via MQTT/API ] ───► [ Update Real-Time di Dashboard ]
              │
              ▼
[ Petugas Menyimpan Pakaian & Konfirmasi di Website ]
```

---

## ⚙️ Mekanisme Kerja

### 1. Konveyor & Kontroler PID
Motor DC memutar belt. Encoder HC-020K mengirim 20 pulsa/putaran ke ESP32. RPM dihitung dari jarak antar pulsa (karena 20 pulsa/putaran membuat hitungan RPM kasar):

$$RPM = \frac{60.000.000}{20 \times \Delta t_{pulsa}\,(\mu s)}$$

ESP32 menghitung error terhadap *setpoint* 50 RPM dengan PID diskrit:

$$u[k] = K_p \cdot e[k] + K_i \sum e \cdot \Delta t + K_d \frac{e[k] - e[k-1]}{\Delta t}$$

$u[k]$ dibatasi menjadi PWM 0–255 ke L298N agar belt stabil di 50 RPM saat diberi beban.

**Contoh perhitungan** (Kp = 5, Ki = 1, Kd = 0,5, Δt = 0,1 s; setpoint 50, RPM aktual 40, e sebelumnya 12, integral sebelumnya 3,0):
- e = 10 → P = 50, I = 1 × (3,0 + 10 × 0,1) = 4,0, D = 0,5 × (10 − 12)/0,1 = −10
- u = 50 + 4 − 10 = 44 (≈ 17% PWM)

> Angka di atas adalah contoh, bukan hasil tuning. Tuning dilakukan: Kp dulu, lalu Ki, lalu Kd (trial & error / Ziegler-Nichols). Kinerja dinilai dari *rise time, overshoot, settling time,* dan *steady-state error*.

### 2. Pencacahan IR & Alokasi Rak
Sensor IR mengirim sinyal digital ke ESP32 saat pakaian melintas. ESP32 memilih rak paling kosong (kapasitas 10 unit/rak) dan menyalakan LED rak tujuan 1,5 detik. Petugas menyimpan pakaian ke rak tersebut lalu mengonfirmasi di dashboard.

| Status | Warna LED | Isi Rak |
|---|---|---|
| Tersedia | 🟢 Hijau | < 8 dari 10 |
| Hampir Penuh | 🟡 Kuning | 8–9 dari 10 |
| Penuh | 🔴 Merah | 10 dari 10 |

### 3. Traceability QR Code & Dashboard
Setiap item punya kode unik (mis. `PYJ-PNK-M-001`: jenis produk, warna, ukuran, nomor barang). QR Code dibuat lewat website, dicetak, lalu ditempel pada pakaian. Jejak item: **Diproduksi** (terhitung IR) → **Rak penyimpanan** (lokasi tercatat) → **Terjual** (stok berkurang otomatis).

Dashboard menampilkan:
| Tampilan | Sumber Data |
|---|---|
| Total produksi | Hitungan sensor IR dari ESP32 |
| Stok tersedia | Data produk di database |
| Barang terjual | Data produk di database |
| QR Code produk | Gambar QR dan SKU produk |
| Monitoring konveyor | 10 log terakhir: waktu, alokasi rak, kecepatan PID (RPM) |

---

## 📦 Rancangan Fisik

**Pola potongan kardus**

| Bagian | Ukuran | Jumlah |
|---|---|---|
| Alas | 25 × 10 cm | 1 |
| Dinding kiri / kanan | 25 × 7 cm | 2 |
| Penyangga bawah | 10 × 5 cm | 2 |
| Penutup ujung | 10 × 7 cm | 2 |

**Roller & belt:** diameter roller 2 cm, panjang roller 9 cm, lebar belt 7 cm, jarak pusat roller 19 cm, panjang belt ≈ 44 cm (2 × 19 + π × 2 ≈ 44,3 cm).

---

## 🧪 Rencana Pengujian

| Uji | Fokus | Indikator Keberhasilan |
|---|---|---|
| **Uji 1** | LED Rak A, B, C | LED menyala bergantian sesuai sinyal alokasi. |
| **Uji 2** | Sensor IR | Hitungan bertambah tepat 1 tiap benda memotong sinar IR. |
| **Uji 3** | Motor DC & L298N | Belt berputar lurus dan merespons PWM (tanpa PID). |
| **Uji 4** | Encoder HC-020K | RPM tampil stabil di Serial Monitor. |
| **Uji 5** | Kontrol PID | RPM stabil dekat setpoint 50 saat diberi beban. |
| **Uji 6** | Dashboard Web | Data telemetri & kapasitas rak ter-update real-time. |

---

## 🗓️ Hasil Konsultasi

| Dosen | Arahan |
|---|---|
| **Pak Emha (SCM)** | Proyek dapat dilanjutkan; tiap item harus mudah ditelusuri (traceability). |
| **Pak Agus (Kendali)** | Perhitungan PID dijelaskan; kinerja dinilai dari rise time, overshoot, settling time, steady-state error. |
| **Pak Irham (Web 2)** | Rancangan web sudah tepat; perhatikan struktur database, alur ESP–MQTT–Laravel, dan QR Code. |

## 🚀 Langkah Berikutnya
- Pasang encoder HC-020K dan siapkan daya motor.
- Tuning PID pada motor TT yang sebenarnya.
- Hubungkan dashboard Laravel ke ESP32 dan atur pembaruan data real-time.

---

<div align="center">

**Disusun untuk Memenuhi Tugas Besar Mata Kuliah & project praktikum (IoT)**  
*Dosen Pengampu / Penguji: Pak Irham, Pak Agus, Pak Emha*

</div>
