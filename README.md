# Dynamic Form Pengguna

Aplikasi formulir dinamis berbasis Vue untuk memasukkan satu atau beberapa data pengguna. Setelah formulir berhasil divalidasi, aplikasi menampilkan notifikasi sukses dan data yang tersimpan langsung di bawah formulir.

## Fitur

- Menambahkan beberapa pengguna dalam satu formulir.
- Menghapus entri pengguna yang tidak diperlukan.
- Validasi nama minimal 3 karakter.
- Validasi alamat minimal 10 karakter.
- Indikator progres pengisian formulir.
- Penanda otomatis ketika data pengguna sudah lengkap.
- Notifikasi setelah data berhasil disimpan.
- Menampilkan data yang baru disimpan di bawah formulir.
- Tombol reset untuk mengosongkan formulir dan hasil.
- Tampilan responsif untuk desktop, tablet, dan perangkat seluler.
- Dukungan aksesibilitas dasar melalui label, status, dan pesan validasi.

## Teknologi

- [Vue 3](https://vuejs.org/) dengan Composition API
- [Vite](https://vite.dev/) sebagai development server dan build tool
- JavaScript
- CSS

## Persyaratan

Pastikan Node.js dan npm sudah tersedia di komputer. Periksa instalasinya dengan perintah berikut:

```bash
node --version
npm --version
```

## Instalasi

Clone repository dan masuk ke direktori project:

```bash
git clone https://github.com/ridhoakbarsyah23/dynamic-form.git
cd dynamic-form
```

Instal seluruh dependency:

```bash
npm install
```

## Menjalankan Aplikasi

Jalankan development server:

```bash
npm run dev
```

Buka alamat lokal yang ditampilkan Vite pada terminal, biasanya `http://localhost:5173`.

## Build Produksi

Buat build aplikasi untuk produksi:

```bash
npm run build
```

Hasil build akan dibuat di direktori `dist`.

Untuk melihat hasil build secara lokal:

```bash
npm run preview
```

## Cara Menggunakan

1. Isi nama lengkap dan alamat pengguna.
2. Klik **Tambah pengguna lain** untuk menambahkan entri baru jika diperlukan.
3. Pastikan seluruh entri memiliki status **Lengkap**.
4. Klik **Simpan data**.
5. Notifikasi sukses dan daftar data tersimpan akan muncul di bawah formulir.
6. Klik **Reset** untuk menghapus seluruh isian dan hasil yang ditampilkan.

## Struktur Project

```text
dynamic-form/
|-- src/
|   |-- App.vue       # Komponen utama dan logika formulir
|   |-- main.js       # Entry point aplikasi Vue
|   `-- style.css     # Seluruh gaya tampilan
|-- index.html        # Dokumen HTML utama
|-- package.json      # Script dan dependency project
|-- vite.config.js    # Konfigurasi Vite
`-- README.md
```

## Aturan Validasi

| Kolom | Ketentuan |
| --- | --- |
| Nama lengkap | Wajib diisi dan minimal 3 karakter |
| Alamat lengkap | Wajib diisi, minimal 10 karakter, dan maksimal 200 karakter |

## Catatan Penyimpanan Data

Saat ini data hanya diproses dan ditampilkan selama aplikasi berjalan di browser. Project ini belum terhubung ke API, database, atau penyimpanan permanen. Data akan hilang ketika halaman dimuat ulang.

## Lisensi

Project ini dibuat untuk keperluan pembelajaran dan pengembangan formulir dinamis.
