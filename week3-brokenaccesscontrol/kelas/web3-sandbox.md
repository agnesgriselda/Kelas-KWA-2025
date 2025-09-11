## Web3 Sandbox

### 1.1. [Soal]

-   **Nama Challenge:** Web3 Sandbox
-   **Deskripsi:** Menemukan sebuah *code sandbox* yang tidak sengaja terpasang (*deployed*) untuk menulis *smart contract*.
-   **Tingkat Kesulitan:** ★★★☆☆☆

### 1.2. [Link resource untuk latihan.]

`OWASP Juice Shop (https://github.com/juice-shop/juice-shop)`

### 1.3. Jawaban + Bukti

#### Step-by-step yang sudah dicoba (Metode A: Forced Browsing / Menebak URL)

1.  Saya mulai dengan hipotesis bahwa *sandbox* ini berada di sebuah URL yang tersembunyi dan tidak tertaut dari halaman mana pun.
2.  Saya mencoba menebak beberapa URL umum yang biasa digunakan untuk lingkungan pengembangan, seperti `/sandbox`, `/test`, atau `/dev`.
3.  Saya mencoba tebakan yang lebih spesifik berdasarkan nama tantangan, yaitu `web3-sandbox`.
4.  Saya mengakses URL `http://localhost:3000/#/web3-sandbox` secara langsung di *address bar* browser.
5.  Halaman *sandbox* untuk *smart contract* berhasil dimuat, dan tantangan terselesaikan.

#### Step-by-step yang sudah dicoba (Metode B: Analisis Kode Sumber)

1.  Saya membuka Developer Tools di browser (F12) dan pergi ke tab **Sources**.
2.  Saya menggunakan fitur pencarian global (Ctrl+Shift+F) untuk mencari di semua file JavaScript yang dimuat oleh aplikasi.
3.  Saya mencari kata kunci `sandbox`.
4.  Hasil pencarian menunjukkan sebuah definisi rute (*route*) di dalam file `main.js` yang terlihat seperti `{ path: 'solc/sandbox', component: Web3SandboxComponent, ... }`.
5.  Berdasarkan temuan ini, saya membuat URL yang benar: `http://localhost:3000/#/solc/sandbox`.
6.  Mengakses URL tersebut juga berhasil membuka halaman Web3 Sandbox.

#### Bukti Screenshot


#### Catatan hasil percobaan

-   **Status:** Berhasil.
-   **Alasan:** Aplikasi ini rentan karena membocorkan endpoint pengembangan yang sensitif. Kerentanan ini adalah contoh dari *Security through Obscurity*, di mana keamanan hanya bergantung pada kerahasiaan URL. Tidak ada mekanisme kontrol akses (otorisasi) yang diterapkan pada endpoint ini.
-   **Refleksi:** Fitur atau alat untuk pengembangan dan debugging tidak seharusnya ada di lingkungan produksi. Jika terpaksa harus ada di lingkungan *staging*, endpoint tersebut harus dilindungi dengan mekanisme otorisasi yang ketat, sama seperti halaman administrasi, untuk memastikan hanya personel yang berwenang yang dapat mengaksesnya.