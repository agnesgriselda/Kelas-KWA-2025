## Forged Feedback

### 3.1. [Soal]

-   **Nama Challenge:** Forged Feedback
-   **Deskripsi:** Mengirimkan sebuah *feedback* atau ulasan atas nama pengguna lain.
-   **Tingkat Kesulitan:** ★★★★☆☆

### 3.2. [Link resource untuk latihan.]

`OWASP Juice Shop (https://tryhackme.com/room/owaspjuiceshop)`

### 3.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  Saya mengirim *feedback* normal dari halaman "Contact Us" dan menangkap *request* `POST /api/Feedbacks/` di tab Network.
2.  Saya memeriksa payload JSON dan melihat bahwa tidak ada `UserId` yang dikirim. Server mengidentifikasi saya dari token otentikasi.
3.  Saya menggunakan "Copy as fetch" pada *request* `POST` yang valid tersebut.
4.  Di Console, saya menempelkan kode `fetch` dan memodifikasi string `body` JSON-nya. Saya menambahkan sebuah field baru: `"UserId":1`, untuk meniru admin.
5.  Setelah menjalankan kode yang dimodifikasi, saya memeriksa halaman "About Us" dan melihat *feedback* saya muncul atas nama `admin@juice-sh.op`, menyelesaikan tantangan.

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Server secara buta memercayai parameter `UserId` jika dikirimkan dalam *request body*. Logika backend gagal memprioritaskan identitas dari *session token* yang aman, dan malah menimpanya dengan data yang bisa dimanipulasi oleh klien.
-   **Refleksi:** Backend harus selalu mengabaikan parameter terkait identitas yang dikirim dari klien dan hanya mengandalkan sumber identitas yang terpercaya (*session/token*). Ini adalah contoh klasik dari **Mass Assignment Vulnerability**.