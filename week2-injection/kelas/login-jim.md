## 2. Challenge: Login Jim

### 2.1. [Soal]

-   **Nama Challenge:** Login Jim
-   **Deskripsi:** Log in with Jim's user account.
-   **Tingkat Kesulitan:** ★★★☆☆☆

### 2.2. [Link Resource untuk Latihan]

-   **Endpoint Rentan:** Halaman Login
-   **URL:** `http://127.0.0.1:3000/#/login`

### 2.3. Jawaban Mahasiswa + Bukti

#### Step-by-step yang sudah dicoba

1.  **Analisis Kerentanan:** Kerentanan yang dieksploitasi sama dengan tantangan Login Admin, yaitu **SQL Injection** pada form login.

2.  **Tahap Pengumpulan Informasi (Reconnaissance):** Berbeda dengan admin, email Jim tidak diketahui secara umum. Langkah pertama adalah mencari tahu email Jim.
    -   Saya membuka halaman detail salah satu produk.
    -   Di bagian bawah, terdapat kolom ulasan produk (*Customer Reviews*).
    -   Dengan membaca ulasan, saya menemukan ulasan dari pengguna "Jim" beserta emailnya. Ini merupakan kerentanan *Information Disclosure*. Email Jim adalah `jim@juice-sh.op`.

3.  **Penyusunan Payload:** Menggunakan metode yang sama seperti sebelumnya, payload disusun dengan target email Jim.

4.  **Eksekusi Serangan:**
    -   Pastikan sudah logout dari akun admin.
    -   Buka halaman Login.
    -   Pada kolom **Email**, masukkan payload: `jim@juice-sh.op'--`
    -   Pada kolom **Password**, masukkan teks acak.
    -   Klik tombol "Log in".

#### Bukti Screenshot


#### Catatan Hasil Percobaan

-   **Hasil:** Berhasil.

-   **Alasan:** Mekanisme serangan identik dengan Login Admin, yaitu membypass pengecekan password melalui SQL Injection.