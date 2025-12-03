
# 🧹 SPKPPSU Kelurahan Rawamangun

**Sistem Pelaporan Kerja PPSU (Penanganan Prasarana dan Sarana Umum)**

Aplikasi web berbasis **React & Supabase** yang dirancang untuk mendigitalkan proses pelaporan kinerja petugas PPSU di Kelurahan Rawamangun. Aplikasi ini mendukung pelaporan lapangan berbasis lokasi (GPS) dan pemantauan real-time oleh admin.

![Tech](https://img.shields.io/badge/Stack-React%20%7C%20Vite%20%7C%20Supabase-blue)

---

## 🌟 Fitur Unggulan

### 👷 Modul Petugas (Mobile First)
*   **📍 Absensi & Lokasi Real-time**: Menggunakan **Leaflet Maps** & GPS bawaan HP untuk mendeteksi lokasi kerja secara akurat (Reverse Geocoding: Koordinat -> Nama Jalan).
*   **📸 Laporan Visual**: Ambil foto langsung dari kamera aplikasi atau upload dari galeri.
*   **🔄 Perbaikan Laporan (Resubmit)**: Fitur "Perbaiki & Kirim Ulang" untuk laporan yang ditolak admin, tanpa perlu mengetik ulang data dari awal.
*   **👤 Manajemen Profil Mandiri**: Edit foto profil (disimpan sebagai Base64) dan ubah kata sandi dengan aman.
*   **📜 Riwayat & Filter**: Timeline laporan yang dikelompokkan per tanggal dengan filter status/kategori.

### 👨‍💼 Modul Admin (Dashboard)
*   **📊 Dashboard Analitik**: Statistik kinerja total, grafik batang, dan "Leaderboard" petugas paling rajin.
*   **📝 Verifikasi Laporan**:
    *   List view responsif (nyaman di Desktop & Mobile).
    *   Filter canggih: Berdasarkan Petugas, Tanggal, dan Status.
    *   Modal Detail: Lihat bukti foto resolusi penuh & peta lokasi.
    *   **Tolak dengan Alasan**: Memberikan feedback spesifik kenapa laporan ditolak.
*   **👥 Manajemen User**:
    *   CRUD Petugas (Tambah, Edit, Nonaktifkan).
    *   **Hapus Permanen (Hard Delete)**: Menghapus akun beserta seluruh riwayat laporannya dari database (Safety Confirmation).
*   **📥 Ekspor Data**: Download rekap laporan ke format `.csv` untuk arsip kelurahan.

---

## 🛠 Teknologi yang Digunakan

*   **Frontend**: React 18, TypeScript, Vite.
*   **Styling**: Tailwind CSS (Utility-first framework).
*   **Database & Realtime**: Supabase (PostgreSQL).
*   **Peta**: Leaflet.js & OpenStreetMap (Gratis, tanpa API Key Google).
*   **Icons**: Lucide React.
*   **Charts**: Recharts.

---

## 📂 Struktur Folder

```text
spkppsu-rawamangun/
├── components/       # Komponen UI Reusable (Layout, Toast)
├── context/          # State Management Global (Auth, Data, Notifikasi)
├── lib/              # Konfigurasi Client Supabase
├── pages/            # Halaman Aplikasi
│   ├── admin/        # Dashboard, Kelola Laporan, Kelola User
│   └── petugas/      # Home, Buat Laporan, Riwayat, Profil
├── types.ts          # Definisi Tipe TypeScript (Interface User, Report)
└── constants.ts      # Mock Data (untuk mode offline)
```

---

## 🚀 Panduan Instalasi & Menjalankan (Lokal)

### 1. Prasyarat
*   Node.js (versi 16+)
*   NPM

### 2. Instalasi Dependensi
```bash
# Clone repository (jika dari git)
git clone https://github.com/Mriskiali/SPKPPSU-Rawamangun.git

# Masuk ke folder
cd spkppsu-rawamangun

# Install library
npm install
```

### 3. Konfigurasi Database (Supabase)

Aplikasi ini membutuhkan database PostgreSQL via Supabase.

1.  Buat project baru di [Supabase.com](https://supabase.com).
2.  Masuk ke **SQL Editor** di dashboard Supabase.
3.  Jalankan script berikut untuk membuat tabel dan akun Admin:

```sql
-- 1. Aktifkan Ekstensi UUID (Wajib)
create extension if not exists "uuid-ossp";

-- 2. Buat Tabel Profile (User)
create table public."Profile" (
  id text primary key default uuid_generate_v4(),
  "pjlpNumber" text unique not null,
  name text not null,
  role text not null default 'PETUGAS',
  "isActive" boolean not null default true,
  phone text,
  "avatarUrl" text,
  password text, 
  "createdAt" timestamp with time zone default timezone('utc'::text, now()) not null,
  "updatedAt" timestamp with time zone default timezone('utc'::text, now()) not null
);

-- 3. Buat Tabel Report (Laporan)
create table public."Report" (
  id text primary key default uuid_generate_v4(),
  "userId" text not null references public."Profile"(id) on delete cascade,
  "userName" text not null,
  category text not null,
  description text not null,
  "imageUrl" text not null,
  location text not null,
  status text not null default 'PENDING',
  feedback text,
  "createdAt" timestamp with time zone default timezone('utc'::text, now()) not null
);

-- 4. Aktifkan Realtime (Agar dashboard update otomatis)
alter publication supabase_realtime add table "Report";
alter publication supabase_realtime add table "Profile";

-- 5. Seeding Akun Admin Pertama
insert into public."Profile" (
  "pjlpNumber", name, role, "isActive", password
) values (
  'admin', 'Super Admin Kelurahan', 'ADMIN', true, 'admin'
);
```

### 4. Setup Environment Variables
Buat file bernama `.env` di root folder proyek, lalu isi dengan kredensial Supabase Anda:

```env
VITE_SUPABASE_URL=https://project-id-anda.supabase.co
VITE_SUPABASE_ANON_KEY=key-anon-public-anda
```

### 5. Jalankan Aplikasi
```bash
npm start
```
Akses di browser: `http://localhost:5173`

---

## 🛡 Mode Offline / Mock Data

Jika Anda **tidak** mengatur file `.env` atau koneksi internet mati, aplikasi otomatis berjalan dalam **Mode Mock**. Data tidak akan disimpan ke database, tapi Anda tetap bisa mencoba fitur UI.

**Akun Login Default (Mode Mock):**
*   **Admin**: `admin` / `admin`
*   **Petugas**: `50422231` / `password123`

---
