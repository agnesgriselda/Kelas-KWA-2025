## Five-Star Feedback

### 2.1. [Soal]

-   **Nama Challenge:** Five-Star Feedback
-   **Deskripsi:** Menyingkirkan (menghapus) semua *feedback* pelanggan yang memiliki rating bintang 5.
-   **Tingkat Kesulitan:** ★★★★☆☆

### 2.2. [Link resource untuk latihan.]

`OWASP Juice Shop (https://tryhackme.com/room/owaspjuiceshop)`

### 2.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  Saya login sebagai admin dan mengakses panel administrasi (`/#/administration`).
2.  Saya membuka Developer Tools -> Network dan me-refresh halaman. Saya menemukan *request* `GET /api/Feedbacks/` yang memuat semua data *feedback*. Di tab "Response", saya bisa melihat ID unik untuk setiap *feedback*.
3.  Saya menyadari tombol hapus di UI sengaja dibuat tidak berfungsi. Ini adalah petunjuk untuk berinteraksi langsung dengan API.
4.  Saya menggunakan teknik "Copy as fetch" pada *request* `GET /api/Feedbacks/` yang valid.
5.  Di Console, saya menempelkan kode `fetch` tersebut dan memodifikasinya:
    -   Mengubah URL dengan menambahkan ID *feedback* bintang 5 yang ingin dihapus (misalnya, `/api/Feedbacks/3`).
    -   Mengubah metode dari `"GET"` menjadi `"DELETE"`.
6.  Saya menjalankan kode untuk setiap *feedback* bintang 5 hingga semuanya terhapus dan tantangan terselesaikan.

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Sebagai admin, saya memiliki hak akses untuk menghapus *feedback*. Kerentanannya terletak pada UI yang rusak, yang mencoba menyembunyikan fungsionalitas ini. Dengan mem-bypass UI dan berinteraksi langsung dengan API `DELETE /api/Feedbacks/:id`, saya dapat menjalankan fungsi yang memang menjadi hak saya.
-   **Refleksi:** Antarmuka pengguna bukanlah mekanisme keamanan. Fungsionalitas yang disembunyikan atau dinonaktifkan di frontend sering kali masih dapat diakses sepenuhnya di backend API. Selalu analisis API secara langsung.