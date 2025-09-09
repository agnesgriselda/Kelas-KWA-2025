## 2. Reflected Cross-Site Scripting (XSS)

### 2.1. [Soal]

-   **Nama Laboratorium:** Reflected XSS into HTML context with nothing encoded
-   **Deskripsi:** Laboratorium ini memiliki kerentanan Reflected Cross-Site Scripting (XSS) sederhana pada fungsi pencarian. Tujuannya adalah menyuntikkan script yang akan menjalankan fungsi `alert(1)` di browser.

### 2.2. [Link resource untuk latihan.]

`https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded`

### 2.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  **Mencari Titik Refleksi:** Saya melihat ada fitur pencarian di halaman utama. Saya mencoba memasukkan teks acak, misalnya `test`, dan melihat apakah teks tersebut muncul kembali (direfleksikan) di halaman hasil.
    -   **Input:** `test`
    -   **Hasil:** Halaman menampilkan "You searched for: test". Ini adalah titik refleksi yang potensial.

2.  **Mencoba Injeksi HTML Sederhana:** Untuk memastikan browser me-render input saya sebagai HTML, saya mencoba menyuntikkan tag HTML sederhana.
    -   **Payload yang dicoba:** `<h1>Hello</h1>`
    -   Jika berhasil, kata "Hello" akan muncul sebagai heading besar, bukan sebagai teks biasa. Dan ternyata berhasil!

3.  **Injeksi Payload XSS:** Karena input saya dirender sebagai HTML, sekarang saya bisa mencoba menyuntikkan tag `<script>`. Payload standar untuk pengujian adalah `<script>alert(1)</script>`.
    -   **Payload Final:** `<script>alert(1)</script>`
    -   Saya memasukkan payload ini ke dalam kotak pencarian dan menekan Enter.

4.  **Verifikasi:** Seketika halaman dimuat, sebuah kotak dialog `alert` dengan angka `1` muncul di browser. Ini menandakan bahwa JavaScript saya berhasil dieksekusi. Laboratorium pun terselesaikan.

#### Bukti Screenshot

<img width="1896" height="1060" alt="Screenshot 2025-09-07 220916" src="https://github.com/user-attachments/assets/e306f330-743f-4964-8ee4-6897327cfded" />


<img width="1890" height="1052" alt="Screenshot 2025-09-07 220952" src="https://github.com/user-attachments/assets/0c8435b4-50eb-4b45-b060-c23ce70bdd9f" />


<img width="1919" height="1058" alt="Screenshot 2025-09-07 221021" src="https://github.com/user-attachments/assets/e500feaa-8b96-4cbb-ac86-86ad5059bf44" />


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Aplikasi mengambil input dari parameter pencarian dan menampilkannya kembali di halaman HTML tanpa melakukan *output encoding*. Karakter khusus seperti `<` dan `>` tidak diubah menjadi entitas HTML (`&lt;` dan `&gt;`), sehingga browser menginterpretasikannya sebagai tag HTML dan mengeksekusi script yang disuntikkan.
-   **Refleksi:** Kerentanan XSS sangat umum terjadi. Mitigasi utamanya adalah melakukan *Context-Aware Output Encoding*. Artinya, setiap data yang berasal dari pengguna harus di-encode dengan benar sesuai dengan konteks di mana data itu akan ditempatkan (misalnya, di dalam tag HTML, di dalam atribut, atau di dalam script).
