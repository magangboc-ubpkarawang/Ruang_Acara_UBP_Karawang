# Ruang Acara — aplikasi Google Apps Script

Website internal untuk acara, sesi, lokasi, petugas, dokumentasi, laporan, arsip, dan honor. Frontend di-host melalui GitHub Pages, sedangkan Google Apps Script menjadi backend JSON. Data bersama tersimpan di Google Sheets. Berkas dan folder hasil dibuat di Google Drive.

## Berkas

- `Code.gs` — backend JSON, login, penyimpanan Sheets, pembuatan dan pembagian folder Drive, serta unggah berkas.
- `Index.html` — frontend responsif untuk GitHub Pages. QR dibuat di browser tanpa mengirim tautan folder ke layanan QR pihak ketiga.
- `appsscript.json` — zona waktu dan runtime.
- `Ruang_Acara_Database_Template.xlsx` — struktur database siap diubah menjadi Google Sheets.

Template database sudah berisi enam petugas awal beserta NIM. Peran penugasan yang digunakan adalah **Operator**, **Kameramen**, dan **Dokumentasi**. Jika satu sesi membutuhkan dua kameramen, gunakan label **Kameramen 1** dan **Kameramen 2** pada penugasan.

## Pasang di akun Google pengelola

1. Buka [Google Apps Script](https://script.google.com/) dan buat proyek kosong bernama **Ruang Acara**.
2. Salin isi `Code.gs` ke berkas `Code.gs` proyek. Tidak perlu membuat file HTML di Apps Script.
3. Di **Project Settings**, tampilkan manifest `appsscript.json`, lalu salin isi manifest dalam paket ini.
4. Upload `Ruang_Acara_Database_Template.xlsx` ke Google Drive, buka dengan Google Sheets, lalu pilih **File → Simpan sebagai Google Spreadsheet**. Jangan mengganti nama sheet, nama kolom, atau urutan kolom. Salin Spreadsheet ID dari URL file hasil konversi.
5. Di **Script Properties**, tambahkan `RA_SPREADSHEET_ID`, `ADMIN_EMAIL` (email login admin), `ADMIN_TEMP_PASSWORD` (minimal 12 karakter), dan opsional `ADMIN_NAME`. Jangan menaruh kata sandi di kode. Properti sandi sementara akan dihapus oleh setup.
6. Jalankan `setupWorkspace` satu kali dari editor dan berikan izin Google yang diminta. Fungsi ini memeriksa struktur database, membuat folder arsip bila belum ditentukan, dan menambahkan akun admin. URL spreadsheet dan folder arsip dicatat dalam execution log.
7. Jika tidak ingin memakai file Excel, abaikan `RA_SPREADSHEET_ID`; `setupWorkspace` akan membuat spreadsheet dan seluruh sheet database secara otomatis.
8. Pastikan akun yang menjalankan/deploy script memiliki izin menulis di kedua folder induk Drive yang diberikan. ID awalnya sudah diisi pada Pengaturan; ubah jika perlu.
9. Pilih **Deploy → New deployment → Web app**. Pilih **Execute as: Me** dan **Who has access: Anyone** agar frontend GitHub dapat menghubungi backend. Keamanan data tetap memakai login aplikasi. Jika pilihan Anyone tidak tersedia, kebijakan Google Workspace perlu diubah atau backend harus dipasang menggunakan akun yang mengizinkan web app publik.
10. Salin URL deployment yang berakhiran `/exec`. Membuka URL tersebut langsung harus menampilkan status JSON `ready`.
11. Buka `Index.html`, ganti `PASTE_APPS_SCRIPT_EXEC_URL_HERE` dengan URL `/exec`, lalu unggah atau commit file tersebut ke repository GitHub Pages.
12. Buka website GitHub Pages, login dengan `ADMIN_EMAIL` dan sandi awal tadi. Tambahkan akun petugas dari menu **Pengaturan → Akun internal**.

Frontend mengirim request sebagai `text/plain` agar browser tidak membuat preflight CORS. Saat `Code.gs` diperbarui, simpan dan buat **versi deployment baru** agar URL `/exec` memakai perubahan. Hindari menjalankan `setupWorkspace` lagi: fungsi akan menolak setup kedua agar data lama aman.

## Alur penggunaan

1. Buat acara dan isi PIC, jadwal, format, layanan foto, serta live streaming. Jika live streaming dimatikan, kebutuhan video menjadi recording.
2. Tambahkan sesi per hari. Jam selesai yang lebih awal daripada jam mulai dianggap melewati tengah malam. Beberapa ruangan/area dapat dipisahkan dengan koma.
3. Tambahkan petugas dan penugasan per sesi. Isi nominal honor pada setiap tugas; total honor petugas adalah jumlah seluruh tugasnya dalam acara.
4. Dari **Dokumentasi & arsip**, buat folder foto/rekaman. Nama folder mengikuti judul dan tanggal acara; pembuatan ulang akan memakai folder yang sudah ada. Acara beberapa hari mendapat subfolder per tanggal sesi.
5. Setelah folder siap dibuka panitia, klik **Aktifkan akses tautan panitia**. Jika kebijakan Google Workspace melarang akses tautan umum, bagikan folder secara manual kepada penerima laporan.
6. Tambahkan berkas pengajuan, scan final, atau dokumen pendukung dengan tautan; unggah PDF/DOC/DOCX/JPG/PNG sampai 8 MB. Berkas unggahan masuk folder arsip acara.
7. Isi hasil pelaksanaan, periksa QR yang menuju **folder acara**, finalkan laporan, lalu klik **Cetak / simpan PDF**. Pada dialog cetak browser, pilih **Save as PDF** atau printer.
8. Administrator mencatat pembayaran honor per petugas, termasuk tanggal, nominal, catatan, dan tautan bukti.

## Batasan dan keputusan awal

- Format laporan saat ini adalah template umum dan bisa disesuaikan setelah format resmi disepakati.
- Dasar honor saat ini **per penugasan/sesi**. Jika kebijakannya per acara atau per hari, ubah nominal tugas sesuai ketentuan atau revisi perhitungannya.
- Arsip rekaman live streaming merupakan pilihan. Jika dinyalakan, folder recording dan QR rekaman disertakan.
- **Simpan PDF** memakai dialog cetak browser. Belum membuat berkas PDF otomatis di Drive.
- Data di Google Sheets bersifat operasional internal. Akun `editor` dapat melihat honor, tetapi hanya `admin` yang dapat mencatat pembayaran, mengatur akun, dan mengubah pengaturan.
- Folder Drive baru tetap terbatas sampai tombol berbagi diklik. Ketersediaan `Anyone with the link` ditentukan oleh kebijakan akun/organisasi Google.
- Demo Sites yang menyertai paket ini menggunakan data contoh di browser. Data demo tidak tersinkron ke spreadsheet produksi.

## Lisensi QR

Encoder QR disertakan langsung di `Index.html`, berasal dari karya Kazuhiko Arase (MIT), melalui paket `qrcode-terminal` (Apache-2.0). Lihat `THIRD_PARTY_LICENSES.txt`.
