# REQUIREMENTS TRACEABILITY ANALYSIS

## 1. Document Information

| Atribut | Nilai |
|---|---|
| Document title | Requirements Traceability Analysis — Inventory Management System |
| Document ID | Akan ditetapkan setelah dokumen disetujui |
| Version | 0.1 — Draft for review |
| Date | 22 November 2026 |
| Owner | Pengembang proyek / pemilik proyek |
| Status | Draft — menunggu review dan persetujuan |
| Project type | Proyek pribadi / prototype pembelajaran PKL |
| Related sources | `AGENTS.md`; `docs/01-business-requirements-analysis.md` (BRA-IMS-001 v1.0); `docs/03-business-decision-log.md` (BDL v0.1) |

**Pernyataan status proyek:** Dokumen ini adalah artefak analisis proyek pribadi. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Seluruh kebutuhan bersifat baseline draft yang masih dapat ditinjau oleh pemilik proyek.

---

## 2. Introduction

Requirements Traceability Analysis (RTA) memetakan kebutuhan dari sumbernya hingga ke perencanaan implementasi dan pengujian. Dalam proyek ini, BRA adalah sumber utama requirement, Business Decision Log mencatat keputusan yang memengaruhi requirement, dan AGENTS.md membatasi cara kerja serta mengingatkan konflik yang belum boleh diselesaikan sepihak.

RTA tidak menambah requirement baru. Jika ditemukan gap, dokumen ini menandainya sebagai gap atau proposed requirement tanpa memberikan ID resmi sebelum change control disetujui.

---

## 3. Purpose and Scope

Tujuan dokumen ini:

- Memastikan seluruh ID requirement BRA dapat ditelusuri ke tujuan, KPI, modul, aturan bisnis, kriteria penerimaan, keputusan, dan rencana pengujian.
- Memastikan seluruh keputusan D-B01–D-B17 dan D-T01–D-T08 memiliki hubungan dengan requirement.
- Mengungkap gap, konflik, dan requirement yang belum memiliki acceptance criteria.
- Menyediakan dasar review sebelum System Design dan implementasi per modul dimulai.

Ruang lingkup dokumen ini:

- Seluruh requirement yang tercantum secara eksplisit di BRA.
- Seluruh keputusan yang tercantum di BDL.
- Batasan kerja dan bukti konflik pada AGENTS.md serta file konfigurasi repositori yang disebut sebagai bukti, bukan sebagai sumber requirement.

Di luar ruang lingkup:

- Implementasi source code, migration, dan perubahan database.
- Perubahan BRA, BDL, atau AGENTS.md.
- Perubahan konfigurasi Laravel, composer, Vite, dan PHPUnit.
- Perintah database, instalasi package, atau eksekusi aplikasi.
- Penetapan status akhir keputusan yang masih Proposed, Open, atau Deferred.

---

## 4. Reference Documents

| No | Dokumen | Peran | Bagian yang dipakai |
|---|---|---|---|
| 1 | `AGENTS.md` | Instruksi kerja dan batasan | Sumber kebenaran, aturan bisnis wajib backend, kontrol usulan, kewajiban pengujian, rekomendasi teknis non-requirement |
| 2 | `docs/01-business-requirements-analysis.md` | Sumber utama requirement | §4–§25, termasuk §8 BR, §9 FR, §10 RULE, §13 NFR, §15 REP, §16 ASM, §17 RISK, §18 AC, §19 Skenario, §21 Q |
| 3 | `docs/03-business-decision-log.md` | Sumber keputusan | §3 definisi status, §4 D-B01–D-B17, §5 D-T01–D-T08, §6 conflict resolution, §7–§11 |
| 4 | `composer.json` | Bukti konflik, bukan requirement | Constraint PHP `^8.2` vs target BRA PHP 8.3+ |
| 5 | `config/app.php` | Bukti konflik, bukan requirement | Timezone UTC vs RULE-028 Asia/Jakarta |
| 6 | `phpunit.xml` | Bukti konflik, bukan requirement | Override DB SQLite masih dikomentari |
| 7 | `bootstrap/app.php` dan `routes/web.php` | Bukti kondisi repositori | Skeleton Laravel, belum ada middleware kustom, auth/RBAC, atau kode domain inventory |
| 8 | Vite dan scaffold Tailwind | Bukti konflik, bukan requirement | `resources/css/app.css`, `@tailwindcss/vite` vs target Blade + Bootstrap 5 |

---

## 5. Traceability Methodology

### 5.1 Kode bantu yang dipakai dalam matriks

| Jenis kode | Kode | Arti |
|---|---|---|
| Role | SA | System Administrator |
| Role | IA | Inventory Administrator |
| Role | WO | Warehouse Operator |
| Role | MV | Manager/Viewer |
| Role | ALL | Seluruh peran pembaca atau aktor terkait |
| Modul | M01 | Authentication & Authorization |
| Modul | M02 | User Management |
| Modul | M03 | Master Data Management |
| Modul | M04 | Warehouse & Location Management |
| Modul | M05 | Stock Inbound |
| Modul | M06 | Stock Outbound |
| Modul | M07 | Stock Transfer |
| Modul | M08 | Stock Opname |
| Modul | M09 | Stock Balance & Stock Card |
| Modul | M10 | Dashboard |
| Modul | M11 | Reporting |
| Modul | M12 | Audit Trail |
| AC | §18.x | Acceptance criteria formal di BRA bagian 18 |
| AC | S A–S E | Skenario uji bisnis minimum di BRA bagian 19 |
| AC | KR §9.x | Hanya kriteria ringkas di tabel functional requirement BRA bagian 9 |
| AC | — | Acceptance criteria formal tidak tersedia |
| Status validasi | AC tersedia | Memiliki AC formal di §18 atau skenario di §19 |
| Status validasi | AC tidak tersedia | Hanya KR §9.x atau tidak ada AC sama sekali |
| Status implementasi | Belum diimplementasikan | Kondisi repositori saat ini masih skeleton sesuai AGENTS.md |

### 5.2 Aturan traceability

- ID requirement menggunakan ID asli BRA. Tidak ada ID requirement baru yang dibuat dalam dokumen ini.
- `REP-01`–`REP-08` adalah ID laporan di BRA §15. `FR-REP-001`–`FR-REP-004` adalah ID functional requirement di BRA §9.7. Keduanya berbeda dan tidak digabung.
- `D-Bxx` dan `D-Txx` adalah ID decision register di BDL, bukan ID requirement BRA.
- Status keputusan disalin apa adanya dari BDL. Tidak ada status yang dinaikkan.
- Dokumen BDL berstatus Approved tidak berarti semua keputusan di dalamnya Accepted.
- Rekomendasi teknis dan saran AI tidak otomatis menjadi business requirement atau keputusan Accepted.
- Keputusan Deferred tidak dijadikan dasar implementasi MVP.
- Keputusan Open tidak dijadikan dasar implementasi.
- Angka coverage dihitung dari jumlah ID yang tercantum, bukan estimasi persentase.

### 5.3 Jumlah dasar requirement

| Kategori | Jumlah ID | Sumber |
|---|---:|---|
| Project Goals | 7 | GOAL-01–GOAL-07, BRA §4.1 |
| KPI | 8 | KPI-01–KPI-08, BRA §4.2 |
| Business Requirements | 15 | BR-001–BR-015, BRA §8 |
| Functional Requirements | 51 | BRA §9.1–§9.8 |
| Non-Functional Requirements | 19 | BRA §13 |
| Business Rules | 28 | RULE-001–RULE-028, BRA §10 |
| Reporting Requirements | 8 | REP-01–REP-08, BRA §15 |
| Assumptions | 10 | ASM-01–ASM-10, BRA §16.1 |
| Risks | 10 | RISK-01–RISK-10, BRA §17 |
| Open Questions | 12 | Q-01–Q-12, BRA §21 |
| **Total** | **168** | Seluruh ID requirement BRA |

Selain 168 ID tersebut, BRA juga memuat 4 peran (§6.2), 6 kelompok AC (§18.1–§18.6), dan 5 skenario uji (§19) yang dipakai sebagai artefak pendukung traceability.

---

## 6. Requirement Classification

### 6.1 Project Goals

| ID | Tujuan | AC formal |
|---|---|---|
| GOAL-01 | Memusatkan data master inventory | — |
| GOAL-02 | Mengendalikan perubahan stok | S A–S E |
| GOAL-03 | Menelusuri pergerakan barang | §18.6 |
| GOAL-04 | Mengelola identitas barang | S C |
| GOAL-05 | Mendukung pemeriksaan fisik | §18.5, S D |
| GOAL-06 | Menyajikan informasi operasional | — |
| GOAL-07 | Menerapkan kontrol akses | §18.6 |

### 6.2 KPI

| ID | Indikator | AC formal |
|---|---|---|
| KPI-01 | 100% transaksi posted memiliki stock movement | S A–S E |
| KPI-02 | 0 kasus stok negatif | S A |
| KPI-03 | Saldo sama dengan penjumlahan ledger | S B, S D |
| KPI-04 | Jumlah serial valid sama dengan kuantitas | S C |
| KPI-05 | Total stok sebelum dan sesudah transfer sama | S B |
| KPI-06 | Aksi tanpa hak akses ditolak | §18.6 |
| KPI-07 | Transaksi posted menampilkan pelaku dan waktu | §18.6 |
| KPI-08 | Dokumen kebutuhan, aturan, diagram, pengujian, dan panduan tersedia | — |

### 6.3 Business Requirements

| ID | Kebutuhan bisnis | Prioritas BRA | AC formal |
|---|---|---|---|
| BR-001 | Satu sumber pencatatan master barang dan lokasi | Must | §18.1 |
| BR-002 | Saldo stok per barang dan lokasi | Must | S B, S D |
| BR-003 | Penambahan stok dapat ditelusuri ke inbound | Must | §18.2 |
| BR-004 | Pengurangan stok dapat ditelusuri ke outbound | Must | §18.3 |
| BR-005 | Perpindahan mempertahankan total stok | Must | §18.4 |
| BR-006 | Mencegah stok negatif | Must | §18.3, S A |
| BR-007 | Setiap perubahan stok memiliki movement | Must | §18.2–§18.5 |
| BR-008 | Mendukung material number dan serial number | Must | S C |
| BR-009 | Struktur gudang, area, rak, level | Must | §18.1 |
| BR-010 | Perbandingan stok sistem dan fisik | Must | §18.5 |
| BR-011 | Fungsi sesuai kewenangan | Must | §18.6 |
| BR-012 | Laporan saldo dan histori transaksi | Must | — |
| BR-013 | Aktivitas kritis dapat ditelusuri | Should | §18.6 |
| BR-014 | Dapat dikembangkan ke multi-warehouse | Could | — |
| BR-015 | Dapat dikembangkan ke QR/barcode | Could | — |

### 6.4 Functional Requirements

| ID | Kebutuhan fungsional | Prioritas | AC formal |
|---|---|---|---|
| FR-AUTH-001 | Login akun terdaftar | Must | — |
| FR-AUTH-002 | Logout | Must | — |
| FR-AUTH-003 | Batasi halaman berdasarkan autentikasi | Must | §18.6 |
| FR-USER-001 | Kelola pengguna | Must | — |
| FR-USER-002 | Tetapkan role pengguna | Must | §18.6 |
| FR-USER-003 | Catat pengguna dan waktu aksi kritis | Should | §18.6 |
| FR-MD-001 | Kelola kategori barang | Must | §18.1 |
| FR-MD-002 | Kelola satuan barang | Must | — |
| FR-MD-003 | Kelola master barang | Must | §18.1 |
| FR-MD-004 | Simpan metode pelacakan | Must | — |
| FR-MD-005 | Simpan minimum stock | Should | — |
| FR-MD-006 | Kelola supplier referensi | Could | — |
| FR-WH-001 | Kelola gudang | Must | — |
| FR-WH-002 | Kelola struktur lokasi | Must | §18.1 |
| FR-WH-003 | Nonaktifkan barang/lokasi tanpa hapus histori | Must | §18.1 |
| FR-IN-001 | Draft inbound bernomor unik | Must | §18.2 |
| FR-IN-002 | Beberapa baris barang inbound | Must | — |
| FR-IN-003 | Validasi kuantitas lebih dari nol | Must | KR §9.3 |
| FR-IN-004 | Validasi serial barang berserial | Must | §18.2, S C |
| FR-IN-005 | Post inbound | Must | §18.2 |
| FR-IN-006 | Simpan referensi sumber penerimaan | Should | — |
| FR-OUT-001 | Draft outbound bernomor unik | Must | §18.3 |
| FR-OUT-002 | Beberapa baris barang outbound | Must | — |
| FR-OUT-003 | Periksa saldo tersedia | Must | §18.3, S A |
| FR-OUT-004 | Validasi serial yang dikeluarkan | Must | §18.3, S C |
| FR-OUT-005 | Post outbound | Must | §18.3 |
| FR-OUT-006 | Simpan tujuan/penerima/alasan | Must | — |
| FR-TRF-001 | Draft transfer bernomor unik | Must | §18.4 |
| FR-TRF-002 | Lokasi asal dan tujuan berbeda | Must | §18.4 |
| FR-TRF-003 | Validasi stok lokasi asal | Must | §18.4 |
| FR-TRF-004 | Movement keluar dan masuk atomik | Must | §18.4, S B, S E |
| FR-TRF-005 | Jaga total stok gabungan | Must | §18.4, S B |
| FR-OPN-001 | Buat sesi opname | Must | §18.5 |
| FR-OPN-002 | Rekam snapshot sistem | Must | §18.5 |
| FR-OPN-003 | Input hitung fisik | Must | §18.5 |
| FR-OPN-004 | Hitung selisih | Must | §18.5, S D |
| FR-OPN-005 | Penyesuaian butuh alasan dan kewenangan | Must | §18.5 |
| FR-OPN-006 | Movement penyesuaian saat session diposting | Must | §18.5, S D |
| FR-STK-001 | Saldo per barang dan lokasi | Must | — |
| FR-STK-002 | Kartu stok | Must | — |
| FR-STK-003 | Pencarian dan filter transaksi | Must | — |
| FR-DASH-001 | Ringkasan jumlah barang dan total kuantitas | Must | — |
| FR-DASH-002 | Barang di bawah minimum stock | Should | — |
| FR-REP-001 | Laporan inbound dan outbound | Must | — |
| FR-REP-002 | Laporan mutasi dan saldo | Must | — |
| FR-REP-003 | Ringkasan hasil opname | Should | — |
| FR-REP-004 | Export Excel/PDF | Could | — |
| FR-AUD-001 | Catat aksi kritis | Should | §18.6 |
| FR-AUD-002 | Cegah perubahan langsung Posted | Must | §18.6 |
| FR-AUD-003 | Koreksi Posted via reversal/adjustment | Should | — |
| FR-AUD-004 | Posting atomik | Must | §18.2, S E |

### 6.5 Non-Functional Requirements

| ID | Aspek | Prioritas | AC formal |
|---|---|---|---|
| NFR-SEC-001 | Password hashing Laravel | NFR | — |
| NFR-SEC-002 | Authorization divalidasi backend | NFR | §18.6 |
| NFR-SEC-003 | CSRF, validasi, escaping, query aman | NFR | — |
| NFR-SEC-004 | Sesi aman dan logout mengakhiri sesi | NFR | — |
| NFR-SEC-005 | Tidak pakai data rahasia perusahaan tanpa izin | NFR | — |
| NFR-INT-001 | Atomik dokumen, movement, serial, saldo | NFR | §18.2, S E |
| NFR-INT-002 | Concurrency posting tanpa stok negatif | NFR | S A, S E |
| NFR-INT-003 | Movement punya dokumen, waktu, aktor | NFR | §18.6 |
| NFR-PERF-001 | Target ≤3 detik pada dataset uji | NFR | — |
| NFR-PERF-002 | Pagination dan filter | NFR | — |
| NFR-USE-001 | Label, validasi, pesan error, konfirmasi | NFR | — |
| NFR-USE-002 | Responsive Bootstrap | NFR | — |
| NFR-MNT-001 | Konvensi Laravel, logic tidak di Blade | NFR | — |
| NFR-MNT-002 | Automated test untuk logika stok kritis | NFR | S A–S E |
| NFR-REL-001 | Rollback saat posting gagal | NFR | S E |
| NFR-OPS-001 | Backup dan restore terdokumentasi | NFR | — |
| NFR-COMP-001 | Versi runtime didokumentasikan | NFR | — |
| NFR-LOG-001 | Logging tanpa password/token | NFR | — |
| NFR-DOC-001 | README, setup, dummy, role, aturan, panduan demo | NFR | — |

### 6.6 Business Rules

| ID | Ringkasan aturan | AC formal |
|---|---|---|
| RULE-001 | Kode barang unik | §18.1 |
| RULE-002 | Kode kategori, satuan, gudang, lokasi unik | §18.1 |
| RULE-003 | Master nonaktif tidak dipakai transaksi baru | §18.1 |
| RULE-004 | Kuantitas lebih dari nol | KR §9.3 |
| RULE-005 | Presisi tetap, bukan floating point | — |
| RULE-006 | Non-serialized dilacak per kuantitas | — |
| RULE-007 | Serialized per unit, satu status/lokasi aktif | S C |
| RULE-008 | Jumlah serial sama dengan qty bulat | §18.2, S C |
| RULE-009 | Serial tidak boleh didaftarkan dua kali | S C |
| RULE-010 | Draft tidak mengubah saldo | §18.2, §18.3 |
| RULE-011 | Posting hanya oleh pengguna berpermission | §18.6 |
| RULE-012 | Posted tidak diubah/dihapus langsung | §18.6 |
| RULE-013 | Inbound menambah saldo saat posting | §18.2 |
| RULE-014 | Outbound mengurangi saldo, tidak negatif | §18.3, S A |
| RULE-015 | Transfer dua sisi dalam satu transaksi | §18.4, S B |
| RULE-016 | Lokasi transfer berbeda dan aktif | §18.4 |
| RULE-017 | Total stok transfer tidak berubah | §18.4, S B |
| RULE-018 | Setiap perubahan saldo menghasilkan movement | S A–S E |
| RULE-019 | Saldo tidak diedit dari form master barang | — |
| RULE-020 | Opening balance lewat dokumen khusus | — |
| RULE-021 | Selisih opname = fisik dikurangi snapshot | §18.5, S D |
| RULE-022 | Penyesuaian opname butuh alasan, pelaku, waktu, otorisasi | §18.5 |
| RULE-023 | Lokasi opname aktif diblokir transaksi lain | — |
| RULE-024 | Nomor dokumen unik dan tidak dipakai ulang | §18.2 |
| RULE-025 | Pembatalan Draft tanpa movement | — |
| RULE-026 | Koreksi Posted lewat reversal/adjustment berreferensi | — |
| RULE-027 | Saldo dan status dokumen atomik | S E |
| RULE-028 | Waktu konsisten, tampilan Asia/Jakarta | — |

### 6.7 Reporting Requirements

| ID | Laporan | AC formal |
|---|---|---|
| REP-01 | Stock Summary | — |
| REP-02 | Stock Card | — |
| REP-03 | Inbound Report | — |
| REP-04 | Outbound Report | — |
| REP-05 | Transfer Report | — |
| REP-06 | Stock Opname Report | — |
| REP-07 | Low Stock Report | — |
| REP-08 | Audit Activity Report | — |

### 6.8 Assumptions

| ID | Asumsi | Status |
|---|---|---|
| ASM-01 | Prototype lokal satu gudang operasional | Asumsi BRA |
| ASM-02 | Satu gudang memiliki banyak lokasi bertingkat | Asumsi BRA |
| ASM-03 | Transaksi dibuat pengguna internal berrole | Asumsi BRA |
| ASM-04 | Inbound/outbound manual, tanpa PO atau sistem lain | Asumsi BRA |
| ASM-05 | Barang serialized dan non-serialized | Asumsi BRA |
| ASM-06 | Opening balance lewat dokumen saldo awal | Asumsi BRA, FR belum tersedia |
| ASM-07 | Data demo dummy/sintetis | Asumsi BRA, wajib sesuai AGENTS.md |
| ASM-08 | Draft dan Posted, approval bertingkat berikutnya | Asumsi BRA |
| ASM-09 | Satu satuan dasar per item pada MVP | Asumsi BRA |
| ASM-10 | Opname mengunci lokasi selama sesi aktif | Asumsi BRA, terkait D-B09 Open |

### 6.9 Risks

| ID | Risiko | AC formal |
|---|---|---|
| RISK-01 | Asumsi alur berbeda dari proses nyata | — |
| RISK-02 | Lingkup proyek terlalu besar | — |
| RISK-03 | AI menghasilkan kode tanpa paham aturan stok | S A–S E |
| RISK-04 | Saldo tidak konsisten dengan ledger | S B, S D, S E |
| RISK-05 | Duplikasi serial number | S C |
| RISK-06 | Dua posting bersamaan menyebabkan stok negatif | S A, S E |
| RISK-07 | Dokumen posted dapat diedit/dihapus | §18.6 |
| RISK-08 | Data internal dipakai tanpa izin | — |
| RISK-09 | Ketergantungan berlebihan pada OpenCode | — |
| RISK-10 | Perubahan kebutuhan tidak tercatat | — |

### 6.10 Open Questions

| ID | Pertanyaan | Status di BDL |
|---|---|---|
| Q-01 | Material number sebagai kode unik atau atribut terpisah | D-B01 Proposed |
| Q-02 | Semua barang memakai serial atau tidak | D-B02 Proposed |
| Q-03 | Serial unik global atau per item dan pabrikan | D-B03 Proposed |
| Q-04 | Kedalaman struktur lokasi | D-B04 Proposed |
| Q-05 | Kuantitas desimal atau bulat | D-B05 Proposed |
| Q-06 | Supplier wajib atau opsional | D-B06 Proposed |
| Q-07 | Approval outbound | D-B07 Proposed |
| Q-08 | Koreksi transaksi Posted | D-B08 Proposed |
| Q-09 | Proteksi lokasi saat opname | D-B09 Open |
| Q-10 | Minimum stock per item atau lokasi | D-B10 Proposed |
| Q-11 | Dukungan retur barang | D-B11 Deferred |
| Q-12 | Batch/lot/expiry tracking | D-B12 Deferred |

---

## 7. Requirements Traceability Matrix

### 7.1 Project Goals

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| GOAL-01 | Memusatkan master inventory | BRA §4.1 | Goal | IA, SA | M03, M04 | RULE-001–RULE-003 | — | D-B01, D-B02, D-B04, D-B05 | Must implisit | MVP | AC tidak tersedia | Belum diimplementasikan |
| GOAL-02 | Mengendalikan perubahan stok | BRA §4.2 | Goal | IA, WO | M05–M09, M12 | RULE-010, RULE-011, RULE-013–RULE-015, RULE-027 | S A–S E | D-B07, D-T06 | Must implisit | MVP | AC tersedia | Belum diimplementasikan |
| GOAL-03 | Menelusuri pergerakan barang | BRA §4.2 | Goal | ALL | M09, M11, M12 | RULE-018, RULE-027 | §18.6 | D-B08, D-T06 | Must implisit | MVP | AC tersedia | Belum diimplementasikan |
| GOAL-04 | Mengelola identitas barang | BRA §4.2 | Goal | IA, WO | M03, M05–M08 | RULE-001, RULE-007–RULE-009 | S C | D-B01, D-B02, D-B03, D-B05 | Must implisit | MVP | AC tersedia | Belum diimplementasikan |
| GOAL-05 | Mendukung pemeriksaan fisik | BRA §4.2 | Goal | IA, WO | M08, M09, M11 | RULE-021–RULE-023 | §18.5, S D | D-B09 | Must implisit | MVP, kontrol Open | AC tersedia, kontrol Open | Belum diimplementasikan |
| GOAL-06 | Menyajikan informasi operasional | BRA §4.2 | Goal | ALL | M10, M11 | RULE-005, RULE-018 | — | D-B13, D-B17 | Must implisit | MVP, export Future | AC tidak tersedia, konflik D-B13 | Belum diimplementasikan |
| GOAL-07 | Menerapkan kontrol akses | BRA §4.2 | Goal | SA, IA, ALL | M01, M02, M12 | RULE-011 | §18.6 | D-T05 | Must implisit | MVP | AC tersedia | Belum diimplementasikan |

### 7.2 KPI

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| KPI-01 | Posting memiliki stock movement | BRA §4.2 | KPI | IA, WO | M05–M09, M12 | RULE-013, RULE-014, RULE-018, RULE-027 | S A–S E | D-T06 | Target prototype | MVP | AC tersedia | Belum diimplementasikan |
| KPI-02 | Nol stok negatif | BRA §4.2 | KPI | IA, WO | M06, M07, M09 | RULE-014 | §18.3, S A | D-T06 | Target prototype | MVP | AC tersedia | Belum diimplementasikan |
| KPI-03 | Saldo sama dengan ledger | BRA §4.2 | KPI | ALL | M09, M11 | RULE-015, RULE-017, RULE-018 | S B, S D | D-T06 | Target prototype | MVP | AC tersedia | Belum diimplementasikan |
| KPI-04 | Validasi serial sesuai kuantitas | BRA §4.2 | KPI | IA, WO | M03, M05–M08 | RULE-007–RULE-009 | §18.2, S C | D-B02, D-B03, D-B05 | Target prototype | MVP | AC tersedia | Belum diimplementasikan |
| KPI-05 | Total stok transfer tetap | BRA §4.2 | KPI | IA, WO | M07, M09 | RULE-015, RULE-017 | §18.4, S B | D-T06 | Target prototype | MVP | AC tersedia | Belum diimplementasikan |
| KPI-06 | Aksi tanpa hak ditolak | BRA §4.2 | KPI | ALL | M01, M02, M12 | RULE-011 | §18.6 | D-T05 | Target prototype | MVP | AC tersedia | Belum diimplementasikan |
| KPI-07 | Posted menampilkan pelaku dan waktu | BRA §4.2 | KPI | ALL | M12, M09 | RULE-018, RULE-027 | §18.6 | D-B16 | Target prototype | MVP | AC tersedia, retensi Open | Belum diimplementasikan |
| KPI-08 | Dokumen proyek tersedia | BRA §4.2 | KPI | ALL | M10, M11, lintas modul | — | — | D-B13, D-T07, D-T08 | Target prototype | MVP | AC tidak tersedia | Belum diimplementasikan, RTA ini draft |

### 7.3 Business Requirements

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| BR-001 | Satu sumber master barang dan lokasi | BRA §8 | Business | IA, SA | M03, M04 | RULE-001–RULE-003 | §18.1 | D-B01, D-B02, D-B04, D-B05 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-002 | Saldo per barang dan lokasi | BRA §8 | Business | ALL | M09 | RULE-018 | S B, S D | D-B10 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-003 | Penambahan stok dapat ditelusuri | BRA §8 | Business | IA, WO | M05 | RULE-013, RULE-018 | §18.2 | D-B06, D-B15 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-004 | Pengurangan stok dapat ditelusuri | BRA §8 | Business | IA, WO | M06 | RULE-014, RULE-018 | §18.3 | D-B07, D-B11 | Must | MVP, retur Deferred | AC tersedia | Belum diimplementasikan |
| BR-005 | Total stok transfer tetap | BRA §8 | Business | IA, WO | M07, M09 | RULE-015–RULE-017 | §18.4, S B | D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-006 | Cegah stok negatif | BRA §8 | Business | IA, WO | M06, M07, M09 | RULE-014 | §18.3, S A | D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-007 | Setiap perubahan stok memiliki movement | BRA §8 | Business | IA, WO | M05–M09, M12 | RULE-018, RULE-027 | §18.2–§18.5 | D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-008 | Material number dan serial number | BRA §8 | Business | IA, WO | M03, M05–M08 | RULE-007–RULE-009 | S C | D-B01, D-B02, D-B03 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-009 | Struktur gudang, area, rak, level | BRA §8 | Business | IA | M04 | RULE-002, RULE-003 | §18.1 | D-B04 | Must | MVP, bin Future | AC tersedia | Belum diimplementasikan |
| BR-010 | Bandingkan stok sistem dan fisik | BRA §8 | Business | IA, WO | M08, M09 | RULE-021–RULE-023 | §18.5, S D | D-B09 | Must | MVP, kontrol Open | AC tersedia | Belum diimplementasikan |
| BR-011 | Fungsi sesuai kewenangan | BRA §8 | Business | SA, IA, ALL | M01, M02, M12 | RULE-011 | §18.6 | D-T05 | Must | MVP | AC tersedia | Belum diimplementasikan |
| BR-012 | Laporan saldo dan histori transaksi | BRA §8 | Business | ALL | M09, M10, M11 | RULE-018 | — | D-B13, D-B17 | Must | MVP, export Deferred | AC tidak tersedia | Belum diimplementasikan |
| BR-013 | Aktivitas kritis dapat ditelusuri | BRA §8 | Business | SA, IA | M12 | RULE-018, RULE-027 | §18.6 | D-B16 | Should | MVP | AC tersedia, retensi Open | Belum diimplementasikan |
| BR-014 | Dapat dikembangkan multi-warehouse | BRA §8 | Business | IA | M04 | RULE-002 | — | D-B04 | Could | Future | AC tidak tersedia | Belum diimplementasikan |
| BR-015 | Dapat dikembangkan QR/barcode | BRA §8 | Business | IA, WO | M03, M04 | — | — | — | Could | Future | AC tidak tersedia | Belum diimplementasikan |

### 7.4 Functional Requirements

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| FR-AUTH-001 | Login akun terdaftar | BRA §9.1 | Functional | ALL | M01 | RULE-011 | KR §9.1 | D-T05 | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-AUTH-002 | Logout mengakhiri sesi | BRA §9.1 | Functional | ALL | M01 | RULE-011 | KR §9.1 | D-T05 | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-AUTH-003 | Batasi halaman berdasarkan autentikasi | BRA §9.1 | Functional | ALL | M01 | RULE-011 | §18.6 | D-T05 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-USER-001 | Kelola pengguna | BRA §9.1 | Functional | SA | M02 | RULE-011 | KR §9.1 | D-T05 | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-USER-002 | Tetapkan role pengguna | BRA §9.1 | Functional | SA | M02 | RULE-011 | §18.6 | D-T05 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-USER-003 | Catat aktor dan waktu aksi kritis | BRA §9.1 | Functional | SA, IA | M02, M12 | RULE-018 | §18.6 | D-B16 | Should | MVP | AC tersedia | Belum diimplementasikan |
| FR-MD-001 | Kelola kategori barang | BRA §9.2 | Functional | IA | M03 | RULE-002 | §18.1 | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-MD-002 | Kelola satuan barang | BRA §9.2 | Functional | IA | M03 | RULE-002, RULE-005 | KR §9.2 | D-B05 | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-MD-003 | Kelola master barang | BRA §9.2 | Functional | IA | M03 | RULE-001 | §18.1 | D-B01, D-B02 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-MD-004 | Simpan metode pelacakan | BRA §9.2 | Functional | IA | M03 | RULE-006–RULE-008 | KR §9.2 | D-B02 | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-MD-005 | Simpan minimum stock | BRA §9.2 | Functional | IA | M03, M10, M11 | — | KR §9.2 | D-B10 | Should | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-MD-006 | Kelola supplier referensi | BRA §9.2 | Functional | IA | M03, M05 | RULE-003 | KR §9.2 | D-B06 | Could | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-WH-001 | Kelola gudang | BRA §9.2 | Functional | IA | M04 | RULE-002 | KR §9.2 | D-B04 | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-WH-002 | Kelola struktur lokasi | BRA §9.2 | Functional | IA | M04 | RULE-002, RULE-003 | §18.1 | D-B04 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-WH-003 | Nonaktifkan barang/lokasi tanpa hapus histori | BRA §9.2 | Functional | IA | M03, M04 | RULE-003 | §18.1 | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-IN-001 | Draft inbound bernomor unik | BRA §9.3 | Functional | IA, WO | M05 | RULE-010, RULE-024 | §18.2 | D-B15 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-IN-002 | Beberapa baris barang inbound | BRA §9.3 | Functional | IA, WO | M05 | RULE-004 | KR §9.3 | — | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-IN-003 | Validasi kuantitas lebih dari nol | BRA §9.3 | Functional | IA, WO | M05 | RULE-004, RULE-005 | KR §9.3 | D-B05 | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-IN-004 | Validasi serial barang berserial | BRA §9.3 | Functional | IA, WO | M05 | RULE-008, RULE-009 | §18.2, S C | D-B03 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-IN-005 | Post inbound | BRA §9.3 | Functional | IA | M05, M09, M12 | RULE-013, RULE-018, RULE-027 | §18.2 | D-B15, D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-IN-006 | Simpan referensi sumber penerimaan | BRA §9.3 | Functional | IA, WO | M05 | — | KR §9.3 | D-B06 | Should | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-OUT-001 | Draft outbound bernomor unik | BRA §9.4 | Functional | IA, WO | M06 | RULE-010, RULE-024 | §18.3 | D-B07, D-B15 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OUT-002 | Beberapa baris barang outbound | BRA §9.4 | Functional | IA, WO | M06 | RULE-004 | KR §9.4 | — | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-OUT-003 | Periksa saldo tersedia | BRA §9.4 | Functional | IA, WO | M06, M09 | RULE-014 | §18.3, S A | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OUT-004 | Validasi serial yang dikeluarkan | BRA §9.4 | Functional | WO | M06 | RULE-007, RULE-008 | §18.3, S C | D-B03 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OUT-005 | Post outbound | BRA §9.4 | Functional | IA | M06, M09, M12 | RULE-014, RULE-018, RULE-027 | §18.3 | D-B07, D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OUT-006 | Simpan tujuan/penerima/alasan | BRA §9.4 | Functional | IA, WO | M06 | — | KR §9.4 | — | Must | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| FR-TRF-001 | Draft transfer bernomor unik | BRA §9.5 | Functional | IA, WO | M07 | RULE-010, RULE-024 | §18.4 | D-B15 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-TRF-002 | Lokasi asal dan tujuan berbeda | BRA §9.5 | Functional | IA, WO | M04, M07 | RULE-016 | §18.4 | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-TRF-003 | Validasi stok lokasi asal | BRA §9.5 | Functional | IA, WO | M07, M09 | RULE-014, RULE-016 | §18.4 | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-TRF-004 | Movement keluar dan masuk atomik | BRA §9.5 | Functional | IA | M07, M09, M12 | RULE-015, RULE-027 | §18.4, S B, S E | D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-TRF-005 | Jaga total stok gabungan | BRA §9.5 | Functional | IA, WO | M07, M09 | RULE-017 | §18.4, S B | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OPN-001 | Buat sesi opname | BRA §9.6 | Functional | IA, WO | M08 | RULE-021 | §18.5 | D-B09 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OPN-002 | Rekam snapshot sistem | BRA §9.6 | Functional | IA, WO | M08 | RULE-021 | §18.5 | D-B09 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OPN-003 | Input hitung fisik | BRA §9.6 | Functional | WO | M08 | RULE-021 | §18.5 | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OPN-004 | Hitung selisih stok | BRA §9.6 | Functional | IA, WO | M08 | RULE-021 | §18.5, S D | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OPN-005 | Penyesuaian butuh alasan dan kewenangan | BRA §9.6 | Functional | IA | M08, M12 | RULE-022 | §18.5 | — | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-OPN-006 | Movement penyesuaian saat sesi diposting | BRA §9.6 | Functional | IA | M08, M09, M12 | RULE-022, RULE-027 | §18.5, S D | D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-STK-001 | Saldo per barang dan lokasi | BRA §9.7 | Functional | ALL | M09 | RULE-018 | — | D-B10 | Must | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-STK-002 | Kartu stok | BRA §9.7 | Functional | ALL | M09, M11 | RULE-018 | — | — | Must | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-STK-003 | Pencarian dan filter transaksi | BRA §9.7 | Functional | ALL | M09, M11 | — | — | — | Must | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-DASH-001 | Ringkasan jumlah barang dan total kuantitas | BRA §9.7 | Functional | ALL | M10 | RULE-005 | — | D-B13 | Must | MVP, interpretasi Proposed | AC tidak tersedia, konflik | Belum diimplementasikan |
| FR-DASH-002 | Barang di bawah minimum stock | BRA §9.7 | Functional | IA, MV | M10, M11 | — | — | D-B10 | Should | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-REP-001 | Laporan inbound dan outbound | BRA §9.7 | Functional | ALL | M11 | RULE-018 | — | D-B17 | Must | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-REP-002 | Laporan mutasi dan saldo | BRA §9.7 | Functional | ALL | M09, M11 | RULE-018 | — | — | Must | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-REP-003 | Ringkasan hasil opname | BRA §9.7 | Functional | IA, MV | M08, M11 | RULE-021, RULE-022 | — | — | Should | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-REP-004 | Export Excel/PDF | BRA §9.7 | Functional | IA, MV | M11 | — | — | D-B17 | Could | Future, Deferred | AC tidak tersedia | Belum diimplementasikan |
| FR-AUD-001 | Catat aksi kritis | BRA §9.8 | Functional | SA, IA | M12 | RULE-018, RULE-027 | §18.6 | D-B16 | Should | MVP | AC tersedia | Belum diimplementasikan |
| FR-AUD-002 | Cegah perubahan langsung Posted | BRA §9.8 | Functional | SA, IA | M05–M08, M12 | RULE-012 | §18.6 | D-B08 | Must | MVP | AC tersedia | Belum diimplementasikan |
| FR-AUD-003 | Koreksi Posted via reversal/adjustment | BRA §9.8 | Functional | IA | M05–M08, M12 | RULE-026 | — | D-B08 | Should | MVP | AC tidak tersedia | Belum diimplementasikan |
| FR-AUD-004 | Posting atomik | BRA §9.8 | Functional | IA, WO | M05–M09, M12 | RULE-027 | §18.2, S E | D-T06 | Must | MVP | AC tersedia | Belum diimplementasikan |

### 7.5 Non-Functional Requirements

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| NFR-SEC-001 | Password hashing Laravel | BRA §13 | NFR | SA, ALL | M01, M02 | RULE-011 | — | D-T05 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-SEC-002 | Authorization divalidasi backend | BRA §13 | NFR | ALL | M01, M02, lintas transaksi | RULE-011 | §18.6 | D-T05 | NFR | MVP | AC tersedia | Belum diimplementasikan |
| NFR-SEC-003 | CSRF, validasi, escaping, query aman | BRA §13 | NFR | ALL | Lintas modul | — | — | D-T05 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-SEC-004 | Sesi aman dan logout | BRA §13 | NFR | ALL | M01 | — | — | D-T05 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-SEC-005 | Tanpa data rahasia perusahaan tanpa izin | BRA §13, AGENTS.md §4 | NFR | ALL | Lintas modul | — | — | — | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-INT-001 | Atomik dokumen, movement, serial, saldo | BRA §13 | NFR | IA, WO | M05–M09 | RULE-027 | §18.2, S E | D-T06 | NFR | MVP | AC tersedia | Belum diimplementasikan |
| NFR-INT-002 | Concurrency posting tanpa stok negatif | BRA §13 | NFR | IA, WO | M05–M09 | RULE-014, RULE-027 | S A, S E | D-T06 | NFR | MVP | AC tersedia | Belum diimplementasikan |
| NFR-INT-003 | Movement punya dokumen, waktu, aktor | BRA §13 | NFR | ALL | M09, M12 | RULE-018 | §18.6 | D-B16, D-T06 | NFR | MVP | AC tersedia | Belum diimplementasikan |
| NFR-PERF-001 | Target ≤3 detik pada dataset uji | BRA §13 | NFR | ALL | Lintas modul | — | — | — | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-PERF-002 | Pagination dan filter | BRA §13 | NFR | ALL | M03, M09, M11 | — | — | — | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-USE-001 | Label, validasi, pesan error, konfirmasi | BRA §13 | NFR | ALL | Lintas modul | — | — | D-T03 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-USE-002 | Responsive Bootstrap | BRA §13 | NFR | ALL | Lintas modul | — | — | D-T03 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-MNT-001 | Konvensi Laravel, logic tidak di Blade | BRA §13 | NFR | ALL | Lintas modul | — | — | D-T03 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-MNT-002 | Automated test logika stok kritis | BRA §13, AGENTS.md §5 | NFR | ALL | M03, M05–M08 | RULE-004–RULE-027 | S A–S E | D-T04 | NFR | MVP | AC tersedia | Belum diimplementasikan |
| NFR-REL-001 | Rollback saat posting gagal | BRA §13 | NFR | IA, WO | M05–M09 | RULE-027 | S E | D-T06 | NFR | MVP | AC tersedia | Belum diimplementasikan |
| NFR-OPS-001 | Backup dan restore terdokumentasi | BRA §13 | NFR | SA | Lintas modul | — | — | D-T07 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-COMP-001 | Versi runtime didokumentasikan | BRA §13 | NFR | SA | Lintas modul | — | — | D-T02, D-T03, D-T07 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-LOG-001 | Logging tanpa password/token | BRA §13, AGENTS.md §5 | NFR | SA, IA | M12 | — | — | D-B16 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |
| NFR-DOC-001 | README, setup, dummy, role, aturan, panduan demo | BRA §13 | NFR | ALL | Lintas modul | — | — | D-T07, D-T08 | NFR | MVP | AC tidak tersedia | Belum diimplementasikan |

### 7.6 Business Rules

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RULE-001 | Kode barang unik | BRA §10 | Business Rule | IA | M03 | RULE-001 | §18.1 | D-B01 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-002 | Kode master unik dalam ruang lingkup | BRA §10 | Business Rule | IA | M03, M04 | RULE-002 | §18.1 | D-B01, D-B04 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-003 | Master nonaktif tidak dipakai transaksi baru | BRA §10 | Business Rule | IA, WO | M03, M04, M05–M08 | RULE-003 | §18.1 | D-B06 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-004 | Kuantitas lebih dari nol | BRA §10 | Business Rule | IA, WO | M05–M08 | RULE-004 | KR §9.3 | D-B05 | Wajib backend | MVP | AC formal tidak tersedia | Belum diimplementasikan |
| RULE-005 | Presisi tetap, bukan floating point | BRA §10 | Business Rule | IA, WO | M03, M05–M09 | RULE-005 | — | D-B05 | Wajib backend | MVP | AC tidak tersedia | Belum diimplementasikan |
| RULE-006 | Non-serialized dilacak per kuantitas | BRA §10 | Business Rule | IA, WO | M03, M05, M06, M09 | RULE-006 | — | D-B02 | Wajib backend | MVP | AC tidak tersedia | Belum diimplementasikan |
| RULE-007 | Satu serial satu status/lokasi aktif | BRA §10 | Business Rule | IA, WO | M03, M05–M08 | RULE-007 | S C | D-B02, D-B03 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-008 | Jumlah serial sama dengan qty bulat | BRA §10 | Business Rule | IA, WO | M05–M08 | RULE-008 | §18.2, S C | D-B03, D-B05 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-009 | Serial tidak didaftarkan dua kali | BRA §10 | Business Rule | IA, WO | M03, M05–M08 | RULE-009 | S C | D-B03 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-010 | Draft tidak mengubah saldo | BRA §10 | Business Rule | IA, WO | M05–M07 | RULE-010 | §18.2, §18.3 | D-B07 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-011 | Posting hanya oleh berpermission | BRA §10 | Business Rule | ALL | M01, M02, M05–M08 | RULE-011 | §18.6 | D-T05 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-012 | Posted tidak diubah/dihapus langsung | BRA §10 | Business Rule | IA, SA | M05–M08, M12 | RULE-012 | §18.6 | D-B08 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-013 | Inbound menambah saldo saat posting | BRA §10 | Business Rule | IA | M05, M09 | RULE-013 | §18.2 | — | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-014 | Outbound tanpa stok negatif | BRA §10 | Business Rule | IA, WO | M06, M09 | RULE-014 | §18.3, S A | D-T06 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-015 | Transfer dua movement satu transaksi | BRA §10 | Business Rule | IA | M07, M09 | RULE-015 | §18.4, S B | D-T06 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-016 | Lokasi transfer berbeda dan aktif | BRA §10 | Business Rule | IA, WO | M04, M07 | RULE-016 | §18.4 | — | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-017 | Total stok transfer tidak berubah | BRA §10 | Business Rule | IA, WO | M07, M09 | RULE-017 | §18.4, S B | — | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-018 | Perubahan saldo menghasilkan movement | BRA §10 | Business Rule | IA, WO | M05–M09, M12 | RULE-018 | S A–S E | D-T06 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-019 | Saldo tidak diedit manual | BRA §10 | Business Rule | IA | M03, M09 | RULE-019 | — | D-B14 | Wajib backend | MVP | AC tidak tersedia | Belum diimplementasikan |
| RULE-020 | Opening balance berdokumen | BRA §10 | Business Rule | IA | M05, M09 | RULE-020 | — | D-B14 | Wajib backend | MVP, FR belum ada | AC tidak tersedia | Belum diimplementasikan |
| RULE-021 | Selisih opname = fisik dikurangi snapshot | BRA §10 | Business Rule | IA, WO | M08 | RULE-021 | §18.5, S D | — | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-022 | Penyesuaian opname beralasan dan terotorisasi | BRA §10 | Business Rule | IA | M08, M12 | RULE-022 | §18.5 | — | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-023 | Blokir lokasi saat opname aktif | BRA §10, AGENTS.md §4 | Business Rule | IA, WO | M08, M05–M07 | RULE-023 | — | D-B09 | Wajib jika disetujui | Open | Menunggu keputusan D-B09 | Belum diimplementasikan |
| RULE-024 | Nomor dokumen unik dan tidak dipakai ulang | BRA §10 | Business Rule | IA, WO | M05–M08 | RULE-024 | §18.2 sebagian | D-B15 | Wajib backend | MVP | AC sebagian tersedia | Belum diimplementasikan |
| RULE-025 | Pembatalan Draft tanpa movement | BRA §10 | Business Rule | IA, WO | M05–M07, M12 | RULE-025 | — | D-B15 | Wajib backend | MVP | AC tidak tersedia | Belum diimplementasikan |
| RULE-026 | Koreksi Posted berreferensi | BRA §10 | Business Rule | IA | M05–M08, M12 | RULE-026 | — | D-B08 | Wajib backend | MVP | AC tidak tersedia | Belum diimplementasikan |
| RULE-027 | Saldo dan status atomik | BRA §10 | Business Rule | IA, WO | M05–M09, M12 | RULE-027 | S E | D-T06 | Wajib backend | MVP | AC tersedia | Belum diimplementasikan |
| RULE-028 | Waktu konsisten Asia/Jakarta | BRA §10 | Business Rule | ALL | Lintas transaksi, M12 | RULE-028 | — | D-T01 | Wajib backend | MVP, config Proposed | AC tidak tersedia | Belum diimplementasikan |

### 7.7 Reporting Requirements

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| REP-01 | Stock Summary | BRA §15 | Reporting | ALL | M11, M09 | RULE-018 | — | D-B13 | — | MVP | AC tidak tersedia | Belum diimplementasikan |
| REP-02 | Stock Card | BRA §15 | Reporting | ALL | M11, M09 | RULE-018 | — | — | — | MVP | AC tidak tersedia | Belum diimplementasikan |
| REP-03 | Inbound Report | BRA §15 | Reporting | IA, MV, WO terbatas | M11, M05 | RULE-018 | — | D-B17 | — | MVP | AC tidak tersedia | Belum diimplementasikan |
| REP-04 | Outbound Report | BRA §15 | Reporting | IA, MV, WO terbatas | M11, M06 | RULE-018 | — | D-B17 | — | MVP | AC tidak tersedia | Belum diimplementasikan |
| REP-05 | Transfer Report | BRA §15 | Reporting | IA, MV | M11, M07 | RULE-015, RULE-017 | — | — | — | MVP | AC tidak tersedia | Belum diimplementasikan |
| REP-06 | Stock Opname Report | BRA §15 | Reporting | IA, MV | M11, M08 | RULE-021, RULE-022 | — | D-B09 | — | MVP | AC tidak tersedia | Belum diimplementasikan |
| REP-07 | Low Stock Report | BRA §15 | Reporting | IA, MV | M11, M10 | — | — | D-B10 | — | MVP | AC tidak tersedia | Belum diimplementasikan |
| REP-08 | Audit Activity Report | BRA §15 | Reporting | SA, IA | M11, M12 | RULE-018, RULE-027 | — | D-B16, D-B17 | — | MVP, export audit Future | AC tidak tersedia | Belum diimplementasikan |

### 7.8 Assumptions

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| ASM-01 | Prototype lokal satu gudang operasional | BRA §16.1 | Assumption | ALL | M04 | RULE-002 | — | — | — | MVP | Asumsi, belum divalidasi | Belum diimplementasikan |
| ASM-02 | Lokasi bertingkat dalam satu gudang | BRA §16.1 | Assumption | IA | M04 | RULE-002 | §18.1 | D-B04 | — | MVP | Asumsi, belum divalidasi | Belum diimplementasikan |
| ASM-03 | Pengguna internal berrole | BRA §16.1 | Assumption | ALL | M01, M02 | RULE-011 | §18.6 | D-T05 | — | MVP | Asumsi, belum divalidasi | Belum diimplementasikan |
| ASM-04 | Inbound/outbound manual tanpa integrasi | BRA §16.1 | Assumption | IA, WO | M05, M06 | — | — | — | — | MVP | Asumsi, belum divalidasi | Belum diimplementasikan |
| ASM-05 | Barang serialized dan non-serialized | BRA §16.1 | Assumption | IA, WO | M03, M05–M08 | RULE-006–RULE-009 | S C | D-B02 | — | MVP | Asumsi, belum divalidasi | Belum diimplementasikan |
| ASM-06 | Opening balance lewat dokumen saldo awal | BRA §16.1 | Assumption | IA | M05, M09 | RULE-019, RULE-020 | — | D-B14 | — | MVP, FR belum ada | Asumsi, AC tidak tersedia | Belum diimplementasikan |
| ASM-07 | Data demo dummy/sintetis | BRA §16.1, AGENTS.md §4 | Assumption | ALL | Lintas modul | — | — | — | — | MVP | Asumsi, wajib dipatuhi | Belum diimplementasikan |
| ASM-08 | Draft dan Posted, approval bertingkat berikutnya | BRA §16.1 | Assumption | IA, WO | M05–M08 | RULE-010–RULE-012 | §18.6 | D-B07 | — | MVP | Asumsi, belum divalidasi | Belum diimplementasikan |
| ASM-09 | Satu satuan dasar per item pada MVP | BRA §16.1 | Assumption | IA | M03, M05–M09 | RULE-005 | — | D-B05 | — | MVP | Asumsi, belum divalidasi | Belum diimplementasikan |
| ASM-10 | Opname mengunci lokasi selama sesi aktif | BRA §16.1, AGENTS.md §4 | Assumption | IA, WO | M08, M05–M07 | RULE-023 | — | D-B09 | — | Open | Menunggu keputusan | Belum diimplementasikan |

### 7.9 Risks

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RISK-01 | Asumsi alur berbeda dari proses nyata | BRA §17 | Risk | ALL | Lintas modul | — | — | — | — | Lintas MVP | Risiko, mitigasi dokumentasi | Belum diimplementasikan |
| RISK-02 | Lingkup proyek terlalu besar | BRA §17 | Risk | ALL | Lintas modul | — | — | D-B11, D-B12, D-B17 | — | Lintas MVP | Risiko, prioritas Must | Belum diimplementasikan |
| RISK-03 | AI menghasilkan kode tanpa paham aturan stok | BRA §17, AGENTS.md | Risk | ALL | M05–M08 | RULE-010–RULE-027 | S A–S E | D-T04 | — | Lintas MVP | Risiko, mitigasi test | Belum diimplementasikan |
| RISK-04 | Saldo tidak konsisten dengan ledger | BRA §17 | Risk | IA, WO | M09, M11 | RULE-018, RULE-027 | S B, S D, S E | D-T06 | — | Lintas MVP | Risiko, mitigasi transaksi | Belum diimplementasikan |
| RISK-05 | Duplikasi serial number | BRA §17 | Risk | IA, WO | M03, M05–M08 | RULE-007–RULE-009 | S C | D-B03 | — | Lintas MVP | Risiko, mitigasi unique constraint | Belum diimplementasikan |
| RISK-06 | Dua posting bersamaan menyebabkan stok negatif | BRA §17 | Risk | IA, WO | M05–M09 | RULE-014, RULE-027 | S A, S E | D-T06 | — | Lintas MVP | Risiko, mitigasi locking | Belum diimplementasikan |
| RISK-07 | Dokumen posted dapat diedit/dihapus | BRA §17 | Risk | IA, SA | M05–M08, M12 | RULE-012, RULE-026 | §18.6 | D-B08 | — | Lintas MVP | Risiko, mitigasi immutable | Belum diimplementasikan |
| RISK-08 | Data internal dipakai tanpa izin | BRA §17, AGENTS.md §4 | Risk | ALL | Lintas modul | — | — | — | — | Lintas MVP | Risiko, mitigasi data dummy | Belum diimplementasikan |
| RISK-09 | Ketergantungan berlebihan pada OpenCode | BRA §17 | Risk | ALL | Lintas modul | — | — | — | — | Lintas MVP | Risiko, mitigasi review | Belum diimplementasikan |
| RISK-10 | Perubahan kebutuhan tidak tercatat | BRA §17 | Risk | ALL | Lintas modul | — | — | D-T08 | — | Lintas MVP | Risiko, mitigasi change log | Belum diimplementasikan |

### 7.10 Open Questions

| Requirement ID | Requirement Description | Source | Type | Role | Module | Business Rules | Acceptance Criteria | Decision ID | Priority | MVP / Future | Validation Status | Implementation Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Q-01 | Material number sebagai kode unik atau atribut terpisah | BRA §21 | Open Question | IA | M03 | RULE-001 | §18.1 | D-B01 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-02 | Semua barang memakai serial atau tidak | BRA §21 | Open Question | IA | M03, M05–M08 | RULE-006–RULE-008 | S C | D-B02 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-03 | Serial unik global atau per item dan pabrikan | BRA §21 | Open Question | IA, WO | M03, M05–M08 | RULE-009 | S C | D-B03 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-04 | Kedalaman struktur lokasi | BRA §21 | Open Question | IA | M04 | RULE-002 | §18.1 | D-B04 | — | MVP, bin Future | Proposed, belum final | Belum diimplementasikan |
| Q-05 | Kuantitas desimal atau bulat | BRA §21 | Open Question | IA, WO | M03, M05–M09 | RULE-004, RULE-005, RULE-008 | KR §9.3 | D-B05 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-06 | Supplier wajib atau opsional | BRA §21 | Open Question | IA, WO | M03, M05 | RULE-003 | KR §9.3 | D-B06 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-07 | Approval outbound | BRA §21 | Open Question | IA, WO | M06, M01 | RULE-010, RULE-011 | §18.6 | D-B07 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-08 | Koreksi transaksi Posted | BRA §21 | Open Question | IA | M05–M08, M12 | RULE-012, RULE-026 | — | D-B08 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-09 | Proteksi lokasi saat opname | BRA §21 | Open Question | IA, WO | M08, M05–M07 | RULE-023 | — | D-B09 | — | Open | Menunggu keputusan | Belum diimplementasikan |
| Q-10 | Minimum stock per item atau lokasi | BRA §21 | Open Question | IA | M03, M10, M11 | — | — | D-B10 | — | MVP | Proposed, belum final | Belum diimplementasikan |
| Q-11 | Dukungan retur barang | BRA §21 | Open Question | IA, WO | M06 | — | — | D-B11 | — | Future, Deferred | Deferred | Belum diimplementasikan |
| Q-12 | Batch/lot/expiry tracking | BRA §21 | Open Question | IA, WO | M03, M05–M08 | — | — | D-B12 | — | Future, Deferred | Deferred | Belum diimplementasikan |

---

## 8. Business Decision Traceability

| Decision ID | Status BDL | Requirement yang dipengaruhi | Modul terdampak | Pengaruh | Dapat jadi dasar MVP |
|---|---|---|---|---|---|
| D-B01 | Proposed | Q-01, FR-MD-003, RULE-001 | M03 | Memisahkan `item_code` dan `material_number` | Tidak, masih Proposed |
| D-B02 | Proposed | Q-02, FR-MD-004, ASM-05, RULE-006–RULE-008 | M03, M05–M08 | Tracking type per item | Tidak, masih Proposed |
| D-B03 | Proposed | Q-03, RULE-007–RULE-009, KPI-04 | M03, M05–M08 | Unique constraint serial global | Tidak, masih Proposed |
| D-B04 | Proposed | Q-04, BR-009, FR-WH-002, ASM-02 | M04 | Hierarki Warehouse > Area > Rack > Level, bin future | Tidak, masih Proposed |
| D-B05 | Proposed | Q-05, RULE-004, RULE-005, RULE-008, FR-IN-003 | M03, M05–M09 | Presisi quantity dan serialized integer | Tidak, masih Proposed |
| D-B06 | Proposed | Q-06, FR-IN-006, FR-MD-006, ASM-04 | M03, M05 | Supplier opsional pada inbound | Tidak, masih Proposed |
| D-B07 | Proposed | Q-07, FR-OUT-001–FR-OUT-005, ASM-08, RULE-010–RULE-012 | M01, M06, M12 | Draft/Posted dengan permission posting | Tidak, masih Proposed |
| D-B08 | Proposed | Q-08, FR-AUD-003, RULE-012, RULE-026 | M05–M08, M12 | Reversal/adjustment berreferensi | Tidak, masih Proposed |
| D-B09 | Open | Q-09, RULE-023, ASM-10, FR-OPN-001–FR-OPN-006 | M08, M05–M07 | Proteksi lokasi opname belum disetujui | Tidak, Open |
| D-B10 | Proposed | Q-10, FR-MD-005, FR-DASH-002, REP-07 | M03, M10, M11 | Minimum stock per item | Tidak, masih Proposed |
| D-B11 | Deferred | Q-11, RISK-02, BRA §5.2 | M06 | Retur di luar MVP | Tidak, Deferred |
| D-B12 | Deferred | Q-12, BRA §5.2, §5.3 | M03, M05–M08 | Batch/expiry di luar MVP | Tidak, Deferred |
| D-B13 | Proposed | FR-DASH-001, BRA §14.2, KPI-08 | M10 | Jumlah SKU dan ringkasan per satuan menggantikan total qty lintas satuan | Tidak, masih Proposed dan konflik belum final |
| D-B14 | Proposed | RULE-020, ASM-06, BRA §22 tahap 2 | M05, M09 | Dokumen opening balance, FR detail belum tersedia | Tidak, masih Proposed |
| D-B15 | Proposed | RULE-024–RULE-026, BRA §7.6, §18.2 | M05–M08, M12 | Cancel Draft dan double posting perlu AC eksplisit | Tidak, masih Proposed |
| D-B16 | Open | FR-AUD-001, BRA §11.5, NFR-LOG-001 | M12 | Retensi audit log belum diputus | Tidak, Open |
| D-B17 | Deferred | FR-REP-004, BRA §5.3, RISK-02 | M11 | Export Excel/PDF ditunda | Tidak, Deferred |

Ringkasan status business decision: Proposed 12, Open 2, Deferred 3, Accepted 0, Rejected 0.

---

## 9. Technical Decision Traceability

| Decision ID | Status BDL | Requirement yang dipengaruhi | Modul terdampak | Pengaruh | Dapat jadi dasar MVP |
|---|---|---|---|---|---|
| D-T01 | Proposed | RULE-028, NFR-COMP-001, `config/app.php` | Lintas transaksi, M12 | Timezone Asia/Jakarta belum diterapkan di config | Tidak, masih Proposed |
| D-T02 | Accepted | NFR-COMP-001, BRA target PHP 8.3+, `composer.json ^8.2` | Lintas modul | PHP minimum 8.3 menjadi baseline | Ya, untuk runtime |
| D-T03 | Accepted | NFR-USE-002, BRA stack Blade+Bootstrap 5, scaffold Tailwind | Lintas modul | Blade + Bootstrap 5 menjadi baseline frontend | Ya, untuk UI |
| D-T04 | Proposed | AGENTS.md, `phpunit.xml`, NFR-MNT-002 | Lintas modul pengujian | Database testing terpisah belum disetujui | Tidak, masih Proposed |
| D-T05 | Proposed | FR-AUTH-003, NFR-SEC-002, KPI-06 | M01, M02, lintas transaksi | Middleware auth dan Policy/Gate belum disetujui | Tidak, masih Proposed |
| D-T06 | Proposed | NFR-INT-001, NFR-INT-002, NFR-REL-001, RISK-04, RISK-06 | M05–M09, M12 | Transaction dan lock saldo belum disetujui | Tidak, masih Proposed |
| D-T07 | Proposed | NFR-OPS-001, NFR-COMP-001, NFR-DOC-001 | Lintas modul, SA | Backup/restore dan daftar versi belum didokumentasikan | Tidak, masih Proposed |
| D-T08 | Proposed | Q-01–Q-12, BRA §20–§22 | Lintas modul | Penambahan/revisi ID BRA harus lewat change control | Tidak, masih Proposed |

Ringkasan status technical decision: Accepted 2, Proposed 6, Open 0, Deferred 0, Rejected 0.

**Catatan penting:** Status Approved pada BDL tidak mengubah status keputusan di atas. Hanya D-T02 dan D-T03 yang Accepted.

---

## 10. Business Rules Traceability

| Rule ID | Aturan | FR terkait | Modul | AC terkait | Decision ID | Status penerapan |
|---|---|---|---|---|---|---|
| RULE-001 | Kode barang unik | FR-MD-003 | M03 | §18.1 | D-B01 | Wajib backend, belum diimplementasikan |
| RULE-002 | Kode master unik | FR-MD-001, FR-MD-002, FR-WH-001, FR-WH-002 | M03, M04 | §18.1 | D-B01, D-B04 | Wajib backend, belum diimplementasikan |
| RULE-003 | Master nonaktif tidak dipakai | FR-WH-003, FR-IN-002, FR-OUT-002 | M03, M04, M05, M06 | §18.1 | D-B06 | Wajib backend, belum diimplementasikan |
| RULE-004 | Qty lebih dari nol | FR-IN-003, FR-OUT-002, FR-TRF-001, FR-OPN-003 | M05–M08 | KR §9.3 | D-B05 | Wajib backend, AC formal tidak tersedia |
| RULE-005 | Presisi tetap | FR-MD-002, FR-IN-003 | M03, M05–M09 | — | D-B05 | Wajib backend, AC tidak tersedia |
| RULE-006 | Non-serialized per kuantitas | FR-MD-004 | M03, M05, M06, M09 | — | D-B02 | Wajib backend, AC tidak tersedia |
| RULE-007 | Satu serial satu status/lokasi | FR-MD-004, FR-OUT-004 | M03, M05–M08 | S C | D-B02, D-B03 | Wajib backend, belum diimplementasikan |
| RULE-008 | Jumlah serial sama dengan qty bulat | FR-IN-004, FR-OUT-004 | M05, M06, M08 | §18.2, S C | D-B03, D-B05 | Wajib backend, belum diimplementasikan |
| RULE-009 | Serial tidak duplikat | FR-IN-004 | M03, M05–M08 | S C | D-B03 | Wajib backend, belum diimplementasikan |
| RULE-010 | Draft tidak ubah saldo | FR-IN-001, FR-OUT-001, FR-TRF-001 | M05–M07 | §18.2, §18.3 | D-B07 | Wajib backend, belum diimplementasikan |
| RULE-011 | Posting berpermission | FR-AUTH-003, FR-USER-002, FR-IN-005, FR-OUT-005 | M01, M02, M05–M08 | §18.6 | D-T05 | Wajib backend, belum diimplementasikan |
| RULE-012 | Posted immutable | FR-AUD-002 | M05–M08, M12 | §18.6 | D-B08 | Wajib backend, belum diimplementasikan |
| RULE-013 | Inbound menambah saldo | FR-IN-005 | M05, M09 | §18.2 | — | Wajib backend, belum diimplementasikan |
| RULE-014 | Outbound tanpa stok negatif | FR-OUT-003, FR-OUT-005, FR-TRF-003 | M06, M07, M09 | §18.3, S A | D-T06 | Wajib backend, belum diimplementasikan |
| RULE-015 | Transfer atomik dua sisi | FR-TRF-004 | M07, M09 | §18.4, S B | D-T06 | Wajib backend, belum diimplementasikan |
| RULE-016 | Lokasi transfer berbeda dan aktif | FR-TRF-002 | M04, M07 | §18.4 | — | Wajib backend, belum diimplementasikan |
| RULE-017 | Total stok transfer tetap | FR-TRF-005 | M07, M09 | §18.4, S B | — | Wajib backend, belum diimplementasikan |
| RULE-018 | Perubahan saldo menghasilkan movement | FR-IN-005, FR-OUT-005, FR-TRF-004, FR-OPN-006, FR-AUD-001 | M05–M09, M12 | S A–S E | D-T06 | Wajib backend, belum diimplementasikan |
| RULE-019 | Saldo tidak diedit manual | FR-MD-003, FR-STK-001 | M03, M09 | — | D-B14 | Wajib backend, AC tidak tersedia |
| RULE-020 | Opening balance berdokumen | Tidak tersedia | M05, M09 | — | D-B14 | Gap FR, Proposed |
| RULE-021 | Selisih fisik dikurangi snapshot | FR-OPN-002, FR-OPN-004 | M08 | §18.5, S D | — | Wajib backend, belum diimplementasikan |
| RULE-022 | Penyesuaian beralasan dan terotorisasi | FR-OPN-005, FR-OPN-006 | M08, M12 | §18.5 | — | Wajib backend, belum diimplementasikan |
| RULE-023 | Blokir lokasi saat opname aktif | FR-OPN-001–FR-OPN-006 | M08, M05–M07 | — | D-B09 | Open, jangan diimplementasikan |
| RULE-024 | Nomor dokumen unik | FR-IN-001, FR-OUT-001, FR-TRF-001 | M05–M08 | §18.2 sebagian | D-B15 | Wajib backend, AC sebagian |
| RULE-025 | Cancel Draft tanpa movement | Tidak tersedia FR eksplisit | M05–M07, M12 | — | D-B15 | Wajib backend, AC tidak tersedia |
| RULE-026 | Koreksi Posted berreferensi | FR-AUD-003 | M05–M08, M12 | — | D-B08 | Wajib backend, AC tidak tersedia |
| RULE-027 | Saldo dan status atomik | FR-AUD-004, FR-TRF-004, FR-IN-005, FR-OUT-005 | M05–M09, M12 | S E | D-T06 | Wajib backend, belum diimplementasikan |
| RULE-028 | Waktu konsisten Asia/Jakarta | NFR-COMP-001 | Lintas transaksi, M12 | — | D-T01 | Wajib backend, config masih UTC, Proposed |

---

## 11. Requirement Gap Analysis

| Gap ID jika tersedia | Description | Related Requirement ID | Related Decision ID | Evidence | Business Impact | Technical Impact | Recommended Resolution | Approval Status |
|---|---|---|---|---|---|---|---|---|
| — topik Opening Balance | Tidak ada FR khusus untuk saldo awal, hanya prinsip dan tahap implementasi | RULE-019, RULE-020, ASM-06 | D-B14 | BRA §10 RULE-020, §16.1 ASM-06, §22 tahap 2; BDL D-B14 | Saldo awal berisiko dicatat tanpa jejak | Tidak ada kontrak dokumen dan movement opening | Tambahkan FR dan AC opening balance melalui change control BRA | Belum disetujui, D-B14 Proposed |
| — topik Cancel Draft | Aturan ada, tetapi tidak ada FR eksplisit maupun AC formal | RULE-025, BRA §7.6 | D-B15 | BRA §10 RULE-025, §7.6; BDL D-B15 | Draft dapat dibatalkan secara tidak konsisten | Tidak ada guard status dan uji movement nol | Tetapkan FR cancel Draft dan AC sebelum desain | Belum disetujui, D-B15 Proposed |
| — topik Double Posting Prevention | AC sebagian tersedia di §18.2, FR belum eksplisit untuk idempotensi posting | FR-IN-005, FR-OUT-005, RULE-024, RULE-027 | D-B15 | BRA §18.2 butir 3, BDL conflict §6 | Posting ulang dapat menggandakan saldo | Perlu status guard dan constraint nomor unik | Perkuat FR dan AC idempotent posting | Belum disetujui, D-B15 Proposed |
| — topik Posted Reversal/Adjustment | FR-AUD-003 ada, tetapi detail alur, alasan, dan AC belum tersedia | FR-AUD-003, RULE-012, RULE-026 | D-B08 | BRA §7.6, §9.8, BDL D-B08 | Koreksi Posted dapat tidak konsisten | Belum ada spesifikasi service reversal dan relasi dokumen | Tambahkan FR detail dan AC reversal/adjustment | Belum disetujui, D-B08 Proposed |
| — topik Stock Opname Location Protection | RULE-023 dan ASM-10 menyebut penguncian, tetapi BDL menandainya Open | FR-OPN-001–FR-OPN-006, RULE-023, ASM-10 | D-B09 | BRA §7.5, §10, §16.1; AGENTS.md §4; BDL D-B09 | Transaksi paralel dapat mengubah hasil hitung fisik | Desain concurrency dan override tidak bisa dibekukan | Putuskan soft block, reconcile movement, atau tanpa lock | Belum disetujui, D-B09 Open |
| — topik Audit Log Retention | Periode retensi dan akses audit belum diputus | FR-AUD-001, REP-08, KPI-07 | D-B16 | BRA §11.5, BDL D-B16 | Jangkauan histori audit tidak jelas | Kebijakan penyimpanan dan akses belum final | Tetapkan periode retensi dan aturan akses audit | Belum disetujui, D-B16 Open |
| — topik Backup and Restore | NFR ada, runbook belum tersedia | NFR-OPS-001, NFR-DOC-001 | D-T07 | BRA §13, BDL D-T07 | Demo sulit dipulihkan jika gagal | Tidak ada prosedur dump dan restore | Tulis runbook backup/restore di dokumentasi teknis | Belum disetujui, D-T07 Proposed |
| — topik Requirement Acceptance Criteria | Hanya 6 kelompok AC formal. Banyak FR, seluruh NFR, dan seluruh REP tidak memiliki AC formal | FR-AUTH-001–FR-REP-004, NFR-SEC-001–NFR-DOC-001, REP-01–REP-08 | D-B15, D-T08 | BRA §18 hanya 18.1–18.6 vs 51 FR dan 19 NFR | Validasi hasil tidak seragam | Test case sulit distandardisasi | Susun AC tambahan lewat change control BRA | Belum disetujui |
| — topik Requirement Detailed Specification | Beberapa kebutuhan hanya berupa ringkasan: dashboard, format serial, presisi satuan, koreksi posted, minimum stock | FR-DASH-001, FR-MD-004, RULE-005, RULE-009, D-B13, D-B10 | D-B05, D-B10, D-B13 | BRA §9, §11.5, §14.2; BDL D-B05, D-B10, D-B13 | Interpretasi UI dan aturan dapat berbeda | Desain query dan constraint belum final | Tandai sebagai proposed requirement detail, minta persetujuan | Belum disetujui |
| — topik Runtime Version List | PHP minimum sudah Accepted, daftar versi Laravel, MySQL, Node, dan browser belum didokumentasikan | NFR-COMP-001 | D-T02, D-T07 | BDL D-T02, D-T07; BRA §13 | Environment demo tidak reproducible | Matrix kompatibilitas belum ada | Lengkapi daftar versi sebelum implementasi | D-T02 Accepted, D-T07 Proposed |
| — topik Requirement ID Change Control | Gap perlu ID baru, tetapi BRA tidak boleh diubah langsung | Q-01–Q-12, BRA §20–§22 | D-T08 | AGENTS.md §3, BDL D-T08 | Requirement baru berisiko tidak tercatat | Traceability dapat putus | Ajukan revisi BRA dan catat di change history | Belum disetujui, D-T08 Proposed |
| — topik Implementation Gap | Seluruh requirement masih belum diimplementasikan karena repositori skeleton | Seluruh 168 ID | D-T05, D-T06, D-T02, D-T03 | AGENTS.md §6, `routes/web.php`, `bootstrap/app.php` | Prototype belum dapat didemonstrasikan | Belum ada modul, migration, dan test domain | Ikuti BRA §22 per modul dengan gerbang kualitas | Perlu persetujuan untuk mulai implementasi |

**Catatan:** Gap di atas bukan requirement baru. Jika hendak dijadikan requirement, beri status proposed requirement dan ajukan ID resmi melalui change control sesuai D-T08.

---

## 12. Conflict Analysis

| Conflict | Sumber dan bukti | Requirement terdampak | Dampak desain sistem | Keputusan tersedia | Status menunggu persetujuan |
|---|---|---|---|---|---|
| FR-DASH-001 total kuantitas vs larangan §14.2 | BRA §9.7 FR-DASH-001 vs BRA §14.2 catatan satuan berbeda | FR-DASH-001, KPI-08, GOAL-06 | Agregasi lintas satuan menyesatkan | D-B13 usul SKU count dan ringkasan per satuan | Ya, D-B13 Proposed |
| RULE-023 dan §7.5 dan ASM-10 menyebut blokir, AGENTS.md menyebut usulan | BRA §7.5, §10 RULE-023, §16.1 ASM-10, AGENTS.md §4 | RULE-023, FR-OPN-001–006, BR-010 | Concurrency opname tidak bisa dibekukan | D-B09 menunggu analisis risiko | Ya, D-B09 Open |
| RULE-028 Asia/Jakarta vs `config/app.php` UTC | BRA §10 RULE-028 vs `config/app.php` | RULE-028, NFR-COMP-001, seluruh transaksi | Timestamp ledger dan tampilan bisa berbeda | D-T01 usul Asia/Jakarta | Ya, D-T01 Proposed |
| BRA PHP 8.3+ vs `composer.json ^8.2` | BRA cover dan §16.3 vs `composer.json` | NFR-COMP-001 | Constraint runtime tidak konsisten | D-T02 menetapkan PHP minimum 8.3 | Tidak, D-T02 Accepted, perubahan file belum dilakukan |
| Blade+Bootstrap 5 vs scaffold Tailwind | BRA cover, §13 NFR-USE-002, AGENTS.md §3, Vite config | NFR-USE-002, seluruh UI | Asset pipeline dan view perlu diselaraskan | D-T03 menetapkan Blade+Bootstrap 5 | Tidak, D-T03 Accepted, perubahan file belum dilakukan |
| NFR-USE-002 responsive Bootstrap vs Tailwind scaffold | BRA §13 vs Vite dan `resources/css/app.css` | NFR-USE-002 | Target CSS ganda | D-T03 | Tidak, Accepted |
| `phpunit.xml` override SQLite dikomentari vs DB test terpisah | AGENTS.md §6, `phpunit.xml` | NFR-MNT-002, seluruh test DB | Tes dapat memakai DB demo dan merusak data | D-T04 usul DB test terpisah | Ya, D-T04 Proposed |
| FR-AUTH-003 vs skeleton tanpa middleware | BRA §9.1 vs `bootstrap/app.php` | FR-AUTH-003, RULE-011, KPI-06 | URL langsung belum terlindungi | D-T05 usul middleware dan Policy/Gate | Ya, D-T05 Proposed |
| RULE-020 dan ASM-06 vs tidak ada FR opening balance | BRA §10, §16.1, §22 | RULE-020, ASM-06 | Saldo awal tanpa jejak | D-B14 | Ya, D-B14 Proposed |
| RULE-025 dan §7.6 vs tidak ada FR cancel Draft | BRA §10, §7.6 | RULE-025 | Status Draft tidak terkendali | D-B15 | Ya, D-B15 Proposed |
| §18.2 double posting AC vs tidak ada FR eksplisit | BRA §18.2 vs §9.3 dan §9.4 | FR-IN-005, FR-OUT-005, RULE-024 | Guard posting belum dispesifikasi | D-B15 | Ya, D-B15 Proposed |
| BRA §11.5 data policies terbuka vs BDL keputusan Proposed | BRA §11.5, §21, BDL §4 | Q-01–Q-08, Q-10 | Desain database belum boleh dibekukan | D-B01–D-B08, D-B10 | Ya, seluruhnya Proposed |
| BDL dokumen Approved vs keputusan belum seluruhnya Accepted | BDL §1 status Approved, §3 definisi, §4–§5 status keputusan | Seluruh decision ID | Baseline MVP sebagian belum final | BDL §7 memisahkan Accepted dan kandidat | Tidak ada konflik baru, tetapi status tidak boleh dinaikkan |

**Ketentuan:** RTA tidak menyelesaikan konflik di atas secara sepihak. Resolusi mengikuti persetujuan pemilik proyek dan perubahan BRA melalui change control.

---

## 13. Requirements Coverage Analysis

### 13.1 Goals ke KPI

| Goal | KPI terkait | Hasil |
|---|---|---|
| GOAL-01 | Tidak ada KPI langsung, KPI-08 hanya dokumentasi | Belum terhubung langsung |
| GOAL-02 | KPI-01, KPI-02, KPI-03 | Terhubung |
| GOAL-03 | KPI-01, KPI-07 | Terhubung |
| GOAL-04 | KPI-04 | Terhubung |
| GOAL-05 | KPI-03 sebagian, KPI-08 dokumentasi | Belum terhubung langsung |
| GOAL-06 | KPI-08 | Terhubung lemah |
| GOAL-07 | KPI-06, KPI-07 | Terhubung |

Jumlah: 7 goals, 5 dengan KPI langsung, 2 tanpa KPI langsung, 1 dengan hubungan lemah.

### 13.2 Business Requirements ke Functional Requirements

| BR | FR terkait | Hasil |
|---|---|---|
| BR-001 | FR-MD-001–006, FR-WH-001–003 | Terhubung |
| BR-002 | FR-STK-001–003 | Terhubung, AC tidak tersedia |
| BR-003 | FR-IN-001–006 | Terhubung |
| BR-004 | FR-OUT-001–006 | Terhubung |
| BR-005 | FR-TRF-001–005 | Terhubung |
| BR-006 | FR-OUT-003, FR-TRF-003 | Terhubung |
| BR-007 | FR-IN-005, FR-OUT-005, FR-TRF-004, FR-OPN-006, FR-AUD-004 | Terhubung |
| BR-008 | FR-MD-003, FR-MD-004, FR-IN-004, FR-OUT-004 | Terhubung |
| BR-009 | FR-WH-002 | Terhubung |
| BR-010 | FR-OPN-001–006 | Terhubung |
| BR-011 | FR-AUTH-001–003, FR-USER-001–003 | Terhubung |
| BR-012 | FR-STK-001–003, FR-REP-001–004 | Terhubung, AC tidak tersedia |
| BR-013 | FR-AUD-001–004, FR-USER-003 | Terhubung |
| BR-014 | Tidak ada FR | Future, tidak terhubung ke FR |
| BR-015 | Tidak ada FR | Future, tidak terhubung ke FR |

Jumlah: 15 BR, 13 terhubung ke FR, 2 future tanpa FR.

### 13.3 Functional Requirements ke Modul

| Modul | FR primer | Jumlah |
|---|---|---:|
| M01 Authentication & Authorization | FR-AUTH-001–003 | 3 |
| M02 User Management | FR-USER-001–003 | 3 |
| M03 Master Data Management | FR-MD-001–006 | 6 |
| M04 Warehouse & Location Management | FR-WH-001–003 | 3 |
| M05 Stock Inbound | FR-IN-001–006 | 6 |
| M06 Stock Outbound | FR-OUT-001–006 | 6 |
| M07 Stock Transfer | FR-TRF-001–005 | 5 |
| M08 Stock Opname | FR-OPN-001–006 | 6 |
| M09 Stock Balance & Stock Card | FR-STK-001–003 | 3 |
| M10 Dashboard | FR-DASH-001–002 | 2 |
| M11 Reporting | FR-REP-001–004 | 4 |
| M12 Audit Trail | FR-AUD-001–004 | 4 |

Jumlah: 51 FR, seluruhnya terhubung ke minimal satu modul.

### 13.4 Business Rules ke Modul

| Modul | Rule utama | Catatan |
|---|---|---|
| M03 | RULE-001–RULE-006, RULE-009, RULE-019 | Serial dan presisi dituliskan di master |
| M04 | RULE-002, RULE-003, RULE-016 | Lokasi aktif dan unik |
| M05 | RULE-003–RULE-005, RULE-007–RULE-010, RULE-013, RULE-018, RULE-024, RULE-025, RULE-027 | Inbound dan Draft |
| M06 | RULE-003–RULE-005, RULE-007–RULE-011, RULE-014, RULE-018, RULE-024, RULE-025, RULE-027 | Outbound dan stok negatif |
| M07 | RULE-004, RULE-005, RULE-007, RULE-008, RULE-010, RULE-015–RULE-018, RULE-024, RULE-025, RULE-027 | Transfer atomik |
| M08 | RULE-003–RULE-005, RULE-007, RULE-008, RULE-021–RULE-023, RULE-027 | Opname, RULE-023 Open |
| M09 | RULE-005, RULE-013–RULE-019, RULE-027 | Ledger dan saldo |
| M01, M02 | RULE-011 | Otorisasi backend |
| M10 | RULE-005 | Satuan tidak dijumlahkan |
| M11 | RULE-018, RULE-021, RULE-022, RULE-027 | Laporan mengikuti ledger |
| M12 | RULE-010, RULE-012, RULE-018, RULE-022, RULE-025, RULE-026, RULE-028 | Audit dan immutable |

Jumlah: 28 rule, 27 dapat dipetakan langsung ke modul MVP, RULE-023 masih Open. RULE-020 memiliki modul tetapi FR belum tersedia.

### 13.5 User Roles ke Fitur

| Role | Modul dan aksi sesuai BRA §12 | Catatan |
|---|---|---|
| System Administrator | M02 C/R/U/D, M01, M03 R/U, M04 R/U, M09 R, M11 R, M12 R | Tidak mengubah histori posted |
| Inventory Administrator | M03 dan M04 C/R/U/D, M05–M08 C/R/U dan P, M09 R, M11 R, M12 R terbatas | Posting default untuk transaksi |
| Warehouse Operator | M03 R, M04 R, M05–M08 C/R/U, post sesuai permission, M09 R, M11 R terbatas | Default tidak punya P |
| Manager/Viewer | M03 R, M04 R, M09 R, M11 R, tanpa transaksi | Baca-saja |

Jumlah: 4 role, seluruhnya memiliki pemetaan fitur.

### 13.6 Acceptance Criteria Traceability

| AC | Requirement terkait | Hasil |
|---|---|---|
| §18.1 Master data | FR-MD-001, FR-MD-003, FR-WH-002, FR-WH-003, RULE-001–RULE-003 | Tersedia |
| §18.2 Inbound | FR-IN-001, FR-IN-004, FR-IN-005, RULE-010, RULE-013, RULE-024, RULE-027 | Tersedia, cancel Draft belum |
| §18.3 Outbound | FR-OUT-001, FR-OUT-003, FR-OUT-004, FR-OUT-005, RULE-014 | Tersedia |
| §18.4 Transfer | FR-TRF-001–005, RULE-015–RULE-017 | Tersedia |
| §18.5 Stock opname | FR-OPN-001–006, RULE-021, RULE-022 | Tersedia |
| §18.6 Otorisasi dan audit | FR-AUTH-003, FR-USER-002, FR-AUD-001, FR-AUD-002, NFR-SEC-002 | Tersedia |
| S A–S E | Inbound, outbound, transfer, serial, opname, atomicity | Tersedia |

Jumlah: 6 kelompok AC formal dan 5 skenario. Tidak tersedia untuk 51 FR secara individual, 19 NFR, dan 8 REP. Kriteria ringkas §9 bukan pengganti AC formal.

### 13.7 Requirement ke Rencana Testing

| Kelompok | Jumlah ID | Rencana testing tersedia |
|---|---:|---|
| FR transaksi dan serial | 29 FR | S A–S E mencakup alur utama |
| FR master dan auth | 14 FR | Sebagian hanya KR §9.x |
| NFR | 19 | NFR-MNT-002 menyaratkan test, AC tidak tersedia |
| REP | 8 | Belum ada test spesifik |
| RULE | 28 | 27 teruji lewat skenario, RULE-023 menunggu D-B09 |
| BR | 15 | 13 teruji lewat FR, BR-014 dan BR-015 future |

### 13.8 Decision Log ke Requirement

| Kategori keputusan | Jumlah | Memiliki requirement terkait | Memiliki dampak modul |
|---|---:|---:|---:|
| Business Decision | 17 | 17 | 17 |
| Technical Decision | 8 | 8 | 8 |

Jumlah: 25 dari 25 keputusan dapat ditelusuri ke requirement dan modul.

---

## 14. Unresolved Requirements

| Item | Jenis | Alasan belum selesai | Tindakan yang dibutuhkan |
|---|---|---|---|
| D-B09 | Open decision | Proteksi lokasi opname belum disetujui | Putuskan opsi dan otorisasi override |
| D-B16 | Open decision | Retensi audit belum diputus | Tetapkan periode dan akses audit |
| D-B01–D-B08, D-B10, D-B13–D-B15 | Proposed decision | Belum ada persetujuan final | Accepted atau Rejected |
| D-T01, D-T04–D-T08 | Proposed decision | Belum ada persetujuan final | Accepted atau Rejected |
| Opening Balance | Gap requirement | RULE-020 tanpa FR | Proposed FR dan AC lewat change control |
| Cancel Draft | Gap requirement | RULE-025 tanpa FR dan AC formal | Proposed FR dan AC |
| Double posting | Gap requirement | AC §18.2 sebagian | Perkuat FR dan AC |
| Reversal/adjustment | Gap requirement | FR-AUD-003 belum detail | Tambahkan FR detail |
| FR-DASH-001 | Conflict | Bertentangan dengan §14.2 | Setujui resolusi D-B13 |
| RULE-023 | Conflict | Status usulan dan Open | Setujui atau tolak D-B09 |
| RULE-028 | Conflict | Config masih UTC | Setujui D-T01 sebelum ubah config |
| NFR-COMP-001 | Conflict | PHP 8.3 vs composer 8.2 | D-T02 Accepted, implementasi belum |
| NFR-USE-002 | Conflict | Bootstrap vs Tailwind | D-T03 Accepted, implementasi belum |
| NFR-MNT-002 | Conflict | DB test belum terpisah | Setujui D-T04 |
| BR-014, BR-015 | Future requirement | Tidak ada FR pada MVP | Tetapkan sebagai future backlog |
| Seluruh NFR dan REP | AC gap | Tidak ada AC formal | Susun AC tambahan |
| GOAL-01 dan GOAL-05 | Coverage gap | Tidak ada KPI langsung | Tetapkan KPI tambahan bila diperlukan |

---

## 15. Testing Traceability Recommendation

| Area uji | FR dan RULE | AC atau skenario | Decision terkait | Catatan |
|---|---|---|---|---|
| Login, logout, redirect | FR-AUTH-001–003, RULE-011 | §18.6 | D-T05 | Uji URL langsung tanpa login |
| Kelola user dan role | FR-USER-001–002 | §18.6 | D-T05 | Uji permission posting |
| Master data unik | FR-MD-001–003, FR-WH-002, RULE-001–RULE-003 | §18.1 | D-B01, D-B04 | Uji duplikat kode dan parent invalid |
| Tracking type dan serial | FR-MD-004, FR-IN-004, RULE-006–RULE-009 | S C | D-B02, D-B03, D-B05 | Uji serial mismatch dan duplikat |
| Inbound | FR-IN-001–005, RULE-010, RULE-013, RULE-027 | §18.2, S A, S E | D-B15, D-T06 | Draft nol movement, post sekali |
| Outbound | FR-OUT-001–005, RULE-014, RULE-027 | §18.3, S A, S E | D-B07, D-T06 | Uji stok kurang dan serial keluar |
| Transfer | FR-TRF-001–005, RULE-015–RULE-017 | §18.4, S B, S E | D-T06 | Uji lokasi sama dan rollback |
| Stock opname | FR-OPN-001–006, RULE-021–RULE-022 | §18.5, S D | D-B09 | RULE-023 belum diuji karena Open |
| Atomicity | FR-AUD-004, RULE-027, NFR-INT-001, NFR-REL-001 | S E | D-T06 | Simulasikan error sebelum movement selesai |
| Posted immutable | FR-AUD-002, FR-AUD-003, RULE-012, RULE-026 | §18.6 | D-B08 | Uji edit langsung ditolak |
| Cancel Draft dan double posting | FR-IN-001, FR-OUT-001, RULE-024, RULE-025 | §18.2 sebagian | D-B15 | AC belum final |
| Dashboard dan laporan | FR-DASH-001–002, FR-REP-001–004, REP-01–REP-08 | — | D-B13, D-B17 | AC belum tersedia, konflik satuan belum final |
| Timezone | RULE-028, NFR-COMP-001 | — | D-T01 | Uji timestamp sebelum config diubah |
| Environment test | NFR-MNT-002 | S A–S E | D-T04 | Gunakan DB test terpisah setelah disetujui |

---

## 16. Conclusion and Recommended Next Steps

1. RTA ini menemukan 168 ID requirement BRA, 25 decision ID BDL, 12 gap, dan 13 konflik. Tidak ada ID requirement fiktif yang ditambahkan.
2. Seluruh keputusan masih sesuai BDL: D-B09 dan D-B16 Open, D-B11, D-B12, D-B17 Deferred, D-T02 dan D-T03 Accepted, sisanya Proposed. Tidak ada Rejected.
3. Dokumen BDL Approved tidak mengubah status keputusan menjadi Accepted secara otomatis.
4. Sebelum System Design, minta keputusan atas seluruh item Proposed dan Open pada bagian 14.
5. Ajukan penambahan FR dan AC untuk opening balance, cancel Draft, double posting, reversal, dan detail laporan melalui change control BRA sesuai D-T08.
6. Jangan mengimplementasikan RULE-023, D-B13, D-T01, D-T04, D-T05, atau D-T06 sebagai final sebelum persetujuan.
7. D-T02 dan D-T06 dapat dipakai sebagai baseline runtime dan frontend, tetapi perubahan file komponen masih memerlukan persetujuan implementasi terpisah.
8. Setelah status final ditetapkan, perbarui RTA dan BDL terlebih dahulu, lalu susun Use Case, Activity Diagram, ERD, dan Data Dictionary sesuai BRA §25.
9. Implementasi mengikuti BRA §22 per modul dengan gerbang kualitas: tidak lanjut jika tes konsistensi stok modul sebelumnya belum lulus.
10. Gunakan hanya data dummy/sintetis, jangan commit `.env`, dan jangan menjalankan perintah database sebelum lingkungan test disetujui.

Akhir dokumen — REQUIREMENTS TRACEABILITY ANALYSIS v0.1 draft.
