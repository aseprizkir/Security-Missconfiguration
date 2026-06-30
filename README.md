# Security Misconfiguration (Konfigurasi Keamanan yang Salah)

Seperti biasa repositori ini berisi catatan tentang sebuah kerentanan pada web app, khususnya security missconfigurasi, disini gw mau bahas bagaimana cara menganalisis, contoh skenarionya, dan sedikit panduan mitigasi terkait **Security Misconfiguration**, yang secara konsisten menjadi salah satu kerentanan paling umum dalam **OWASP Top 10**.

---

## 📌 Apa itu Security Misconfiguration?

**Security Misconfiguration** itu terjadi ketika komponen sistem informasi tidak dikonfigurasi dengan benar atau dibiarkan dalam kondisi default yang tidak aman. Kerentanan ini bisa terjadi di seluruh lapisan aplikasi (*application stack*), termasuk web server, database, framework, platform cloud, hingga sistem operasi.

Konfigurasi yang buruk sering kali memberikan akses yang tidak sah kepada penyerang atau membocorkan informasi sensitif sistem yang dapat digunakan untuk serangan lanjutan (*reconnaissance*).

---

## 🔍 Penyebab Umum & Contoh Kasus

nih beberapa bentuk *security misconfiguration* yang paling sering gw temui pas lagi jadi bug hunter:

### 1. Kredensial Default (*Default Credentials*)
Ngebiarin username dan password bawaan pabrik atau framework tetap aktif.
* **Contoh:** Menggunakan kombinasi `admin:admin`, `root:root`, atau `guest:guest` pada panel login admin atau database (seperti MongoDB yang terbuka tanpa autentikasi).

### 2. Fitur Debug Aktif di Produksi (*Debug Mode Enabled*)
Menjalankan aplikasi dalam mode pengembangan (*development/debug mode*) di lingkungan produksi (*production*).
* **Dampak:** Jika terjadi error, aplikasi akan menampilkan *stack trace* lengkap, jalur folder (*file path*), versi framework, hingga variabel lingkungan (*environment variables* / `.env`) yang biasanya berisi API key atau kredensial database.
  
catatan:(Vibe coding sering kecolongan disini)

### 3. *Directory Listing* Aktif
Server web dikonfigurasi untuk mengizinkan pengguna melihat daftar file di dalam direktori jika file indeks (seperti `index.html` atau `index.php`) tidak ditemukan.
* **Dampak:** Penyerang dapat menjelajahi folder server dan mengunduh file sensitif, kode sumber, atau file cadangan (`.bak`, `.zip`).

catatan: kerentanan clasic yg gampang banget ditemui cuma modal google dork / make gobuster

### 4. Header Keamanan HTTP yang Hilang (*Missing Security Headers*)
Tidak menerapkan header HTTP yang berfungsi untuk melindungi browser pengguna dari serangan sisi klien.
* **Header yang sering terlewat:**
    * `Content-Security-Policy (CSP)` (Mencegah XSS)
    * `Strict-Transport-Security (HSTS)` (Memaksa koneksi HTTPS)
    * `X-Content-Type-Options: nosniff` (Mencegah MIME-sniffing)
    * `X-Frame-Options` (Mencegah Clickjacking)

catatan: ini mah kalau dibug bounty dianggap informational doang atau malah out of scope.

### 5. Layanan atau Port yang Tidak Diperlukan Tetap Terbuka
Menjalankan layanan (*services*) atau membuka port yang tidak esensial untuk fungsi utama aplikasi.
* **Contoh:** Membiarkan port SSH (22), FTP (21), atau SMB (445) terbuka untuk publik pada web server yang seharusnya hanya membuka port 80/443.

catatan: ini lumayan buat yg paham nmap/metasploit biasanya ngincer port-port terbuka kek gini

---

## 🛡️ Panduan Mitigasi & Pencegahan

Untuk mencegah terjadinya *security misconfiguration*, proses pengerasan sistem (*system hardening*) harus dilakukan secara berkala dan otomatis contohnya:

1. **(*Automated Hardening Process*):**
   * Buat template konfigurasi server yang aman dan terapkan secara konsisten di lingkungan *development*, *QA*, dan *production*.
   * Gunakan konsep *Infrastructure as Code* (IaC) seperti Ansible, Terraform, atau Docker untuk memastikan konfigurasi yang konsisten.

2. **Hapus Komponen Tidak Penting:**
   * Hapus atau nonaktifkan fitur, komponen, dokumentasi, dan sampel kode bawaan (*default*) yang tidak digunakan.

3. **Ubah Kredensial Default:**
   * Wajibkan perubahan password pada seluruh akun sistem segera setelah instalasi selesai.

4. **Matikan Mode Debug:**
   * Pastikan parameter seperti `display_errors = Off` di `php.ini` atau `DEBUG = False` di framework seperti Django/Laravel diterapkan di lingkungan produksi.

5. **Tinjau Ulang Izin Akses (Permissions):**
   * Konfigurasikan hak akses file dan direktori dengan prinsip *Least Privilege* (hak akses minimal yang diperlukan untuk beroperasi).
   * Amankan *bucket* penyimpanan cloud (seperti AWS S3 atau Google Cloud Storage) agar tidak dapat diakses secara publik secara tidak sengaja.

---

## 📝 Kesimpulan

*Security misconfiguration* bukanlah masalah kelemahan kode (*code flaw*), melainkan masalah **tata kelola dan operasional**. Mengamankan konfigurasi membutuhkan konsistensi, audit berkala, dan pemahaman mendalam tentang setiap lapisan teknologi yang digunakan dalam infrastruktur aplikasi.

---
