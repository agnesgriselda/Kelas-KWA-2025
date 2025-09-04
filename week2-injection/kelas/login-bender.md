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

<img width="712" height="638" alt="Screenshot 2025-09-04 164146" src="https://github.com/user-attachments/assets/d2ef944f-4e95-4cee-9b98-d048aca2a95b" />

<img width="1899" height="1070" alt="Screenshot 2025-09-04 163524" src="https://github.com/user-attachments/assets/510e93f7-b85e-47f9-8c82-403f0ab37073" />


<img width="335" height="303" alt="Screenshot 2025-09-04 163540" src="https://github.com/user-attachments/assets/92094b14-876b-493e-89b5-2ebd9230751e" />

<img width="1722" height="277" alt="Screenshot 2025-09-04 163600" src="https://github.com/user-attachments/assets/7b83e366-b7e9-4fe3-956f-a2e13efc34f8" />


#### Catatan Hasil Percobaan

-   **Hasil:** Berhasil.

-   **Alasan:** Kerentanan SQL Injection yang sama memungkinkan pengambilalihan akun pengguna mana pun selama emailnya diketahui.
