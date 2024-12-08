
# Coffee VNT Management System

Coffee VNT Management System adalah aplikasi berbasis web yang dirancang untuk mempermudah pengelolaan operasional di Coffee Shop VNT. Aplikasi ini berfokus pada pengelolaan data pengguna, menu, transaksi, dan aktivitas log pengguna yang dapat diakses oleh tiga peran utama: Admin, Manager, dan Kasir.

## Fitur Utama

### **Admin**
- Melihat dashboard dengan data user dan menu.
- Menambahkan user baru.
- Mengekspor data user ke format Excel dan PDF.
- Melihat log aktivitas pengguna (seperti penambahan user atau ekspor data).
- Mengelola profil pengguna.

### **Manager**
- Melihat dashboard dengan data serupa Admin.
- Menambahkan dan mengelola menu makanan dan minuman.
- Mengekspor data menu dan transaksi ke Excel dan PDF.
- Melihat data transaksi (nama pelanggan, menu, jumlah, total, nama pegawai, tanggal transaksi).
- Mengelola profil pengguna.

### **Kasir**
- Melihat dashboard serupa Admin.
- Mengelola transaksi pelanggan (nama pelanggan, menu yang dipesan, total harga).
- Mengekspor data transaksi ke Excel dan PDF.
- Mengelola profil pengguna.

## Teknologi yang Digunakan
- **Backend Framework:** Laravel (PHP)
- **Frontend Framework:** Blade (Laravel Template Engine)
- **Database:** MySQL
- **CI/CD Tools:** Jenkins, Ngrok
- **Containerization:** Docker
- **Version Control:** Git, GitHub

## Instalasi

### **1. Clone Repository**
```bash
git clone https://github.com/<username>/coffee-vnt-management-system.git
cd coffee-vnt-management-system
