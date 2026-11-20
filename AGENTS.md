# AGENTS.md

## Sumber kebenaran dan batasan
- `README.md` adalah boilerplate Laravel. Sumber konteks proyek adalah `docs/01-business-requirements-analysis.md` (BRA). Baca penuh sebelum perubahan besar; minimal bagian 10 (aturan bisnis), 12 (matriks peran), 18–19 (kriteria dan skenario uji), serta 23 (instruksi kerja OpenCode).
- BRA adalah baseline draft untuk prototipe pembelajaran, bukan SOP/kebijakan resmi PT Indonesia Comnets Plus/PLN Icon Plus. Jangan mengarang alur resmi, jangan menyatakan sistem sebagai sistem resmi perusahaan, dan tandai asumsi/usulan jika belum divalidasi.
- Target BRA: Laravel 12, PHP 8.3+, MySQL, Blade, Bootstrap 5, HTML/CSS/JavaScript; lingkungan Laragon, Composer, Node/NPM, VS Code, Git/GitHub. `composer.json` saat ini hanya mensyaratkan PHP `^8.2`; flag konflik versi ini daripada mengabaikannya.
- Frontend target proyek adalah Blade + Bootstrap 5. Tailwind CSS yang datang bersama scaffold Laravel bukan teknologi frontend final proyek. Konfirmasi sebelum mengganti atau menambah Bootstrap 5.
- Keputusan kebutuhan, validasi hasil, pengujian, dan persetujuan perubahan tetap menjadi tanggung jawab pengembang.

## Aturan bisnis BRA — wajib dijaga di backend
- Draft tidak mengubah stok; pembatalan Draft tidak menghasilkan stock movement (RULE-010, RULE-025).
- Posting mengubah stock balance dan wajib mencatat stock movement yang mereferensikan dokumen, waktu, dan aktor. Perubahan saldo dan status dokumen harus berhasil atau gagal bersama-sama (RULE-013, RULE-014, RULE-018, RULE-027; FR-AUD-004).
- Tidak boleh ada negative stock. Posting harus ditolak jika saldo tidak cukup (RULE-014; BR-006; KPI-02).
- Transfer hanya antar lokasi aktif yang berbeda; lokasi asal berkurang, lokasi tujuan bertambah, total stok gabungan tidak berubah, dan kedua movement dibuat dalam satu transaksi (RULE-015–017).
- Dokumen Posted tidak dihapus atau diubah langsung. Koreksi dilakukan lewat reversal/adjustment yang merujuk dokumen awal dan memiliki alasan (RULE-012, RULE-026; FR-AUD-002–003).
- Serial number harus unik untuk barang yang menggunakan serial tracking: satu serial per unit, jumlah serial sama dengan kuantitas bulat, dan satu serial hanya memiliki satu status/lokasi aktif. BRA Q-03 mengarahkan unique constraint global untuk MVP kecuali domain data membuktikan aturan lain diperlukan (RULE-007–009; Q-03).
- Kuantitas disimpan dengan tipe presisi tetap seperti DECIMAL, bukan floating point (RULE-005).
- Otorisasi divalidasi di backend; menyembunyikan tombol di UI saja tidak cukup (RULE-011; NFR-SEC-002).
- Stock opname merekam snapshot stok sistem; selisih = hasil hitung fisik − snapshot. Penyesuaian memerlukan alasan dan otorisasi (RULE-021–022).

## Kontrol yang masih usulan — butuh persetujuan
- Penguncian lokasi selama sesi Stock Opname aktif adalah proposed control, bukan keputusan final: BRA bagian 7.5 menyebutnya “kontrol MVP yang diusulkan”, RULE-023 memuatnya, dan ASM-10 menandainya sebagai asumsi. Minta persetujuan pengembang sebelum mengimplementasikannya.
- Pertanyaan terbuka BRA Q-01–Q-12 dan kebijakan data yang belum diputuskan di bagian 11.5 tidak boleh diputuskan diam-diam oleh agen. Ajukan ke pengembang dan dokumentasikan keputusan beserta statusnya.

## Kewajiban BRA pendukung
- Gunakan hanya data dummy/sintetis untuk pengujian dan demo. Jangan memakai data produksi/operasional perusahaan tanpa izin dan jangan mengunggah data internal ke AI/cloud tanpa izin (bagian 12, ASM-07, NFR-SEC-005, RISK-08).
- Logika stok kritis wajib memiliki automated tests: inbound, outbound, transfer, opname, dan serial (NFR-MNT-002; bagian 23.1).
- Error teknis dicatat tanpa membocorkan password/token (NFR-LOG-001).
- Git/GitHub digunakan untuk version control. Jangan commit `.env` atau artefak yang diabaikan seperti `vendor/`, `node_modules/`, `public/build/`, dan log.

## Rekomendasi teknis tambahan — bukan aturan BRA
- Repo masih berupa skeleton Laravel 12: `routes/web.php` hanya punya rute `/`, `bootstrap/app.php` belum memiliki middleware kustom, dan belum ada auth/RBAC atau kode domain inventory. Jangan mengira fitur BRA sudah terpasang.
- Perintah repo: `composer dev` menjalankan `php artisan serve`, `queue:listen --tries=1`, `pail`, dan Vite sekaligus. Aset: `npm run dev` / `npm run build`. Tes: `php artisan test` atau `vendor/bin/phpunit`; satu tes: `php artisan test --filter=<nama>`. Format: `vendor/bin/pint`. Tidak ada script lint/typecheck lain.
- Vite memakai `resources/css/app.css` dan `resources/js/app.js`. Tailwind 4 aktif via `@tailwindcss/vite`; tidak ada `tailwind.config.js`, dan tema berada di blok `@theme` pada CSS.
- `.env` sudah diabaikan Git dan berisi kredensial database lokal. Jangan commit, tampilkan, salin ke dokumentasi/chat, atau tulis ke log. Gunakan `.env.example` sebagai contoh; jika nilai bocor, minta rotate.
- `phpunit.xml` saat ini tidak meng-override koneksi DB ke SQLite karena baris `DB_CONNECTION`/`DB_DATABASE` masih dikomentari, sehingga tes memakai DB dari `.env`. Siapkan DB tes terpisah dan aktifkan `RefreshDatabase` sebelum menulis tes yang menyentuh DB; contoh FeatureTest masih mengomentarinya.
- `config/app.php` saat ini memakai UTC, sementara BRA RULE-028 menargetkan Asia/Jakarta. Jangan diam-diam mengubah konfigurasi Laravel; flag konflik dan minta keputusan sebelum menambahkan logika waktu yang sensitif.

## Alur kerja perubahan
- Implementasikan per modul, sertakan automated tests, dan hentikan pekerjaan jika konsistensi stok modul sebelumnya belum lulus (bagian 22–23 BRA).
- Sebelum mengubah kode, ringkas tujuan, batasan, aktor, modul MVP, aturan bisnis yang terdampak, serta konflik/asumsi baru.
- Setelah perubahan, jelaskan file yang diubah, alasan, cara menguji, dan risiko yang belum ditangani.
