# Laravel 12 on Vercel with Free Cloud Database

Proyek ini adalah aplikasi Laravel 12 (PHP 8.3) yang di-deploy ke **Vercel** menggunakan **database MySQL dari free cloud platform**.  
Semua konfigurasi `.env` untuk database wajib di-set sebelum deployment.

---

## 🚀 Teknologi yang Digunakan
- **PHP** 8.3
- **Laravel** 12
- **MySQL** (Free Cloud DB)
- **Vercel** sebagai hosting

---

## 📦 Persiapan Sebelum Deploy
1. Pastikan **PHP 8.3** sudah terinstall di lokal.
2. Pastikan **Composer** sudah terinstall.
3. Pastikan kamu punya **database cloud** (misalnya dari [filess.io](https://filess.io) atau platform lainnya) dan catat host, port, username, password, dan nama database.
4. Edit file `.env` sesuai database kamu.
5. Publish aset jika diperlukan / menggunakan library lain
```bash
php artisan livewire:publish --assets
php artisan storage:link
```

---

## ⚙️ Konfigurasi Deployment ke Vercel

### 1. Buat folder `api` dan file `index.php`
```php
<?php
require __DIR__ . "/../public/index.php";
```

### 2. Buat folder `dist` dengan file kosong `.gitkeep`
```bash
mkdir dist
touch dist/.gitkeep
```

### 3. Buat file `.vercelignore`
```
/vendor
```

### 4. Buat file `vercel.json`
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
        "APP_ENV": "local",
        "APP_KEY": "base64:d71R5oYAEeZ3Bur0tFofFYOPTVBjeBvomlAIfaPV52k=",
        "APP_DEBUG": "true",
        "APP_URL": "https://link-to-your-app.vercel.app",
        "DB_CONNECTION": "mysql",
        "DB_HOST": "",
        "DB_PORT": "",
        "DB_DATABASE": "",
        "DB_USERNAME": "",
        "DB_PASSWORD": "",
        "FILESYSTEM_DISK": "",
        "AWS_ACCESS_KEY_ID": "",
        "AWS_SECRET_ACCESS_KEY": "",
        "AWS_DEFAULT_REGION": "",
        "AWS_BUCKET": "",
        "AWS_URL": "",
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

> ⚠️ **Penting:** Ubah `APP_URL`, database host, dan credential sesuai environment kamu.

### Modifikasi apabila menggunakan api route di baris "APP_URL" menjadi seperti berikut :

```bash
"APP_URL": "https://link-to-your-app.vercel.app/api/api/",
```

---

### 5. Modifikasi `app/Providers/AppServiceProvider.php`
```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        //
    }

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        // Force HTTPS in production
        if (config('app.url') == 'https://link-to-your-app.vercel.app') {
            \URL::forceScheme('https');
        }
    }
}
```

---

## 🚀 Deploy ke Vercel
1. Install Vercel CLI
   ```bash
   npm i -g vercel
   ```
2. Login ke Vercel:
   ```bash
   vercel login
   ```
3. Deploy:
   ```bash
   vercel
   ```

---