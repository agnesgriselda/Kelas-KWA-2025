## 3. Challenge: Login Bender

### 3.1. [Soal]

-   **Nama Challenge:** Login Bender
-   **Deskripsi:** Log in with Bender's user account.
-   **Tingkat Kesulitan:** ★★★☆☆☆

### 3.2. [Link Resource untuk Latihan]

-   **Endpoint Rentan:** Halaman Login
-   **URL:** `http://127.0.0.1:3000/#/login`

### 3.3. Jawaban Mahasiswa + Bukti

#### Step-by-step yang sudah dicoba

1.  **Analisis Kerentanan:** Kerentanan yang dieksploitasi masih sama, yaitu **SQL Injection** pada form login.

2.  **Tahap Pengumpulan Informasi (Reconnaissance):** Sama seperti Jim, saya perlu menemukan email Bender terlebih dahulu.
    -   Saya kembali menjelajahi ulasan-ulasan produk.
    -   Saya menemukan ulasan dari pengguna "Bender" dan mendapatkan emailnya: `bender@juice-sh.op`.

3.  **Penyusunan Payload dan Eksekusi:** Prosesnya identik dengan dua tantangan sebelumnya, hanya target emailnya yang diubah.
    -   Logout dari akun sebelumnya.
    -   Buka halaman Login.
    -   Pada kolom **Email**, masukkan payload: `bender@juice-sh.op'--`
    -   Pada kolom **Password**, masukkan teks acak.
    -   Klik tombol "Log in".

#### Bukti Screenshot


#### Catatan Hasil Percobaan

-   **Hasil:** Berhasil.

-   **Alasan:** Kerentanan SQL Injection yang sama memungkinkan pengambilalihan akun pengguna mana pun selama emailnya diketahui.
