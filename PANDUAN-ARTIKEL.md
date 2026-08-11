# Panduan Menambah Artikel — Studi Kasus Kantor Berita IHSC

> Dokumen ini bukan bagian dari situs (tidak ikut ter-deploy — lihat `.github/workflows/deploy.yml`).
> Tujuannya: jadi bahan belajar terbuka untuk siapa pun yang mau tahu cara kerja
> situs statis bilingual sederhana ini, dipakai lewat contoh nyata: artikel
> "Humanitarian Action Framework (HAF)" (`berita/artikel/artikel-003.html`).

## Kenapa situs ini begini

- **Statis murni.** HTML/CSS/JS vanilla, tanpa framework, tanpa build step.
  Setiap halaman berdiri sendiri sebagai file `.html` — mudah dibaca, mudah
  ditiru, tidak butuh `npm install` untuk mulai.
- **Bilingual tanpa i18n library.** Tiap teks punya dua versi sejajar dalam
  HTML: `data-lang="en"` dan `data-lang="id" hidden`. Satu skrip kecil
  (`setLang()`) membalik atribut `hidden` saat pengguna klik tombol EN/ID.
  Tidak ada build step, tidak ada file terjemahan terpisah yang bisa
  desinkron dari markup-nya.
- **Deploy = push.** Push ke `main` men-trigger GitHub Actions yang meng-upload
  seluruh repo ke hosting via WebDAV. Tidak ada staging, tidak ada tombol
  "publish" manual — artinya *setiap commit di `main` harus sudah pantas
  tayang.*

## Langkah menambah artikel (pola baku)

1. **Salin artikel referensi terbaru** (`berita/artikel/artikel-002.html`)
   jadi `artikel-XXX.html` dengan nomor urut berikutnya.
2. **`<head>`**: sesuaikan `<title>`, `meta description`, semua tag
   `og:*`/`twitter:*` — termasuk `og:image` yang harus URL **absolut**
   (`https://ihsc.iiknutuban.ac.id/assets/...`), bukan path relatif.
3. **Setiap blok teks berpasangan.** Tulis versi EN dulu (tanpa `hidden`),
   lalu versi ID tepat di bawahnya (`hidden`). Jangan biarkan satu bahasa
   tanpa pasangannya — situs akan tampak bolong saat toggle bahasa dipakai.
4. **Hero image** taruh di `assets/`, tampilkan via `<figure>` setelah
   `.article-meta`, dengan `figcaption` sumber/kredit foto di dua bahasa.
5. **Kotak sumber** (`.sources-box`) — cantumkan rujukan yang dipakai,
   idealnya dengan tautan `target="_blank" rel="noopener"`.
6. **Skrip di bawah halaman** — update objek `TITLES` dan `META_DESC` supaya
   `document.title` dan meta description ikut berubah saat bahasa di-toggle.
7. **Tambahkan kartu** di dua tempat: `berita/index.html` (grid Kantor
   Berita) dan `index.html` (3 kartu terbaru di homepage). Kartu baru selalu
   masuk **paling atas**; kalau homepage sudah penuh, kartu tertua yang
   dibuang duluan.
8. **Verifikasi** tag seimbang, path relatif benar (`../` vs `../../`
   tergantung kedalaman folder), commit, push.

Detail lengkap ada di [`AGENTS.md`](AGENTS.md) — dokumen itu yang jadi
sumber kebenaran teknis; panduan ini fokus ke *alasan* di baliknya.

## Studi kasus: artikel HAF (`artikel-003.html`)

Bahan mentahnya poster flyer event dari WhatsApp. Beberapa keputusan yang
diambil saat menuliskan ulang jadi artikel web, dan kenapa:

**1. Info rekening bank dihapus dari isi artikel.**
Poster asli mencantumkan nomor rekening bank tujuan pembayaran. Artikel web
publik **tidak** mereproduksi nomor rekening itu — cukup diarahkan ke
formulir pendaftaran resmi (`forms.gle/...`) tempat info investasi & SKP
bisa diperiksa langsung oleh peserta yang benar-benar mendaftar.

*Kenapa ini penting sebagai prinsip umum:* detail finansial (nomor
rekening, angka nominal) yang dipublikasikan di halaman web publik
gampang diindeks mesin pencari dan gampang disalahgunakan untuk
peniruan/penipuan (orang membuat rekening palsu meniru info yang beredar).
Prinsipnya: **artikel publik seharusnya jadi pengumuman, bukan salinan
mentah dari materi promosi yang berisi data transaksi.** Kalau ada
kebutuhan menampilkan info pembayaran, arahkan ke kanal resmi
(formulir/WhatsApp CP) yang bisa diverifikasi langsung oleh pendaftar,
bukan ditulis ulang di teks yang terbuka untuk siapa saja.

**2. Konteks program ditambahkan, bukan sekadar disalin dari poster.**
Poster tidak menyebutkan bahwa HAF adalah jenjang dasar sebelum
Humanitarian Action Workshop (HAW). Info ini didapat dari arahan langsung
prinsipal proyek dan ditambahkan sebagai paragraf baru — supaya pembaca
paham posisi pelatihan ini dalam rangkaian yang lebih besar, bukan cuma
event berdiri sendiri.

*Prinsip umum:* artikel berita idealnya menambahkan konteks yang membantu
pembaca mengambil keputusan (ikut atau tidak, urutan pelatihan apa yang
relevan) — bukan sekadar transkripsi ulang materi promosi.

## Kesalahan yang perlu dihindari

- Menulis ulang naskah resmi (Visi & Misi, SK) berdasarkan ingatan/asumsi.
  Sumbernya harus dari dokumen resmi (`REFERENSI-SK-PSH-IIKNU.md`), tidak
  boleh dikarang.
- Menaruh kredensial (WebDAV, API key) di file mana pun di repo — deploy
  memakai GitHub Actions secrets, bukan file di kode.
- Mengubah `assets/ihsc.css` tanpa bump versi query string (`?v=N`) di
  semua halaman — CSS statis di-cache Cloudflare, jadi tanpa bump versi
  pengunjung bisa tetap melihat gaya lama.
- `git add -A` sembarangan — selalu stage path spesifik yang memang diubah.

## Untuk yang belajar dari repo ini

Repo ini publik dan sengaja didokumentasikan (lihat juga [`AGENTS.md`](AGENTS.md))
supaya bisa jadi referensi buat siapa pun yang mau bikin situs statis
bilingual sederhana dengan alur kerja "push = tayang" — tanpa CMS, tanpa
build step, tapi tetap punya disiplin: pola konten yang konsisten, dan
kehati-hatian soal apa yang pantas dipublikasikan di halaman publik.
