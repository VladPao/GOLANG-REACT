# Aplikasi Manajemen User — Go + React (TypeScript)

Aplikasi web full-stack dengan autentikasi JWT dan manajemen user. Backend berupa REST API yang ditulis dengan Go (Gin + GORM + MySQL), sedangkan frontend adalah single-page app yang dibangun dengan React, TypeScript, dan Vite.

Dibuat sebagai proyek pembelajaran kelas Full-Stack Developer (Golang & React, berdasarkan modul SantriKoding).

---

## Fitur

- Registrasi akun baru dan login
- Menerima JWT saat login (berlaku 60 menit) dan menggunakannya untuk mengakses halaman yang dilindungi
- Dashboard yang menyapa user yang sedang login
- Membuat, melihat, mengubah, dan menghapus user dari panel admin
- Password di-hash dengan bcrypt sebelum disimpan

## Teknologi

| Layer     | Teknologi                                                 |
|-----------|-----------------------------------------------------------|
| Backend   | Go, Gin, GORM, MySQL, golang-jwt, bcrypt, godotenv, CORS  |
| Frontend  | React 18, TypeScript, Vite, React Router 7, TanStack Query, Axios, js-cookie |
| Styling   | Bootstrap 5 (dimuat lewat CDN)                            |
| Dev tools | Air (live reload untuk server Go)                         |

## Struktur proyek

```
.
├── main.go              # Entry point: load env, koneksi DB, jalankan server
├── config/              # Membaca .env dan environment variable
├── database/            # Koneksi MySQL + auto-migrasi
├── models/              # Model GORM (User)
├── structs/             # Bentuk request / response
├── controllers/         # Handler: register, login, CRUD user
├── middlewares/         # Middleware autentikasi JWT
├── helpers/             # Hash password, pembuatan JWT, pesan validasi
├── routes/              # Definisi route dan pengaturan CORS
└── frontend-react-ts/
    └── src/
        ├── views/       # Halaman: home, login, register, dashboard, users
        ├── hooks/       # Hook data (auth + CRUD user) dengan TanStack Query
        ├── context/     # AuthContext (status login)
        ├── routes/      # Route guard untuk halaman publik vs. terlindungi
        ├── components/  # UI bersama (menu sidebar)
        └── services/    # Instance Axios
```

## Endpoint API

| Method | Endpoint          | Auth  | Deskripsi                 |
|--------|-------------------|-------|---------------------------|
| POST   | `/api/register`   | Tidak | Membuat akun              |
| POST   | `/api/login`      | Tidak | Login, mengembalikan JWT  |
| GET    | `/api/users`      | Ya    | Daftar semua user         |
| POST   | `/api/users`      | Ya    | Membuat user              |
| GET    | `/api/users/:id`  | Ya    | Detail satu user          |
| PUT    | `/api/users/:id`  | Ya    | Mengubah user             |
| DELETE | `/api/users/:id`  | Ya    | Menghapus user            |

Endpoint yang dilindungi membutuhkan header `Authorization: Bearer <token>`.

## Memulai

### Prasyarat

- Go (versi terbaru; lihat `go.mod`)
- Node.js dan npm
- MySQL yang berjalan di komputer lokal

### 1. Siapkan database

Buat database kosong, misalnya:

```sql
CREATE DATABASE db_golang;
```

Tabel `users` dibuat otomatis saat server dijalankan.

### 2. Atur environment variable

Buat file `.env` di root proyek:

```env
APP_PORT=3000
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASS=
DB_NAME=db_golang
JWT_SECRET=ganti-dengan-string-acak-yang-panjang
```

> Gunakan `JWT_SECRET` acak milikmu sendiri dan jangan pernah meng-commit file `.env`.

### 3. Jalankan backend

```bash
go mod tidy
go run .
```

API akan tersedia di `http://localhost:3000`.
Opsional, gunakan [Air](https://github.com/air-verse/air) untuk live reload: `air`.

### 4. Jalankan frontend

```bash
cd frontend-react-ts
npm install
npm run dev
```

Buka URL yang ditampilkan Vite (biasanya `http://localhost:5173`). Frontend mengharapkan API berada di `http://localhost:3000`, yang diatur di `frontend-react-ts/src/services/api.tsx`.

## Cara kerja autentikasi

1. User mengisi form login, lalu frontend memanggil `POST /api/login`.
2. Server memeriksa password yang sudah di-hash dengan bcrypt dan mengembalikan JWT yang sudah ditandatangani.
3. Frontend menyimpan token di cookie dan mengatur status login.
4. Setiap request ke endpoint yang dilindungi mengirim token lewat header `Authorization`.
5. Middleware Go memverifikasi token sebelum request sampai ke handler.

## Kredit

Berdasarkan modul kelas Full-Stack Developer dari [SantriKoding](https://santrikoding.com).

## Showcase

https://santrikoding.com/showcases/golang-react-maikel-q33z5
