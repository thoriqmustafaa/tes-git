# PKKMB-PKBN 2023 System

## Dependency PHP yang digunakan

-   "laravel/framework": "^8.40",
-   "laravel/jetstream": "^2.3",
-   "livewire/livewire": "^2.5",
-   "spatie/laravel-permission": "^4.2"
-   "doctrine/dbal": "^3.1"
-   "barryvdh/laravel-debugbar": "^3.6",
-   "pusher/pusher-php-server": "^7.0",
-   "google/apiclient": "^2.17",

## Dependency Javascript yang digunakan

-   "alpinejs": "^3.0.6"
-   "tailwindcss": "^2.2.2",
-   "@fortawesome/fontawesome-free": "^5.15.3",
-   "notyf": "^3.10.0"
-   "laravel-echo": "^1.11.0"
-   "pusher-js": "^7.0.3",
-   "sweetalert2": "^11.7.18"

## Panduan Instalasi

Yang dilakukan setelah clone

1. Install composer-dependency pake `composer install`
2. Install npm package pake `npm install`, ini buat install beberapa package seperti tailwind dan alphine
3. Jalankan build css nya, pake `npm run dev`
4. Copy file `.env.example` di root folder terus ganti jadi `.env`. Setting sesuai dengan environment yang digunakan
5. Buat shortcut storage, ini biar folder storage tadi bisa diakses dari folder `public`. Bisa pake `php artisan storage:link`
6. Setting database, sesuaikan nama databasenya dengan yang ada di `.env`
7. Migrasikan database, `php artisan migrate`
8. Jalankan seeder `php artisan db:seed`
9. Jalankan project nya `php artisan serve`

Tambahan

1. Untuk reset password membutuhkan email, mungkin bisa pake mailhog buat testing email di local. [download disini](https://github.com/mailhog/MailHog). Setelah dijalanin buka `http://localhost:8025/`. Kemudian setting file `.env` menjadi

```env
MAIL_HOST=localhost
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=mp2k-2021@stis.ac.id  # bebas aja
MAIL_FROM_NAME="${APP_NAME}" # bebas aja
```

2. Dibagian admin submenu absensi dan pengaduan menggunakan web socket untuk update secara real time. Library yang digunakan `Laravel Echo` dan `Pusher Js`. Agar bisa berjalan maka harus mendaftarkan aplikasi di [Pusher](https://pusher.com/). Setelah daftar copy kan keynya dan masukkan kedalam file `.env`

```env
PUSHER_APP_ID=yourpusherid
PUSHER_APP_KEY=yourpusherkey
PUSHER_APP_SECRET=yourpushersecret
PUSHER_APP_CLUSTER=yourpuhercluster
```

3. Untuk menggunakan fitur sinkronisasi data dari google sheets pada submenu formulir, input data formulir sesuai [tutorial](https://docs.google.com/document/d/1b2B-dNNWW1WgV56zGy2wHyvw7-SBC_aVoAbPaQSrgcE/edit?usp=sharing) yang telah dibuat.
4. Jangan menghapus `.env.example`, di copykan aja
5. Untuk membuat database ke settingan awal sesuai file migration dan seeder dapat menjalankan `php artisan migrate:fresh --seed`
6. Ketika pengen mengubah tabel usahakan jangan mengubah manual ataupun mengubah file migration yang udah ada. Buat aja file migration yang baru.
7. Setiap merge atau pull dari branch lain silakan cek depedency, kali aja ada yang menambahkan depedency yang baru. Jika ada jalanin aja lagi `composer install`, `npm install`, dan `npm run dev`
8. Jika masih ada yang tidak berjalan sebagaimana mestinya coba salah satu perintah dibawah ini

```cli
composer dump-autoload

php artisan route:clear
php artisan route:cache
```

## List Permission

| Nama                 | Penjelasan                                                                                        |
| -------------------- | ------------------------------------------------------------------------------------------------- |
| `akses-admin`        | Untuk bisa mengakses menu admin dan sebagai penanda utama user yang dapat bertindak sebagai admin |
| `table-pengumuman`   | Melihat daftar semua pengumuman, harus ada jika ingin memberikan permission update/create/delete  |
| `update-pengumuman`  | Melakukan update pada pengumuman                                                                  |
| `post-pengumuman`    | Menambah pengumuman                                                                               |
| `destroy-pengumuman` | Menghapus pengumuman                                                                              |

## Makna Field `category` Pada Setiap Tabel

| Tabel       | Kategori         |
| ----------- | ---------------- |
| Event       | 1 => Timeline    |
| Event       | 2 => Presensi    |
| Jenis_poin  | 1 => Bonus       |
| Jenis_poin  | 2 => Pelanggaran |
| Jenis_poin  | 3 => Penebusan   |
| Publishable | 1 => Pengumuman  |
| Publishable | 2 => Materi      |
| Publishable | 3 => Sertifikat  |

# Sinkronisasi Data Menggunakan Google Service Account dan Google Sheets API

## Cara Membuat Google Service Account

1. **Buka Google Cloud Console**: Akses [Google Cloud Console](https://console.cloud.google.com/).
2. **Buat Proyek Baru atau Gunakan yang Ada**: Pilih proyek Anda atau buat proyek baru.
3. **Aktifkan Google Sheets API**:
    - Navigasikan ke **API & Services > Library**.
    - Cari **Google Sheets API** dan klik **Enable**.
4. **Buat Google Service Account**:
    - Pergi ke **API & Services > Credentials**.
    - Klik **Create Credentials** dan pilih **Service Account**.
    - Isi detail Service Account dan simpan.
5. **Buat dan Unduh File JSON Credentials**:
    - Setelah Service Account dibuat, klik **Manage Keys**.
    - Tambahkan kunci baru dengan memilih **JSON**.
    - Rename file menjadi `credentials.json` dan save file di folder /storage/credentials/
6. **Bagikan Google Sheet dengan Service Account**:
    - Buka Google Sheet yang akan digunakan untuk sinkronisasi.
    - Bagikan akses dengan email Service Account yang diakhiri dengan `@<project-id>.iam.gserviceaccount.com`.

# Integrasi Google Sheets dengan Laravel API Menggunakan Google Apps Script

## Langkah-Langkah Implementasi

### 1. Menjalankan Ngrok untuk Pengujian Lokal

1. **Install Ngrok**
   Jika belum memiliki Ngrok, dapat mengunduhnya dari [situs resmi Ngrok](https://ngrok.com/).

    - **Unduh** versi yang sesuai dengan sistem operasi.
    - **Ekstrak** file unduhan ke folder yang diinginkan.
    - **Tambahkan** Ngrok ke PATH sistem atau simpan di lokasi yang mudah diakses.

2. **Daftar dan Dapatkan Authtoken**

    - Buka [Ngrok Dashboard](https://dashboard.ngrok.com/signup) dan buat akun gratis.
    - Setelah mendaftar, navigasikan ke tab "Auth" di dashboard Ngrok.
    - Salin authtoken yang disediakan.

3. **Masukkan Authtoken ke Ngrok**

    ```bash
    ngrok authtoken <your-auth-token>
    ```

    Gantilah <your-auth-token> dengan authtoken yang Anda salin dari dashboard Ngrok. Ini akan menyimpan authtoken di konfigurasi lokal Anda.

4. **Jalankan Server Lokal**

    ```bash
    php artisan serve
    ```

5. **Jalankan Ngrok**

    ```bash
    ngrok http 8000
    ```

    Dari output ini, Anda akan mendapatkan URL publik seperti `https://<ngrok-id>.ngrok-free.app`. URL ini dapat digunakan untuk mengakses server lokal Anda dari internet.

### 2. Mengatur Google Apps Script

1. **Buka Google Sheets**:
   Buka Google Sheets yang akan digunakan untuk formulir input data.

2. **Tambahkan Google Apps Script**:

    - Klik **Extensions** > **Apps Script**.
    - Hapus skrip default dan tambahkan skrip berikut:

    ```javascript
    function onFormSubmit(e) {
        Logger.log("onFormSubmit triggered"); // Logging untuk memastikan fungsi dipicu

        var sheet = e.source.getActiveSheet();
        var range = e.range;
        var editedRow = range.getRow();
        var nimCell = sheet.getRange(editedRow, 2); // Kolom ke-2 adalah kolom NIM, sesuaikan dengan kebutuhan

        var nim = nimCell.getDisplayValue();
        var sheetName = sheet.getName();

        // URL endpoint Laravel (ganti dengan URL Ngrok untuk pengujian lokal atau domain publik untuk produksi)
        var url = "https://<ngrok-id>.ngrok-free.app/api/google-sheets/webhook"; // atau 'https://pkkmb.stis.ac.id/2045/api/google-sheets/webhook'

        var payload = {
            nim: nim,
            sheet_name: sheetName,
        };

        var options = {
            method: "post",
            contentType: "application/json",
            payload: JSON.stringify(payload),
        };

        try {
            var response = UrlFetchApp.fetch(url, options);
            Logger.log(
                "Request sent successfully: " + response.getContentText()
            );
        } catch (error) {
            Logger.log("Error sending request: " + error.toString());
        }
    }
    ```
