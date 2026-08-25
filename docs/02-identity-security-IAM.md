# Fase 2: Identity & Security (IAM)
Fase ini mengimplementasikan *Best Practice* dalam keamanan cloud: menghapus penggunaan *hardcoded keys* dan beralih ke IAM Instance Profile.

## 🛡️ Pendekatan Keamanan
Alih-alih menyimpan *Access Key* secara statis di dalam kode (yang berisiko bocor ke publik), kita memberikan "ID Card" (IAM Role) langsung ke EC2 Instance.

## ⚙️ Implementasi
1. Membuat IAM Role dengan kebijakan AmazonS3FullAccess.
2. Menempelkan (*attach*) Role tersebut ke EC2 melalui AWS Console (Modify IAM Role).

## 🔍 Verifikasi & Validasi
Kami memverifikasi bahwa EC2 telah mengadopsi identitas IAM Role tersebut melalui AWS CLI:

Verifikasi Identitas:
```bash
aws sts get-caller-identity
aws s3 ls (menunjukan koneksi lancar tidak ada error "accessdenied")
```
![penunjukan pembuatan file dan hasil test iam lewat vscode](img/aws-iam-test.png)