## 3. OS Command Injection

### 3.1. [Soal]

-   **Nama Laboratorium:** OS command injection, simple case
-   **Deskripsi:** Laboratorium ini memiliki kerentanan OS command injection pada fitur pengecekan stok produk. Aplikasi menjalankan perintah *shell* yang menyertakan input dari pengguna. Tujuannya adalah mengeksekusi perintah `whoami` untuk menemukan nama pengguna sistem operasi saat ini.

### 3.2. [Link resource untuk latihan.]

`https://portswigger.net/web-security/os-command-injection/lab-simple`

### 3.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba

1.  **Menganalisis Fungsi Aplikasi:** Saya mengunjungi halaman detail produk dan menemukan tombol "Check stock". Saat diklik, aplikasi mengirimkan data ke server untuk mendapatkan informasi stok. Saya menduga data yang dikirim adalah ID produk dan ID toko. Karena kita tidak bisa mengubah *request* ini dengan mudah saat tombol diklik, saya akan membuat *request* HTTP saya sendiri secara manual.

2.  **Membuka Developer Tools:** Untuk membuat *request* manual, saya akan menggunakan Developer Tools yang ada di browser.
    -   Klik kanan di mana saja pada halaman, lalu pilih "Inspect" atau "Inspect Element".
    -   Buka tab "Console". Di sini saya bisa menjalankan kode JavaScript secara langsung di halaman.

3.  **Membuat dan Mengirim Request Manual dengan JavaScript `fetch()`:** Saya akan menggunakan fungsi `fetch()` di JavaScript untuk membuat *request* POST ke endpoint `/product/stock`. Di dalam *request* ini, saya akan menyisipkan payload *command injection*. Saya mengetik atau menempelkan kode berikut ke dalam Console, lalu menekan Enter:
    ```javascript
    fetch('/product/stock', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: 'productId=1&storeId=1 | whoami'
    })
    .then(response => response.text())
    .then(data => console.log(data));
    ```
    -   **Penjelasan Kode:**
        -   `fetch('/product/stock', ...)`: Mengirim *request* ke endpoint pengecekan stok.
        -   `method: 'POST'`: Menentukan metode *request* adalah POST.
        -   `headers: ...`: Memberitahu server bahwa data yang saya kirim berformat form standar.
        -   `body: 'productId=1&storeId=1 | whoami'`: Ini adalah bagian terpenting. Saya mengirim data form dengan `productId=1` dan `storeId` yang sudah disisipi payload `| whoami`.
        -   `.then(...)`: Kode ini berfungsi untuk mengambil respons dari server dan menampilkannya di console.

4.  **Menganalisis Hasil di Console:** Setelah menekan Enter, kode JavaScript dieksekusi. Beberapa saat kemudian, output dari server muncul di console. Output tersebut berisi nama pengguna sistem, yang dalam kasus ini adalah `peter-.....`. Ini membuktikan bahwa perintah `whoami` berhasil dieksekusi di server.

#### Bukti Screenshot

<img width="1919" height="1063" alt="Screenshot 2025-09-07 224206" src="https://github.com/user-attachments/assets/20627c49-c36e-42f7-8ab5-1ee31869a984" />


<img width="1887" height="1057" alt="Screenshot 2025-09-07 224241" src="https://github.com/user-attachments/assets/0d3f8ecb-11bc-4ab2-a574-1bcd662254d1" />


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Aplikasi menggabungkan input pengguna (dari `storeId`) ke dalam perintah *shell* tanpa validasi. Dengan menggunakan Developer Console, saya bisa membuat *request* POST kustom untuk mengirim payload tanpa memerlukan *tools* eksternal seperti Burp Suite. Karakter `|` (*pipe*) berhasil memisahkan perintah asli dengan perintah `whoami` yang saya suntikkan.
-   **Refleksi:** Metode ini menunjukkan bahwa kerentanan bisa dieksploitasi bahkan tanpa *proxy tool* khusus. Memahami cara kerja HTTP dan memiliki kemampuan untuk membuat *request* secara manual menggunakan *tools* bawaan browser adalah *skill* yang sangat berguna. Pencegahan untuk kerentanan ini tetap sama: jangan pernah menggabungkan input pengguna ke dalam string perintah *shell*. Gunakan API yang aman.
