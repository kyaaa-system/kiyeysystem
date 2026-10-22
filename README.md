# Dashboard MA 50/100/200 (Kiya)

Dashboard statis (`index.html`) + dua fungsi proxy Vercel (`api/`) untuk mengambil harga dari Yahoo Finance tanpa masalah CORS.

## Struktur

```
index.html      dashboard (satu file)
api/yahoo.js    GET /api/yahoo?url=<URL chart Yahoo>
api/batch.js    GET /api/batch?range=5d&symbols=BBCA,BBRI,...
api/_yahoo.js   helper bersama (bukan endpoint)
vercel.json     batas waktu fungsi 30 detik
package.json    menetapkan Node >= 18
```

## Dua mode data

- **Mode Yahoo** (bawaan): memakai 100 saham bawaan dashboard, harga live dari Yahoo, auto-refresh aktif.
- **Mode JSON**: setelah klik **Upload JSON**, hanya saham dari file yang dipakai dan auto-refresh Yahoo dimatikan. Klik **Refresh Yahoo** untuk kembali ke mode Yahoo.

Format file JSON: `{"data": {"BBCA": {"d": ["2024-01-02", ...], "o": [...], "c": [...], "v": [...]}, ...}}`

## Upload ke GitHub

```bash
git init
git add .
git commit -m "dashboard MA 50/100/200"
git branch -M main
git remote add origin https://github.com/USERNAME/NAMA-REPO.git
git push -u origin main
```

## Deploy ke Vercel

1. Buka https://vercel.com/new dan import repo GitHub tadi.
2. Framework Preset: **Other**. Build Command dan Output Directory dikosongkan. Root Directory tetap `./`.
3. Klik **Deploy**. Setiap `git push` ke `main` otomatis deploy ulang.

Cek proxy setelah deploy:

```
https://DOMAIN-ANDA.vercel.app/api/batch?range=5d&symbols=BBCA
```

Hasilnya harus JSON berisi kunci `BBCA`. Jika kosong atau error 502, Yahoo sedang membatasi permintaan; coba lagi beberapa saat.

## Uji lokal

```bash
npm i -g vercel
vercel dev
```

Membuka `index.html` langsung dari file (`file://`) tetap jalan, tetapi memakai proxy publik yang lebih lambat dan sering gagal. Pakai `vercel dev` atau domain Vercel untuk hasil yang stabil.

## Catatan

- Yahoo Finance tidak punya API resmi dan bisa membatasi permintaan dari IP server Vercel. Fungsi proxy sudah mencoba host cadangan (`query2`) dan klien punya rute proxy publik sebagai cadangan.
- Data JSON dari Upload tidak disimpan ke cache browser; setelah reload halaman kembali ke snapshot Yahoo.
