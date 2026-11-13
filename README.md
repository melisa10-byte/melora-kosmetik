# Melora Kosmetik – Product Manager

**Melora Kosmetik – Product Manager** adalah aplikasi web inventaris produk kecantikan dan kosmetik modern yang dibangun menggunakan **PHP Native, MySQL, PDO, HTML5, dan CSS3**. Project ini dikembangkan sesuai seluruh instruksi, materi, arsitektur keamanan, pola perancangan, dan kriteria penilaian praktikum **Pemrograman Web Pertemuan 3 (Integrasi PHP, MySQL & UI Styling)**.

---

## 1. Deskripsi Aplikasi

Aplikasi ini memudahkan administrator toko kecantikan **Melora Kosmetik** dalam mengelola persediaan katalog kosmetik. Sistem menyediakan kapabilitas operasional lengkap mulai dari penambahan produk baru (*Create*), visualisasi katalog berbasis kartu responsif (*Read*), pengubahan data (*Update*), penghapusan aman (*Delete*), penyaringan data (*Search & Filter*), hingga ringkasan statistik inventaris (*Dashboard Overview*).

### Nilai Utama & Standar Teknis:
- **Keamanan Berlapis (Defense in Depth):**
  - **SQL Injection Prevention:** Penggunaan *PDO Prepared Statements* dengan *parameter binding* pada seluruh query yang melibatkan masukan pengguna.
  - **Cross-Site Scripting (XSS) Prevention:** Sanitasi dan *context-aware output escaping* menggunakan `htmlspecialchars($value, ENT_QUOTES, 'UTF-8')`.
  - **Cross-Site Request Forgery (CSRF) Protection:** Penerapan token kriptografis acak `bin2hex(random_bytes(32))` berbasis sesi dan diverifikasi menggunakan `hash_equals()`.
  - **HTTP Method Enforcement:** Operasi manipulasi dan penghapusan data secara ketat menggunakan metode `POST`, menolak metode `GET`.
- **Integritas Alur Request (PRG Pattern):**
  - Menerapkan pola *Post-Redirect-Get* (PRG) pada seluruh proses manipulasi data (Create, Update, Delete) untuk mencegah duplikasi data akibat *refresh* browser.
- **Validasi Ketat di Sisi Server (Server-Side Validation):**
  - Mengandalkan fungsi PHP murni (`trim()`, `mb_strlen()`, `filter_var()` / `filter_input()`, `FILTER_VALIDATE_FLOAT`, `FILTER_VALIDATE_INT`), bukan sekadar atribut HTML.
- **Modern & Responsive UI Styling:**
  - Mengimplementasikan prinsip **CSS Box Model** (`box-sizing: border-box`, padding, margin, border) dan **Flexbox Layout** (`display: flex`, `flex-wrap: wrap`, basis lebar fleksibel).
  - Skema warna eksklusif bertema *Pink Pastel*, *White*, dan aksen *Rose Gold*.

---

## 2. Persyaratan Perangkat Lunak (Software Requirements)

1. **Web Server & Database:**
   - XAMPP v8.x (atau yang setara) dengan modul **Apache** dan **MySQL / MariaDB**.
2. **Interpreter:**
   - PHP 8.1 / 8.2 / 8.3 dengan ekstensi aktif:
     - `pdo_mysql`
     - `mbstring`
     - `openssl` / `session`
3. **Database Management Tool:**
   - phpMyAdmin atau MySQL Command Line.
4. **Web Browser:**
   - Google Chrome, Mozilla Firefox, Microsoft Edge, atau Safari versi modern.

---

## 3. Struktur Direktori Project

```text
Melora_Kosmetik/
│
├── config/
│   └── db.php                     # Konfigurasi koneksi PDO ke MySQL
│
├── database/
│   └── store_db.sql               # Skema database melora_kosmetik & contoh data
│
├── public/
│   ├── assets/
│   │   └── style.css              # File CSS utama (Box Model, Flexbox, Pink Pastel Theme)
│   │
│   ├── helpers.php                # Helper keamanan (CSRF, XSS escaping, validasi, rupiah)
│   ├── header.php                 # Header halaman dan navigasi utama
│   ├── footer.php                 # Footer halaman dan hak cipta
│   ├── index.php                  # Dashboard statistik, pencarian GET, & katalog produk (READ)
│   ├── create.php                 # Formulir dan logika tambah produk baru (CREATE + PRG)
│   ├── edit.php                   # Formulir dan logika ubah data produk (UPDATE + PRG)
│   └── delete.php                 # Endpoint penghapusan data via POST (DELETE + CSRF + PRG)
│
└── README.md                      # Dokumentasi komprehensif project
```

---

## 4. Cara Menjalankan Aplikasi

Ikuti tahapan berikut untuk mengoperasikan aplikasi pada lingkungan lokal:

1. **Salin Folder Project:**
   Letakkan folder `Melora_Kosmetik` ke dalam direktori root web server XAMPP:
   ```text
   C:\xampp\htdocs\Melora_Kosmetik
   ```

2. **Aktifkan Layanan XAMPP:**
   Buka **XAMPP Control Panel**, kemudian klik tombol **Start** pada modul **Apache** dan modul **MySQL**.

3. **Import Database MySQL:**
   - Buka browser dan akses phpMyAdmin di `http://localhost/phpmyadmin/`.
   - Pilih tab **Import** pada menu atas.
   - Klik **Choose File / Telusuri**, lalu pilih file `database/store_db.sql` yang berada di dalam folder project.
   - Klik tombol **Import / Kirim / Go** di bagian bawah.
   - Database `melora_kosmetik` dan tabel `products` beserta data sampel awal akan otomatis terbuat.

4. **Konfigurasi Database (Bila Diperlukan):**
   Pengaturan koneksi pada `config/db.php` telah disesuaikan secara *default* dengan XAMPP:
   - Host: `localhost`
   - Database: `melora_kosmetik`
   - Username: `root`
   - Password: `""` (kosong)
   - Port: `3306`

5. **Akses Aplikasi Melalui Browser:**
   Buka URL berikut pada web browser Anda:
   ```text
   http://localhost/Melora_Kosmetik/public/
   ```

---

## 5. Skema Basis Data (`store_db.sql`)

Aplikasi menggunakan database bernama `melora_kosmetik` dengan struktur tabel `products`:

```sql
CREATE DATABASE IF NOT EXISTS `melora_kosmetik`
  CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE `melora_kosmetik`;

CREATE TABLE IF NOT EXISTS `products` (
    `id` INT AUTO_INCREMENT PRIMARY KEY,
    `name` VARCHAR(100) NOT NULL UNIQUE,
    `category` VARCHAR(50) NOT NULL DEFAULT 'Umum',
    `price` DECIMAL(12,2) NOT NULL,
    `stock` INT NOT NULL DEFAULT 0,
    `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Karakteristik & Integritas Data:**
- `id`: Kunci primer (*Primary Key*) dengan penambahan otomatis (*AUTO_INCREMENT*).
- `name`: Nama produk wajib diisi (*NOT NULL*) dan tidak boleh duplikat (*UNIQUE*).
- `category`: Kategori kosmetik (*Skincare, Makeup, Body Care, Hair Care, Parfum, Umum*).
- `price`: Nilai moneter presisi menggunakan tipe data `DECIMAL(12,2)`.
- `stock`: Kuantitas stok bertipe integer bulat (*INT*), minimal bernilai `0`.
- `created_at`: Waktu pencatatan produk secara otomatis.

---

## 6. Fitur-Fitur Aplikasi

### A. Dashboard Overview (Statistik Cepat)
- **Total Produk:** Menghitung akumulasi seluruh produk terdaftar.
- **Jumlah Kategori:** Menghitung ragam kategori kosmetik aktif (`COUNT(DISTINCT category)`).
- **Total Stok Kosmetik:** Menjumlahkan total unit persediaan fisik (`SUM(stock)`).

### B. Create (Tambah Produk Baru)
- Formulir input: Nama Produk, Kategori, Harga (Rp), dan Stok.
- Pengiriman data menggunakan metode `POST`.
- Validasi data di sisi server sebelum proses database.
- Eksekusi query menggunakan *PDO Prepared Statement*.
- Menerapkan pola *Post-Redirect-Get* (PRG) menuju `index.php?status=created`.

### C. Read (Katalog Produk Responsif)
- Menampilkan daftar produk kosmetik dalam bentuk kartu (*card*) Flexbox.
- Menyajikan informasi: Nama, Kategori, Format Rupiah (`Rp xx.xxx`), Kuantitas Stok, dan Indikator Ketersediaan (*Badge*).
- Produk diurutkan berdasarkan produk terbaru terlebih dahulu (`ORDER BY id DESC`).
- Seluruh teks yang dicetak dilindungi menggunakan `htmlspecialchars()`.

### D. Update (Edit Produk)
- Membuka halaman ubah data berdasarkan parameter ID yang tervalidasi (`FILTER_VALIDATE_INT`).
- Memeriksa keberadaan produk di database (memberikan respon 404 jika data tidak ditemukan).
- Menampilkan data lama pada formulir untuk memudahkan pengubahan.
- Memvalidasi keunikan nama produk (memperbolehkan nama yang sama untuk produk yang sedang diedit).
- Eksekusi query pembaruan data menggunakan *PDO Prepared Statement*.
- Pengalihan PRG menuju `index.php?status=updated`.

### E. Delete (Hapus Produk)
- Penghapusan produk **wajib menggunakan metode POST** (menolak metode GET untuk mencegah manipulasi URL).
- Dilengkapi perlindungan token **CSRF** yang divalidasi dengan `hash_equals()`.
- Menampilkan dialog konfirmasi JavaScript (`confirm`) sebelum form dikirimkan.
- Eksekusi query penghapusan menggunakan *PDO Prepared Statement*.
- Pengalihan PRG menuju `index.php?status=deleted`.

### F. Search & Filter (Fitur Bonus PPT)
- Pencarian fleksibel menggunakan metode `GET` dengan query string `?q=...`.
- Mencari kecocokan kata kunci pada **Nama Produk** maupun **Kategori Produk**.
- Query SQL aman menggunakan parameter binding `LIKE :q`.
- Mengembalikan nilai kata kunci pada kotak pencarian secara aman (`e($q)`).
- Tombol *Reset* instan untuk kembali menampilkan seluruh katalog.

---

## 7. Desain Antarmuka & CSS

- **Palet Warna:**
  - *Primary Pink:* `#d96f90`, `#c4577a`
  - *Soft Pastel Background:* `#fff8fa`, `#fdf0f4`
  - *Rose Gold & Champagne Accent:* `#b76e79`, `#d4a373`
  - *Neutral Text:* `#33222a`, `#7d6872`
- **Penerapan CSS Box Model:**
  - Penggunaan `box-sizing: border-box` global untuk kepastian kalkulasi dimensi elemen.
  - Pengaturan *padding*, *margin*, dan *border* yang proporsional pada kartu dan formulir.
- **Penerapan Flexbox Layout:**
  - `display: flex`, `flex-wrap: wrap`, dan pengaturan `gap: 20px - 24px`.
  - Basis lebar kartu fleksibel (`flex: 1 1 240px; min-width: 240px; max-width: calc(33.333% - 16px)`).
- **Responsivitas Multi-Perangkat:**
  - Optimal pada resolusi layar **Desktop** (3 kolom kartu).
  - Menyesuaikan pada resolusi **Tablet** (2 kolom kartu).
  - Tampilan ergonomis pada **Smartphone** (1 kolom kartu penuh, navigasi ringkas).

---

## 8. Panduan Pengujian (Testing Checklist)

Lakukan verifikasi terhadap 13 skenario uji wajib berikut:

| No | Skenario Pengujian | Masukan Uji | Hasil yang Diharapkan | Status |
|:--:|:-------------------|:------------|:----------------------|:------:|
| 1 | Tambah produk valid | Nama: `Hydrating Facial Wash`, Kategori: `Skincare`, Harga: `55000`, Stok: `10` | Produk berhasil tersimpan dan muncul di urutan teratas katalog | **LULUS** |
| 2 | Nama produk < 3 karakter | Nama: `AB` | Form menolak simpan; pesan error *"Nama produk minimal 3 karakter"* tampil | **LULUS** |
| 3 | Harga 0 atau negatif | Harga: `0` atau `-25000` | Form menolak simpan; pesan error *"Harga harus berupa angka lebih dari 0"* tampil | **LULUS** |
| 4 | Stok negatif | Stok: `-5` | Form menolak simpan; pesan error *"Stok tidak boleh bernilai negatif"* tampil | **LULUS** |
| 5 | Nama produk duplikat | Menginput nama yang sudah ada (misal: `Melora Brightening Glow Serum`) | Form menolak simpan; pesan error *"Nama produk sudah terdaftar"* tampil | **LULUS** |
| 6 | Refresh browser sesudah submit | Menekan tombol F5 / Refresh browser setelah berhasil menambahkan produk | Tidak terjadi pengiriman ulang form (*no duplicate entry*), halaman tetap aman | **LULUS** |
| 7 | Proteksi XSS pada nama produk | Nama: `<b>Serum Glowing</b> <script>alert(1)</script>` | Ditampilkan murni sebagai string teks pada HTML, bukan markup tebal / script | **LULUS** |
| 8 | Pencarian berdasarkan nama | Query: `Serum` | Menampilkan hanya produk yang memiliki kata "Serum" pada namanya | **LULUS** |
| 9 | Pencarian berdasarkan kategori | Query: `Makeup` | Menampilkan seluruh produk dalam kategori "Makeup" | **LULUS** |
| 10 | Edit produk | Mengubah harga produk ID 1 menjadi `90000` | Data berhasil diperbarui di database dan notifikasi sukses muncul | **LULUS** |
| 11 | Hapus produk aman | Klik tombol Hapus, konfirmasi "OK" | Data terhapus melalui POST, diarahkan kembali dengan notifikasi sukses | **LULUS** |
| 12 | Proteksi CSRF | Mengirim request POST tanpa token CSRF atau token salah | Server mengembalikan kode HTTP `403 Forbidden` dan menolak operasi | **LULUS** |
| 13 | Uji Tampilan Responsif | Menguji pada ukuran Desktop (1200px), Tablet (768px), dan Ponsel (375px) | Layout membungkus rapi (*flex-wrap*), tidak ada elemen bertumpuk | **LULUS** |

---

## 9. Refleksi Keamanan (Security Review)

Pertanyaan Refleksi Slide 21 PPT:
> *"Di bagian mana aplikasi paling rentan: input, query, output, atau alur request? Jelaskan kontrol keamanan yang telah Anda implementasikan."*

Aplikasi web memiliki empat titik kerentanan kritis, dan setiap titik telah dilengkapi kontrol keamanan terpadu:

1. **Input (Data Masuk):**
   - *Titik Rentan:* Input manipulatif (tipe data salah, nilai negatif, string kosong, atau ukuran berlebih).
   - *Kontrol Keamanan:* Sanitasi `trim()`, pembatasan panjang karakter `mb_strlen()`, serta validasi ketat dengan `filter_var()` menggunakan flag `FILTER_VALIDATE_FLOAT` dan `FILTER_VALIDATE_INT`.

2. **Query (Interaksi Database):**
   - *Titik Rentan:* Serangan **SQL Injection** yang dapat mengekstrak, mengubah, atau menghapus data database.
   - *Kontrol Keamanan:* Menggunakan **PDO Prepared Statements** untuk semua operasi (SELECT, INSERT, UPDATE, DELETE). Placeholder `:name`, `:category`, `:price`, `:stock`, `:id`, dan `:q` memastikan nilai input diperlakukan sebagai parameter murni, bukan kode SQL. `PDO::ATTR_EMULATE_PREPARES => false` diaktifkan untuk native prepared statement.

3. **Output (Penyajian Data ke Browser):**
   - *Titik Rentan:* Serangan **Cross-Site Scripting (XSS)** melalui injeksi tag HTML atau skrip JavaScript jahat.
   - *Kontrol Keamanan:* Seluruh output dinamis yang berasal dari input pengguna maupun database di-escape melalui fungsi `htmlspecialchars($value, ENT_QUOTES, 'UTF-8')`.

4. **Alur Request (Siklus Permintaan Klien-Server):**
   - *Titik Rentan:* **Form Resubmission** akibat refresh browser dan serangan **Cross-Site Request Forgery (CSRF)** dari situs eksternal.
   - *Kontrol Keamanan:*
     - Penerapan pola **Post-Redirect-Get (PRG)** dengan pemanggilan `header('Location: ...')` diikuti `exit;` memastikan browser beralih ke metode GET setelah mutasi data.
     - Operasi destruktif (Delete) secara tegas menolak metode GET dan hanya mengizinkan POST.
     - Penggunaan token kriptografi acak `random_bytes(32)` yang disimpan dalam session dan diverifikasi menggunakan `hash_equals()` pada setiap mutasi POST.

---

## 10. Panduan Pengemasan dan Pengumpulan (Submission)

Sesuai ketentuan, project ini dikemas dalam format arsip berkas:
```text
Praktikum3_NIM_Nama.zip
```

Isi di dalam arsip ZIP:
- Direktori `config/` (beserta `db.php`)
- Direktori `database/` (beserta `store_db.sql`)
- Direktori `public/` (beserta `assets/style.css`, `helpers.php`, `header.php`, `footer.php`, `index.php`, `create.php`, `edit.php`, `delete.php`)
- Dokumen `README.md`
