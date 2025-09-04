## Challenge 8: User Credentials

### 1. [Soal]

-   **Nama Challenge:** User Credentials
-   **Deskripsi:** Retrieve a list of all user credentials via SQL Injection.
-   **Kategori:** Injection
-   **Tingkat Kesulitan:** ★★★★★☆

### 2. [Link resource untuk latihan.]

`http://127.0.0.1:3000/`

### 3. Jawaban + bukti

#### Step-by-step yang sudah dicoba

1.  **Identifikasi Titik Injeksi:** Sama seperti tantangan sebelumnya, fungsionalitas pencarian produk diidentifikasi sebagai titik masuk yang rentan terhadap **SQL Injection**.

2.  **Menentukan Jumlah Kolom:** Langkah pertama adalah menentukan jumlah kolom yang dipilih oleh query pencarian asli. Saya menggunakan teknik `ORDER BY`.
    -   Payload `test')) ORDER BY 9--` berhasil.
    -   Payload `test')) ORDER BY 10--` gagal.
    -   Ini mengkonfirmasi bahwa query asli memilih **9 kolom**.

3.  **Menyusun Payload `UNION SELECT`:**
    -   Tujuannya adalah mengambil data dari tabel `Users`. Saya perlu membuat query `SELECT` yang juga memilih 9 kolom dari tabel `Users`.
    -   Saya menebak nama-nama kolom yang umum seperti `id`, `email`, `password`, `role`, dll.

4.  **Eksekusi Serangan:**
    -   Setelah beberapa kali percobaan untuk mencocokkan kolom, payload final yang berhasil ditemukan adalah:
        ```sql
        x')) UNION SELECT id,email,password,role,deluxeToken,lastLoginIp,profileImage,totpSecret,isActive FROM Users--
        ```
    -   Payload ini dimasukkan ke dalam **kolom pencarian (search bar)**.
    -   `x'))` digunakan untuk "keluar" dari sintaks query pencarian yang asli.
    -   `UNION SELECT ... FROM Users` menempelkan hasil dari tabel `Users`.
    -   `--` mengomentari sisa query asli untuk menghindari error.

5.  **Analisis Hasil:** Setelah menekan Enter, halaman menampilkan daftar pengguna seolah-olah mereka adalah produk. Nama "produk" adalah email pengguna, dan deskripsi/harga "produk" adalah hash password dan role mereka.

#### Bukti Screenshot

#### Catatan hasil percobaan

-   **Hasil:** Berhasil.
-   **Alasan:** Serangan `UNION SELECT` berhasil karena payload yang dibuat cocok dengan jumlah kolom dari query pencarian produk yang asli. Ini memungkinkan saya untuk "menjahit" hasil dari tabel `Users` yang sensitif ke dalam hasil pencarian produk yang tidak sensitif, sehingga membocorkan seluruh kredensial pengguna.
