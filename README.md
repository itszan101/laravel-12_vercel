# Laravel 12 Deployment on Vercel with Supabase Database

Proyek ini merupakan aplikasi **Laravel 12 (PHP 8.3)** yang dideploy menggunakan **Vercel** sebagai platform serverless dan **Supabase** sebagai database cloud (PostgreSQL / MySQL compatible).

Repository ini ditujukan sebagai implementasi Laravel di Vercel dengan konfigurasi yang aman, minimal, dan siap production.

---

## Teknologi yang Digunakan

- PHP 8.3  
- Laravel 12.21.0  
- Supabase (PostgreSQL / MySQL)  
- Vercel (Serverless Hosting)  
- Composer  
- Node.js (Vercel CLI)

---

## Persiapan Lokal

Pastikan environment lokal memenuhi kebutuhan berikut:

1. PHP versi 8.3
2. Composer
3. Akun Supabase atau database cloud lain
4. Node.js dan npm
5. Vercel CLI

Jika menggunakan Livewire atau storage, jalankan:

```bash
php artisan livewire:publish --assets
php artisan storage:link
```

---

## Konfigurasi Database (Supabase)

1. Buat project baru di Supabase.
2. Ambil kredensial database:
   - Host
   - Port
   - Database name
   - Username
   - Password
3. Pastikan koneksi database dapat diakses secara publik.
4. Laravel dapat menggunakan:
   - `pgsql` (disarankan)
   - `mysql` (jika menggunakan MySQL compatible)

Konfigurasi database **tidak disimpan di `.env` production**, melainkan di Vercel Environment Variables.

---

## Konfigurasi Deployment ke Vercel

### 1. Entry Point Serverless

Buat folder `api` dan file `index.php`:

```php
<?php
require __DIR__ . '/../public/index.php';
```

---

### 2. Folder Output

```bash
mkdir dist
touch dist/.gitkeep
```

---

### 3. File `.vercelignore`

```
/vendor
```

---

### 4. File `vercel.json`

```json
{
  "version": 2,
  "framework": null,
  "functions": {
    "api/index.php": {
      "runtime": "vercel-php@0.7.3"
    }
  },
  "routes": [
    {
      "src": "/(.*\\.(?:css|js|png|jpg|jpeg|gif|svg|ico|ttf|woff|woff2|eot|otf|webp|avif))$",
      "dest": "/public/$1"
    },
    {
      "src": "/livewire/update",
      "dest": "/api/index.php"
    },
    {
      "src": "/livewire/(.*)",
      "dest": "/public/vendor/livewire/$1"
    },
    {
      "src": "/storage/(.*)",
      "dest": "/public/storage/$1"
    },
    {
      "src": "/favicon.ico",
      "dest": "/public/favicon.ico"
    },
    {
      "src": "/(.*)",
      "dest": "/api/index.php"
    }
  ],
  "env": {
    "APP_NAME": "Laravel",
    "APP_ENV": "production",
    "APP_KEY": "base64:CHANGE_THIS_KEY",
    "APP_DEBUG": "false",
    "APP_URL": "https://your-app-name.vercel.app",

    "APP_CONFIG_CACHE": "/tmp/config.php",
    "APP_EVENTS_CACHE": "/tmp/events.php",
    "APP_PACKAGES_CACHE": "/tmp/packages.php",
    "APP_ROUTES_CACHE": "/tmp/routes.php",
    "APP_SERVICES_CACHE": "/tmp/services.php",
    "VIEW_COMPILED_PATH": "/tmp",

    "CACHE_DRIVER": "array",
    "LOG_CHANNEL": "stderr"
  },
  "outputDirectory": "public"
}
```

### Modifikasi apabila menggunakan "api" route di baris "APP_URL" menjadi seperti berikut :

```bash
"APP_URL": "https://link-to-your-app.vercel.app/api/api/",
```

---

### Environment Variables di Vercel

Tambahkan variabel berikut melalui Dashboard Vercel:

```env
DB_CONNECTION=pgsql
DB_HOST=
DB_PORT=
DB_DATABASE=
DB_USERNAME=
DB_PASSWORD=
```

---

## Force HTTPS di Production

Edit `app/Providers/AppServiceProvider.php`:

```php
use Illuminate\Support\Facades\URL;

public function boot(): void
{
    if (config('app.env') === 'production') {
        URL::forceScheme('https');
    }
}
```

---

## Deploy ke Vercel

```bash
npm install -g vercel
vercel login
vercel
```

---

## Catatan Penting

- Vercel tidak mendukung filesystem persisten.
- Gunakan Supabase Storage, S3, atau Cloudflare R2 untuk upload file.
- Jangan gunakan cache driver `file`, `database`, atau `redis` di Vercel.

---

## Lisensi

MIT License
