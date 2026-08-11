# IHSC — Indonesian Humanitarian Study Center

Situs resmi IHSC (Pusat Studi Kemanusiaan — IIK NU Tuban): homepage + kantor berita.
Situs statis murni (HTML/CSS), tanpa build step, bilingual EN/ID tanpa library i18n.

- **Live:** https://ihsc.iiknutuban.ac.id
- **Hosting:** cPanel (srv.iiknutuban.ac.id, WebDAV port 2078)

Repo ini sengaja didokumentasikan agar bisa jadi bahan belajar terbuka —
lihat [`PANDUAN-ARTIKEL.md`](PANDUAN-ARTIKEL.md) untuk tutorial lengkap
menambah artikel bilingual (dengan studi kasus nyata) dan
[`AGENTS.md`](AGENTS.md) untuk detail teknis lengkap yang dipakai agent AI.

## Struktur

```
index.html                  # homepage (hero, tentang, pilar, kabar, kontak)
assets/ihsc.css             # design system bersama (tokens, komponen)
berita/index.html           # kantor berita (grid artikel)
berita/artikel/artikel-002.html  # pola baku artikel terbaru (rujukan saat menyalin)
```

## Cara kerja deploy (push = tayang)

Setiap push ke `main` → GitHub Actions menjalankan `.github/workflows/deploy.yml`
yang meng-upload seluruh isi repo ke docroot via WebDAV.

Secrets yang dibutuhkan (sudah di-set di repo):
- `WEBDAV_URL` — `https://srv.iiknutuban.ac.id:2078`
- `WEBDAV_USER` — akun WebDisk
- `WEBDAV_PASS` — password WebDisk

> **Catatan cache:** situs berada di belakang Cloudflare. HTML selalu fresh
> (DYNAMIC), tapi CSS statis di-cache. Setiap mengubah `assets/ihsc.css`,
> **bump versi query string** di ketiga halaman (`?v=2` → `?v=3`, dst.)
> supaya pengunjung mendapat CSS terbaru.

## Menambah artikel

Lihat [`PANDUAN-ARTIKEL.md`](PANDUAN-ARTIKEL.md) — tutorial langkah demi
langkah lengkap dengan studi kasus, atau [`AGENTS.md`](AGENTS.md) untuk
versi ringkas teknis.

## TODO (jangan dikerjakan dulu)

- Foto-foto pengurus akan menyusul; layout foto belum diputuskan Prinsipal.
  **Jangan edit layout web untuk foto sebelum ada arahan.**

