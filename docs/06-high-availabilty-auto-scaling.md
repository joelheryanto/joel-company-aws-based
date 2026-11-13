# Fase 6: High Availability & Auto Scaling (ALB & ASG)

Fase ini mentransformasi arsitektur *single-instance* menjadi sistem *Multi-AZ High Availability* yang mampu mendistribusikan trafik secara merata dan melakukan pemulihan otomatis (*self-healing*).

---

## 1. Golden AMI Creation (Immutable Server Image)

### Implementasi
- Health Check Endpoint: Menambahkan index.html sebagai *root health check* agar Load Balancer dapat memvalidasi status HTTP 200 OK.
- **Custom AMI (joel-web-ami):** Membuat *image* dari instance joel-web-server yang telah terkonfigurasi dengan Apache, PHP, modul php-mysqli, dan kode integrasi RDS.

### Tangkapan Layar Dokumentasi
![Golden AMI Available](../image-screenshot/ha-asg/golden-ami.png)

---

## 2. Application Load Balancer (ALB) & Security Isolation

### Implementasi
- Security Group Chaining: Membuat joel-alb-sg (publik HTTP 80) dan mengunci inbound HTTP pada joel-web-sg agar hanya menerima trafik dari joel-alb-sg.
- **Target Group (joel-web-tg):** Konfigurasi *HTTP Health Check* pada endpoint / untuk memantau kesehatan *backend instance*.
- **Multi-AZ ALB (joel-company-alb):** Mendistribusikan trafik masuk lintas 2 Availability Zones pada subnet publik.

### Tangkapan Layar Dokumentasi
![ALB Active State](../image-screenshot/ha-asg/alb-active.png)
![Target Group Healthy](../image-screenshot/ha-asg/target-group-healthy.png)