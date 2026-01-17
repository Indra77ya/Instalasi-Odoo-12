# 🚀 Odoo 12 HRMS - Docker Deployment Guide (Windows)

[![Odoo](https://img.shields.io/badge/Odoo-12.0-purple.svg)](https://www.odoo.com/)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue.svg)](https://www.docker.com/)
[![Platform](https://img.shields.io/badge/Platform-Windows-lightgrey.svg)]()

Panduan lengkap instalasi dan deployment **Odoo 12 (Community Edition)** dengan fokus pada modul **HRMS & Payroll Indonesia** menggunakan Docker di lingkungan Windows. Panduan ini telah dioptimalkan untuk stabilitas sistem, termasuk pada perangkat dengan spesifikasi terbatas (RAM 4GB).

---

## 📋 Daftar Isi
- [Prasyarat & Optimasi Sistem](#-prasyarat--optimasi-sistem)
- [Struktur Folder](#-struktur-folder)
- [Instalasi](#-instalasi)
- [Konfigurasi Custom Addons](#-konfigurasi-custom-addons)
- [Troubleshooting](#-troubleshooting)

---

## ⚙️ Prasyarat & Optimasi Sistem

### 1. Docker Desktop
Pastikan **Docker Desktop** sudah terinstall dan berjalan di Windows Anda.

### 2. Optimasi Virtual Memory (Khusus RAM 4GB)
> ⚠️ **Note:** Jika Laptop Anda memiliki RAM 8GB atau lebih, silakan lewati langkah ini.

Jika menggunakan RAM 4GB, **wajib** menambah Virtual Memory agar container tidak crash (OOM Kill).
1. Buka **View advanced system settings** di Windows.
2. Tab **Advanced** > **Performance Settings** > Tab **Advanced**.
3. **Virtual Memory** > **Change**.
4. Uncheck *Automatically manage paging file size*.
5. Set **Custom size** pada Drive C:
   - **Initial size:** `8000` MB
   - **Maximum size:** `12000` MB
6. **Restart PC**.

---

## 📂 Struktur Folder

Agar deployment berjalan lancar dan rapi, gunakan struktur direktori berikut:

```bash
odoo-hrms/
├── addons/                  # Tempat menaruh modul custom (Payroll, HRMS, dll)
│   ├── asa_payroll/         # Contoh modul 1
│   └── asa_custom_hrms/     # Contoh modul 2
└── docker-compose.yml       # File konfigurasi Docker
