# MODUL PEMBELAJARAN: Instalasi Odoo 12 HRMS di Windows dengan Docker

**Target:** Membangun sistem HRMS/Payroll Odoo 12 yang stabil.
**Platform:** Windows 10/11.
**Metode:** Docker Container.

---

## BAB 1: Optimasi Virtual Memory (OPSIONAL)
**Catatan Penting:**
- **Jika RAM laptop Anda 8GB atau lebih:** Silakan **LEWATI** bab ini dan langsung lanjut ke BAB 2.
- **Jika RAM laptop Anda hanya 4GB:** Sangat disarankan melakukan langkah ini agar Odoo tidak mati mendadak (crash) saat dijalankan.

**Langkah-langkah:**
1. Tekan tombol **Windows**, ketik **"View advanced system settings"**, lalu Enter.
2. Masuk Tab **Advanced** > Di kotak *Performance*, klik tombol **Settings**.
3. Masuk Tab **Advanced** lagi > Di kotak *Virtual memory*, klik tombol **Change**.
4. Hilangkan centang pada **"Automatically manage paging file size..."**.
5. Pilih Drive **C:** > Klik bulatan **Custom size**.
6. Isi angka berikut (dalam MB):
   - **Initial size:** 8000 (Setara 8 GB)
   - **Maximum size:** 12000 (Setara 12 GB)
7. Klik **Set**, lalu klik **OK** terus sampai keluar.
8. **RESTART LAPTOP** sebelum lanjut ke Bab 2.

---

## BAB 2: Menyiapkan Folder Kerja
Kita buat folder khusus agar data tersimpan rapi dan aman.

1. Buka **File Explorer**, masuk ke folder **Documents** (atau Drive D:).
2. Buat folder baru bernama: `odoo-hrms`
3. Masuk ke dalam folder `odoo-hrms`, buat **satu folder kosong** saja:
   - Nama folder: `addons`
   - *(Fungsi: Tempat menaruh modul custom Payroll/HRMS nanti).*
   - **PENTING:** Jangan buat folder lain (seperti config/data) secara manual agar tidak error.

---

## BAB 3: Membuat File Konfigurasi (Docker Compose)
Ini adalah "Resep" stabil untuk menjalankan Odoo di Windows tanpa error file hilang.

1. Buka aplikasi **Notepad**.
2. Copy (Salin) kode di bawah ini:

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

3. Simpan file ini di dalam folder `odoo-hrms` yang tadi dibuat.
4. Beri nama file: `docker-compose.yml`
   *(Pastikan pilih "Save as type: All Files" agar tidak tersimpan sebagai .txt)*

---

## BAB 4: Menyalakan Odoo
Saatnya menghidupkan mesin Odoo.

1. Buka folder `odoo-hrms`.
2. Klik kanan di ruang kosong > Pilih **Open in Terminal** (atau Git Bash/PowerShell).
3. Ketik perintah berikut untuk menyalakan:
   docker-compose up -d
4. Tunggu proses download selesai (butuh internet).
5. Cek status (pastikan berhasil) dengan perintah:
   docker-compose logs -f --tail=20
   *(Jika muncul tulisan "HTTP service running", tekan Ctrl+C untuk keluar).*

---

## BAB 5: Konfigurasi Database Awal
1. Buka browser (Chrome/Edge), akses alamat: http://localhost:8069
2. Isi formulir pembuatan database:
   - **Master Password:** (Password admin server, catat baik-baik!)
   - **Database Name:** hrms_db
   - **Email:** admin
   - **Password:** admin
   - **Country:** Indonesia (Wajib, agar mata uang jadi Rp).
   - **Demo Data:** JANGAN DICENTANG (Biarkan kosong agar bersih).
3. Klik **Create Database**.

---

## BAB 6: Cara Install Modul Custom (Payroll/HRMS)
Langkah agar modul dari folder Windows bisa terbaca dan diinstall di Odoo.

1. **Copy File:** Masukkan folder modul custom (misal `asa_payroll`) ke dalam folder `odoo-hrms/addons`.
   *(Pastikan strukturnya: addons > nama_modul > __manifest__.py)*.
2. **Buka Izin Akses (Penting di Windows):**
   Buka terminal di folder odoo-hrms, ketik:
   docker-compose exec -u root web chmod -R 777 /mnt/extra-addons
3. **Restart Odoo:**
   docker-compose restart web
4. **Install di Aplikasi:**
   - Login ke Odoo.
   - Masuk menu Settings > Scroll bawah > Klik **Activate developer mode**.
   - Masuk menu Apps.
   - Klik **Update Apps List** > Update.
   - **Hapus filter "Apps"** di kolom pencarian.
   - Ketik nama modul > Klik Install.

---

## BAB 7: Solusi Masalah Umum (Troubleshooting)

**1. Tampilan Rusak / Layar Putih (Broken Assets)**
   - Masalah ini sering terjadi setelah restore database.
   - Solusi:
     1. Ubah URL di browser menjadi: http://localhost:8069/web?debug=assets
     2. Masuk menu Settings > Technical > Database Structure > Attachments.
     3. Cari file `web.assets...`, hapus semua isinya.
     4. Refresh browser (hapus `?debug=assets`).

**2. Odoo Gagal Restore / Error Connection**
   - Biasanya karena RAM penuh atau file terlalu besar.
   - Solusi: Pastikan sudah melakukan BAB 1 (Virtual Memory).

**3. Modul Tidak Muncul di Pencarian**
   - Cek apakah filter "Apps" di kolom pencarian sudah dihapus (tanda silang kecil).
   - Cek apakah struktur folder di `addons` sudah benar (tidak double folder).

---
*Tutorial Odoo 12 Docker Windows - Januari 2026*
