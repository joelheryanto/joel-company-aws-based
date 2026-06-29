# 🌐 AWS Cloud Infrastructure: Deploys Apache Web Server & PHP Runtime on EC2

Repository ini mendokumentasikan proses *end-to-end* pembangunan infrastruktur server web dinamis di atas layanan cloud Amazon Web Services (AWS). Proyek ini dirancang sebagai implementasi praktis arsitektur jaringan, manajemen keamanan akses, serta pemecahan masalah (*real-world troubleshooting*) pada lingkungan Linux server.

##  Ringkasan Proyek
- Penyedia Cloud: Amazon Web Services (AWS)
- Layanan Utama: Amazon EC2 (Elastic Compute Cloud)
- Sistem Operasi Server: Amazon Linux 2023
- Web Stack: Apache HTTP Server (httpd) & PHP Runtime (PHP-FPM)
- Tujuan: Menyediakan halaman web dinamis "Joel Company" yang menampilkan status server secara *real-time*.

---

## Kronologi & Dokumentasi Masalah (*Troubleshooting Logs*)

Sebuah perjalanan *deployment* tidak pernah lepas dari tantangan. Berikut adalah ringkasan kendala teknis yang berhasil diidentifikasi dan diselesaikan selama proses pembangunan infrastruktur ini:

### Scene 1: Mengatasi Konflik Hak Akses Kunci Privat SSH (.pem) di Windows

Saat mencoba melakukan koneksi remote via SSH menggunakan kunci privat joel-company.pem, sistem menolak koneksi dengan pesan eror fatal keamanan.

- Identifikasi Masalah: Eror WARNING: UNPROTECTED PRIVATE KEY FILE! dan Permission denied (publickey). Protokol OpenSSH mendeteksi bahwa file .pem memiliki hak akses yang terlalu terbuka (*too open*) di sistem operasi Windows, sehingga sistem mengabaikan kunci tersebut demi keamanan.
- Resolusi Masalah: Menggunakan Windows PowerShell dengan utilitas icacls.exe untuk mematikan fitur pewarisan hak akses (*inheritance*) dan menghapus hak akses pengguna lain (seperti *Authenticated Users* atau *BUILTIN\Users*), menyisakan hak akses eksklusif hanya untuk akun pengguna lokal aktif.

`powershell
# Mematikan pewarisan hak akses pada file kunci privat
icacls.exe .\joel-company.pem /inheritance:d

# Menghapus hak akses kelompok pengguna yang terlalu luas
icacls.exe .\joel-company.pem /remove "NT AUTHORITY\Authenticated Users"
icacls.exe .\joel-company.pem /remove "BUILTIN\Users"

### Scene 2: Mengonfigurasi Jembatan Web Server & Aktivasi PHP-FPM
​Setelah berhasil masuk ke server via SSH dan mengaktifkan Apache, halaman web belum mampu mengeksekusi skrip dinamis PHP dan sempat tertahan pada halaman default.
# ​Identifikasi Masalah: 
1. Perintah awal menghasilkan eror Unit php-fpm.service not found, menandakan bahwa pemroses PHP (PHP Runtime) belum terpasang di ekosistem Amazon Linux 2023.
2. Browser sempat menampilkan halaman teks statis default Apache "It works!" karena konfigurasi bawaan AWS menimpa file index kustom.
​Resolusi Masalah: Melakukan instalasi paket PHP dan PHP-FPM menggunakan manajer paket dnf, mengaktifkan layanannya di latar belakang, serta mengamankan konfigurasi welcome Apache.

# 1. Instalasi PHP dan dependensi pemroses proses PHP
sudo dnf install -y php php-fpm

# 2. Menjalankan dan mengaktifkan service PHP-FPM agar otomatis berjalan saat boot
sudo systemctl start php-fpm
sudo systemctl enable php-fpm

# 3. Memindahkan konfigurasi default welcome Apache agar memprioritaskan index kustom
sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.bak

# 4. Melakukan restart pada Apache Web Server
sudo systemctl restart httpd

### Scene 3: Resolusi Masalah Penamaan File (Typo Detection)
​Setelah melakukan konfigurasi runtime, server sempat menampilkan halaman *Index of / * yang memperlihatkan struktur folder internal, alih-alih mengeksekusi aplikasi utama.
# ​Identifikasi Masalah: Terjadi kesalahan pengetikan (typo) pada nama file di direktori /var/www/html/. File terbuat dengan nama inddex.php (double 'd'). Akibatnya, Apache tidak mengenali file tersebut sebagai pintu gerbang utama (DirectoryIndex) yang secara default mencari file bernama tepat index.php.
​Resolusi Masalah: Mengubah nama file (rename) di dalam terminal Linux menggunakan perintah mv:
sudo mv inddex.php index.php

### Hasil Akhir (Final Deployment)
​Setelah seluruh rangkaian troubleshooting selesai, infrastruktur berhasil berjalan dengan sempurna:
# ​Akses URL: Dapat diakses secara publik menggunakan IP Publik EC2 Instance.
# ​Konektivitas Visual: Halaman web menampilkan identitas Joel Company - AWS CLOUD SERVER dengan indikator status ONLINE.
# ​Eksekusi Komponen Backend: Script PHP berhasil dieksekusi secara dinamis dengan menampilkan waktu operasional server lokal secara akurat dan berubah setiap kali halaman dimuat ulang (refresh).