## View Basket

### 1.1. [Soal]

-   **Nama Challenge:** View Another User's Basket
-   **Deskripsi:** Melihat keranjang belanja (*shopping basket*) milik pengguna lain.
-   **Tingkat Kesulitan:** ★★☆☆☆☆

### 1.2. [Link resource untuk latihan.]

`OWASP Juice Shop (https://tryhackme.com/room/owaspjuiceshop)`

### 1.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  Saya membuka Developer Tools (F12) -> tab "Network" dan me-refresh halaman untuk menemukan *request* yang memuat data keranjang. Saya menemukan *request* `GET` ke endpoint `/rest/basket/X` (di mana X adalah ID keranjang saya).
2.  Percobaan pertama menggunakan `fetch('/rest/basket/Y')` di Console gagal dengan error `401 Unauthorized`. Ini karena *request* sederhana tidak menyertakan token otentikasi.
3.  Solusinya, saya kembali ke tab Network, klik kanan pada *request* `/rest/basket/X` yang valid, dan memilih "Copy" -> "Copy as fetch".
4.  Saya menempelkan kode `fetch` yang lengkap (termasuk *header* otentikasi) ke dalam Console.
5.  Saya hanya mengubah satu hal: ID keranjang di URL, dari ID saya menjadi ID lain (misalnya, dari `.../basket/1` menjadi `.../basket/2`).
6.  Setelah menjalankan kode yang dimodifikasi, console mencetak data JSON dari keranjang pengguna lain, dan tantangan terselesaikan.

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Kerentanan ini adalah **Insecure Direct Object Reference (IDOR)**. Aplikasi menggunakan ID objek yang dapat diprediksi di URL, tetapi gagal memvalidasi di sisi server apakah pengguna yang terotentikasi (berdasarkan *session token*) benar-benar pemilik dari objek (keranjang) yang diminta.
-   **Refleksi:** "Copy as fetch" adalah teknik yang sangat kuat untuk mereplikasi *request* yang terotentikasi. Ini membuktikan bahwa tanpa validasi kepemilikan di backend, otentikasi saja tidak cukup untuk mengamankan data.