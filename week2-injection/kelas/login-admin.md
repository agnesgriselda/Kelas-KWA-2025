# Challenge: Login Admin

### 1.1. [Soal]

-   **Nama Challenge:** Login Admin
-   **Deskripsi:** Log in with the administrator's user account.
-   **Tingkat Kesulitan:** ★★☆☆☆☆

### 1.2. [Link Resource untuk Latihan]

-   **Endpoint Rentan:** Halaman Login
-   **URL:** `http://127.0.0.1:3000/#/login`

### 1.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  **Analisis Kerentanan:** Kerentanan utama pada form login ini adalah **SQL Injection**. Aplikasi menggabungkan input dari kolom email secara langsung ke dalam string query SQL di backend tanpa proses sanitasi atau *parameterized query*.

2.  **Tujuan Serangan:** Mem-bypass validasi password untuk akun `admin`.

3.  **Penyusunan Payload:**
    -   Target email admin pada Juice Shop adalah `admin@juice-sh.op`.
    -   Untuk memanipulasi query, kita perlu "menutup" string email dengan menambahkan karakter kutip tunggal (`'`).
    -   Setelah itu, kita menggunakan karakter komentar SQL (`--` atau `#`) untuk membuat database mengabaikan sisa query, yaitu bagian `AND password = '...'`.

4.  **Eksekusi Serangan:**
    -   Buka halaman Login.
    -   Pada kolom **Email**, masukkan payload: `admin@juice-sh.op'--`
    -   Pada kolom **Password**, masukkan teks acak (contoh: `password`).
    -   Klik tombol "Log in".

#### Bukti Screenshot

#### Catatan Hasil Percobaan

-   **Hasil:** Berhasil.

-   **Alasan:** Serangan berhasil karena payload yang dimasukkan mengubah query SQL di backend.

    -   **Query Asli (diharapkan):**
        ```sql
        SELECT * FROM Users WHERE email = 'admin@juice-sh.op' AND password = '<hashed_password>' AND deletedAt IS NULL
        ```

    -   **Query Setelah Dimanipulasi:**
        ```sql
        SELECT * FROM Users WHERE email = 'admin@juice-sh.op'--' AND password = '...' AND deletedAt IS NULL
        ```
    Database hanya mengeksekusi `SELECT * FROM Users WHERE email = 'admin@juice-sh.op'` dan mengabaikan sisa query, sehingga password tidak pernah diperiksa.
