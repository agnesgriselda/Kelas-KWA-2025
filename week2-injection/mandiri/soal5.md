## 5. SQL Injection UNION Attack: Mengambil Data dari Tabel Lain

### 5.1. [Soal]

-   **Nama Laboratorium:** SQL injection UNION attack, retrieving data from other tables
-   **Deskripsi:** Laboratorium ini memiliki kerentanan SQL injection pada filter kategori produk. Hasil dari query asli ditampilkan di halaman. Database berisi tabel bernama `users` dengan kolom `username` dan `password`. Tujuannya adalah melakukan serangan SQL injection UNION untuk mengambil semua username dan password dari tabel `users`, lalu gunakan kredensial tersebut untuk login sebagai pengguna `administrator`.

### 5.2. [Link resource untuk latihan.]

`https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables`

### 5.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  **Menentukan Jumlah Kolom:** Tugas pertama adalah menemukan jumlah kolom yang dikembalikan oleh query asli. Saya melakukan ini dengan menyuntikkan payload `UNION SELECT` dengan jumlah `NULL` yang berbeda sampai halaman tidak lagi menampilkan error. Saya menyuntikkan payload ini langsung ke parameter `category` di URL.
    -   **Percobaan 1:** Menggunakan satu `NULL`.
        -   **Payload:** `' UNION SELECT NULL--`
        -   **Hasil:** Halaman menampilkan pesan "Internal Server Error". Ini menandakan jumlah kolom bukan satu.
    -   **Percobaan 2:** Menggunakan dua `NULL`.
        -   **Payload:** `' UNION SELECT NULL,NULL--`
        -   **Hasil:** Halaman dimuat dengan normal, tanpa pesan error. Ini adalah konfirmasi bahwa query asli mengembalikan **2 kolom**.

2.  **Menentukan Tipe Data Kolom:** Setelah mengetahui ada 2 kolom, saya perlu memastikan kolom mana yang dapat menampilkan data teks (*string*). Saya mengganti `NULL` dengan nilai string acak.
    -   **Payload:** `' UNION SELECT 'abc','def'--`
    -   **Hasil:** String `abc` dan `def` muncul di halaman, menggantikan nama produk dan deskripsi. Ini membuktikan bahwa kedua kolom dapat digunakan untuk menampilkan data string.

3.  **Mengambil Data dari Tabel `users`:** Sesuai petunjuk soal, target saya adalah tabel `users` dengan kolom `username` dan `password`. Dengan informasi yang sudah saya kumpulkan (2 kolom string), saya dapat membuat payload final untuk mengekstrak data ini.
    -   **Payload Final:** `' UNION SELECT username, password FROM users--`
    -   **Hasil:** Saya memasukkan payload ini ke URL. Halaman sekarang menampilkan daftar semua username dan password yang ada di tabel `users`. Saya dapat dengan jelas melihat kredensial untuk pengguna `administrator`.

4.  **Login sebagai Administrator:** Dengan kredensial yang berhasil diekstrak, langkah terakhir adalah login.
    -   Saya menemukan username `administrator` dan passwordnya dari daftar yang ditampilkan.
    -   Saya pergi ke halaman login "My account".
    -   Saya memasukkan kredensial tersebut dan berhasil login. Lab menampilkan pesan "Congratulations, you solved the lab!".

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Kerentanan ini ada karena aplikasi secara tidak aman menggabungkan input pengguna dari URL langsung ke dalam query SQL. Hal ini memungkinkan saya untuk:
    1.  Mengakhiri *query string* yang sah dengan karakter `'`.
    2.  Menambahkan query saya sendiri menggunakan `UNION SELECT`.
    3.  Mengomentari sisa query asli dengan `--` untuk menghindari error sintaks.
-   **Refleksi:** Serangan SQL Injection `UNION` sangat kuat karena memungkinkan penyerang tidak hanya memanipulasi query yang ada, tetapi juga membaca data arbitrer dari tabel lain di dalam database. Ini menyoroti betapa pentingnya untuk tidak pernah memercayai input pengguna. Penggunaan *Parameterized Queries* (*Prepared Statements*) adalah mitigasi paling efektif, karena memisahkan data dari perintah, sehingga input tidak akan pernah dieksekusi sebagai kode SQL.