# Catatan Keuangan

Web pencatat keuangan pribadi. Catat pemasukan, pengeluaran, dan tabungan, lalu pantau ringkasannya lewat dashboard. Data tersimpan di akun sendiri sehingga sinkron di semua perangkat.

## Fitur

- Login dan daftar dengan email, tombol lihat/sembunyikan kata sandi, dan reset kata sandi lewat email
- Catat transaksi: pemasukan, pengeluaran, menabung, dan tarik tabungan, lengkap dengan kategori dan keterangan
- Ringkasan saldo, pemasukan dan pengeluaran bulan ini, serta total tabungan
- Beberapa target tabungan, masing-masing dengan bar progres
- Grafik pemasukan dan pengeluaran 6 bulan terakhir
- Arsip bulanan: filter riwayat per bulan dengan ringkasan tiap bulan
- Tips otomatis berdasarkan data bulan berjalan
- Tampilan responsif untuk HP dan laptop, mendukung mode gelap

## Teknologi

- HTML, CSS, dan JavaScript murni dalam satu file (`index.html`)
- [Supabase](https://supabase.com) untuk login dan penyimpanan data
- [Netlify](https://netlify.com) untuk hosting

## Cara memasang

### 1. Siapkan Supabase

1. Buat project baru di Supabase.
2. Buka **SQL Editor**, lalu jalankan:

   ```sql
   create table ledger (
     user_id uuid primary key references auth.users(id) on delete cascade,
     data jsonb not null default '{}',
     updated_at timestamptz default now()
   );
   alter table ledger enable row level security;
   create policy "own row" on ledger for all
     using (auth.uid() = user_id) with check (auth.uid() = user_id);
   ```

   Kode ini membuat tabel data dan memastikan tiap akun hanya bisa membaca dan mengubah datanya sendiri.
3. Salin **Project URL** dan **anon public key** dari pengaturan API.
4. Buka **Authentication**, bagian **URL Configuration**, lalu isi **Site URL** dan **Redirect URLs** dengan alamat web yang akan kamu pakai.

### 2. Isi kunci di `index.html`

Cari dua baris ini di bagian `<script>`, lalu ganti isinya dengan milikmu:

```js
var SB_URL='https://PROJECT-ID-KAMU.supabase.co';
var SB_KEY='ANON-PUBLIC-KEY-KAMU';
```

Pakai kunci **anon public** saja. Jangan pernah menaruh kunci **service_role** di repository.

### 3. Deploy

- **Netlify:** seret `index.html` ke dashboard Netlify, atau hubungkan repository ini agar deploy berjalan otomatis setiap ada perubahan.
- **Hosting statis lain:** unggah `index.html` ke GitHub Pages, Cloudflare Pages, atau layanan serupa.

## Keamanan

- Kunci anon memang aman terlihat publik karena akses data dibatasi oleh Row Level Security di tabel `ledger`.
- Pastikan RLS tetap aktif. Tanpa itu, data bisa diakses orang lain.
- Data transaksi tidak disimpan di repository ini, hanya di database Supabase milikmu.

## Struktur

```
.
├── index.html    # seluruh aplikasi
├── README.md
└── .gitignore
```
