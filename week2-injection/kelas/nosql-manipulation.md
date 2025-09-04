## Challenge 7: NoSQL Manipulation

### 1. [Soal]

-   **Nama Challenge:** NoSQL Manipulation
-   **Deskripsi:** Update multiple product reviews at the same time.
-   **Kategori:** Injection
-   **Tingkat Kesulitan:** ★★★☆☆☆

### 2. [Link resource untuk latihan.]

`http://127.0.0.1:3000/`

### 3. Jawaban + bukti

#### Step-by-step yang sudah dicoba

1.  **Membuat Ulasan Awal:** Pertama, saya login ke akun pengguna dan menavigasi ke halaman produk mana pun untuk menulis ulasan baru sebagai titik awal.

2.  **Menganalisis Permintaan Edit:** Saya membuka Developer Tools (F12) ke tab **Network**. Kemudian, saya mengklik tombol "Edit" pada ulasan yang baru dibuat, mengubah sedikit teksnya, dan menekan "Submit". Ini memungkinkan saya untuk menangkap request `PATCH` yang dikirim ke API endpoint `/api/Feedbacks/`.

3.  **Identifikasi Kerentanan:** Dengan memeriksa *request body* dari request `PATCH`, saya melihat struktur JSON yang dikirim, contohnya: `{"id": "...", "message": "..."}`. Tantangan ini mengindikasikan adanya kerentanan **NoSQL Injection**, di mana kita bisa memanipulasi struktur query MongoDB, bukan hanya nilainya.

4.  **Penyusunan Payload Injeksi:**
    -   Tujuannya adalah memperbarui banyak ulasan sekaligus. Operator MongoDB yang cocok untuk ini adalah `$ne` (not equal).
    -   Saya memanipulasi field `id` untuk mencocokkan semua dokumen. Payload `{"$ne": null}` berarti "pilih semua dokumen yang `id`-nya tidak kosong". Karena semua dokumen pasti memiliki `_id`, ini akan menargetkan semua ulasan.
    -   *Request body* akhir yang dimodifikasi menjadi:
        ```json
        {
          "id": {"$ne": null},
          "message": "All your reviews are belong to us!"
        }
        ```

5.  **Eksekusi Serangan:** Saya mengirim ulang request yang telah dimodifikasi (menggunakan fitur "Edit and Resend" di browser). Setelah me-refresh halaman produk, semua ulasan telah berubah sesuai dengan pesan injeksi saya.

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Hasil:** Berhasil.
-   **Alasan:** Serangan berhasil karena aplikasi tidak melakukan validasi skema atau tipe data pada *request body* yang diterima. Ini memungkinkan saya untuk mengganti nilai `id` dari *string* menjadi *objek*, yang kemudian diinterpretasikan oleh MongoDB sebagai operator query (`$ne`). Akibatnya, operasi `update` diterapkan pada semua dokumen, bukan hanya satu.

