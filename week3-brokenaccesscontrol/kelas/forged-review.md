## Forged Review

### 4.1. [Soal]

-   **Nama Challenge:** Forged Review
-   **Deskripsi:** Memposting sebuah *review* produk atas nama pengguna lain, ATAU mengedit sebuah *review* yang sudah ada milik pengguna lain.
-   **Tingkat Kesulitan:** ★★★★★☆

### 4.2. [Link resource untuk latihan.]

`OWASP Juice Shop (https://tryhackme.com/room/owaspjuiceshop)`

### 4.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba (Metode A: Memposting Review Baru)

1.  Saya mengirim *review* produk normal dan menangkap *request* `PUT /rest/products/X/reviews` di tab Network.
2.  Saya melihat *body* JSON berisi `message` dan `author`, di mana `author` diisi dengan email saya.
3.  Saya menggunakan "Copy as fetch" pada *request* `PUT` tersebut.
4.  Di Console, saya memodifikasi string *body* JSON dan mengubah nilai `author` menjadi email pengguna lain (misalnya, `bender@juice-sh.op`).
5.  Setelah dijalankan, *review* baru muncul atas nama Bender, menyelesaikan tantangan.

#### Step-by-step yang sudah dicoba (Metode B: Mengedit Review Orang Lain - IDOR)

1.  Saya menemukan ID *review* target dengan memeriksa respons dari *request* `GET /rest/products/X/reviews` di tab Network.
2.  Saya menggunakan "Copy as fetch" pada *request* `GET` yang terotentikasi.
3.  Di Console, saya menempelkan kode `fetch` dan memodifikasinya secara signifikan:
    -   Mengubah URL menjadi endpoint edit: `/api/Reviews/Y` (di mana Y adalah ID target).
    -   Mengubah metode menjadi `"PATCH"`.
    -   Mengubah *body* menjadi JSON yang hanya berisi pesan baru: `{\"message\":\"This review was hacked!\"}`.
4.  Setelah dijalankan, *review* asli milik pengguna lain di halaman produk berubah isinya.

#### Bukti Screenshot

#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Aplikasi ini rentan dalam dua hal: (1) Saat membuat *review*, ia memercayai parameter `author` dari klien. (2) Saat mengedit *review*, ia menderita kerentanan **IDOR** karena tidak memverifikasi kepemilikan dari ID *review* yang akan di-`PATCH`.
-   **Refleksi:** Validasi di sisi server harus diterapkan pada semua operasi CRUD (*Create, Read, Update, Delete*). Baik saat membuat data baru maupun saat memodifikasi data yang sudah ada, server harus selalu memastikan pengguna memiliki hak akses yang sesuai berdasarkan identitas dari *session token* mereka.