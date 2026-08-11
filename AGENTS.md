# AGENTS.md — IHSC (putrosm/ihsc)

> File ini dibaca otomatis oleh agent AI (Claude Code, Cursor, dll.) saat membuka repo.
> Baca SELURUH file ini sebelum mengubah apa pun. Situs ini produksi (live).

## Identitas proyek

- **Situs resmi IHSC** — Indonesian Humanitarian Study Center / Pusat Studi Humanitarian (PSH), IIK Nahdlatul Ulama Tuban.
- **Live:** https://ihsc.iiknutuban.ac.id
- **Repo:** https://github.com/putrosm/ihsc — branch `main` satu-satunya.
- **Hosting:** cPanel di srv.iiknutuban.ac.id (WebDAV port 2078), di belakang Cloudflare.
- **Stack:** statis murni HTML/CSS/JS vanilla. **TANPA build step, tanpa framework.**
- **Bahasa situs:** EN default + toggle ID (bilingual via `data-lang`, lihat di bawah).

## DEPLOY: push ke main = otomatis tayang (jangan pernah deploy manual)

`.github/workflows/deploy.yml` meng-upload seluruh repo ke WebDAV via curl.
Kredensial WebDAV sudah tersimpan sebagai GH Actions secrets — **jangan pernah menulisnya di file repo**.
Agent cukup melakukan `git add` + `commit` + `push origin main`. Tidak butuh akses hosting langsung.

File yang **TIDAK** ikut ter-upload (sudah dikecualikan di workflow): `.git/*`, `.github/*`, `README.md`, `.gitignore`, `NARASI-*`, `REFERENSI-*`, `PANDUAN-*`, `AGENTS.md`, `CLAUDE.md`.

Setelah push, cek: `https://github.com/putrosm/ihsc/actions` → run terbaru `success`, lalu verifikasi URL live.

## Struktur repo

```
index.html                    # homepage (hero, tentang, visi-misi, bidang, program, kabar, kontak)
berita/index.html             # kantor berita (grid kartu + search JS + pagination 6/halaman)
berita/artikel/artikel-001.html  # artikel: pleno perdana
berita/artikel/artikel-002.html  # artikel: lanskap kemanusiaan Indonesia (pola baku artikel terbaru)
assets/ihsc.css               # design system (tokens di :root)
assets/logo.png, assets/artikel-002-flood-relief.jpg
NARASI-UNTUK-REVIEW.md        # file kerja review (tidak ikut deploy)
REFERENSI-SK-PSH-IIKNU.md     # dokumen SK (tidak ikut deploy)
```

## Menambah artikel baru (pola baku — tiru artikel-002.html)

1. Salin `berita/artikel/artikel-002.html` → `berita/artikel/artikel-XXX.html`.
2. **`<head>`:** sesuaikan `meta description`, `og:title`, `og:description`, `og:image` (URL **absolut** `https://ihsc.iiknutuban.ac.id/...`), `og:url`, `twitter:*`, `<title>`.
3. **Konten bilingual:** setiap elemen teks berpasangan — versi EN `data-lang="en"` (tanpa `hidden`, default) + versi ID `data-lang="id" hidden`. Header/footer/WA button/toggle script salin utuh dari artikel-002.
4. **Hero image:** simpan ilustrasi di `assets/` (unduh dari Wikimedia Commons lebih disukai — lisensi bebas; cantumkan kredit di figcaption). Tampilkan `<figure>` setelah `.article-meta`, sebelum paragraf pertama. Style inline mengikuti artikel-002.
5. **Byline:** penulis + tanggal di `.article-meta`.
6. **Sumber:** kotak `.sources-box` dengan `<ol>`; tiap link pakai `target="_blank" rel="noopener"`.
7. **Script bawah:** update objek `TITLES` dan `META_DESC` (dua bahasa).
8. **Kartu di `berita/index.html`:** sisipkan `<a class="news-card">` **paling atas** grid `#newsGrid`; **WAJIB** `<img class="card-img" src="../assets/...">` sebagai elemen pertama kartu (badge, judul, kutipan, `card-more` mengikuti pola kartu artikel-002).
9. **Kartu di homepage `index.html`:** sisipkan kartu teratas di `#berita .news-grid` dengan `<img class="card-img" src="assets/...">`; **jaga grid tetap 3 kartu** (buang kartu placeholder terbawah bila perlu).
10. Verifikasi (di bawah) → commit → push.

## CACHE CLOUDFLARE — sangat penting

- HTML selalu fresh (DYNAMIC), tapi **CSS statis di-cache** (`cache-control: public, max-age=14400`).
- Setiap mengubah `assets/ihsc.css` → **bump versi query string** `?v=N` di **SEMUA** file HTML (index.html, berita/index.html, semua artikel). Versi saat ini: **`?v=11`** → jadi `?v=12`, dst.
- Setelah deploy + bump, verifikasi CSS baru benar-benar ter-serve (curl dan grep `?v=`).

## Bahasa & toggle

- `setLang()` di tiap halaman: membalik `hidden` pada `[data-lang]`, set `document.title` + meta description dari `TITLES`/`META_DESC`, simpan ke `localStorage` key `ihsc-lang`.
- Konten ID = versi resmi; EN = terjemahan. Toggle ada di header kanan (tombol EN/ID).

## Design tokens (dari assets/ihsc.css :root)

- Palet: hijau institusi `--brand #1B6B2A` (green-800), aksen emas `--accent #C9A84C` (gold-500), latar `--mist #F7F9F7`, teks `--charcoal #1C1C1C`, `--stone #6B7280`.
- Font: `--font-display: 'Playfair Display'` (judul), `--font-body: 'Source Serif 4'` (body) — Google Fonts.
- Badge kategori: `--featured` (Kabar, hijau solid), `--research` (Riset, hijau muda), `--humanitarian` (Kemanusiaan, biru muda), `--policy` (Kebijakan, emas muda), `--emergency` (Darurat, merah muda).
- Radius 6px, card padding 36px, container 1160px. Kartu berita: `.news-card` flex column, hero `.card-img` bleed penuh (negative margin = card-pad, `max-width: none` — jangan dihapus, itu fix penting).

## Aturan kerja (dari Prinsipal)

- **Jangan ubah layout homepage untuk foto pengurus** — foto menyusul, nunggu arahan. Jangan edit layout web di luar kebutuhan.
- Naskah resmi (Visi & Misi, SK) jangan dikarang/diubah — sumber dari `REFERENSI-SK-PSH-IIKNU.md`.
- Jangan `git add -A` sembarangan — stage path spesifik yang diubah.
- Jangan taruh kredensial apa pun di repo.
- Bahasa komunikasi laporan: **Indonesia**.

## Verifikasi sebelum push (wajib)

- Struktur HTML seimbang (tag balance), link relatif benar (`../`, `../../` sesuai kedalaman).
- Gambar hero ada di `assets/` dan path-nya benar.
- Semua `data-lang` berpasangan EN/ID.
- Kalau CSS berubah: semua file pakai `?v=` baru.
- Setelah deploy: `curl` URL artikel → 200 + konten ada; cek Actions `success`.
