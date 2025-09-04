## 4. Challenge: Database Schema

### 4.1. [Soal]

-   **Nama Challenge:** Database Schema
-   **Deskripsi:** Exfiltrate the entire DB schema definition via SQL Injection.
-   **Tingkat Kesulitan:** ★★★★☆☆

### 4.2. [Link Resource untuk Latihan]

-   **Endpoint Rentan:** API Pencarian Produk
-   **URL:** `http://127.0.0.1:3000/rest/products/search?q=`

### 4.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  **Analisis Kerentanan:** Tujuannya adalah untuk mengambil data internal (skema database), bukan untuk login. Metode yang paling cocok untuk ini adalah **`UNION SELECT` SQL Injection**. Kerentanan ini terdapat pada fungsionalitas pencarian produk.

2.  **Identifikasi Titik Injeksi:** Meskipun kolom pencarian di halaman web bisa digunakan, cara yang paling andal adalah dengan menyerang API endpoint secara langsung. Ini untuk menghindari validasi atau encoding dari sisi frontend (JavaScript). Endpoint yang rentan adalah `/rest/products/search` dengan parameter `q`.

3.  **Menentukan Jumlah Kolom:** Sebelum melakukan `UNION SELECT`, jumlah kolom dari query asli harus diketahui. Melalui metode *trial-and-error* (mencoba `UNION SELECT` dengan jumlah kolom yang berbeda-beda hingga tidak ada error), ditemukan bahwa query asli memilih **9 kolom**.

4.  **Menemukan Sumber Skema Database:** OWASP Juice Shop menggunakan database **SQLite**. Informasi skema pada SQLite disimpan dalam tabel master yang disebut `sqlite_master` atau aliasnya `sqlite_schema`. Kolom yang berisi definisi tabel (`CREATE TABLE ...`) adalah kolom `sql`.

5.  **Penyusunan Payload:** Payload dirancang untuk:
    -   "Keluar" dari query pencarian asli menggunakan sintaks `'))`.
    -   Menggunakan `UNION SELECT` dengan 9 kolom.
    -   Menempatkan kolom `sql` dari `sqlite_schema` pada salah satu posisi agar datanya dikembalikan oleh API. Posisi ke-9 terbukti berhasil.

6.  **Eksekusi Serangan:**
    -   Payload yang digunakan adalah:
        ```
        test')) UNION SELECT 1,2,3,4,5,6,7,8,sql FROM sqlite_schema--
        ```
    -   Payload ini di-encode untuk URL dan digabungkan dengan endpoint API, lalu diakses langsung melalui *address bar* browser.
    -   URL final yang dieksekusi:
        ```http
        http://127.0.0.1:3000/rest/products/search?q=test'))%20UNION%20SELECT%201,2,3,4,5,6,7,8,sql%20FROM%20sqlite_schema--
        ```

#### Bukti Screenshot


#### Catatan Hasil Percobaan

-   **Hasil:** Berhasil.

-   **Alasan:** Dengan menyerang API endpoint secara langsung, payload `UNION SELECT` berhasil dieksekusi oleh database. Server kemudian mengembalikan hasil gabungan dari query produk yang asli dan query berbahaya kita. Hasilnya adalah data JSON yang tidak hanya berisi produk, tetapi juga seluruh definisi skema dari tabel `sqlite_schema`, yang persis seperti tujuan tantangan.
