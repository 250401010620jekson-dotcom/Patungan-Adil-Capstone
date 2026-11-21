# Paket Submission: Patungan Adil

## A. PROJECT OVERVIEW (isi form)

**Judul:** Patungan Adil: Split Bill Tanpa Drama

**Ringkasan Project:** Web app satu file HTML (tanpa library dan tanpa internet) untuk membagi tagihan makan bareng secara adil. Pengguna menambahkan peserta, mencatat menu beserta siapa saja yang ikut makan, lalu mengatur service, pajak, dan diskon. Aplikasi menghitung bagian tiap orang, memastikan jumlah total pas sampai rupiah terakhir, dan membuat ringkasan yang bisa disalin atau dikirim lewat WhatsApp. Project ini dibuat dengan IBM Bob (vibe coding, prompt 3 layer).

**Problem Statement:** Saat patungan, tagihan sering dibagi rata, padahal pesanan tiap orang berbeda. Hasilnya ada yang merasa rugi dan ada yang sungkan menagih. Menghitung manual juga ribet karena ada menu yang dimakan bersama, serta pajak, service, dan diskon yang harus dibagi proporsional.

**Target Users:** Mahasiswa dan pekerja muda (Gen Z/milenial) yang sering makan atau nongkrong bareng dan ingin pembagian tagihan yang transparan lewat HP.

---

## B. PROMPT ARCHITECTURE (tempel ke IBM Bob, mode Agent)

### Prompt Utama (3 layer)

```
# LAYER 1: ROLE & CONTEXT
Bertindaklah sebagai senior frontend engineer yang ikut IBM Global AI Builders
Challenge (track IT). Nama project: "Patungan Adil", web app Gen Z untuk membagi
tagihan makan bareng secara adil (split bill), dipakai lewat HP.
Constraint: single HTML file, inline CSS dan inline JS saja, tanpa library
eksternal, tanpa framework, tanpa font atau gambar eksternal, harus jalan
sepenuhnya offline.

# LAYER 2: TASK & REQUIREMENTS
ARSITEKTUR (wajib, mencegah bug alur):
- Tiap scene adalah satu <section> dengan atribut hidden. Pindah scene hanya
  lewat fungsi goToScene(n). JANGAN memakai animationend, transitionend, atau
  animation-delay untuk menentukan alur. Alur dikontrol JS.
- Inisialisasi tiap scene dibungkus try/catch tersendiri dengan console.error.
- Elemen yang harus bisa diklik tidak boleh disembunyikan dengan opacity: 0.

FLOW 3 SCENE:
(1) Peserta: input nama + tombol "Tambah", daftar peserta dalam bentuk chip yang
    bisa dihapus. Minimal 2 peserta untuk lanjut, nama tidak boleh kosong atau
    duplikat (tampilkan pesan error inline).
(2) Menu: input nama menu, harga (format ribuan otomatis), dan checkbox "siapa
    yang ikut makan ini" (boleh lebih dari satu). Daftar menu bisa dihapus.
    Validasi: nama terisi, harga > 0, minimal satu orang dipilih.
(3) Hasil: input service (%), pajak (%), diskon (Rp). Hitung bagian tiap orang:
    harga menu dibagi rata ke orang yang ikut menu itu; diskon dikurangkan dari
    subtotal, service dihitung dari subtotal setelah diskon, pajak dihitung dari
    (subtotal setelah diskon + service). Biaya tambahan dibagi proporsional
    terhadap subtotal masing-masing orang. Gunakan metode sisa terbesar
    (largest remainder) agar jumlah semua orang PERSIS sama dengan total tagihan
    dalam rupiah bulat. Hasil update langsung saat input berubah.
    Tombol: "Salin ringkasan" (clipboard, ada pesan jika gagal),
    "Kirim via WhatsApp" (buka https://wa.me/?text=<encodeURIComponent> di tab
    baru), "Kembali", dan "Mulai ulang".
- Jika peserta dihapus, menu yang tidak punya peserta ikut dihapus dan pengguna
  diberi tahu.

PERSONALISASI & TES: parameter ?scene=1 sampai ?scene=3 membuka scene tersebut
(scene 2 dan 3 memuat data contoh bila data kosong), ?demo=1 memuat data
contoh. Tanpa parameter, alur mulai dari scene 1. Semua teks dari pengguna
dimasukkan hanya lewat textContent, jangan pernah innerHTML.

CONFIG: objek CONFIG di atas script berisi mata uang, nilai default
(service 5, pajak 10, diskon 0), data demo, dan SEMUA teks UI. Tidak boleh ada
teks tampil yang di-hardcode di tempat lain. Semua teks Bahasa Indonesia.

# LAYER 3: CONSTRAINTS & OUTPUT
Output satu file index.html. Mobile-first, gunakan dvh bukan vh, area sentuh
minimal 44px, tema "struk belanja" (kertas krem dengan tepi bergerigi, latar
mint, aksen biru dan kuning), font sistem yang rounded, CSS custom properties,
kontras WCAG AA di semua teks, focus state terlihat, hormati
prefers-reduced-motion (hanya memperpendek animasi, tidak menyembunyikan
konten), aria-live untuk pesan status, zero console errors, kode dikomentari
jelas per bagian (CONFIG, state, goToScene, scene 1 sampai 3, hitung,
WhatsApp). Sebelum selesai, telusuri kode langkah demi langkah dan konfirmasi:
(a) ketiga scene bisa dicapai berurutan dan lewat ?scene=, (b) total semua orang
selalu sama persis dengan total tagihan, (c) validasi input bekerja,
(d) tidak ada logika alur yang bergantung pada animationend atau transitionend.
```

### Iterasi Prompt (targeted refinement: 1 masalah = 1 layer)

Jalankan minimal 2 iterasi di IBM Bob, screenshot, lalu isi hasilnya. Contoh siap pakai:

| Iterasi | Masalah yang ditemukan | Layer yang direvisi | Prompt perbaikan |
|---|---|---|---|
| 1 | Tampilan terasa generik | Layer 3 | "Ubah tema jadi lebih mirip struk belanja thermal: font monospace untuk angka, garis putus-putus pemisah, total ditonjolkan dengan highlight kuning. Jangan ubah logika dan layout scene." |
| 2 | Fitur kurang lengkap | Layer 2 | "Tambahkan tombol 'Bagi rata' di scene 2 yang mencentang semua peserta sekaligus. Jangan ubah perhitungan yang sudah ada." |
| 3 (opsional) | Edge case | Layer 2 | "Tambahkan opsi pembulatan ke Rp500 terdekat per orang, dengan selisih ditampung oleh peserta pertama." |

---

## C. CHECKLIST BUKTI (kerjakan sendiri di IBM Bob)

- [ ] Screenshot prompt 3 layer yang dikirim ke IBM Bob (mode Agent)
- [ ] Screenshot iterasi perbaikan (minimal 1)
- [ ] Screenshot hasil pengujian: buka `index.html?demo=1` dan `?scene=3`, uji validasi, uji tombol WhatsApp
- [ ] Bukti eksplorasi IBM Bob: email login trial + screenshot layar utama Bob
- [ ] Upload `index.html` ke Google Drive/GitHub, akses **bukan private** (Anyone with the link)
- [ ] Submit di https://bit.ly/capstone-uni (maks. H+2)

## D. DATA UJI (untuk screenshot)

`?demo=1`: Rani, Dimas, Sari. Menu: Nasi goreng 35.000 (Rani), Mie ayam 28.000 (Dimas), Es teh 8.000 (bertiga), Pizza keju 90.000 (bertiga). Service 5%, pajak 10%. Total tagihan Rp185.955 dan jumlah ketiga bagian harus persis sama dengan angka itu.
