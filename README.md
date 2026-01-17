Markdown

# 🚀 Odoo 12 HRMS - Docker Deployment Guide (Windows)

[![Odoo](https://img.shields.io/badge/Odoo-12.0-purple.svg)](https://www.odoo.com/)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue.svg)](https://www.docker.com/)
[![Platform](https://img.shields.io/badge/Platform-Windows-lightgrey.svg)]()

Panduan lengkap instalasi dan deployment **Odoo 12 (Community Edition)** dengan fokus pada modul **HRMS & Payroll Indonesia** menggunakan Docker di lingkungan Windows. Panduan ini mencakup optimasi sistem untuk perangkat spesifikasi rendah (RAM 4GB) hingga troubleshooting masalah umum.

---

## 📋 Daftar Isi
1. [Prasyarat & Optimasi Sistem (Virtual Memory)](#1-prasyarat--optimasi-sistem)
2. [Persiapan Workspace](#2-persiapan-workspace)
3. [Konfigurasi Docker Compose](#3-konfigurasi-docker-compose)
4. [Instalasi & Menjalankan Odoo](#4-instalasi--menjalankan-odoo)
5. [Setup Database Awal](#5-setup-database-awal)
6. [Instalasi Custom Addons (Payroll/HRMS)](#6-instalasi-custom-addons-payrollhrms)
7. [Troubleshooting & Solusi Error](#7-troubleshooting--solusi-error)

---

## 1. Prasyarat & Optimasi Sistem

### ⚠️ Optimasi Virtual Memory (Khusus RAM 4GB)
> **Note:** Jika Laptop Anda memiliki RAM 8GB atau lebih, silakan lewati langkah ini.

Jika menggunakan Windows dengan RAM 4GB, **wajib** menambah Virtual Memory agar Docker tidak *crash* (OOM Kill) saat proses berat seperti restore database.

1. Tekan tombol `Windows`, ketik **View advanced system settings**, lalu Enter.
2. Masuk Tab **Advanced** > Pada kotak *Performance*, klik **Settings**.
3. Masuk Tab **Advanced** lagi > Pada kotak *Virtual memory*, klik **Change**.
4. Hilangkan centang pada **Automatically manage paging file size**.
5. Pilih Drive **C:** > Klik opsi **Custom size**.
6. Isi nilai berikut:
   - **Initial size (MB):** `8000` (Setara 8 GB)
   - **Maximum size (MB):** `12000` (Setara 12 GB)
7. Klik **Set** > **OK**.
8. **Restart Laptop** Anda.

---

## 2. Persiapan Workspace

Agar deployment rapi dan data aman, gunakan struktur folder berikut.

1. Buat folder utama bernama `odoo-hrms` di dalam Documents.
2. Buat folder `addons` di dalamnya.

**Struktur Direktori:**
```bash
odoo-hrms/
├── addons/                  # Tempat menaruh modul custom (Payroll, HRMS, dll)
│   ├── asa_payroll/         # Contoh modul 1
│   └── asa_custom_hrms/     # Contoh modul 2
└── docker-compose.yml       # File konfigurasi (Dibuat di langkah berikutnya)
❌ PENTING: Jangan membuat folder config atau data secara manual. Biarkan Docker membuatnya otomatis untuk menghindari error Permission Denied.

3. Konfigurasi Docker Compose
Buat file baru bernama docker-compose.yml di dalam folder odoo-hrms. Copy-paste konfigurasi stabil berikut:

YAML

version: '3.1'
services:
  web:
    image: odoo:12.0
    depends_on:
      - db
    ports:
      - "8069:8069"
    volumes:
      - odoo-web-data:/var/lib/odoo
      - ./addons:/mnt/extra-addons
    environment:
      - HOST=db
      - USER=odoo
      - PASSWORD=odoo
  
  db:
    image: postgres:10
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_PASSWORD=odoo
      - POSTGRES_USER=odoo
    volumes:
      - odoo-db-data:/var/lib/postgresql/data

volumes:
  odoo-web-data:
  odoo-db-data:
4. Instalasi & Menjalankan Odoo
Buka terminal (PowerShell atau Git Bash) di dalam folder odoo-hrms, lalu jalankan perintah berikut:

1. Jalankan Container
PowerShell

docker-compose up -d
Tunggu proses download image selesai.

2. Cek Status Log
Pastikan tidak ada error dengan memantau log:

PowerShell

docker-compose logs -f --tail=20
Jika muncul tulisan: HTTP service (werkzeug) running on ..., berarti Odoo sudah aktif. Tekan Ctrl + C untuk keluar dari log.

5. Setup Database Awal
Buka browser dan akses: http://localhost:8069

Isi formulir pembuatan database:

Master Password: (Password admin server, catat dan simpan!)

Database Name: hrms_db

Email: admin

Password: admin

Country: Indonesia (Wajib, agar mata uang otomatis IDR).

Demo Data: ⬜ Jangan dicentang (Uncheck).

Klik Create Database.

6. Instalasi Custom Addons (Payroll/HRMS)
Langkah ini krusial agar modul dari folder Windows terbaca oleh sistem Linux Odoo.

Langkah 1: Copy Modul
Masukkan folder modul custom (misal: asa_payroll_indonesia) ke dalam folder odoo-hrms/addons. Pastikan strukturnya: addons/nama_modul/__manifest__.py

Langkah 2: Fix Permission (WAJIB di Windows)
Eksekusi perintah ini di terminal agar Odoo bisa membaca file dari Windows:

PowerShell

docker-compose exec -u root web chmod -R 777 /mnt/extra-addons
Langkah 3: Restart Service
PowerShell

docker-compose restart web
Langkah 4: Install via GUI
Login ke Odoo.

Aktifkan Developer Mode: Masuk Settings > Scroll paling bawah > Klik Activate the developer mode.

Masuk menu Apps.

Klik Update Apps List (Menu atas) > Update.

Hapus filter "Apps" pada kolom pencarian (Klik tanda silang kecil).

Cari nama modul dan klik Install.

7. Troubleshooting & Solusi Error
Daftar masalah umum yang sering terjadi saat instalasi di Windows.

🔴 Kasus A: Tampilan Rusak / Layar Putih (Broken Assets)
Biasanya terjadi setelah Restore Database. Odoo kehilangan file CSS/Style. Solusi:

Ubah URL browser menjadi: http://localhost:8069/web?debug=assets

Setelah menu terlihat, masuk ke Settings > Technical > Database Structure > Attachments.

Cari file dengan nama web.assets....

Hapus semua file tersebut.

Refresh browser (hapus ?debug=assets dari URL).

🔴 Kasus B: Modul Tidak Muncul di Pencarian
Solusi:

Pastikan filter default "Apps" di search bar sudah dihapus.

Pastikan struktur folder tidak ganda (Contoh salah: addons/modul_folder/modul_asli/__manifest__.py).

Pastikan modul induk (Dependencies) sudah terinstall lebih dulu.

🔴 Kasus C: Gagal Restore Database (Connection Lost)
Solusi: Ini biasanya masalah RAM atau Timeout.

Pastikan langkah Virtual Memory (Bab 1) sudah dilakukan.

Jika file backup sangat besar (>200MB), gunakan metode restore manual via terminal (psql).

Dokumentasi dibuat untuk Odoo HRMS Indonesia Deployment.
