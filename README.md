# End-to-End AWS Infrastructure & Web Deployment

Proyek ini mendokumentasikan langkah-langkah pembangunan infrastruktur jaringan cloud di Amazon Web Services (AWS) hingga tahap *deployment* aplikasi web dinamis berbasis PHP secara aman.

---

## Scene 1: AWS Core Infrastructure Provisioning

Pada tahap pertama ini, seluruh komponen jaringan dasar dan komputasi di AWS disiapkan untuk membangun pondasi server yang terisolasi dan aman.

### 1. Konfigurasi Jaringan & VM
- Membuat Virtual Private Cloud (VPC) kustom sebagai ruang terisolasi.
![Pembuatan VPC di AWS Console](img/aws-vpc.png)

- Mengatur Subnet Publik agar server dapat menjangkau internet.
![Pembuatan Subnet di AWS Console](img/aws-subnet-awal.png)

- Meluncurkan Amazon EC2 Instance dengan sistem operasi Amazon Linux 2023.
![Pembuatan EC2 di AWS Console](img/aws-ec2-instance.png)

- Merancang Internet Gateway, Route Table, Security Group di AWS Console
![Pembuatan IGW di AWS Console](img/aws-igw.png)
![Pembuatan Route Table di AWS Console](img/aws-route-table.png)
![Pembuatan Security Group di AWS Console](img/aws-sg.png)

---

## Scene 2: Secure Remote Access & SSH Connection

Setelah infrastruktur cloud aktif, tahap berikutnya adalah membangun koneksi remote yang aman dari perangkat lokal menggunakan protokol OpenSSH.

### 1. Masalah Keamanan Kunci Privat (.pem)
Saat mencoba melakukan koneksi awal, SSH menolak kunci karena hak akses file di Windows terlalu terbuka (*unprotected private key file*).

### 2. Resolusi Hak Akses via PowerShell
Menggunakan utilitas icacls.exe untuk mengunci berkas .pem agar hanya bisa dibaca secara eksklusif oleh pengguna aktif:
`powershell
icacls.exe .\joel-company.pem /inheritance:d
icacls.exe .\joel-company.pem /remove "NT AUTHORITY\Authenticated Users"


## Scene 3: Web Stack Configuration & Application Deployment

​Fase akhir adalah mengubah EC2 Instance kosong menjadi web server fungsional yang mampu melayani traffic publik.

​1. Pemasangan Apache & PHP-FPM

​Melakukan instalasi runtime environment agar server mampu mengeksekusi skrip backend PHP secara dinamis:

sudo dnf install -y php php-fpm
sudo systemctl start php-fpm
sudo systemctl enable php-fpm

- Terjadi error saat mau deploy ketika web tidak menampilkan hasil akhir hanya menampilkan tulisan it works, sebetulnya hasil sudah bagus karna koneksi terhubung antar 2 tempat, namun html yang dibuat tidak bisa ditampilkan. dilakukan pengecekan dan pemasangan php php-fpm.
![Pengecekan dan pemasangan php php-fpm, disebelumnya terdapat stuck website apache menampilkan "it works"](img/sebelum-instal-php-fpm.png)
![Sesudah dilakukan install php php-fpm](img/php-fpm-installed-restart-httpd.png)
- Hasil akhir
![Hasil akhir dicoba pada website dengan ip address tidak pakai jalur pintas (menambahkan index pada akhir ip)](img/web-tampil.png)