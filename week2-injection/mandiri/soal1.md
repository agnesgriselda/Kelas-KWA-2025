## 1. SQL Injection: Bypass Otentikasi Login

### 1.1. [Soal]

-   **Nama Laboratorium:** SQL injection vulnerability allowing login bypass
-   **Deskripsi:** Laboratorium ini memiliki kerentanan SQL injection pada halaman login. Tujuannya adalah untuk login ke dalam aplikasi sebagai pengguna `administrator` tanpa mengetahui passwordnya.

### 1.2. [Link resource untuk latihan.]

`https://portswigger.net/web-security/sql-injection/lab-login-bypass`

### 1.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  **Analisis Halaman Login:** Saya membuka halaman login dan melihat ada dua input: `username` dan `password`. Di belakang layar, query SQL untuk memverifikasi login kemungkinan besar terlihat seperti ini:
    ```sql
    SELECT * FROM users WHERE username = 'INPUT_USER' AND password = 'INPUT_PASS'
    ```
    Tujuan saya adalah membuat kondisi `WHERE` selalu bernilai *true* untuk pengguna `administrator`.

2.  **Mencoba Payload SQL Injection Sederhana:** Saya akan fokus pada field `username` untuk menyuntikkan payload. Ide saya adalah memasukkan username `administrator` lalu mengomentari sisa query SQL agar bagian pengecekan password diabaikan.
    -   **Payload yang dicoba:**
        -   **Username:** `administrator'--`
        -   **Password:** `apapun` (tidak penting karena akan dikomentari)
    -   Karakter `'` digunakan untuk menutup string username, dan `--` adalah sintaks komentar dalam SQL yang akan mengabaikan sisa query.

3.  **Eksekusi dan Hasil:** Saya memasukkan payload tersebut ke form login dan menekan tombol "Log in".
    -   Query yang dieksekusi di server menjadi seperti ini:
        ```sql
        SELECT * FROM users WHERE username = 'administrator'--' AND password = '...'
        ```
    -   Bagian `AND password = '...'` diabaikan, sehingga query hanya mencari pengguna dengan username `administrator`. Karena pengguna tersebut ada, server menganggap otentikasi berhasil.

#### Bukti Screenshot

<img width="1897" height="1058" alt="Screenshot 2025-09-07 220634" src="https://github.com/user-attachments/assets/f260a679-85a1-4d5a-9b8c-2a3be3f350d8" />

<img width="1893" height="1057" alt="Screenshot 2025-09-07 220652" src="https://github.com/user-attachments/assets/f5b4a065-9b7b-4f19-8790-fb3852b68924" />


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Aplikasi menggabungkan input username langsung ke dalam query SQL tanpa validasi atau sanitasi. Ini memungkinkan saya untuk memanipulasi struktur query SQL dengan menyisipkan karakter kontrol (`'`) dan komentar (`--`).
-   **Refleksi:** Ini adalah salah satu bentuk SQL Injection yang paling mendasar. Cara terbaik untuk mencegahnya adalah dengan menggunakan *Parameterized Queries* (atau *Prepared Statements*). Dengan cara ini, input dari pengguna selalu diperlakukan sebagai data, bukan sebagai bagian dari perintah SQL yang bisa dieksekusi.
