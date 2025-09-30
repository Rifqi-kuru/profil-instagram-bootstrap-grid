## 1. Mengapa Memilih Konfigurasi `col` Tertentu untuk Tiap Breakpoint?

Konfigurasi grid untuk feed postingan adalah `class="col-12 col-sm-6 col-md-4 col-lg-3"` pada setiap elemen kolom (div untuk setiap post). Ini dipilih untuk mencapai tampilan responsif optimal, sesuai ketentuan (Mobile: 1 kolom; Tablet: 2–3 kolom; Desktop: 4 kolom). Berikut alasan detailnya:

- **col-12 (untuk Mobile, ≤576px)**:
  - **Alasan**: Pada layar kecil (seperti smartphone portrait, ~320–576px), lebar terbatas. `col-12` membuat setiap postingan memenuhi seluruh lebar (1 kolom penuh). Ini memastikan gambar jelas tanpa cropping berlebih, teks overlay mudah dibaca, dan pengguna tidak perlu zoom. Instagram asli juga menggunakan 1 kolom di mobile untuk prioritas konten vertikal dan scroll nyaman.
  - **Manfaat**: Mengurangi clutter visual, meningkatkan loading speed, dan sesuai UX mobile-first.

- **col-sm-6 (untuk Small/Tablet Kecil, ≥576px)**:
  - **Alasan**: Pada tablet landscape kecil (~576–768px), layar lebih lebar tapi masih terbatas. `col-6` membagi menjadi 2 kolom (50% lebar). Ini keseimbangan antara ruang dan efisiensi—tidak terlalu sempit seperti 1 kolom, tapi tidak overcrowded seperti 3 kolom. Pilihan 2 kolom sesuai ketentuan "2–3 kolom" dan aman untuk gambar persegi (400x400px).
  - **Manfaat**: Meningkatkan utilisasi layar tanpa mengorbankan readability. Jika 3 kolom, gambar terlalu kecil (~150–200px).

- **col-md-4 (untuk Medium/Tablet, ≥768px)**:
  - **Alasan**: Pada tablet standar (~768–992px, seperti iPad portrait), layar cukup untuk 3 kolom (`col-4` = ~33% lebar). Ini sesuai ketentuan "2–3 kolom" dan mirip Instagram di tablet. Memanfaatkan lebar ekstra untuk konten horizontal, dengan proporsi gambar ~200–250px.
  - **Manfaat**: Transisi halus dari mobile ke desktop. Auto-wrapping Bootstrap menjaga rapi tanpa overlap.

- **col-lg-3 (untuk Large/Desktop, ≥992px)**:
  - **Alasan**: Pada desktop (~992px+), layar lebar memungkinkan 4 kolom (`col-3` = 25% lebar). Sesuai ketentuan "4 kolom (boleh 5–6 kalau rapi)", dan Instagram desktop menggunakan 3–5 kolom. Pilihan 4 memberikan ruang konsisten (~250–300px per post), ideal untuk hover effects.
  - **Manfaat**: Optimal untuk scan cepat. Jika 5–6 kolom, gambar terlalu kecil (<200px).

**Keseluruhan Alasan**: Mengikuti "progressive enhancement"—mulai lebar penuh di mobile, tambah kolom seiring lebar layar. Total 12 post wrap otomatis (12 baris mobile, 6 sm, 4 md, 3 lg). Menggunakan minimal 3 fitur grid: col-sm/md/lg (breakpoints), order-* (tombol), nesting (row di container).

## 2. Bagaimana Memastikan Tombol Follow/Edit Profile Tetap Mudah Dijangkau di Mobile? Jelaskan Pendekatannya.

Tombol interaksi (Edit Profile, Follow, settings) ditempatkan di header dengan posisi berubah antar breakpoint menggunakan utility Bootstrap. Pendekatan utama: **mobile-first responsive design** dengan **conditional rendering** (hide/show) dan **order classes** untuk urutan. Ini memastikan tombol dekat username, mudah di-tap (target size ≥44x44px, sesuai Apple HIG).

**Pendekatan Detail**:
- **Struktur HTML Dasar**:
  - Header: `row align-items-center` untuk alignment vertikal.
  - Foto profil: `col-4 col-md-3 col-lg-2`.
  - Username + stats: `col-8 col-md-9 col-lg-6`.
  - Dua set tombol:
    - **Desktop** (`d-none d-md-block`): "Edit Profile" + "View Archive" di samping username (`d-flex gap-2 order-1 order-md-0`). Muncul ≥768px, horizontal untuk mouse.
    - **Mobile** (`d-md-none`): "Edit Profile" + ikon Follow (SVG person-plus) di bawah username (`d-flex justify-content-between mb-2`). Muncul <768px, horizontal penuh untuk tap.

- **Utility Classes untuk Responsivitas**:
  - **Display Utilities** (`d-none`, `d-md-block`, `d-md-none`):
    - Sembunyikan tombol desktop di mobile (hindari overlap/clutter). Versi mobile ringkas (2 tombol: Edit + ikon).
    - Alasan: Layar mobile sempit; tombol panjang bisa wrap line.
  - **Order Classes** (`order-1 order-md-0`):
    - Mobile: `order-1` (setelah username, vertikal stack). Desktop: `order-md-0` (inline samping).
    - Alasan: Flexbox reordering tanpa JS. Ubah dari "bawah" (mobile, mudah scroll) ke "samping" (desktop).
  - **Spacing dan Alignment**:
    - `justify-content-between` di mobile: Sebarkan kiri-kanan, full-width.
    - `gap-2` + `btn-sm`: Jarak 8px, ukuran tappable (≥32px tinggi).
    - Ikon SVG (gear/settings, person-plus/Follow): Hemat ruang, intuitif.

- **Usability di Mobile**:
  - **Aksesibilitas**: Dekat username (fokus utama), no scroll jauh. Bawah layar untuk thumb navigation (one-handed).
  - **Tested Behavior**: Full-width, centered di ≤576px. Hindari accidental taps. Effects Bootstrap (`btn-primary` focus) beri feedback.
  - **Masalah Dihindari**: Tanpa ini, tombol tersembunyi desktop atau padat mobile. `mb-2` cegah menempel stats.

Hasil: Tombol "sticky" di viewport awal, tinggi conversion interaksi.

## 3. Jika Postingan Bertambah Jadi 50, Apa Potensi Masalah dan Bagaimana Solusi Gridmu Mengatasinya?

Menambah dari 12 ke 50 post buat feed panjang, bagus untuk konten tapi potensi masalah performa/UX. Grid (`row g-2` + col responsif) fleksibel, tapi butuh penyesuaian skalabilitas.

**Potensi Masalah**:
- **Performa Loading**: 50 gambar >5MB, load lambat (>3 detik TTI). Mobile boros baterai.
- **Scroll dan UX**: Panjang ekstrem (50 baris mobile = scroll 10+ detik), frustasi. Memori naik, crash low-end.
- **Responsivitas Grid**: Desktop OK (~13 baris 4 kolom), mobile overwhelming (50 baris). Gutter tumpuk, halaman "berat".
- **SEO/Accessibility**: Crawler lambat; screen reader kesulitan navigasi panjang.
- **Lainnya**: Bandwidth tinggi tanpa optimasi; preload semua gambar.

**Bagaimana Solusi Grid Mengatasinya**:
- **Fleksibilitas Grid Bootstrap (Built-in)**:
  - **Auto-Wrapping Responsif**: Col-* wrap otomatis setelah 12/6/4/3 kolom. Tambah 38 div kolom baru—tidak ubah row. Skalabel, handle reflow tanpa JS.
  - **Nesting/Offset**: Nest row untuk sub-grid (kategori). `offset-md-1` untuk spacing khusus, hindari overcrowding.
  - **Konsistensi**: CSS (`height: 100%` card, `object-fit: cover`) seragam tinggi, hindari "jumping". Gutter `g-2` konsisten; kurangi ke `g-1` untuk compact.

- **Solusi Tambahan Skalabilitas**:
  - **Lazy Loading**: Tambah `loading="lazy"` pada `<img>` feed. Load hanya visible (kurangi data 70% mobile). Contoh: `<img src="..." loading="lazy">`.
  - **Pagination/Infinite Scroll**:
    - **Pagination**: Bagi 12–20 post/halaman. Tambah `<nav class="pagination">` Bootstrap. JS/backend load halaman baru. Batasi load, hindari scroll panjang.
    - **Infinite Scroll**: Intersection Observer API (native JS) load dynamic via AJAX. Append div ke `.row` saat scroll bawah.
  - **Optimasi Gambar/Performa**:
    - Kompres <50KB/file (WebP, `<picture>` fallback). Total <2MB untuk 50 post.
    - CSS virtual scroll: `overflow-y: auto` container (fixed 80vh) jika ekstrem.
  - **UX Improvements**: Spinner loading (Bootstrap). `order-*` prioritaskan post baru. ARIA (`role="grid"`) untuk accessibility.
  - **Testing**: Lighthouse score >90 Performance. <2 detik first paint mobile.

Dengan grid ini, tambah 50 post via duplikasi HTML (loop backend). Solusi jaga cepat (<3 detik full load).
