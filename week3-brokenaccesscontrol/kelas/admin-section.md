## Admin Section

### 2.1. [Soal]

-   **Nama Challenge:** Admin Section
-   **Deskripsi:** Mengakses bagian administrasi toko.
-   **Tingkat Kesulitan:** ★☆☆☆☆☆

### 2.2. [Link resource untuk latihan.]

`OWASP Juice Shop (https://github.com/juice-shop/juice-shop)`

### 2.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba (Metode: Forced Browsing & Analisis Kode)

1.  Sama seperti tantangan sebelumnya, saya berasumsi halaman admin tidak memiliki tautan publik dan harus diakses melalui URL langsung.
2.  Saya membuka Developer Tools (F12) -> **Sources** dan menggunakan fitur pencarian (Ctrl+Shift+F).
3.  Saya mencari kata kunci yang relevan seperti `admin` di semua file.
4.  Pencarian menemukan definisi rute di `main.js` yang menunjukkan *path* `administration`.
5.  Saya menyusun URL lengkap: `http://localhost:3000/#/administration`.
6.  Saya mengakses URL tersebut langsung di browser tanpa perlu login.
7.  Halaman "Administrator Registration" langsung terbuka, yang menandakan keberhasilan menyelesaikan tantangan.

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Aplikasi ini menderita kerentanan **Broken Access Control** yang sangat kritis, yaitu *Missing Authorization Check*. Halaman administrasi yang seharusnya sangat terbatas aksesnya ternyata tidak memiliki mekanisme pengecekan sama sekali, baik untuk autentikasi (apakah pengguna sudah login) maupun otorisasi (apakah pengguna adalah admin).
-   **Refleksi:** Semua endpoint yang bersifat sensitif atau administratif harus dilindungi di sisi server. Setiap permintaan ke endpoint tersebut harus divalidasi untuk memastikan bahwa sesi pengguna valid dan memiliki peran (*role*) yang sesuai (misalnya, 'admin'). Akses harus ditolak secara default (*deny-by-default*).