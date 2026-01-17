# MODUL PEMBELAJARAN: Instalasi Odoo 12 HRMS di Windows (Spesifikasi RAM 4GB)

**Target:** Membangun sistem HRMS/Payroll Odoo 12 yang stabil.
**Platform:** Windows 10/11 dengan Docker.
**Kondisi Khusus:** Optimasi untuk Laptop RAM 4GB.

---

## BAB 1: Optimasi Virtual Memory (WAJIB)
Karena RAM terbatas (4GB), kita wajib meminjam Harddisk untuk dijadikan "RAM Cadangan" agar Odoo tidak crash/mati mendadak.

1. Tekan tombol **Windows**, ketik **"View advanced system settings"**, lalu Enter.
2. Masuk Tab **Advanced** > Di kotak *Performance*, klik tombol **Settings**.
3. Masuk Tab **Advanced** lagi > Di kotak *Virtual memory*, klik tombol **Change**.
4. Hilangkan centang pada **"Automatically manage paging file size..."**.
5. Pilih Drive **C:** > Klik bulatan **Custom size**.
6. Isi angka berikut (dalam MB):
   - **Initial size:** 8000 (Setara 8 GB)
   - **Maximum size:** 12000 (Setara 12 GB)
7. Klik **Set**, lalu klik **OK** terus sampai keluar.
8. **RESTART LAPTOP** (Jangan dilewatkan).

---

## BAB 2: Menyiapkan Folder Kerja
Kita buat folder khusus agar data rapi.

1. Buka **File Explorer**, masuk ke folder **Documents**.
2. Buat folder baru bernama: `odoo-hrms`
3. Masuk ke dalam folder `odoo-hrms`, buat **satu folder kosong** saja:
   - Nama folder: `addons`
   - *(Fungsi: Tempat menaruh modul custom Payroll/HRMS).*
   - **PENTING:** Jangan buat folder lain (config/data) secara manual.

---

## BAB 3: Membuat File Docker Compose
Ini adalah konfigurasi "Resep Stabil" untuk Windows.

1. Buka **Notepad**.
2. Copy kode di bawah ini:

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

3. Simpan file ini di dalam folder `odoo-hrms`.
4. Beri nama file: `docker-compose.yml`
   *(Pilih "Save as type: All Files" agar tidak jadi .txt)*

---

## BAB 4: Menyalakan Odoo
1. Buka folder `odoo-hrms`.
2. Klik kanan di ruang kosong > Pilih **Open in Terminal**.
3. Ketik perintah:
   docker-compose up -d
4. Tunggu download selesai.
5. Cek status (pastikan tidak error) dengan perintah:
   docker-compose logs -f --tail=20
   *(Tekan Ctrl+C untuk keluar dari log).*

---

## BAB 5: Konfigurasi Database
1. Buka browser, akses: http://localhost:8069
2. Isi formulir:
   - **Master Password:** (Catat baik-baik!)
   - **Database Name:** hrms_db
   - **Email/Password:** admin / admin
   - **Country:** Indonesia (Agar mata uang otomatis IDR)
   - **Demo Data:** JANGAN DICENTANG (Biarkan bersih).
3. Klik **Create Database**.

---

## BAB 6: Cara Install Custom Addons (Payroll)
Langkah agar modul dari folder Windows terbaca di Odoo.

1. **Copy File:** Masukkan folder modul (misal `asa_payroll`) ke dalam `odoo-hrms/addons`.
2. **Buka Izin (Penting):** Di terminal, ketik:
   docker-compose exec -u root web chmod -R 777 /mnt/extra-addons
3. **Restart:**
   docker-compose restart web
4. **Install:**
   - Masuk Odoo > Settings > **Activate developer mode**.
   - Masuk menu Apps > Klik **Update Apps List** > Update.
   - Hapus filter "Apps" di kolom cari.
   - Ketik nama modul > Install.

---

## BAB 7: Troubleshooting (Solusi Masalah)

1. **Tampilan Rusak (Layar Putih/Berantakan):**
   - Akses URL: http://localhost:8069/web?debug=assets
   - Masuk Settings > Technical > Attachments.
   - Hapus semua file `web.assets...`.
   - Refresh browser.

2. **Gagal Restore Database:**
   - Pastikan BAB 1 sudah dilakukan.
   - Gunakan cara restore manual via terminal jika file terlalu besar.

---
*Dibuat pada: Januari 2026*
