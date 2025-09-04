## Challenge 6: Ephemeral Accountant

### 1. [Soal]

Tantangan ini menuntut kita untuk login sebagai pengguna yang tidak ada (`acc0unt4nt@juice-sh.op`) tanpa pernah mendaftarkannya. Pengguna ini harus "diciptakan" secara sementara hanya selama proses login.

### 2. [Link resource untuk latihan.]

`http://127.0.0.1:3000/`

### 3. Jawaban + bukti

#### Step-by-step yang sudah dicoba

1.  **Identifikasi Target:** Targetnya adalah endpoint API login: `POST /rest/user/login`. Tujuannya adalah menyuntikkan SQL melalui body JSON dari permintaan ini.

2.  **Strategi:** Menggunakan injeksi SQL berbasis `UNION SELECT` di dalam field `email`. Payload akan dirancang untuk membuat baris pengguna palsu yang lengkap di dalam hasil kueri, yang akan diterima oleh aplikasi sebagai pengguna yang valid.

3.  **Percobaan 1: `curl` di Command Prompt (cmd.exe) - GAGAL**
    -   Sebuah perintah `curl` satu baris dibuat untuk mengirim payload.
    -   **Hasil:** Gagal dengan error `SyntaxError: ... is not valid JSON` dan `curl: (3) URL rejected`.
    -   **Alasan:** Command Prompt Windows memiliki aturan penanganan kutip (`'` dan `"`) yang rumit dan berbeda dari Linux, menyebabkan string JSON menjadi rusak saat diinterpretasikan.

4.  **Percobaan 2: `curl` di PowerShell - GAGAL**
    -   Percobaan dipindahkan ke PowerShell, yang diharapkan memiliki penanganan kutip yang lebih baik. Beberapa variasi perintah dicoba.
    -   **Hasil:** Gagal dengan berbagai error seperti `Cannot convert...` (untuk `-H`) dan `parameter name 'X' cannot be found` (untuk `-X`).
    -   **Alasan:** `curl` di PowerShell hanyalah alias untuk `Invoke-WebRequest`, yang memiliki sintaks parameter yang sama sekali berbeda (`-Headers @{...}` dan `-Method POST`). Mencoba menggunakan sintaks `curl` standar tidak akan berhasil.

5.  **Percobaan 3: Browser Console `fetch` - BERHASIL**
    -   Karena kegagalan berulang di command line, metode diganti dengan menggunakan fungsi `fetch` di dalam Developer Tools Console browser. Metode ini menghilangkan semua masalah terkait *shell quoting*.
    -   **Kode Berhasil:**
        ```javascript
        fetch('http://127.0.0.1:3000/rest/user/login', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                "email": "' UNION SELECT * FROM (SELECT 20 AS `id`, 'acc0unt4nt@juice-sh.op' AS `username`, 'acc0unt4nt@juice-sh.op' AS `email`, 'test1234' AS `password`, 'accounting' AS `role`, '123' AS `deluxeToken`, '1.2.3.4' AS `lastLoginIp`, '/assets/public/images/uploads/default.svg' AS `profileImage`, '' AS `totpSecret`, 1 AS `isActive`, 12983283 AS `createdAt`, 133424 AS `updatedAt`, NULL AS `deletedAt`) AS tmp WHERE '1'='1';--",
                "password": "test1234"
            })
        });
        ```
    -   **Hasil:** Perintah berhasil dieksekusi. Setelah me-refresh halaman, login berhasil sebagai `acc0unt4nt@juice-sh.op`.

#### Bukti Screenshot

<img width="1917" height="1074" alt="Screenshot 2025-09-04 190912" src="https://github.com/user-attachments/assets/c97baa75-0ef9-4991-beda-b154834bc129" />


<img width="1896" height="1065" alt="Screenshot 2025-09-04 190949" src="https://github.com/user-attachments/assets/9bd3d201-4b9f-4a59-916f-728f72304a7f" />



#### Catatan hasil percobaan

-   **Berhasil/Gagal:** Gagal total saat menggunakan `curl` di cmd.exe dan PowerShell. Berhasil dengan mudah menggunakan `fetch` di browser console.
-   **Alasan:** Kompleksitas *escaping* tanda kutip di dalam string JSON yang dikirim sebagai argumen command line sangat rentan terhadap kesalahan. Setiap shell (cmd, powershell, bash) memiliki aturannya sendiri. Browser, sebagai lingkungan JavaScript asli, dapat menangani objek dan string JSON secara sempurna tanpa masalah interpretasi.
