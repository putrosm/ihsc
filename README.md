# IHSC — Indonesian Humanitarian Study Center

Situs resmi IHSC (Pusat Studi Kemanusiaan — IIK NU Tuban): homepage + kantor berita.
Situs statis murni (HTML/CSS), tanpa build step.

- **Live:** https://ihsc.iiknutuban.ac.id
- **Hosting:** cPanel (srv.iiknutuban.ac.id, WebDAV port 2078)

## Struktur

```
index.html                  # homepage (hero, tentang, pilar, kabar, kontak)
assets/ihsc.css             # design system bersama (tokens, komponen)
berita/index.html           # kantor berita (grid artikel)
berita/artikel/artikel-001.html  # template artikel standar
```

## Cara kerja deploy (push = tayang)

Setiap push ke `main` → GitHub Actions menjalankan `.github/workflows/deploy.yml`
yang meng-upload seluruh isi repo ke docroot via WebDAV.

Secrets yang dibutuhkan (sudah di-set di repo):
- `WEBDAV_URL` — `https://srv.iiknutuban.ac.id:2078`
- `WEBDAV_USER` — akun WebDisk
- `WEBDAV_PASS` — password WebDisk

## Menambah artikel

1. Salin `berita/artikel/artikel-001.html` → `artikel-XXX.html`, isi konten.
2. Sisipkan kartu baru paling atas grid di `berita/index.html`
   (blok `<a class="news-card">…</a>`).
3. Push — otomatis tayang.
