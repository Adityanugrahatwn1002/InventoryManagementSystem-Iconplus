# SOFTWARE REQUIREMENTS SPECIFICATION (SRS)

## Inventory Management System — ERP-Based Warehouse Inventory Management

## 1. DOCUMENT INFORMATION

| Atribut | Nilai |
|---|---|
| Document Title | Software Requirements Specification — Inventory Management System |
| Document ID | Akan ditetapkan setelah dokumen disetujui |
| Version | 0.1 — Draft for review |
| Date | 23 November 2026 |
| Owner | Pengembang proyek / pemilik proyek |
| Document Status | Draft — menunggu review dan persetujuan |
| Project Type | Proyek pribadi / prototype pembelajaran PKL |
| Related Documents | `AGENTS.md`; `docs/01-business-requirements-analysis.md` (BRA-IMS-001 v1.0); `docs/02-requirements-traceability-analysis.md` (RTA v0.1); `docs/03-business-decision-log.md` (BDL v0.1 Approved) |

**Pernyataan status proyek:** Dokumen ini adalah spesifikasi kebutuhan untuk proyek pribadi/prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Seluruh proses bisnis yang dijelaskan adalah rancangan berdasarkan BRA dan asumsi yang masih dapat ditinjau oleh pemilik proyek.

---

## 2. INTRODUCTION

### 2.1 Purpose
SRS ini mendefinisikan bagaimana sistem harus berperilaku, bukan bagaimana kode ditulis. Dokumen ini menjadi dasar System Design, pembuatan Use Case/Activity Diagram, ERD konseptual, Data Dictionary, dan implementasi Laravel 12 per modul.

### 2.2 Scope
Cakupan mengikuti BRA §5.1: autentikasi dasar, pengguna dan role, master data, gudang dan lokasi, inbound, outbound, transfer, stock opname, saldo dan kartu stok, dashboard, laporan dasar, audit trail, serta data dummy/sintetis. Di luar cakupan mengikuti BRA §5.2 dan fitur future BRA §5.3.

### 2.3 Project Overview
Aplikasi web terpusat untuk satu gudang operasional dengan banyak lokasi penyimpanan. Perubahan stok hanya terjadi melalui transaksi yang diposting; setiap perubahan menghasilkan stock movement; stok negatif dilarang; barang berserial memiliki identitas unik; selisih opname dicatat dengan alasan dan otorisasi.

### 2.4 Definitions
| Istilah | Definisi (BRA §25) |
|---|---|
| Draft | Dokumen yang masih dapat diedit dan belum mengubah stok |
| Posted | Dokumen yang telah diproses dan mengubah saldo serta ledger |
| Stock Movement | Catatan perubahan kuantitas persediaan |
| Stock Balance | Saldo kuantitas pada satu barang dan lokasi |
| On-hand | Kuantitas yang tercatat berada pada lokasi/gudang |
| Available Stock | Kuantitas tersedia; pada MVP sama dengan on-hand karena reservation belum ada |
| SKU | Stock Keeping Unit; identitas item untuk pengelolaan persediaan |
| Reversal | Transaksi pembalik untuk mengoreksi transaksi yang sudah diposting |
| Audit Trail | Jejak yang merekam siapa melakukan apa dan kapan |

### 2.5 Abbreviations
BRA, BRD, SRS, RTA, BDL, ERD, MVP, CRUD, RBAC, SKU, OBSERVED, GENERAL, ASSUMPTION, C/R/U/P/D.

### 2.6 References
1. `AGENTS.md` — instruksi kerja dan batasan.
2. BRA v1.0 — sumber utama requirement, AC §18, skenario §19, aturan §10.
3. RTA v0.1 — sumber pemetaan requirement, 12 gap, 13 konflik.
4. BDL v0.1 — sumber status 25 decision ID.
5. Konfigurasi repositori (`composer.json`, `config/app.php`, `phpunit.xml`, scaffold Vite/Tailwind) — bukti konflik, **bukan** sumber kebutuhan bisnis.

---

## 3. OVERALL SYSTEM DESCRIPTION

### 3.1 Product Perspective
Prototype mandiri, bukan modul ERP perusahaan dan bukan integrasi dengan sistem internal. Repositori saat ini masih skeleton Laravel 12; belum ada auth/RBAC, migration domain, atau kode inventory sesuai catatan AGENTS.md.

### 3.2 Product Functions
| Modul | Fungsi utama | FR |
|---|---|---|
| M01 Authentication & Authorization | Login, logout, proteksi halaman | FR-AUTH-001–003 |
| M02 User Management | Kelola user dan role | FR-USER-001–003 |
| M03 Master Data Management | Kategori, satuan, barang, tracking, minimum, supplier | FR-MD-001–006 |
| M04 Warehouse & Location | Gudang, struktur lokasi, nonaktif data | FR-WH-001–003 |
| M05 Stock Inbound | Draft, validasi serial, posting | FR-IN-001–006 |
| M06 Stock Outbound | Draft, cek saldo, serial, posting | FR-OUT-001–006 |
| M07 Stock Transfer | Draft, validasi lokasi, double movement atomik | FR-TRF-001–005 |
| M08 Stock Opname | Sesi, snapshot, hitung fisik, selisih, penyesuaian | FR-OPN-001–006 |
| M09 Stock Balance & Stock Card | Saldo, kartu stok, filter transaksi | FR-STK-001–003 |
| M10 Dashboard | Ringkasan dan low stock | FR-DASH-001–002 |
| M11 Reporting | Laporan layar | FR-REP-001–004 |
| M12 Audit Trail | Audit, immutable Posted, reversal, atomic posting | FR-AUD-001–004 |

### 3.3 System Objectives
GOAL-01 memusatkan master; GOAL-02 mengendalikan perubahan stok; GOAL-03 menelusuri pergerakan; GOAL-04 mengelola identitas barang; GOAL-05 mendukung pemeriksaan fisik; GOAL-06 menyajikan informasi operasional; GOAL-07 menerapkan kontrol akses.

### 3.4 System Users
System Administrator, Inventory Administrator, Warehouse Operator, Manager/Viewer — lihat bagian 4.

### 3.5 Operating Environment
- PHP minimum **8.3** — **Accepted D-T02**. Catatan: `composer.json` masih `^8.2`; konflik tercatat, perubahan file belum dilakukan.
- Laravel 12, MySQL melalui Laragon.
- Frontend **Blade + Bootstrap 5** — **Accepted D-T03**. Catatan: scaffold Tailwind bukan target final; perubahan asset pipeline belum dilakukan.
- Node.js/NPM, Composer, VS Code, Git/GitHub.
- Browser yang didokumentasikan: daftar versi belum final; terkait NFR-COMP-001 dan D-T07 Proposed.

### 3.6 General Constraints
- Aturan backend wajib: RULE-001–RULE-028, kecuali RULE-023 yang masih **Open D-B09**.
- Otorisasi tidak boleh hanya di UI (RULE-011; NFR-SEC-002).
- Data demo hanya sintetis (ASM-07; NFR-SEC-005).
- Logika stok kritis wajib memiliki automated tests (NFR-MNT-002).
- Dokumen BDL berstatus Approved tidak berarti semua keputusan di dalamnya Accepted.

### 3.7 Assumptions and Dependencies
ASM-01–ASM-10 berlaku sebagai asumsi BRA. ASM-10 terkait D-B09 **Open** dan tidak boleh diimplementasikan. Ketergantungan: PHP/Laravel/MySQL, Composer, Node/NPM, data dummy, Git/GitHub, OpenCode sebagai alat bantu bukan sumber kebenaran.

---

## 4. USER CHARACTERISTICS

| Peran BRA | Karakteristik (BRA §6.2) |
|---|---|
| System Administrator | Mengelola akun, role, konfigurasi, data referensi; tidak mengubah histori Posted langsung |
| Inventory Administrator | Mengelola master, meninjau transaksi, menjalankan opname, melihat laporan; dapat diberi permission posting |
| Warehouse Operator | Membuat draft penerimaan, pengeluaran, transfer, hitung fisik; hak posting dibatasi sesuai permission (default tidak) |
| Manager / Viewer | Baca-saja: dashboard, saldo, kartu stok, laporan |

**Authorization matrix (BRA §12; C=Create, R=Read, U=Update, P=Post, D=Delete/Nonaktifkan):**

| Modul/aksi | System Admin | Inventory Admin | Warehouse Operator | Manager/Viewer |
|---|---:|---:|---:|---:|
| Kelola pengguna dan role | C/R/U/D | — | — | — |
| Lihat master data | R | R | R | R |
| Kelola master barang/kategori/satuan | R/U | C/R/U/D | — | — |
| Kelola warehouse/location | R/U | C/R/U/D | R | R |
| Buat draft inbound/outbound/transfer | — | C/R/U | C/R/U | — |
| Post inbound/outbound/transfer | — | P | Sesuai permission; default tidak | — |
| Buat dan isi stock opname | — | C/R/U | C/R/U | — |
| Otorisasi/post penyesuaian opname | — | P | — | — |
| Lihat saldo dan kartu stok | R | R | R | R |
| Lihat laporan | R | R | R terbatas | R |
| Lihat audit log | R | R terbatas | — | — |

Pemisahan tugas: pembuat dan pemosting sebaiknya berbeda; approval bertingkat adalah future (ASM-08).

---

## 5. SYSTEM CONSTRAINTS

### 5.1 Accepted — boleh menjadi keputusan final
| ID | Keputusan | Status |
|---|---|---|
| D-T02 | PHP minimum 8.3 | Accepted |
| D-T03 | Frontend Blade + Bootstrap 5 | Accepted |

### 5.2 Proposed — rancangan sementara, belum final
D-B01–D-B08, D-B10, D-B13–D-B15; D-T01, D-T04–D-T08. Tidak boleh dianggap Accepted.

### 5.3 Open — unresolved, tidak diputus AI
D-B09 (lock lokasi opname), D-B16 (retensi audit).

### 5.4 Deferred — tidak diimplementasikan MVP
D-B11 (retur), D-B12 (batch/expiry), D-B17 (export Excel/PDF).

### 5.5 Rejected
Tidak ada decision ID berstatus Rejected di BDL.

### 5.6 Constraint teknis dan scope
- PHP 8.3+, Laravel 12, MySQL, Blade, Bootstrap 5, HTML/CSS/JavaScript; Laragon.
- Kompatibilitas browser: target yang didokumentasikan, daftar final belum tersedia (NFR-COMP-001; D-T07 Proposed).
- Security: hashing Laravel, backend authorization, CSRF, validasi server, escaping Blade, query binding/ORM, sesi aman, tanpa data rahasia (NFR-SEC-001–005).
- Scope: tanpa integrasi ERP/eksternal, tanpa costing/akuntansi, tanpa multi-warehouse operasional, tanpa notifikasi otomatis (BRA §5.2).

---

## 6. EXTERNAL INTERFACE REQUIREMENTS

### 6.1 User Interface
Navigasi usulan BRA §14.1: Dashboard; Master Data (Barang, Kategori, Satuan, Supplier); Warehouse (Gudang, Lokasi/Rak/Level); Inventory Transactions (Inbound, Outbound, Transfer, Opname); Inventory Reports; User & Access Management; Audit Log; Settings. Menu terlarang disembunyikan, tetapi pemeriksaan permission tetap di backend. Standar tampilan transaksi BRA §14.3 wajib dipenuhi.

### 6.2 Software Interface
Tidak ada integrasi perangkat lunak eksternal pada MVP. Antarmuka hanya antara Blade, Laravel application layer, dan MySQL. OpenCode bukan antarmuka runtime.

### 6.3 Database Interface
MySQL. Hubungan data bersifat konseptual menurut BRA §11; tidak ditentukan ERD final atau daftar kolom pada SRS ini.

### 6.4 Communication Interface
Tidak ada email, WhatsApp, webhook, atau API pihak ketiga pada MVP sesuai BRA §5.2.

---

## 7. FUNCTIONAL REQUIREMENTS

Format 20 field dipakai untuk seluruh 51 FR. Contoh data sintetis: `ITM-0001 Kabel UTP Sintetis`, `LOC-A-01-01`, `SN-SINT-0001`.

### A. Authentication & Authorization (3 FR)

#### FR-AUTH-001 — Login menggunakan akun terdaftar
- **Requirement ID:** FR-AUTH-001
- **Requirement Name:** Login akun terdaftar
- **Description:** Sistem memverifikasi kredensial pengguna sebelum membuat sesi.
- **Source Reference:** BRA §9.1; RTA §7.4; BDL D-T05 (Proposed)
- **Requirement Status:** Requirement BRA aktif; teknik auth terkait D-T05 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Seluruh peran
- **Preconditions:** Akun aktif tersedia dalam data dummy
- **Trigger:** Pengguna mengirim form login
- **Input Data:** Email/username dan password (sintetis: `user.demo01`)
- **Business Process:** Terima input → validasi → autentikasi → buat sesi → redirect sesuai role
- **Validation Rules:** Kredensial wajib; akun nonaktif ditolak
- **Business Rules:** RULE-011
- **Authorization Requirements:** Endpoint login dapat diakses publik; halaman lain mensyaratkan sesi
- **Expected Output:** Sesi aktif dan halaman sesuai role
- **Alternative Flow:** Kredensial salah → pengguna tetap di form login
- **Error Handling:** Pesan generik tanpa membocorkan akun; error teknis dicatat tanpa password/token (NFR-LOG-001)
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given akun sintetis aktif, when kredensial valid dikirim, then sesi terbentuk; given kredensial salah, when login, then ditolak.

#### FR-AUTH-002 — Logout
- **Requirement ID:** FR-AUTH-002
- **Requirement Name:** Logout pengguna
- **Description:** Sistem mengakhiri sesi pengguna.
- **Source Reference:** BRA §9.1; RTA §7.4; BDL D-T05 (Proposed)
- **Requirement Status:** Requirement BRA aktif; teknik sesi terkait D-T05 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Seluruh peran
- **Preconditions:** Pengguna memiliki sesi aktif
- **Trigger:** Pengguna memilih aksi logout
- **Input Data:** Sesi aktif
- **Business Process:** Invalidasi sesi → hapus kredensial sesi → redirect ke login
- **Validation Rules:** Sesi harus ada
- **Business Rules:** RULE-011
- **Authorization Requirements:** Pengguna terautentikasi
- **Expected Output:** Sesi berakhir; halaman terproteksi tidak dapat diakses tanpa login ulang
- **Alternative Flow:** Jika sesi sudah berakhir, redirect ke login
- **Error Handling:** Tidak menampilkan detail token/sesi
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given sesi aktif, when logout, then akses ke URL terproteksi ditolak dan pengguna diarahkan ke login.

#### FR-AUTH-003 — Batasi halaman berdasarkan autentikasi
- **Requirement ID:** FR-AUTH-003
- **Requirement Name:** Proteksi halaman
- **Description:** Halaman hanya dapat diakses setelah autentikasi; otorisasi tetap diverifikasi backend.
- **Source Reference:** BRA §9.1, §18.6; RTA §7.4; BDL D-T05 (Proposed)
- **Requirement Status:** Requirement BRA aktif; middleware/Policy terkait D-T05 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Seluruh peran
- **Preconditions:** Route terproteksi telah ditetapkan
- **Trigger:** Permintaan langsung ke URL terproteksi
- **Input Data:** Permintaan HTTP dan sesi
- **Business Process:** Cek sesi → cek permission → izinkan atau tolak
- **Validation Rules:** Tanpa sesi ditolak; tanpa permission ditolak
- **Business Rules:** RULE-011
- **Authorization Requirements:** Backend middleware/Policy; UI saja tidak cukup
- **Expected Output:** Halaman tampil hanya untuk pengguna berwenang
- **Alternative Flow:** Pengguna tanpa sesi diarahkan ke login; tanpa permission menerima error otorisasi
- **Error Handling:** Tidak mengubah data atau saldo saat aksi ditolak
- **Acceptance Criteria:** BRA §18.6 — Given pengguna tanpa login, when mengakses URL langsung, then diarahkan ke login; given pengguna tanpa permission, when mengakses aksi, then ditolak tanpa perubahan saldo.

### B. User Management (3 FR)

#### FR-USER-001 — Kelola pengguna
- **Requirement ID:** FR-USER-001
- **Requirement Name:** Create/read/update/nonaktifkan pengguna
- **Description:** Admin mengelola akun pengguna, termasuk status aktif/nonaktif.
- **Source Reference:** BRA §9.1; RTA §7.4; BDL D-T05 (Proposed)
- **Requirement Status:** Requirement BRA aktif; detail implementasi Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** System Administrator
- **Preconditions:** Admin terautentikasi dan berpermission
- **Trigger:** Admin mengelola akun
- **Input Data:** Nama, email/username, status, role (data dummy)
- **Business Process:** Validasi → simpan/update/nonaktifkan → audit
- **Validation Rules:** Email/username unik; password mengikuti hashing Laravel
- **Business Rules:** RULE-011
- **Authorization Requirements:** C/R/U/D hanya System Administrator
- **Expected Output:** Pengguna tersimpan; pengguna nonaktif tidak dapat login
- **Alternative Flow:** Duplikat email/username ditolak
- **Error Handling:** Pesan validasi jelas; error teknis tanpa kredensial
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given admin sintetis, when menonaktifkan pengguna, then pengguna tersebut tidak dapat login.

#### FR-USER-002 — Tetapkan role pengguna
- **Requirement ID:** FR-USER-002
- **Requirement Name:** Penetapan role
- **Description:** Admin menetapkan role sehingga hak menu dan aksi mengikuti matriks BRA §12.
- **Source Reference:** BRA §9.1, §12, §18.6; RTA §7.4; BDL D-T05 (Proposed)
- **Requirement Status:** Requirement BRA aktif; Policy/Gate terkait D-T05 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** System Administrator
- **Preconditions:** Role tersedia
- **Trigger:** Admin mengubah role pengguna
- **Input Data:** User dan role
- **Business Process:** Simpan role → terapkan permission → audit
- **Validation Rules:** Role harus valid
- **Business Rules:** RULE-011
- **Authorization Requirements:** Backend permission validation
- **Expected Output:** Hak aksi pengguna berubah sesuai role
- **Alternative Flow:** Role invalid ditolak
- **Error Handling:** Aksi ditolak tanpa perubahan data
- **Acceptance Criteria:** BRA §18.6 — Given role Viewer, when mencoba aksi posting melalui URL, then ditolak.

#### FR-USER-003 — Catat pengguna dan waktu aksi kritis
- **Requirement ID:** FR-USER-003
- **Requirement Name:** Pelaku dan waktu aksi kritis
- **Description:** Sistem merekam aktor dan waktu pada aksi kritis.
- **Source Reference:** BRA §9.1, §18.6; RTA §7.4; BDL D-B16 (Open)
- **Requirement Status:** Requirement BRA aktif; retensi audit D-B16 Open
- **Implementation Priority:** Should
- **MVP / Future Scope:** MVP; kebijakan retensi unresolved
- **Related User Roles:** System Administrator, Inventory Administrator
- **Preconditions:** Aksi kritis terjadi
- **Trigger:** Create/update/post/cancel pada objek kritis
- **Input Data:** Identifier aktor, waktu, objek
- **Business Process:** Catat log → tampilkan pada detail transaksi
- **Validation Rules:** Aktor dan waktu wajib tercatat
- **Business Rules:** RULE-018
- **Authorization Requirements:** Log dapat dilihat sesuai matriks
- **Expected Output:** Detail transaksi menampilkan pelaku dan waktu
- **Alternative Flow:** Jika pencatatan gagal, posting tidak boleh berhasil (atomik)
- **Error Handling:** Error teknis dicatat tanpa data sensitif
- **Acceptance Criteria:** BRA §18.6 — Given posting berhasil, when detail dilihat, then nama pengguna dan waktu tampil.

### C. Master Data Management (6 FR)

#### FR-MD-001 — Kelola kategori barang
- **Requirement ID:** FR-MD-001
- **Requirement Name:** Kategori barang
- **Description:** Pengguna berwenang mengelola kategori barang.
- **Source Reference:** BRA §9.2, §18.1; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Admin berwenang terautentikasi
- **Trigger:** Kelola kategori
- **Input Data:** Kode, nama, deskripsi, status (sintetis: `CAT-KBL`)
- **Business Process:** Validasi → simpan → audit
- **Validation Rules:** Kode/nama wajib; kode unik
- **Business Rules:** RULE-002
- **Authorization Requirements:** Inventory Admin C/R/U/D
- **Expected Output:** Kategori tersimpan dan dapat dipilih pada master barang
- **Alternative Flow:** Kode duplikat ditolak
- **Error Handling:** Pesan duplikat jelas
- **Acceptance Criteria:** BRA §18.1 — Given kode `CAT-KBL` sudah ada, when dibuat lagi, then sistem menolak.

#### FR-MD-002 — Kelola satuan barang
- **Requirement ID:** FR-MD-002
- **Requirement Name:** Satuan barang
- **Description:** Pengguna berwenang mengelola satuan barang.
- **Source Reference:** BRA §9.2; RTA §7.4; BDL D-B05 (Proposed)
- **Requirement Status:** Requirement BRA aktif; presisi satuan terkait D-B05 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Admin berwenang
- **Trigger:** Kelola satuan
- **Input Data:** Kode satuan, nama, presisi (sintetis: `PCS`, `MTR`)
- **Business Process:** Validasi → simpan → audit
- **Validation Rules:** Kode unik; presisi mengikuti kebijakan yang belum final
- **Business Rules:** RULE-002, RULE-005
- **Authorization Requirements:** Inventory Admin C/R/U/D
- **Expected Output:** Satuan tersedia pada master barang
- **Alternative Flow:** Kode duplikat ditolak
- **Error Handling:** Validasi ditampilkan
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given satuan `PCS`, when disimpan, then kode unik tervalidasi; presisi akhir menunggu D-B05.

#### FR-MD-003 — Kelola master barang
- **Requirement ID:** FR-MD-003
- **Requirement Name:** Master barang
- **Description:** Pengguna berwenang mengelola data barang.
- **Source Reference:** BRA §9.2, §18.1; RTA §7.4; BDL D-B01 (Proposed)
- **Requirement Status:** Requirement BRA aktif; pemisahan `item_code`/`material_number` Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Kategori dan satuan tersedia
- **Trigger:** Kelola barang
- **Input Data:** Kode internal, material number, nama, deskripsi, kategori, satuan, minimum stock, status (sintetis: `ITM-0001`, `MAT-SINT-001`)
- **Business Process:** Validasi → simpan → audit
- **Validation Rules:** Kode unik; field wajib lengkap; saldo tidak diinput di form ini
- **Business Rules:** RULE-001, RULE-003, RULE-019
- **Authorization Requirements:** Inventory Admin C/R/U/D; System Admin R/U
- **Expected Output:** Barang tersimpan; barang nonaktif tetap tampil di histori
- **Alternative Flow:** Duplikat kode ditolak
- **Error Handling:** Error validasi jelas; tidak mengubah saldo
- **Acceptance Criteria:** BRA §18.1 — Given kode `ITM-0001` sudah ada, when dibuat duplikat, then ditolak; given barang nonaktif, when dipakai transaksi baru, then ditolak.

#### FR-MD-004 — Simpan metode pelacakan
- **Requirement ID:** FR-MD-004
- **Requirement Name:** Tracking type
- **Description:** Master barang menyimpan apakah barang serialized atau non-serialized.
- **Source Reference:** BRA §9.2; RTA §7.4; BDL D-B02 (Proposed)
- **Requirement Status:** Requirement BRA aktif; aturan tracking per item Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Barang sudah ada
- **Trigger:** Menetapkan atau mengubah tracking type
- **Input Data:** Tracking type (sintetis: `ITM-0001` = serialized)
- **Business Process:** Simpan tracking type → validasi transaksi mengikuti tipe
- **Validation Rules:** Nilai harus salah satu dari serialized/non-serialized
- **Business Rules:** RULE-006, RULE-007, RULE-008
- **Authorization Requirements:** Inventory Admin
- **Expected Output:** Alur serial hanya berlaku untuk barang berserial
- **Alternative Flow:** Perubahan tracking type setelah ada transaksi memerlukan kebijakan tambahan (usulan, belum final)
- **Error Handling:** Serial pada barang non-serialized ditolak
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given `ITM-0001` serialized, when inbound kuantitas 2, then dua serial unik wajib diisi.

#### FR-MD-005 — Simpan minimum stock
- **Requirement ID:** FR-MD-005
- **Requirement Name:** Minimum stock
- **Description:** Master barang menyimpan ambang stok minimum.
- **Source Reference:** BRA §9.2; RTA §7.4; BDL D-B10 (Proposed)
- **Requirement Status:** Requirement BRA aktif; scope per item Proposed
- **Implementation Priority:** Should
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Manager/Viewer
- **Preconditions:** Barang tersedia
- **Trigger:** Menetapkan minimum stock
- **Input Data:** Nilai ambang (sintetis: 5 PCS)
- **Business Process:** Simpan ambang → pakai dashboard/laporan low stock
- **Validation Rules:** Ambang tidak negatif; mengikuti presisi satuan
- **Business Rules:** RULE-005
- **Authorization Requirements:** Inventory Admin mengubah; Viewer membaca
- **Expected Output:** Indikator low stock tersedia
- **Alternative Flow:** Minimum per lokasi belum termasuk MVP
- **Error Handling:** Nilai invalid ditolak
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given minimum 5 dan saldo 3, when dashboard dibuka, then barang muncul dalam daftar low stock.

#### FR-MD-006 — Kelola supplier referensi
- **Requirement ID:** FR-MD-006
- **Requirement Name:** Supplier referensi
- **Description:** Sistem mengelola data supplier sebagai referensi opsional penerimaan.
- **Source Reference:** BRA §9.2; RTA §7.4; BDL D-B06 (Proposed)
- **Requirement Status:** Requirement BRA aktif; supplier opsional Proposed
- **Implementation Priority:** Could
- **MVP / Future Scope:** MVP sebagai referensi opsional
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Admin berwenang
- **Trigger:** Kelola supplier
- **Input Data:** Kode, nama, kontak, status (sintetis: `SUP-SINT-01`)
- **Business Process:** Validasi → simpan → dapat dipilih saat inbound
- **Validation Rules:** Kode unik; bersifat opsional pada inbound
- **Business Rules:** RULE-003
- **Authorization Requirements:** Inventory Admin
- **Expected Output:** Supplier dapat dipilih pada dokumen inbound
- **Alternative Flow:** Inbound tanpa supplier tetap valid
- **Error Handling:** Supplier nonaktif tidak dapat dipilih
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given supplier opsional, when inbound tanpa supplier diposting, then dokumen tetap valid.

### D. Warehouse & Location Management (3 FR)

#### FR-WH-001 — Kelola gudang
- **Requirement ID:** FR-WH-001
- **Requirement Name:** Gudang
- **Description:** Pengguna berwenang mengelola master gudang.
- **Source Reference:** BRA §9.2; RTA §7.4; BDL D-B04 (Proposed)
- **Requirement Status:** Requirement BRA aktif; struktur MVP satu gudang
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP satu gudang operasional
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Admin berwenang
- **Trigger:** Kelola gudang
- **Input Data:** Kode, nama, alamat/deskripsi, status (sintetis: `WH-01`)
- **Business Process:** Validasi → simpan → audit
- **Validation Rules:** Kode unik
- **Business Rules:** RULE-002
- **Authorization Requirements:** Inventory Admin C/R/U/D; System Admin R/U
- **Expected Output:** Gudang tersedia sebagai parent lokasi
- **Alternative Flow:** Kode duplikat ditolak
- **Error Handling:** Validasi jelas
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given `WH-01` sudah ada, when dibuat lagi, then ditolak.

#### FR-WH-002 — Kelola struktur lokasi
- **Requirement ID:** FR-WH-002
- **Requirement Name:** Area/rak/level/lokasi
- **Description:** Pengguna berwenang mengelola struktur lokasi penyimpanan.
- **Source Reference:** BRA §9.2, §18.1; RTA §7.4; BDL D-B04 (Proposed)
- **Requirement Status:** Requirement BRA aktif; hierarki Warehouse>Area>Rack>Level Proposed; Bin future
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP tanpa Bin/slot
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Gudang tersedia
- **Trigger:** Kelola lokasi
- **Input Data:** Kode, nama, tipe, parent, status (sintetis: `LOC-A-01-01`)
- **Business Process:** Validasi kode dan parent → simpan → audit
- **Validation Rules:** Kode unik; parent harus valid
- **Business Rules:** RULE-002, RULE-003
- **Authorization Requirements:** Inventory Admin C/R/U/D
- **Expected Output:** Struktur lokasi dapat dipakai transaksi
- **Alternative Flow:** Parent invalid ditolak
- **Error Handling:** Error validasi ditampilkan
- **Acceptance Criteria:** BRA §18.1 — Given parent tidak ada, when lokasi dibuat, then ditolak; given kode duplikat, then ditolak.

#### FR-WH-003 — Nonaktifkan barang/lokasi tanpa hapus histori
- **Requirement ID:** FR-WH-003
- **Requirement Name:** Deaktivasi master
- **Description:** Sistem menonaktifkan barang/lokasi tanpa menghapus histori.
- **Source Reference:** BRA §9.2, §18.1; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Data master sudah ada
- **Trigger:** Admin menonaktifkan data
- **Input Data:** Barang/lokasi dan status nonaktif
- **Business Process:** Ubah status → transaksi baru memakai validasi aktif
- **Validation Rules:** Master nonaktif tidak dipakai transaksi baru
- **Business Rules:** RULE-003
- **Authorization Requirements:** Inventory Admin
- **Expected Output:** Histori lama tetap terbaca
- **Alternative Flow:** Transaksi lama yang merujuk data nonaktif tetap tampil
- **Error Handling:** Penggunaan master nonaktif ditolak
- **Acceptance Criteria:** BRA §18.1 — Given lokasi `LOC-A-01-01` nonaktif, when dibuat transaksi baru, then ditolak tetapi histori lama tetap tampil.

### E. Stock Inbound (6 FR)

#### FR-IN-001 — Dokumen inbound Draft
- **Requirement ID:** FR-IN-001
- **Requirement Name:** Draft inbound bernomor unik
- **Description:** Pengguna membuat dokumen penerimaan berstatus Draft.
- **Source Reference:** BRA §9.3, §18.2; RTA §7.4; BDL D-B15 (Proposed)
- **Requirement Status:** Requirement BRA aktif; cancel Draft/detail AC terkait D-B15 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Barang dan lokasi aktif tersedia
- **Trigger:** Pengguna membuat penerimaan
- **Input Data:** Nomor dokumen, tanggal, sumber/referensi, catatan (sintetis: `IN-2026-0001`)
- **Business Process:** Buat header Draft → tambah baris → simpan
- **Validation Rules:** Nomor unik dan tidak dipakai ulang
- **Business Rules:** RULE-010, RULE-024, RULE-025
- **Authorization Requirements:** C/R/U untuk IA dan WO
- **Expected Output:** Draft tersimpan; saldo belum berubah
- **Alternative Flow:** Pembatalan Draft tidak menghasilkan movement — AC masih perlu detail
- **Error Handling:** Nomor duplikat ditolak
- **Acceptance Criteria:** BRA §18.2 — Given Draft `IN-2026-0001`, when disimpan, then saldo tetap; detail cancel Draft: **Proposed Acceptance Criteria** menunggu D-B15.

#### FR-IN-002 — Beberapa baris barang inbound
- **Requirement ID:** FR-IN-002
- **Requirement Name:** Baris inbound
- **Description:** Dokumen inbound dapat memuat beberapa baris barang.
- **Source Reference:** BRA §9.3; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; detail AC Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Draft inbound ada
- **Trigger:** Pengguna menambah baris
- **Input Data:** Barang, lokasi tujuan, kuantitas (sintetis: `ITM-0001`, `LOC-A-01-01`, 2)
- **Business Process:** Tambah baris → validasi → simpan Draft
- **Validation Rules:** Barang dan lokasi aktif; minimal satu baris sebelum posting
- **Business Rules:** RULE-003, RULE-004
- **Authorization Requirements:** IA/WO
- **Expected Output:** Baris tersimpan pada Draft
- **Alternative Flow:** Baris dengan master nonaktif ditolak
- **Error Handling:** Validasi per baris ditampilkan
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given Draft dengan dua baris valid, when disimpan, then kedua baris tampil; baris invalid ditolak.

#### FR-IN-003 — Validasi kuantitas lebih dari nol
- **Requirement ID:** FR-IN-003
- **Requirement Name:** Validasi kuantitas inbound
- **Description:** Sistem menolak kuantitas nol atau negatif.
- **Source Reference:** BRA §9.3; RTA §7.4; BDL D-B05 (Proposed)
- **Requirement Status:** Requirement BRA aktif; presisi kuantitas Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Baris inbound diisi
- **Trigger:** Simpan atau post baris
- **Input Data:** Kuantitas (sintetis: 2 PCS; nol/negatif untuk uji negatif)
- **Business Process:** Validasi angka → validasi presisi → simpan
- **Validation Rules:** `qty > 0`; presisi tetap (DECIMAL), bukan floating point; serialized bulat
- **Business Rules:** RULE-004, RULE-005, RULE-008
- **Authorization Requirements:** IA/WO
- **Expected Output:** Kuantitas valid tersimpan
- **Alternative Flow:** Nol/negatif ditolak
- **Error Handling:** Pesan “kuantitas harus lebih dari nol”
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given kuantitas 0 atau -1, when disimpan, then ditolak.

#### FR-IN-004 — Validasi serial barang berserial
- **Requirement ID:** FR-IN-004
- **Requirement Name:** Serial inbound
- **Description:** Sistem memvalidasi serial untuk barang berserial.
- **Source Reference:** BRA §9.3, §18.2, S C; RTA §7.4; BDL D-B03 (Proposed)
- **Requirement Status:** Requirement BRA aktif; unique global Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Tracking type serialized
- **Trigger:** Input serial pada baris inbound
- **Input Data:** Daftar serial (sintetis: `SN-SINT-0001`, `SN-SINT-0002`)
- **Business Process:** Hitung serial → cocokkan kuantitas → cek duplikat → simpan
- **Validation Rules:** Jumlah serial = kuantitas bulat; serial belum terdaftar; satu status/lokasi aktif
- **Business Rules:** RULE-007, RULE-008, RULE-009
- **Authorization Requirements:** IA/WO
- **Expected Output:** Serial valid tersimpan
- **Alternative Flow:** Jumlah serial kurang atau duplikat ditolak
- **Error Handling:** Detail serial bermasalah ditampilkan
- **Acceptance Criteria:** BRA §18.2 + S C — Given kuantitas 2 dan satu serial, when dipost, then ditolak; given `SN-SINT-0001` sudah ada, when didaftarkan lagi, then ditolak.

#### FR-IN-005 — Post inbound
- **Requirement ID:** FR-IN-005
- **Requirement Name:** Posting inbound
- **Description:** Pengguna berwenang memposting dokumen inbound.
- **Source Reference:** BRA §9.3, §18.2, S A/S E; RTA §7.4; BDL D-T06 (Proposed)
- **Requirement Status:** Requirement BRA aktif; strategi transaction/lock Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator (default), WO sesuai permission
- **Preconditions:** Draft lengkap dan valid
- **Trigger:** Aksi Post
- **Input Data:** Dokumen dan baris
- **Business Process:** Cek permission → validasi → transaction → tambah saldo → buat movement INBOUND → update serial → status Posted → audit
- **Validation Rules:** Draft belum Posted; permission posting ada
- **Business Rules:** RULE-011, RULE-013, RULE-018, RULE-027
- **Authorization Requirements:** Backend posting permission
- **Expected Output:** Saldo bertambah satu kali; movement dan audit tercatat
- **Alternative Flow:** Post kedua ditolak; kegagalan di tengah proses dirollback
- **Error Handling:** Rollback penuh; tidak ada saldo parsial
- **Acceptance Criteria:** BRA §18.2 + S A/S E — Given saldo 0, when inbound 10 dipost, then saldo 10 dan movement ada; post ulang tidak menggandakan saldo; error simulate tidak menyisakan perubahan parsial.

#### FR-IN-006 — Referensi sumber penerimaan
- **Requirement ID:** FR-IN-006
- **Requirement Name:** Referensi inbound
- **Description:** Sistem menyimpan referensi sumber penerimaan jika tersedia.
- **Source Reference:** BRA §9.3; RTA §7.4; BDL D-B06 (Proposed)
- **Requirement Status:** Requirement BRA aktif; supplier/referensi opsional Proposed
- **Implementation Priority:** Should
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Referensi tersedia atau sengaja kosong
- **Trigger:** Mengisi header inbound
- **Input Data:** Referensi/sumber (sintetis: `SUP-SINT-01/DO-001`)
- **Business Process:** Simpan referensi → tampilkan pada detail
- **Validation Rules:** Opsional; supplier aktif jika dipilih
- **Business Rules:** RULE-003
- **Authorization Requirements:** IA/WO
- **Expected Output:** Referensi tampil pada detail dokumen
- **Alternative Flow:** Tanpa referensi tetap diterima
- **Error Handling:** Referensi invalid ditolak
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given referensi diisi, when detail dibuka, then referensi tampil persis.

### F. Stock Outbound (6 FR)

#### FR-OUT-001 — Dokumen outbound Draft
- **Requirement ID:** FR-OUT-001
- **Requirement Name:** Draft outbound bernomor unik
- **Description:** Pengguna membuat dokumen pengeluaran berstatus Draft.
- **Source Reference:** BRA §9.4, §18.3; RTA §7.4; BDL D-B07 (Proposed)
- **Requirement Status:** Requirement BRA aktif; model Draft-Posted Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Barang dan lokasi aktif
- **Trigger:** Pengguna membuat pengeluaran
- **Input Data:** Nomor, tanggal, tujuan/penerima/alasan (sintetis: `OUT-2026-0001`)
- **Business Process:** Buat Draft → tambah baris → simpan
- **Validation Rules:** Nomor unik; Draft tidak mengubah saldo
- **Business Rules:** RULE-010, RULE-024, RULE-025
- **Authorization Requirements:** IA/WO
- **Expected Output:** Draft tersimpan, saldo tetap
- **Alternative Flow:** Cancel Draft tanpa movement — detail AC menunggu D-B15
- **Error Handling:** Nomor duplikat ditolak
- **Acceptance Criteria:** BRA §18.3 — Given Draft dibuat, when disimpan, then saldo tidak berkurang.

#### FR-OUT-002 — Beberapa baris barang outbound
- **Requirement ID:** FR-OUT-002
- **Requirement Name:** Baris outbound
- **Description:** Dokumen outbound dapat memuat beberapa baris.
- **Source Reference:** BRA §9.4; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; detail AC Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Draft outbound ada
- **Trigger:** Tambah baris
- **Input Data:** Barang, lokasi asal, kuantitas (sintetis: `ITM-0001`, `LOC-A-01-01`, 4)
- **Business Process:** Tambah baris → validasi → simpan
- **Validation Rules:** Barang/lokasi aktif; kuantitas > 0
- **Business Rules:** RULE-003, RULE-004
- **Authorization Requirements:** IA/WO
- **Expected Output:** Baris tersimpan
- **Alternative Flow:** Baris invalid ditolak
- **Error Handling:** Validasi per baris
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given dua baris valid, when disimpan, then keduanya tampil pada Draft.

#### FR-OUT-003 — Periksa saldo tersedia
- **Requirement ID:** FR-OUT-003
- **Requirement Name:** Cek ketersediaan stok
- **Description:** Sistem memeriksa saldo sebelum posting outbound.
- **Source Reference:** BRA §9.4, §18.3, S A; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; lock saldo terkait D-T06 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Draft outbound siap dipost
- **Trigger:** Aksi Post atau validasi saldo
- **Input Data:** Barang, lokasi asal, kuantitas diminta
- **Business Process:** Baca saldo → bandingkan → izinkan/tolak
- **Validation Rules:** Kuantitas ≤ saldo tersedia; available = on-hand pada MVP
- **Business Rules:** RULE-014
- **Authorization Requirements:** IA/WO
- **Expected Output:** Posting dilanjutkan jika saldo cukup
- **Alternative Flow:** Stok kurang ditolak; saldo tetap
- **Error Handling:** Pesan stok tidak cukup tanpa mengubah data
- **Acceptance Criteria:** BRA §18.3 + S A — Given saldo 6, when outbound 7 dipost, then ditolak dan saldo tetap 6.

#### FR-OUT-004 — Validasi serial yang dikeluarkan
- **Requirement ID:** FR-OUT-004
- **Requirement Name:** Serial outbound
- **Description:** Sistem memvalidasi serial yang dikeluarkan.
- **Source Reference:** BRA §9.4, §18.3, S C; RTA §7.4; BDL D-B03 (Proposed)
- **Requirement Status:** Requirement BRA aktif; unique global Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Warehouse Operator
- **Preconditions:** Barang serialized dengan serial tersedia
- **Trigger:** Memilih serial pada baris outbound
- **Input Data:** Serial (sintetis: `SN-SINT-0001`)
- **Business Process:** Cek status/lokasi serial → validasi → posting mengubah status
- **Validation Rules:** Serial harus berada di lokasi asal dan berstatus tersedia; jumlah serial = kuantitas bulat
- **Business Rules:** RULE-007, RULE-008
- **Authorization Requirements:** WO memilih; posting sesuai permission
- **Expected Output:** Serial yang dikeluarkan menjadi tidak tersedia
- **Alternative Flow:** Serial sudah keluar ditolak tanpa inbound/retur yang sah
- **Error Handling:** Serial invalid ditampilkan
- **Acceptance Criteria:** BRA §18.3 + S C — Given serial sudah keluar, when dipilih lagi, then ditolak.

#### FR-OUT-005 — Post outbound
- **Requirement ID:** FR-OUT-005
- **Requirement Name:** Posting outbound
- **Description:** Pengguna berwenang memposting dokumen outbound.
- **Source Reference:** BRA §9.4, §18.3, S A; RTA §7.4; BDL D-T06 (Proposed)
- **Requirement Status:** Requirement BRA aktif; transaction/lock Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator (default), WO sesuai permission
- **Preconditions:** Draft valid dan saldo cukup
- **Trigger:** Aksi Post
- **Input Data:** Dokumen outbound
- **Business Process:** Cek permission → lock/validasi saldo → transaction → kurangi saldo → movement OUTBOUND → update serial → status Posted → audit
- **Validation Rules:** Draft belum Posted; tidak menghasilkan stok negatif
- **Business Rules:** RULE-011, RULE-014, RULE-018, RULE-027
- **Authorization Requirements:** Backend posting permission
- **Expected Output:** Saldo berkurang satu kali; movement tercatat
- **Alternative Flow:** Post kedua ditolak; kegagalan dirollback
- **Error Handling:** Tidak ada saldo parsial
- **Acceptance Criteria:** BRA §18.3 — Given saldo 10, when outbound 4 dipost, then saldo 6 dan movement OUTBOUND tercatat.

#### FR-OUT-006 — Simpan tujuan/penerima/alasan
- **Requirement ID:** FR-OUT-006
- **Requirement Name:** Informasi tujuan outbound
- **Description:** Sistem menyimpan tujuan, penerima, atau alasan pengeluaran.
- **Source Reference:** BRA §9.4; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; detail AC Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Draft outbound dibuat
- **Trigger:** Mengisi header outbound
- **Input Data:** Tujuan/penerima/alasan (sintetis: `DIV-SINT-A`)
- **Business Process:** Simpan informasi → tampilkan pada detail dan laporan
- **Validation Rules:** Wajib diisi sesuai kriteria ringkas BRA
- **Business Rules:** — (tidak ada RULE khusus)
- **Authorization Requirements:** IA/WO
- **Expected Output:** Informasi tujuan tercatat
- **Alternative Flow:** Kosong ditolak
- **Error Handling:** Validasi ditampilkan
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given tujuan diisi, when dokumen dilihat, then nilai tampil persis pada header.

### G. Stock Transfer (5 FR)

#### FR-TRF-001 — Dokumen transfer Draft
- **Requirement ID:** FR-TRF-001
- **Requirement Name:** Draft transfer bernomor unik
- **Description:** Pengguna membuat dokumen perpindahan antar lokasi berstatus Draft.
- **Source Reference:** BRA §9.5, §18.4; RTA §7.4; BDL D-B15 (Proposed)
- **Requirement Status:** Requirement BRA aktif; cancel Draft terkait D-B15 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP; transfer antar gudang future
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Lokasi asal dan tujuan aktif
- **Trigger:** Pengguna membuat transfer
- **Input Data:** Nomor dokumen, tanggal, lokasi asal, lokasi tujuan, catatan (sintetis: `TRF-2026-0001`)
- **Business Process:** Buat Draft → tambah baris → validasi lokasi → simpan
- **Validation Rules:** Nomor unik; lokasi berbeda dan aktif
- **Business Rules:** RULE-010, RULE-016, RULE-024
- **Authorization Requirements:** IA/WO membuat Draft
- **Expected Output:** Draft tersimpan; saldo belum berubah
- **Alternative Flow:** Lokasi sama atau nonaktif ditolak
- **Error Handling:** Validasi lokasi ditampilkan
- **Acceptance Criteria:** BRA §18.4 — Given transfer dengan lokasi asal sama dengan tujuan, when disimpan, then ditolak.

#### FR-TRF-002 — Lokasi asal dan tujuan berbeda
- **Requirement ID:** FR-TRF-002
- **Requirement Name:** Validasi perbedaan lokasi
- **Description:** Sistem mengharuskan lokasi asal dan tujuan berbeda.
- **Source Reference:** BRA §9.5, §18.4; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Draft transfer memiliki dua lokasi
- **Trigger:** Validasi atau posting transfer
- **Input Data:** Lokasi asal `LOC-A-01-01` dan tujuan `LOC-B-02-01`
- **Business Process:** Bandingkan lokasi → izinkan/tolak
- **Validation Rules:** `lokasi_asal != lokasi_tujuan`; keduanya aktif
- **Business Rules:** RULE-016
- **Authorization Requirements:** IA/WO
- **Expected Output:** Transfer dengan lokasi berbeda dapat diproses
- **Alternative Flow:** Lokasi sama ditolak
- **Error Handling:** Pesan “lokasi asal dan tujuan harus berbeda”
- **Acceptance Criteria:** BRA §18.4 — Given lokasi asal sama dengan tujuan, when dipost, then ditolak.

#### FR-TRF-003 — Validasi stok lokasi asal
- **Requirement ID:** FR-TRF-003
- **Requirement Name:** Ketersediaan stok asal
- **Description:** Sistem memvalidasi ketersediaan stok pada lokasi asal.
- **Source Reference:** BRA §9.5, §18.4; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; lock saldo terkait D-T06 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Draft transfer valid
- **Trigger:** Validasi atau posting transfer
- **Input Data:** Barang, lokasi asal, kuantitas (sintetis: `ITM-0001`, 3)
- **Business Process:** Baca saldo asal → bandingkan → izinkan/tolak
- **Validation Rules:** Kuantitas ≤ saldo lokasi asal; tidak boleh negatif
- **Business Rules:** RULE-014, RULE-016
- **Authorization Requirements:** IA/WO
- **Expected Output:** Transfer dilanjutkan jika saldo cukup
- **Alternative Flow:** Stok tidak cukup ditolak
- **Error Handling:** Pesan stok tidak cukup tanpa perubahan saldo
- **Acceptance Criteria:** BRA §18.4 — Given saldo asal 2, when transfer 3 dipost, then ditolak.

#### FR-TRF-004 — Movement keluar dan masuk atomik
- **Requirement ID:** FR-TRF-004
- **Requirement Name:** Double movement atomik
- **Description:** Sistem membuat movement pengurangan asal dan penambahan tujuan dalam satu transaksi database.
- **Source Reference:** BRA §9.5, §18.4, S B/S E; RTA §7.4; BDL D-T06 (Proposed)
- **Requirement Status:** Requirement BRA aktif; strategi transaction/lock Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator (default), WO sesuai permission
- **Preconditions:** Validasi lokasi dan saldo lulus
- **Trigger:** Aksi Post transfer
- **Input Data:** Dokumen transfer dan baris
- **Business Process:** Cek permission → transaction → kurangi saldo asal → movement TRANSFER_OUT → tambah saldo tujuan → movement TRANSFER_IN → update serial → status Posted → audit → commit
- **Validation Rules:** Kedua movement harus berhasil atau keduanya gagal
- **Business Rules:** RULE-015, RULE-018, RULE-027
- **Authorization Requirements:** Backend posting permission
- **Expected Output:** Saldo asal berkurang, tujuan bertambah, dua movement tercatat dengan referensi dokumen sama
- **Alternative Flow:** Jika salah satu sisi gagal, seluruh transaksi dibatalkan
- **Error Handling:** Rollback penuh; tidak ada movement tunggal
- **Acceptance Criteria:** BRA §18.4 + S B/S E — Given saldo asal 10 dan tujuan 2, when transfer 3 dipost, then asal 7 dan tujuan 5; simulasi error pada sisi kedua membatalkan kedua sisi.

#### FR-TRF-005 — Jaga total stok gabungan
- **Requirement ID:** FR-TRF-005
- **Requirement Name:** Konservasi total stok
- **Description:** Sistem menjaga total stok gabungan tetap sama setelah transfer internal.
- **Source Reference:** BRA §9.5, §18.4, S B; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator, Manager/Viewer
- **Preconditions:** Transfer berhasil diposting
- **Trigger:** Perhitungan saldo setelah posting
- **Input Data:** Saldo asal dan tujuan sebelum/sesudah
- **Business Process:** Hitung total sebelum → hitung total sesudah → bandingkan
- **Validation Rules:** Total sebelum = total sesudah
- **Business Rules:** RULE-017
- **Authorization Requirements:** Pembacaan saldo sesuai matriks
- **Expected Output:** Total gabungan tidak berubah
- **Alternative Flow:** Jika total berubah, posting dianggap gagal dan dirollback
- **Error Handling:** Flag inkonsistensi ledger untuk ditinjau
- **Acceptance Criteria:** BRA §18.4 + S B — Given total 12 sebelum transfer 3, when transfer selesai, then total tetap 12.

### H. Stock Opname (6 FR)

#### FR-OPN-001 — Buat sesi opname
- **Requirement ID:** FR-OPN-001
- **Requirement Name:** Sesi stock opname
- **Description:** Pengguna berwenang membuat sesi opname untuk lokasi dan periode tertentu.
- **Source Reference:** BRA §9.6, §18.5; RTA §7.4; BDL D-B09 (Open)
- **Requirement Status:** Requirement BRA aktif; kontrol lock terkait D-B09 Open — tidak diputus AI
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP tanpa penerapan RULE-023 sebelum D-B09 disetujui
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Lokasi aktif tersedia
- **Trigger:** Admin membuat sesi
- **Input Data:** Nomor sesi, lokasi, tanggal, status (sintetis: `OPN-2026-0001`, `LOC-A-01-01`)
- **Business Process:** Buat header sesi → rekam snapshot → input baris pemeriksaan
- **Validation Rules:** Lokasi dan tanggal wajib; status sesi tercatat
- **Business Rules:** RULE-021; RULE-023 **Open**
- **Authorization Requirements:** IA membuat; WO mengisi hitung fisik
- **Expected Output:** Sesi opname terbentuk dengan snapshot
- **Alternative Flow:** Lokasi nonaktif ditolak; mekanisme kunci lokasi belum ditetapkan
- **Error Handling:** Error validasi ditampilkan
- **Acceptance Criteria:** BRA §18.5 — Given sesi dibuat, when dilihat, then lokasi, tanggal, dan status tampil; penguncian lokasi tidak diuji karena D-B09 Open.

#### FR-OPN-002 — Rekam snapshot stok sistem
- **Requirement ID:** FR-OPN-002
- **Requirement Name:** Snapshot sistem
- **Description:** Sistem merekam kuantitas sistem saat sesi dimulai.
- **Source Reference:** BRA §9.6, §18.5; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Sesi opname dibuat
- **Trigger:** Sesi dimulai
- **Input Data:** Saldo sistem per barang pada lokasi
- **Business Process:** Baca saldo → simpan nilai snapshot → kunci nilai agar tidak berubah karena transaksi berikutnya
- **Validation Rules:** Snapshot diambil sekali pada awal sesi
- **Business Rules:** RULE-021
- **Authorization Requirements:** IA/WO
- **Expected Output:** Nilai snapshot tersimpan dan stabil
- **Alternative Flow:** Jika transaksi lain terjadi, snapshot tidak berubah (mekanisme konkurensi masih menunggu keputusan terkait D-B09)
- **Error Handling:** Gagal menyimpan snapshot membatalkan pembuatan sesi
- **Acceptance Criteria:** BRA §18.5 — Given snapshot 8 untuk `ITM-0001`, when transaksi lain terjadi, then nilai snapshot tetap 8.

#### FR-OPN-003 — Input hitung fisik
- **Requirement ID:** FR-OPN-003
- **Requirement Name:** Hitung fisik
- **Description:** Operator memasukkan hasil penghitungan fisik per barang.
- **Source Reference:** BRA §9.6, §18.5; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Warehouse Operator
- **Preconditions:** Sesi opname aktif dan baris tersedia
- **Trigger:** Operator mengisi hasil hitung
- **Input Data:** Kuantitas fisik (sintetis: 7 untuk snapshot 8)
- **Business Process:** Input → validasi → simpan pada baris opname
- **Validation Rules:** Kuantitas valid mengikuti aturan kuantitas dan presisi satuan
- **Business Rules:** RULE-004, RULE-005, RULE-021
- **Authorization Requirements:** WO mengisi; IA meninjau
- **Expected Output:** Counted quantity tersimpan
- **Alternative Flow:** Nilai invalid ditolak
- **Error Handling:** Pesan validasi jelas
- **Acceptance Criteria:** BRA §18.5 — Given counted qty 7, when disimpan, then nilai tampil pada baris opname.

#### FR-OPN-004 — Hitung selisih stok
- **Requirement ID:** FR-OPN-004
- **Requirement Name:** Variance opname
- **Description:** Sistem menghitung selisih antara hasil hitung fisik dan snapshot sistem.
- **Source Reference:** BRA §9.6, §18.5, S D; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator
- **Preconditions:** Snapshot dan counted qty tersedia
- **Trigger:** Hitung atau simpan baris opname
- **Input Data:** Snapshot 8, counted 7
- **Business Process:** `variance = counted qty − snapshot qty` → tampilkan
- **Validation Rules:** Rumus harus sesuai RULE-021
- **Business Rules:** RULE-021
- **Authorization Requirements:** IA/WO
- **Expected Output:** Variance = -1 pada contoh sintetis
- **Alternative Flow:** Variance 0 tidak memerlukan penyesuaian
- **Error Handling:** Error perhitungan dicegah oleh validasi numerik
- **Acceptance Criteria:** BRA §18.5 + S D — Given snapshot 8 dan fisik 7, when dihitung, then variance -1.

#### FR-OPN-005 — Penyesuaian memerlukan alasan dan kewenangan
- **Requirement ID:** FR-OPN-005
- **Requirement Name:** Otorisasi penyesuaian
- **Description:** Penyesuaian opname hanya dapat diproses dengan alasan dan otorisasi.
- **Source Reference:** BRA §9.6, §18.5; RTA §7.4
- **Requirement Status:** Requirement BRA aktif
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Variance telah dihitung
- **Trigger:** Pengajuan penyesuaian
- **Input Data:** Alasan, aktor, waktu, otorisasi (sintetis: “selisih hitung fisik sintetis”)
- **Business Process:** Cek alasan → cek permission → izinkan/tolak
- **Validation Rules:** Alasan wajib; hanya Inventory Admin yang dapat mengotorisasi
- **Business Rules:** RULE-022
- **Authorization Requirements:** Backend authorization; WO tidak dapat memposting penyesuaian
- **Expected Output:** Penyesuaian siap diposting
- **Alternative Flow:** Tanpa alasan/otorisasi ditolak
- **Error Handling:** Aksi ditolak tanpa perubahan saldo
- **Acceptance Criteria:** BRA §18.5 — Given penyesuaian tanpa alasan, when diajukan, then ditolak.

#### FR-OPN-006 — Movement penyesuaian saat sesi diposting
- **Requirement ID:** FR-OPN-006
- **Requirement Name:** Posting adjustment opname
- **Description:** Sistem membuat stock movement penyesuaian ketika sesi opname diposting.
- **Source Reference:** BRA §9.6, §18.5, S D; RTA §7.4; BDL D-T06 (Proposed)
- **Requirement Status:** Requirement BRA aktif; transaction/lock Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Penyesuaian memiliki alasan dan otorisasi
- **Trigger:** Aksi Post sesi/penyesuaian
- **Input Data:** Variance, alasan, aktor, waktu
- **Business Process:** Transaction → movement OPNAME_ADJUSTMENT → perbarui saldo → status sesi Posted → audit
- **Validation Rules:** Saldo akhir mengikuti hasil penyesuaian
- **Business Rules:** RULE-018, RULE-022, RULE-027
- **Authorization Requirements:** Posting hanya oleh Inventory Admin
- **Expected Output:** Saldo menjadi 7 pada contoh S D; movement tercatat
- **Alternative Flow:** Kegagalan posting dirollback
- **Error Handling:** Tidak ada saldo parsial
- **Acceptance Criteria:** BRA §18.5 + S D — Given snapshot 8 dan fisik 7, when adjustment -1 diposting, then saldo 7 dan movement adjustment tercatat.

### I. Stock Balance & Stock Card (3 FR)

#### FR-STK-001 — Saldo per barang dan lokasi
- **Requirement ID:** FR-STK-001
- **Requirement Name:** Tampilan saldo
- **Description:** Sistem menampilkan saldo stok per kombinasi barang dan lokasi.
- **Source Reference:** BRA §9.7; RTA §7.4; BDL D-B10 (Proposed)
- **Requirement Status:** Requirement BRA aktif; AC formal tidak tersedia
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Seluruh peran pembaca
- **Preconditions:** Data saldo tersedia
- **Trigger:** Pengguna membuka halaman saldo
- **Input Data:** Filter barang, gudang, lokasi
- **Business Process:** Query saldo → tampilkan per barang-lokasi
- **Validation Rules:** Saldo harus konsisten dengan penjumlahan ledger
- **Business Rules:** RULE-018, RULE-019
- **Authorization Requirements:** R untuk seluruh peran (BRA §12)
- **Expected Output:** Daftar saldo dengan satuan masing-masing
- **Alternative Flow:** Filter tanpa hasil menampilkan pesan kosong
- **Error Handling:** Error query dicatat tanpa data sensitif
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given saldo `ITM-0001` di `LOC-A-01-01` = 6, when halaman dibuka dengan filter tersebut, then nilai 6 tampil.

#### FR-STK-002 — Kartu stok
- **Requirement ID:** FR-STK-002
- **Requirement Name:** Stock card
- **Description:** Sistem menyediakan kartu stok/riwayat pergerakan barang.
- **Source Reference:** BRA §9.7, §14.3; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; AC formal tidak tersedia
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Seluruh peran pembaca
- **Preconditions:** Movement tersedia
- **Trigger:** Pengguna membuka kartu stok
- **Input Data:** Barang dan lokasi (sintetis: `ITM-0001`, `LOC-A-01-01`)
- **Business Process:** Ambil movement → urutkan waktu → hitung saldo berjalan
- **Validation Rules:** Kartu menampilkan tanggal, tipe, referensi, masuk, keluar, dan saldo
- **Business Rules:** RULE-018
- **Authorization Requirements:** R untuk seluruh peran
- **Expected Output:** Riwayat pergerakan dengan saldo berjalan
- **Alternative Flow:** Tanpa movement menampilkan pesan kosong
- **Error Handling:** Error query dicatat
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given movement inbound 10 dan outbound 4, when kartu dibuka, then saldo berjalan 10 lalu 6 tampil dengan referensi dokumen.

#### FR-STK-003 — Pencarian dan filter transaksi
- **Requirement ID:** FR-STK-003
- **Requirement Name:** Filter transaksi
- **Description:** Sistem menyediakan pencarian dan filter transaksi.
- **Source Reference:** BRA §9.7; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; AC formal tidak tersedia
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Seluruh peran pembaca
- **Preconditions:** Dokumen transaksi tersedia
- **Trigger:** Pengguna menerapkan filter
- **Input Data:** Tanggal, nomor dokumen, tipe, status
- **Business Process:** Terapkan filter → tampilkan hasil yang sesuai
- **Validation Rules:** Filter harus sesuai kriteria ringkas BRA §9.7
- **Business Rules:** — (tidak ada RULE khusus)
- **Authorization Requirements:** R sesuai matriks; WO terbatas pada laporan
- **Expected Output:** Daftar transaksi terfilter
- **Alternative Flow:** Filter tanpa hasil menampilkan pesan kosong
- **Error Handling:** Input filter invalid ditolak
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given filter status Posted, when diterapkan, then hanya dokumen Posted yang tampil.

### J. Dashboard (2 FR)

#### FR-DASH-001 — Ringkasan jumlah barang dan total kuantitas stok
- **Requirement ID:** FR-DASH-001
- **Requirement Name:** Ringkasan dashboard
- **Description:** Dashboard menampilkan ringkasan jumlah barang dan informasi kuantitas stok sesuai batasan satuan.
- **Source Reference:** BRA §9.7, §14.2; RTA §7.4; BDL D-B13 (Proposed)
- **Requirement Status:** Requirement BRA aktif dengan konflik interpretasi; D-B13 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP; interpretasi final menunggu D-B13
- **Related User Roles:** Seluruh peran pembaca
- **Preconditions:** Data saldo dan master tersedia
- **Trigger:** Pengguna membuka dashboard
- **Input Data:** Item aktif, saldo per satuan, transaksi periode terpilih (sintetis: 100 PCS, 20 MTR, 5 ROLL)
- **Business Process:** Agregasi per satuan/SKU → tampilkan ringkasan → jangan menjumlahkan satuan berbeda
- **Validation Rules:** Dilarang menjumlahkan `100 pcs + 20 meter + 5 roll` menjadi satu angka total unit
- **Business Rules:** RULE-005
- **Authorization Requirements:** R untuk seluruh peran
- **Expected Output:** Jumlah SKU aktif dan ringkasan terpisah per satuan
- **Alternative Flow:** Jika resolusi D-B13 berubah, interpretasi angka dashboard diperbarui melalui change control
- **Error Handling:** Error agregasi dicatat tanpa data sensitif
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given tiga satuan berbeda, when dashboard dibuka, then ringkasan tampil per satuan dan tidak ada angka “total unit” gabungan; resolusi akhir menunggu D-B13.

#### FR-DASH-002 — Barang di bawah minimum stock
- **Requirement ID:** FR-DASH-002
- **Requirement Name:** Low stock dashboard
- **Description:** Dashboard menampilkan barang dengan stok di bawah ambang minimum.
- **Source Reference:** BRA §9.7, §14.2; RTA §7.4; BDL D-B10 (Proposed)
- **Requirement Status:** Requirement BRA aktif; scope per item Proposed
- **Implementation Priority:** Should
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Manager/Viewer
- **Preconditions:** Minimum stock ditetapkan pada master barang
- **Trigger:** Pengguna membuka dashboard
- **Input Data:** Minimum stock dan saldo (sintetis: minimum 5, saldo 3)
- **Business Process:** Bandingkan saldo dan minimum → tampilkan daftar
- **Validation Rules:** Ambang per item untuk MVP
- **Business Rules:** RULE-005
- **Authorization Requirements:** R untuk IA dan Viewer
- **Expected Output:** Daftar SKU dengan stok di bawah minimum
- **Alternative Flow:** Minimum per lokasi tidak termasuk MVP
- **Error Handling:** Error query dicatat
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given minimum 5 dan saldo 3, when dashboard dibuka, then `ITM-0001` muncul dalam daftar low stock.

### K. Reporting (4 FR)

#### FR-REP-001 — Laporan inbound dan outbound
- **Requirement ID:** FR-REP-001
- **Requirement Name:** Laporan inbound/outbound
- **Description:** Sistem menyediakan laporan transaksi penerimaan dan pengeluaran.
- **Source Reference:** BRA §9.7, §15 REP-03/REP-04; RTA §7.4; BDL D-B17 (Deferred untuk export)
- **Requirement Status:** Requirement BRA aktif; laporan layar MVP, export ditunda
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP untuk tampilan layar; export Excel/PDF Future
- **Related User Roles:** Inventory Administrator, Warehouse Operator terbatas, Manager/Viewer, System Administrator
- **Preconditions:** Dokumen inbound/outbound tersedia
- **Trigger:** Pengguna membuka laporan
- **Input Data:** Periode, status, item (sintetis: November 2026)
- **Business Process:** Query dokumen → tampilkan kolom sesuai REP-03/REP-04 → filter
- **Validation Rules:** Data harus konsisten dengan dokumen dan movement
- **Business Rules:** RULE-018
- **Authorization Requirements:** Sesuai matriks BRA §12
- **Expected Output:** Tabel laporan layar dengan filter periode
- **Alternative Flow:** Tidak ada data pada periode → pesan kosong
- **Error Handling:** Error query dicatat tanpa data sensitif
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given filter periode November 2026, when laporan dibuka, then hanya dokumen periode tersebut tampil dan tidak ada tombol export aktif pada MVP.

#### FR-REP-002 — Laporan mutasi dan saldo stok
- **Requirement ID:** FR-REP-002
- **Requirement Name:** Laporan mutasi/saldo
- **Description:** Sistem menyediakan laporan mutasi dan saldo stok yang konsisten dengan stock ledger.
- **Source Reference:** BRA §9.7, §15 REP-01/REP-02; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; AC formal tidak tersedia
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Seluruh peran pembaca
- **Preconditions:** Movement dan saldo tersedia
- **Trigger:** Pengguna membuka laporan
- **Input Data:** Barang, lokasi, periode
- **Business Process:** Agregasi ledger → tampilkan saldo dan mutasi
- **Validation Rules:** Saldo laporan = penjumlahan `quantity_delta`
- **Business Rules:** RULE-018
- **Authorization Requirements:** R sesuai matriks
- **Expected Output:** Laporan stock summary dan stock card sesuai REP-01/REP-02
- **Alternative Flow:** Filter tanpa hasil menampilkan pesan kosong
- **Error Handling:** Inkonsistensi ledger ditandai untuk tinjauan
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given ledger dengan delta +10 dan -4, when laporan dibuka, then saldo 6 tampil dan sesuai kartu stok.

#### FR-REP-003 — Ringkasan hasil stock opname
- **Requirement ID:** FR-REP-003
- **Requirement Name:** Laporan opname
- **Description:** Sistem menyediakan ringkasan hasil stock opname.
- **Source Reference:** BRA §9.7, §15 REP-06; RTA §7.4
- **Requirement Status:** Requirement BRA aktif; AC formal tidak tersedia
- **Implementation Priority:** Should
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Manager/Viewer
- **Preconditions:** Sesi opname tersedia
- **Trigger:** Pengguna membuka laporan opname
- **Input Data:** Nomor sesi, lokasi, snapshot, counted qty, variance, alasan, status
- **Business Process:** Query sesi dan baris → tampilkan ringkasan
- **Validation Rules:** Variance mengikuti RULE-021
- **Business Rules:** RULE-021, RULE-022
- **Authorization Requirements:** R untuk IA dan Viewer
- **Expected Output:** Ringkasan session opname dengan snapshot, fisik, dan selisih
- **Alternative Flow:** Sesi belum diposting tetap tampil dengan status Draft
- **Error Handling:** Error query dicatat
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given sesi dengan snapshot 8 dan fisik 7, when laporan dibuka, then variance -1 tampil.

#### FR-REP-004 — Export laporan ke Excel/PDF
- **Requirement ID:** FR-REP-004
- **Requirement Name:** Export Excel/PDF
- **Description:** Kemampuan mengekspor laporan ke Excel/PDF.
- **Source Reference:** BRA §9.7; RTA §7.4; BDL D-B17 (Deferred)
- **Requirement Status:** Requirement BRA aktif tetapi **Deferred D-B17**
- **Implementation Priority:** Could
- **MVP / Future Scope:** **Future Scope — tidak termasuk MVP**
- **Related User Roles:** Inventory Administrator, Manager/Viewer
- **Preconditions:** Laporan layar stabil dan keputusan D-B17 diubah (tidak dilakukan di SRS ini)
- **Trigger:** Permintaan export (tidak diimplementasikan pada MVP)
- **Input Data:** Laporan yang akan diekspor
- **Business Process:** Tidak dispesifikasikan lebih lanjut pada SRS ini karena Deferred
- **Validation Rules:** Tidak ditentukan pada MVP
- **Business Rules:** — (tidak ada RULE khusus)
- **Authorization Requirements:** Akan mengikuti role laporan jika suatu saat disetujui
- **Expected Output:** Tidak tersedia pada MVP
- **Alternative Flow:** Permintaan export pada MVP ditolak atau tidak disediakan
- **Error Handling:** Tidak ada implementasi export pada MVP
- **Acceptance Criteria:** **Proposed Acceptance Criteria (Future)** — Export hanya boleh diuji setelah D-B17 tidak lagi Deferred; pada MVP tidak ada kriteria penerimaan export.

### L. Audit Trail (4 FR)

#### FR-AUD-001 — Catat aksi create/update/post/cancel
- **Requirement ID:** FR-AUD-001
- **Requirement Name:** Audit aksi kritis
- **Description:** Sistem mencatat aksi create/update/post/cancel pada objek kritis.
- **Source Reference:** BRA §9.8, §18.6; RTA §7.4; BDL D-B16 (Open)
- **Requirement Status:** Requirement BRA aktif; retensi audit D-B16 Open
- **Implementation Priority:** Should
- **MVP / Future Scope:** MVP; kebijakan retensi belum final
- **Related User Roles:** System Administrator, Inventory Administrator
- **Preconditions:** Aksi kritis terjadi
- **Trigger:** Create/update/post/cancel pada dokumen, master, atau sesi opname
- **Input Data:** Aktor, waktu, aksi, referensi objek, nilai penting
- **Business Process:** Catat audit saat aksi berhasil → tampilkan pada log dan detail
- **Validation Rules:** Audit memuat aktor, waktu, aksi, dan referensi objek
- **Business Rules:** RULE-018, RULE-027
- **Authorization Requirements:** Log dapat dilihat sesuai matriks BRA §12
- **Expected Output:** Entri audit yang dapat ditelusuri
- **Alternative Flow:** Jika pencatatan gagal, aksi utama tidak boleh berhasil
- **Error Handling:** Error teknis dicatat tanpa password/token (NFR-LOG-001)
- **Acceptance Criteria:** BRA §18.6 — Given posting berhasil, when audit log dibuka, then entri dengan aktor, waktu, aksi, dan referensi dokumen tampil; retensi menunggu D-B16.

#### FR-AUD-002 — Cegah perubahan langsung dokumen Posted
- **Requirement ID:** FR-AUD-002
- **Requirement Name:** Immutable Posted
- **Description:** Sistem mencegah edit atau hapus langsung pada dokumen Posted.
- **Source Reference:** BRA §9.8, §18.6, §7.6; RTA §7.4; BDL D-B08 (Proposed)
- **Requirement Status:** Requirement BRA aktif; mekanisme koreksi terkait D-B08 Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** System Administrator, Inventory Administrator
- **Preconditions:** Dokumen berstatus Posted
- **Trigger:** Upaya edit/hapus langsung
- **Input Data:** Dokumen Posted
- **Business Process:** Cek status → tolak aksi → arahkan ke reversal/adjustment
- **Validation Rules:** Status Posted bersifat immutable untuk edit/hapus langsung
- **Business Rules:** RULE-012
- **Authorization Requirements:** Berlaku juga untuk System Administrator
- **Expected Output:** Dokumen tetap utuh
- **Alternative Flow:** Koreksi dilakukan melalui FR-AUD-003
- **Error Handling:** Aksi ditolak tanpa perubahan data
- **Acceptance Criteria:** BRA §18.6 — Given dokumen Posted, when pengguna mencoba edit atau hapus langsung, then ditolak dan saldo tidak berubah.

#### FR-AUD-003 — Koreksi transaksi Posted melalui reversal/adjustment
- **Requirement ID:** FR-AUD-003
- **Requirement Name:** Reversal/adjustment
- **Description:** Sistem menyediakan koreksi transaksi Posted melalui reversal atau adjustment yang merujuk dokumen awal dan memiliki alasan.
- **Source Reference:** BRA §9.8, §7.6; RTA §7.4; BDL D-B08 (Proposed)
- **Requirement Status:** Requirement BRA aktif; detail alur dan AC belum final, D-B08 Proposed
- **Implementation Priority:** Should
- **MVP / Future Scope:** MVP dengan prinsip immutable; detail menunggu persetujuan
- **Related User Roles:** Inventory Administrator
- **Preconditions:** Dokumen Posted ditemukan kesalahannya
- **Trigger:** Pengajuan koreksi
- **Input Data:** Dokumen awal, alasan, tipe koreksi, aktor, waktu
- **Business Process:** Validasi referensi → buat dokumen koreksi → posting menghasilkan movement → audit
- **Validation Rules:** Koreksi wajib memiliki referensi dokumen awal dan alasan
- **Business Rules:** RULE-012, RULE-026
- **Authorization Requirements:** Inventory Administrator; edit langsung tetap dilarang
- **Expected Output:** Jejak koreksi yang dapat ditelusuri ke dokumen awal
- **Alternative Flow:** Koreksi tanpa referensi/alasan ditolak
- **Error Handling:** Tidak ada perubahan langsung pada dokumen Posted
- **Acceptance Criteria:** **Proposed Acceptance Criteria** — Given dokumen Posted `IN-2026-0001`, when reversal dibuat dengan alasan dan referensi, then movement pembalik tercatat dan dokumen awal tidak berubah.

#### FR-AUD-004 — Posting transaksi dijalankan secara atomik
- **Requirement ID:** FR-AUD-004
- **Requirement Name:** Atomic posting
- **Description:** Perubahan saldo dan status dokumen harus berhasil atau gagal bersama-sama.
- **Source Reference:** BRA §9.8, §18.2, S E; RTA §7.4; BDL D-T06 (Proposed)
- **Requirement Status:** Requirement BRA aktif; strategi transaction/lock Proposed
- **Implementation Priority:** Must
- **MVP / Future Scope:** MVP
- **Related User Roles:** Inventory Administrator, Warehouse Operator (sesuai permission)
- **Preconditions:** Posting transaksi dimulai
- **Trigger:** Aksi Post pada inbound/outbound/transfer/opname
- **Input Data:** Dokumen, saldo, movement, serial, status
- **Business Process:** Transaction → perbarui saldo → buat movement → perbarui status → audit → commit; jika error → rollback
- **Validation Rules:** Tidak boleh ada saldo, serial, status, atau movement parsial
- **Business Rules:** RULE-018, RULE-027
- **Authorization Requirements:** Posting hanya oleh pengguna berpermission
- **Expected Output:** Semua perubahan konsisten atau tidak ada perubahan sama sekali
- **Alternative Flow:** Error pada tahap mana pun membatalkan seluruh posting
- **Error Handling:** Rollback penuh; error teknis dicatat tanpa data sensitif
- **Acceptance Criteria:** BRA §18.2 + S E — Given error disimulasikan setelah validasi sebelum movement selesai, when posting dijalankan, then saldo, serial, dan status dokumen tidak berubah sebagian.

---

## 8. BUSINESS RULES

Seluruh 28 aturan BRA dipetakan di bawah ini. Aturan wajib dijaga di backend, bukan hanya di tampilan. Status khusus: RULE-023 **Open D-B09**, RULE-020 memiliki gap FR **D-B14 Proposed**, RULE-025/detail cancel Draft **D-B15 Proposed**, RULE-028 terkait **D-T01 Proposed**.

### 8.1 Master Data Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-001 | Setiap barang mempunyai kode unik | M03 | FR-MD-003 | AC BRA §18.1 |
| RULE-002 | Kategori, satuan, gudang, dan kode lokasi unik dalam ruang lingkupnya | M03, M04 | FR-MD-001, FR-MD-002, FR-WH-001, FR-WH-002 | AC BRA §18.1 |

### 8.2 Warehouse Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-003 | Barang dan lokasi nonaktif tidak dapat dipakai transaksi baru | M03, M04, M05–M08 | FR-WH-003, FR-IN-002, FR-OUT-002 | Histori lama tetap terbaca |
| RULE-016 | Lokasi asal dan tujuan transfer harus berbeda dan aktif | M04, M07 | FR-TRF-002 | AC BRA §18.4 |

### 8.3 Serial Number Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-007 | Barang serialized dilacak per unit; satu serial hanya satu status/lokasi aktif | M03, M05–M08 | FR-MD-004, FR-OUT-004 | S C |
| RULE-008 | Jumlah serial sama dengan kuantitas transaksi dan kuantitas bulat | M05, M06, M08 | FR-IN-004, FR-OUT-004 | BRA §18.2, S C; D-B05 Proposed |
| RULE-009 | Serial number tidak boleh didaftarkan dua kali untuk unit yang sama | M03, M05–M08 | FR-IN-004 | Unique global untuk MVP merupakan D-B03 Proposed |

### 8.4 Quantity Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-004 | Kuantitas transaksi lebih dari nol | M05–M08 | FR-IN-003, FR-OUT-002 | Kriteria ringkas BRA §9.3 |
| RULE-005 | Kuantitas memakai tipe numerik presisi tetap, bukan floating point | M03, M05–M09 | FR-MD-002, FR-IN-003 | DECIMAL; D-B05 Proposed |
| RULE-006 | Barang non-serialized dilacak berdasarkan kuantitas | M03, M05, M06, M09 | FR-MD-004 | D-B02 Proposed |

### 8.5 Stock Inbound Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-013 | Inbound menambah saldo lokasi tujuan saat posting berhasil | M05, M09 | FR-IN-005 | BRA §18.2 |

### 8.6 Stock Outbound Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-014 | Outbound mengurangi saldo asal saat posting dan tidak boleh menyebabkan stok negatif | M06, M07, M09 | FR-OUT-003, FR-OUT-005, FR-TRF-003 | BRA §18.3, S A; KPI-02 |

### 8.7 Stock Transfer Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-015 | Transfer mengurangi asal dan menambah tujuan dalam satu transaksi database | M07, M09 | FR-TRF-004 | BRA §18.4, S B/S E |
| RULE-017 | Total stok gabungan tidak berubah akibat transfer internal | M07, M09 | FR-TRF-005 | BRA §18.4, S B |

### 8.8 Stock Opname Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-021 | Selisih opname = hasil fisik dikurangi snapshot sistem | M08 | FR-OPN-002, FR-OPN-004 | BRA §18.5, S D |
| RULE-022 | Penyesuaian opname memiliki alasan, pelaku, waktu, dan otorisasi | M08, M12 | FR-OPN-005, FR-OPN-006 | BRA §18.5 |
| RULE-023 | Selama sesi opname aktif, transaksi yang mengubah stok lokasi diblokir pada MVP | M08, M05–M07 | FR-OPN-001–006 | **Open D-B09 — jangan diimplementasikan sebelum disetujui** |

### 8.9 Transaction Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-010 | Transaksi Draft tidak mengubah saldo stok | M05–M07 | FR-IN-001, FR-OUT-001, FR-TRF-001 | BRA §18.2–§18.3 |
| RULE-011 | Posting hanya oleh pengguna dengan permission sesuai | M01, M02, M05–M08 | FR-AUTH-003, FR-USER-002, FR-IN-005, FR-OUT-005 | BRA §18.6; backend authorization |
| RULE-012 | Dokumen Posted tidak boleh diubah atau dihapus langsung | M05–M08, M12 | FR-AUD-002 | BRA §18.6 |
| RULE-019 | Saldo tidak diedit langsung melalui form master barang | M03, M09 | FR-MD-003, FR-STK-001 | Koreksi lewat dokumen/penyesuaian beralasan |
| RULE-020 | Opening balance dicatat melalui dokumen khusus/penyesuaian awal | M05, M09 | Belum ada FR khusus | **Gap D-B14 Proposed** |
| RULE-024 | Nomor dokumen unik dan tidak dipakai ulang | M05–M08 | FR-IN-001, FR-OUT-001, FR-TRF-001 | D-B15 Proposed untuk detail AC |
| RULE-025 | Pembatalan Draft tidak menghasilkan stock movement | M05–M07, M12 | FR-IN-001, FR-OUT-001, FR-TRF-001 | **Gap FR/AC D-B15 Proposed** |
| RULE-026 | Koreksi Posted melalui reversal/adjustment yang merujuk dokumen awal | M05–M08, M12 | FR-AUD-003 | D-B08 Proposed |
| RULE-027 | Perubahan stok dan status dokumen berhasil atau gagal bersama-sama | M05–M09, M12 | FR-AUD-004, FR-IN-005, FR-OUT-005, FR-TRF-004 | S E |

### 8.10 Audit Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-018 | Setiap perubahan saldo menghasilkan stock movement yang memiliki referensi dokumen | M05–M09, M12 | FR-IN-005, FR-OUT-005, FR-TRF-004, FR-OPN-006, FR-AUD-001 | KPI-01; S A–S E |

### 8.11 Timezone Rules
| Rule ID | Aturan | Modul | FR terkait | Catatan |
|---|---|---|---|---|
| RULE-028 | Semua waktu transaksi disimpan konsisten; tampilan memakai zona waktu aplikasi, default proyek Asia/Jakarta | Lintas transaksi, M12 | NFR-COMP-001 | **D-T01 Proposed**; config saat ini UTC per AGENTS.md |

---

## 9. DATA REQUIREMENTS

Bagian ini bersifat **konseptual** sesuai BRA §11. Bukan ERD final, bukan daftar migration, dan bukan penetapan seluruh kolom database.

| Entitas | Fungsi | Atribut penting (konseptual) | Hubungan utama | Catatan keputusan |
|---|---|---|---|---|
| User | Akun aplikasi | Nama, email/username, password hash, status | Memiliki Role; menjadi aktor transaksi dan audit | Hashing Laravel (NFR-SEC-001) |
| Role | Kelompok hak akses | Nama role | Dimiliki User; memiliki Permission | Matriks BRA §12; Policy/Gate terkait D-T05 Proposed |
| Permission | Hak aksi spesifik | Nama permission | Dimiliki Role | Posting dibedakan dari Draft (D-B07 Proposed) |
| Item | Master barang | Kode internal, material number, nama, deskripsi, kategori, satuan, tracking type, minimum stock, status | Termasuk Category dan Unit; memiliki Serial Number; menjadi baris dokumen | D-B01 Proposed untuk dua field kode; D-B02 Proposed untuk tracking per item; D-B10 Proposed untuk minimum per item |
| Category | Klasifikasi barang | Kode, nama, deskripsi, status | Memiliki banyak Item | RULE-002 |
| Unit | Satuan barang | Kode satuan, nama, precision | Dipakai Item | Presisi DECIMAL terkait D-B05 Proposed |
| Supplier | Referensi penerimaan | Kode, nama, kontak, status | Opsional pada Stock Document inbound | D-B06 Proposed |
| Warehouse | Gudang | Kode, nama, alamat/deskripsi, status | Memiliki banyak Warehouse Location | MVP satu gudang (ASM-01) |
| Warehouse Location | Lokasi penyimpanan | Kode lokasi, nama, tipe, parent, status | Hierarki Warehouse > Area > Rack > Level; dipakai dokumen, movement, saldo | D-B04 Proposed; Bin future |
| Stock Document | Header transaksi | Nomor, tipe, tanggal, status, pembuat, pemosting, waktu posting, catatan | Memiliki banyak Stock Document Item; menghasilkan Stock Movement | Draft/Posted; status dan waktu posting terkait D-B07 Proposed |
| Stock Document Item | Baris transaksi | Dokumen, item, lokasi asal/tujuan, kuantitas, satuan | Milik Stock Document; dapat menghasilkan satu atau lebih movement | Transfer menghasilkan dua movement (RULE-015) |
| Stock Movement | Ledger pergerakan stok | Item, lokasi, quantity delta, tipe movement, referensi dokumen, waktu, aktor | Referensi Stock Document; penjumlahannya membentuk Stock Balance | RULE-018; RULE-027; D-T06 Proposed |
| Stock Balance | Saldo terkini | Item, lokasi, quantity on hand | Dihitung dari Stock Movement | RULE-019; tidak diedit manual |
| Serial Number | Identitas unit berserial | Item, serial number, status, lokasi saat ini | Milik Item; mengikuti lokasi/status transaksi | Unique global untuk MVP adalah D-B03 Proposed |
| Stock Opname | Sesi pemeriksaan fisik | Nomor sesi, lokasi, waktu snapshot, status, pembuat, peninjau | Memiliki banyak baris pemeriksaan (item, snapshot qty, counted qty, variance, alasan) | RULE-021–RULE-022; RULE-023 Open D-B09 |
| Audit Log | Jejak aktivitas kritis | Aktor, aksi, objek, nilai penting, waktu | Merujuk objek kritis | Retensi belum diputus — D-B16 Open |

**Prinsip saldo dan ledger (BRA §11.4):**
- `Saldo(item, lokasi) = jumlah seluruh quantity_delta untuk item dan lokasi tersebut`.
- `Total stok item = jumlah saldo item di seluruh lokasi aktif`.
- `quantity_delta` positif untuk penambahan dan negatif untuk pengurangan.
- Reservation tidak termasuk MVP, sehingga available stock sama dengan on-hand.

Contoh sintetis: `ITM-0001` di `LOC-A-01-01` memiliki delta +100 (opening), +20 (inbound), -15 (outbound), -10 (transfer out) sehingga saldo 95; `LOC-B-02-01` menerima +10. Total sebelum dan sesudah transfer tetap 105.

## 10. NON-FUNCTIONAL REQUIREMENTS

Seluruh 19 NFR BRA dipetakan. Target yang tidak didefinisikan BRA tidak diarang: ditandai sebagai tidak tersedia dan, bila perlu, menjadi **usulan** yang menunggu persetujuan. Komitmen produksi tidak dinyatakan karena BRA hanya menetapkan target prototype.

### 10.1 Security
| Field | NFR-SEC-001 | NFR-SEC-002 | NFR-SEC-003 | NFR-SEC-004 | NFR-SEC-005 |
|---|---|---|---|---|---|
| Requirement ID | NFR-SEC-001 | NFR-SEC-002 | NFR-SEC-003 | NFR-SEC-004 | NFR-SEC-005 |
| Description | Password disimpan dengan hashing bawaan Laravel | Backend memvalidasi permission; sembunyi tombol saja tidak cukup | CSRF, validasi server-side, escaping Blade, query aman via ORM/binding | Sesi dikelola aman; logout mengakhiri sesi | Demo tidak memakai data rahasia/operasional tanpa izin |
| Quality Attribute | Security | Security / Authorization | Web security | Session security | Data exposure |
| Target (prototype) | Sesuai BRA; tidak ada angka tambahan | Sesuai BRA; tidak ada angka tambahan | Sesuai BRA | Sesuai BRA | Nol data produksi tanpa izin |
| Measurement Method | Inspeksi penyimpanan password | Uji akses URL langsung dan service | Inspeksi kode dan uji input | Uji login/logout | Audit sumber data demo |
| Verification Method | Review implementasi | Test permission | Test validasi dan escaping | Test sesi | Review data dummy |
| Related Modules | M01, M02 | M01, M02, M05–M08 | Lintas modul | M01 | Lintas modul |
| Status | Aktif; D-T05 Proposed untuk auth | Aktif; D-T05 Proposed | Aktif | Aktif; D-T05 Proposed | Aktif; wajib ASM-07 |

### 10.2 Reliability — termasuk integritas data dan traceability
| Field | NFR-INT-001 | NFR-INT-002 | NFR-INT-003 | NFR-REL-001 |
|---|---|---|---|---|
| Requirement ID | NFR-INT-001 | NFR-INT-002 | NFR-INT-003 | NFR-REL-001 |
| Description | Dokumen, movement, serial, dan saldo diubah secara atomik dalam database transaction | Posting mengunci atau memvalidasi saldo secara transaksional agar dua permintaan bersamaan tidak menghasilkan stok negatif | Setiap movement memiliki referensi dokumen, waktu, dan aktor | Jika posting gagal, rollback dan tidak ada saldo parsial |
| Quality Attribute | Data integrity | Concurrency | Traceability | Reliability |
| Target (prototype) | Sesuai BRA | 0 kasus stok negatif (KPI-02) | 100% movement memiliki referensi (KPI-01, KPI-07) | Sesuai BRA |
| Measurement Method | Uji transaksi dan rollback | Uji posting bersamaan | Inspeksi field movement | Simulasi error S E |
| Verification Method | Test integrasi | Test konkurensi | Test audit | Test rollback |
| Related Modules | M05–M09 | M05–M09 | M09, M12 | M05–M09 |
| Status | Aktif; D-T06 Proposed | Aktif; D-T06 Proposed | Aktif; D-B16 Open untuk retensi | Aktif; D-T06 Proposed |

### 10.3 Performance
| Field | NFR-PERF-001 | NFR-PERF-002 |
|---|---|---|
| Requirement ID | NFR-PERF-001 | NFR-PERF-002 |
| Description | Halaman utama dan daftar umum tampil dalam target waktu pada dataset uji | Daftar barang/transaksi memakai pagination dan filter |
| Quality Attribute | Performance | Performance / Usability |
| Target (prototype) | ≤3 detik pada 1.000 item dan 5.000 stock movement di lingkungan lokal normal; **bukan SLA produksi** | Sesuai BRA; tidak ada angka tambahan |
| Measurement Method | Pengukuran waktu muat pada dataset uji | Inspeksi daftar dan jumlah data per halaman |
| Verification Method | Uji performa lokal | Uji fungsional pagination/filter |
| Related Modules | Lintas modul | M03, M09, M11 |
| Status | Aktif; target prototype | Aktif |

### 10.4 Usability
| Field | NFR-USE-001 | NFR-USE-002 |
|---|---|---|
| Requirement ID | NFR-USE-001 | NFR-USE-002 |
| Description | Form memakai label, validasi, pesan error jelas, dan konfirmasi sebelum posting | Tampilan dapat dipakai desktop serta layar tablet/ponsel melalui layout responsif Bootstrap |
| Quality Attribute | Usability | Responsiveness / Compatibility |
| Target (prototype) | Sesuai BRA | Sesuai BRA |
| Measurement Method | Review form dan pesan | Uji tampilan pada ukuran layar yang didokumentasikan |
| Verification Method | Uji UI | Uji responsif |
| Related Modules | Lintas modul | Lintas modul |
| Status | Aktif | Aktif; **D-T03 Accepted** untuk Blade + Bootstrap 5 |

### 10.5 Maintainability
| Field | NFR-MNT-001 | NFR-MNT-002 |
|---|---|---|
| Requirement ID | NFR-MNT-001 | NFR-MNT-002 |
| Description | Struktur kode mengikuti konvensi Laravel; business logic tidak ditumpuk di Blade | Logika stok kritis memiliki automated tests |
| Quality Attribute | Maintainability | Testability |
| Target (prototype) | Sesuai BRA | Automated tests untuk inbound, outbound, transfer, opname, dan serial |
| Measurement Method | Review struktur dan Blade | Menjalankan test suite |
| Verification Method | Code review | `php artisan test` / `vendor/bin/phpunit` |
| Related Modules | Lintas modul | M03, M05–M08 |
| Status | Aktif | Aktif; DB test terpisah masih **D-T04 Proposed** |

### 10.6 Compatibility
| Field | NFR-COMP-001 |
|---|---|
| Requirement ID | NFR-COMP-001 |
| Description | Aplikasi dikembangkan dan diuji pada versi PHP, Laravel, MySQL, Node, dan browser yang didokumentasikan |
| Quality Attribute | Compatibility |
| Target (prototype) | PHP minimum 8.3 sudah **Accepted D-T02**; daftar versi Laravel, MySQL, Node, dan browser belum final |
| Measurement Method | Dokumentasi environment dan matriks versi |
| Verification Method | Review dokumentasi dan uji instalasi lokal |
| Related Modules | Lintas modul |
| Status | Aktif; D-T02 Accepted, D-T07 Proposed untuk dokumentasi versi |

### 10.7 Operations
| Field | NFR-OPS-001 |
|---|---|
| Requirement ID | NFR-OPS-001 |
| Description | Database demo dapat di-backup dan dipulihkan dengan prosedur terdokumentasi |
| Quality Attribute | Operations / Recoverability |
| Target (prototype) | Sesuai BRA; runbook belum tersedia |
| Measurement Method | Keberadaan prosedur backup/restore |
| Verification Method | Uji restore mengikuti prosedur |
| Related Modules | Lintas modul, System Administrator |
| Status | Aktif; **D-T07 Proposed** |

### 10.8 Logging
| Field | NFR-LOG-001 |
|---|---|
| Requirement ID | NFR-LOG-001 |
| Description | Error teknis dicatat melalui mekanisme logging aplikasi tanpa membocorkan password/token |
| Quality Attribute | Logging / Security |
| Target (prototype) | Sesuai BRA |
| Measurement Method | Inspeksi log |
| Verification Method | Uji error dan review log |
| Related Modules | M12 |
| Status | Aktif; retensi audit terkait **D-B16 Open** |

### 10.9 Documentation
| Field | NFR-DOC-001 |
|---|---|
| Requirement ID | NFR-DOC-001 |
| Description | README, setup lokal, data dummy, role, aturan bisnis, dan panduan demo disertakan |
| Quality Attribute | Documentation |
| Target (prototype) | Sesuai BRA; KPI-08 |
| Measurement Method | Daftar dokumen tersedia |
| Verification Method | Review dokumentasi |
| Related Modules | Lintas modul |
| Status | Aktif; D-T07 dan D-T08 Proposed |

**Pemisahan target prototype dan komitmen produksi:** seluruh target di atas adalah kriteria demonstrasi prototype menurut BRA §4.2 dan §13. Tidak ada komitmen SLA, retensi, atau standar keamanan produksi yang ditetapkan dalam sumber.

---

## 11. REPORTING REQUIREMENTS

Seluruh REP-01–REP-08 dipetakan. Export Excel/PDF **tidak dimasukkan ke MVP** karena D-B17 Deferred. Kriteria penerimaan laporan belum tersedia secara formal di BRA, sehingga seluruh AC pada bagian ini ditandai **Proposed Acceptance Criteria**.

| Field | REP-01 Stock Summary | REP-02 Stock Card | REP-03 Inbound Report | REP-04 Outbound Report |
|---|---|---|---|---|
| Report Name | Stock Summary | Stock Card | Inbound Report | Outbound Report |
| Purpose | Menampilkan saldo stok ringkas | Menampilkan riwayat pergerakan per barang | Menampilkan transaksi penerimaan | Menampilkan transaksi pengeluaran |
| Data Source | Stock Balance, Item, Category, Warehouse Location | Stock Movement, Stock Document | Stock Document inbound dan barisnya | Stock Document outbound dan barisnya |
| Filters | Barang, kategori, lokasi | Barang, lokasi, periode | Periode, status, item | Periode, tujuan, item |
| Information Displayed | Kode barang, nama, kategori, gudang, lokasi, saldo, satuan, minimum stock | Tanggal, nomor dokumen, tipe movement, masuk, keluar, saldo berjalan, aktor | Nomor dokumen, tanggal, sumber, item, lokasi, qty, status, pemosting | Nomor dokumen, tanggal, tujuan, item, lokasi, qty, status, pemosting |
| User Access | Seluruh peran pembaca | Seluruh peran pembaca | IA, WO terbatas, Viewer, SA | IA, WO terbatas, Viewer, SA |
| Expected Output | Tabel layar, tanpa export | Tabel layar/kartu, tanpa export | Tabel layar, tanpa export | Tabel layar, tanpa export |
| Acceptance Criteria | **Proposed AC** — filter lokasi `LOC-A-01-01` hanya menampilkan baris lokasi tersebut | **Proposed AC** — saldo berjalan sesuai penjumlahan delta | **Proposed AC** — filter periode hanya menampilkan dokumen pada periode itu | **Proposed AC** — kolom tujuan dan pemosting tampil pada dokumen Posted |

| Field | REP-05 Transfer Report | REP-06 Stock Opname Report | REP-07 Low Stock Report | REP-08 Audit Activity Report |
|---|---|---|---|---|
| Report Name | Transfer Report | Stock Opname Report | Low Stock Report | Audit Activity Report |
| Purpose | Menampilkan perpindahan antar lokasi | Menampilkan hasil pemeriksaan fisik | Menampilkan barang di bawah minimum | Menampilkan aktivitas kritis |
| Data Source | Stock Document transfer dan movement | Stock Opname dan barisnya | Item, Stock Balance | Audit Log |
| Filters | Periode, lokasi, item | Periode, lokasi, status | Kategori, lokasi | Aktor, aksi, periode |
| Information Displayed | Nomor dokumen, tanggal, lokasi asal, lokasi tujuan, item, qty, status | Nomor sesi, lokasi, snapshot qty, counted qty, variance, alasan, status | Barang, lokasi (jika minimum per lokasi), saldo, minimum, selisih | Aktor, aksi, objek, waktu, referensi |
| User Access | IA, Viewer, SA | IA, Viewer | IA, Viewer | SA, IA terbatas |
| Expected Output | Tabel layar, tanpa export | Tabel layar, tanpa export | Tabel layar, tanpa export | Tabel layar, tanpa export; export ditunda (D-B17) |
| Acceptance Criteria | **Proposed AC** — setiap baris memiliki lokasi asal dan tujuan berbeda | **Proposed AC** — variance = counted − snapshot | **Proposed AC** — hanya item dengan saldo di bawah minimum yang tampil | **Proposed AC** — entri audit mencatat aktor, aksi, waktu, dan referensi; retensi menunggu D-B16 |

---

## 12. ERROR HANDLING & AUDIT REQUIREMENTS

### 12.1 Validation errors
| Kondisi | Perlakuan sistem | FR / RULE | AC |
|---|---|---|---|
| Kode master duplikat | Tolak simpan, tampilkan pesan validasi | FR-MD-001/002/003, FR-WH-001/002; RULE-001/002 | BRA §18.1 |
| Kuantitas nol/negatif | Tolak simpan/post | FR-IN-003; RULE-004 | **Proposed AC** |
| Serial tidak sesuai kuantitas atau duplikat | Tolak post | FR-IN-004, FR-OUT-004; RULE-007–009 | BRA §18.2, S C |
| Master nonaktif dipakai transaksi baru | Tolak | FR-WH-003; RULE-003 | BRA §18.1 |
| Lokasi transfer sama | Tolak | FR-TRF-002; RULE-016 | BRA §18.4 |

### 12.2 Authentication errors
| Kondisi | Perlakuan sistem | FR | AC |
|---|---|---|---|
| Kredensial salah | Login ditolak dengan pesan generik; tidak membocorkan akun | FR-AUTH-001 | **Proposed AC** |
| Sesi berakhir / logout | Arahkan ke login | FR-AUTH-002, FR-AUTH-003 | BRA §18.6 untuk akses tanpa login |

### 12.3 Authorization errors
| Kondisi | Perlakuan sistem | FR / RULE | AC |
|---|---|---|---|
| URL langsung tanpa sesi | Redirect ke login | FR-AUTH-003; RULE-011 | BRA §18.6 |
| Pengguna tanpa permission mencoba posting | Tolak di backend; saldo tidak berubah | FR-IN-005, FR-OUT-005, FR-TRF-004, FR-OPN-006; RULE-011 | BRA §18.6 |
| WO mencoba otorisasi penyesuaian opname | Tolak | FR-OPN-005 | BRA §18.5 |

### 12.4 Stock insufficiency errors
| Kondisi | Perlakuan sistem | FR / RULE | AC |
|---|---|---|---|
| Outbound melebihi saldo | Posting ditolak; saldo tetap | FR-OUT-003, FR-OUT-005; RULE-014 | BRA §18.3, S A; KPI-02 |
| Transfer melebihi saldo asal | Posting ditolak; saldo tetap | FR-TRF-003; RULE-014/016 | BRA §18.4 |

### 12.5 Duplicate transaction errors
| Kondisi | Perlakuan sistem | FR / RULE | AC |
|---|---|---|---|
| Nomor dokumen duplikat | Tolak pembuatan dokumen | FR-IN-001, FR-OUT-001, FR-TRF-001; RULE-024 | Sebagian BRA §18.2; detail **D-B15 Proposed** |
| Posting dokumen yang sama kedua kalinya | Tolak atau tidak menghasilkan perubahan ganda | FR-IN-005, FR-OUT-005, FR-AUD-004; RULE-027 | BRA §18.2 butir 3; detail **D-B15 Proposed** |

### 12.6 Transaction rollback
| Kondisi | Perlakuan sistem | FR / RULE | AC |
|---|---|---|---|
| Error setelah validasi sebelum movement selesai | Rollback saldo, serial, status, dan movement | FR-AUD-004; RULE-027; NFR-INT-001, NFR-REL-001 | S E; BRA §18.2 |
| Salah satu sisi transfer gagal | Batalkan kedua movement | FR-TRF-004; RULE-015 | BRA §18.4; S B/S E |

### 12.7 Audit activity
- Setiap create/update/post/cancel pada objek kritis dicatat dengan aktor, waktu, aksi, dan referensi objek (FR-AUD-001; FR-USER-003; RULE-018).
- Setiap posting mencatat pemosting dan waktu posting (KPI-07; BRA §18.6).
- Audit dapat dilihat oleh System Administrator dan Inventory Administrator terbatas sesuai BRA §12.
- Periode retensi audit **belum diputus — D-B16 Open**; SRS tidak menetapkan angka retensi.

### 12.8 Reversal and adjustment traceability
- Dokumen Posted tidak diubah atau dihapus langsung (FR-AUD-002; RULE-012).
- Koreksi wajib melalui reversal atau adjustment dengan referensi dokumen awal dan alasan (FR-AUD-003; RULE-026).
- Detail alur koreksi masih **D-B08 Proposed**; AC untuk reversal/adjustment ditandai **Proposed Acceptance Criteria**.
- Opening balance harus memakai dokumen khusus/penyesuaian awal, tetapi FR detail belum tersedia (**D-B14 Proposed**).

---

## 13. ACCEPTANCE CRITERIA

Kriteria di bawah mengacu pada BRA §18 (AC formal), BRA §19 (Skenario A–E), KPI-01–KPI-08, FR, RULE, dan NFR. Kriteria yang belum tersedia di BRA tetap ditandai **Proposed Acceptance Criteria**.

### 13.1 KPI-01 sampai KPI-08
| KPI | Given-When-Then | Sumber |
|---|---|---|
| KPI-01 | **Given** dokumen inbound/outbound/transfer/opname diposting, **when** stock movement diperiksa, **then** 100% posting memiliki minimal satu movement dengan referensi dokumen, waktu, dan aktor | BRA §4.2; §18; S A–S E |
| KPI-02 | **Given** saldo item sintetis 6, **when** outbound 7 dipost, **then** posting ditolak dan tidak ada saldo negatif pada seluruh skenario uji | BRA §4.2; §18.3; S A |
| KPI-03 | **Given** serangkaian movement pada `ITM-0001` di `LOC-A-01-01`, **when** saldo dibandingkan dengan penjumlahan ledger, **then** keduanya sama | BRA §4.2; S B, S D |
| KPI-04 | **Given** `ITM-0001` bertipe serialized dan kuantitas 2, **when** inbound dipost, **then** dua serial unik valid tercatat dan jumlah serial sama dengan kuantitas | BRA §4.2; §18.2; S C |
| KPI-05 | **Given** total stok gabungan 12 sebelum transfer 3, **when** transfer dipost, **then** total gabungan tetap 12 | BRA §4.2; §18.4; S B |
| KPI-06 | **Given** pengguna berrole Viewer, **when** mencoba posting melalui URL langsung, **then** aksi ditolak oleh backend dan saldo tidak berubah | BRA §4.2; §18.6 |
| KPI-07 | **Given** dokumen berhasil diposting, **when** detail dilihat, **then** pelaku dan waktu posting tampil | BRA §4.2; §18.6 |
| KPI-08 | **Given** tahap dokumentasi selesai, **when** direktori proyek diperiksa, **then** kebutuhan, aturan bisnis, diagram, pengujian, dan panduan penggunaan tersedia | BRA §4.2; **Proposed AC** untuk detail kelengkapan |

### 13.2 Acceptance criteria per modul FR
| Modul | AC utama | Status AC |
|---|---|---|
| M01 Authentication | redirect tanpa login, aksi tanpa permission ditolak | BRA §18.6; sebagian **Proposed** |
| M02 User Management | role mengikuti matriks; pelaku/waktu tercatat | BRA §18.6; sebagian **Proposed** |
| M03 Master Data | kode unik, duplikat ditolak, nonaktif tetap di histori | BRA §18.1; sebagian **Proposed** |
| M04 Warehouse & Location | kode lokasi unik dan parent valid | BRA §18.1 |
| M05 Inbound | Draft nol perubahan; post sekali; serial sesuai qty; rollback | BRA §18.2; S A, S C, S E; cancel Draft **Proposed** |
| M06 Outbound | Draft tidak mengurangi saldo; stok kurang ditolak; serial tidak dipakai ulang | BRA §18.3; S A, S C |
| M07 Transfer | lokasi sama ditolak; stok kurang ditolak; total tetap; rollback dua sisi | BRA §18.4; S B, S E |
| M08 Opname | snapshot tersimpan; variance = fisik − snapshot; penyesuaian butuh alasan/akses; movement tercatat | BRA §18.5; S D; lock **Open D-B09** |
| M09 Saldo & Kartu | saldo = ledger; kartu memuat tanggal, tipe, referensi, masuk/keluar, saldo | **Proposed AC** |
| M10 Dashboard | tidak ada penjumlahan satuan berbeda; low stock mengikuti minimum | **Proposed AC**; konflik D-B13 |
| M11 Reporting | filter periode/status/item berfungsi; data konsisten dengan ledger | **Proposed AC** |
| M12 Audit | aksi kritis tercatat; Posted immutable; reversal berreferensi; rollback | BRA §18.6; reversal **Proposed**, retensi **Open D-B16** |

### 13.3 Acceptance criteria untuk business rules
| Aturan | Kriteria uji | Sumber |
|---|---|---|
| RULE-010 | Given Draft dibuat, when saldo diperiksa, then tidak ada perubahan | BRA §18.2–§18.3 |
| RULE-013/014 | Given inbound 10 dipost, when saldo dicek, then +10; given outbound 7 dari saldo 6, then ditolak | BRA §18.2–§18.3; S A |
| RULE-015/017 | Given transfer 3 dari asal 10 ke tujuan 2, when selesai, then 7 dan 5, total 12 | BRA §18.4; S B |
| RULE-018/027 | Given error disimulasikan saat posting, when transaksi gagal, then tidak ada perubahan saldo/status/movement parsial | S E; BRA §18.2 |
| RULE-021/022 | Given snapshot 8 dan fisik 7, when penyesuaian tanpa alasan diajukan, then ditolak; dengan alasan dan otorisasi, then saldo 7 dan movement tercatat | BRA §18.5; S D |
| RULE-023 | Tidak ada kriteria aktif karena **D-B09 Open** | RTA §14 |
| RULE-024/025 | Given Draft dibatalkan, then tidak ada movement; given nomor dokumen dipakai ulang, then ditolak | **Proposed AC**, D-B15 |
| RULE-026 | Given dokumen Posted dikoreksi, then reversal/adjustment memiliki referensi dan alasan | **Proposed AC**, D-B08 |
| RULE-028 | Given waktu transaksi dicatat, when ditampilkan, then mengikuti zona waktu aplikasi yang ditetapkan | **Proposed AC**, D-T01 |

### 13.4 Skenario uji bisnis minimum BRA §19
**Skenario A — Inbound dan outbound**
1. **Given** stok awal `ITM-0001` di `LOC-A-01-01` = 0, **when** inbound 10 dipost, **then** saldo = 10.
2. **Given** saldo 10, **when** outbound 4 dipost, **then** saldo = 6.
3. **Given** saldo 6, **when** outbound 7 dicoba, **then** sistem menolak dan saldo tetap 6.

**Skenario B — Transfer**
1. **Given** saldo `ITM-0001` di `LOC-A-01-01` = 10 dan di `LOC-B-02-01` = 2, **when** transfer 3 dipost, **then** asal = 7, tujuan = 5, total = 12.

**Skenario C — Serial number**
1. **Given** `ITM-0002` bertipe serialized, **when** inbound kuantitas 2 dibuat, **then** dua serial unik wajib ada.
2. **Given** kuantitas 2 dengan satu serial, **when** dipost, **then** ditolak.
3. **Given** serial berstatus tersedia di lokasi asal, **when** outbound dipost, **then** serial tersebut dipakai.
4. **Given** serial sudah keluar, **when** dipilih lagi, **then** ditolak.

**Skenario D — Stock opname**
1. **Given** snapshot `ITM-0001` di `LOC-A-01-01` = 8 dan hitung fisik = 7, **when** variance dihitung, **then** variance = -1.
2. **Given** penyesuaian -1 memiliki alasan dan otorisasi, **when** diposting, **then** saldo = 7 dan movement OPNAME_ADJUSTMENT tercatat.

**Skenario E — Kegagalan atomik**
1. **Given** validasi posting berhasil, **when** error disimulasikan sebelum seluruh movement selesai, **then** saldo, serial, dan status dokumen tidak mengalami perubahan parsial.

### 13.5 Acceptance criteria NFR
| NFR | Kriteria verifikasi | Status |
|---|---|---|
| NFR-SEC-001–005 | Password tidak plaintext; akses URL langsung ditolak; input berbahaya divalidasi; logout mengakhiri sesi; tidak ada data produksi | Aktif, sebagian **Proposed AC** |
| NFR-INT-001–003, NFR-REL-001 | Semua posting atomik; konkurensi tidak menghasilkan stok negatif; movement memiliki dokumen/waktu/aktor; rollback berhasil | Aktif; D-T06 Proposed |
| NFR-PERF-001 | Waktu muat ≤3 detik pada 1.000 item dan 5.000 movement di lokal normal | Target prototype BRA, bukan SLA produksi |
| NFR-PERF-002 | Daftar memakai pagination dan filter | **Proposed AC** |
| NFR-USE-001–002 | Label/error jelas; konfirmasi sebelum posting; layout responsif Bootstrap | D-T03 Accepted untuk stack |
| NFR-MNT-001–002 | Konvensi Laravel dipatuhi; test otomatis untuk inbound, outbound, transfer, opname, serial | D-T04 Proposed untuk DB test |
| NFR-COMP-001 | Versi runtime didokumentasikan; PHP minimum 8.3 | D-T02 Accepted; D-T07 Proposed |
| NFR-OPS-001 | Prosedur backup/restore tersedia dan diuji | D-T07 Proposed |
| NFR-LOG-001 | Log tidak memuat password/token | Aktif; D-B16 Open untuk retensi |
| NFR-DOC-001 | README, setup, dummy, role, aturan, panduan demo tersedia | KPI-08; D-T08 Proposed |

---

## 14. REQUIREMENTS TRACEABILITY

Rantai pelacakan: **BRA → RTA → BDL → SRS → Acceptance Criteria**. BRA adalah sumber requirement, RTA adalah pemetaan, BDL adalah status keputusan, SRS adalah spesifikasi perilaku, dan AC adalah kriteria uji. Konfigurasi repositori bukan sumber requirement.

### 14.1 Dasar jumlah
| Kategori | Jumlah | Sumber |
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
| **Total ID BRA** | **168** | RTA §5.3 |
| Decision ID BDL | 25 | D-B01–D-B17, D-T01–D-T08 |
| Gap | 12 | RTA §11 |
| Conflict | 13 | RTA §12 |

### 14.2 Goals dan KPI
| ID | RTA | BDL | SRS | AC |
|---|---|---|---|---|
| GOAL-01 | §7.1 | D-B01, D-B02, D-B04, D-B05 | §3.3, §7.C, §7.D | §18.1 |
| GOAL-02 | §7.1 | D-B07, D-T06 | §7.E–§7.I, §7.L | S A–S E |
| GOAL-03 | §7.1 | D-B08, D-T06 | §7.I, §7.L, §11 | §18.6 |
| GOAL-04 | §7.1 | D-B01–D-B03, D-B05 | §7.C, §7.E, §7.F | S C |
| GOAL-05 | §7.1 | D-B09 Open | §7.H | §18.5, S D |
| GOAL-06 | §7.1 | D-B13, D-B17 | §7.J, §7.K, §11 | **Proposed AC** |
| GOAL-07 | §7.1 | D-T05 | §4, §7.A, §7.B | §18.6 |
| KPI-01 | §7.2 | D-T06 | §7.E–§7.I, §12.7 | §13.1, S A–S E |
| KPI-02 | §7.2 | D-T06 | §7.F, §8.6 | §13.1, S A |
| KPI-03 | §7.2 | D-T06 | §7.I, §9 | §13.1, S B, S D |
| KPI-04 | §7.2 | D-B02, D-B03, D-B05 | §7.C, §7.E, §7.F | §13.1, S C |
| KPI-05 | §7.2 | D-T06 | §7.G | §13.1, S B |
| KPI-06 | §7.2 | D-T05 | §4, §12.3 | §13.1, §18.6 |
| KPI-07 | §7.2 | D-B16 Open | §7.L, §12.7 | §13.1, §18.6 |
| KPI-08 | §7.2 | D-B13, D-T07, D-T08 | §10.9, §17 | **Proposed AC** |

### 14.3 Business Requirements
| ID | RTA | BDL | SRS | AC |
|---|---|---|---|---|
| BR-001 | §7.3 | D-B01, D-B02, D-B04, D-B05 | §7.C, §7.D | §18.1 |
| BR-002 | §7.3 | D-B10 | §7.I | **Proposed AC** |
| BR-003 | §7.3 | D-B06, D-B15 | §7.E | §18.2 |
| BR-004 | §7.3 | D-B07, D-B11 Deferred | §7.F | §18.3 |
| BR-005 | §7.3 | D-T06 | §7.G | §18.4 |
| BR-006 | §7.3 | D-T06 | §7.F, §7.G | §18.3, S A |
| BR-007 | §7.3 | D-T06 | §7.E–§7.I, §7.L | §18.2–§18.5 |
| BR-008 | §7.3 | D-B01–D-B03 | §7.C, §7.E, §7.F | S C |
| BR-009 | §7.3 | D-B04 | §7.D | §18.1 |
| BR-010 | §7.3 | D-B09 Open | §7.H | §18.5, S D |
| BR-011 | §7.3 | D-T05 | §4, §7.A, §7.B | §18.6 |
| BR-012 | §7.3 | D-B13, D-B17 | §7.I, §7.K, §11 | **Proposed AC** |
| BR-013 | §7.3 | D-B16 Open | §7.L | §18.6 |
| BR-014 | §7.3 | D-B04 | §16 Future | Tidak ada FR MVP |
| BR-015 | §7.3 | — | §16 Future | Tidak ada FR MVP |

### 14.4 Functional Requirements ke SRS dan AC
| Modul SRS | FR | Jumlah | AC |
|---|---|---:|---|
| §7.A Authentication | FR-AUTH-001, FR-AUTH-002, FR-AUTH-003 | 3 | §18.6 + **Proposed AC** |
| §7.B User Management | FR-USER-001, FR-USER-002, FR-USER-003 | 3 | §18.6 + **Proposed AC** |
| §7.C Master Data | FR-MD-001–FR-MD-006 | 6 | §18.1 + **Proposed AC** |
| §7.D Warehouse | FR-WH-001–FR-WH-003 | 3 | §18.1 + **Proposed AC** |
| §7.E Inbound | FR-IN-001–FR-IN-006 | 6 | §18.2, S A, S C, S E |
| §7.F Outbound | FR-OUT-001–FR-OUT-006 | 6 | §18.3, S A, S C |
| §7.G Transfer | FR-TRF-001–FR-TRF-005 | 5 | §18.4, S B, S E |
| §7.H Opname | FR-OPN-001–FR-OPN-006 | 6 | §18.5, S D |
| §7.I Saldo & Kartu | FR-STK-001–FR-STK-003 | 3 | **Proposed AC** |
| §7.J Dashboard | FR-DASH-001, FR-DASH-002 | 2 | **Proposed AC**; D-B13 |
| §7.K Reporting | FR-REP-001–FR-REP-004 | 4 | **Proposed AC**; export Deferred |
| §7.L Audit | FR-AUD-001–FR-AUD-004 | 4 | §18.2, §18.6, S E |
| **Total** |  | **51** |  |

### 14.5 Non-Functional Requirements
| NFR | SRS | Decision | Verification |
|---|---|---|---|
| NFR-SEC-001–005 | §10.1 | D-T05 Proposed untuk auth | §13.5 |
| NFR-INT-001–003 | §10.2 | D-T06 Proposed | §13.5, S E |
| NFR-REL-001 | §10.2 | D-T06 Proposed | §13.5, S E |
| NFR-PERF-001–002 | §10.3 | — | §13.5 |
| NFR-USE-001–002 | §10.4 | D-T03 Accepted | §13.5 |
| NFR-MNT-001–002 | §10.5 | D-T04 Proposed | §13.5 |
| NFR-COMP-001 | §10.6 | D-T02 Accepted, D-T07 Proposed | §13.5 |
| NFR-OPS-001 | §10.7 | D-T07 Proposed | §13.5 |
| NFR-LOG-001 | §10.8 | D-B16 Open | §13.5 |
| NFR-DOC-001 | §10.9 | D-T07, D-T08 Proposed | §13.5 |

### 14.6 Business Rules
RULE-001–RULE-028 dipetakan lengkap di §8. Ringkasan AC: §18.1 untuk RULE-001–003; KR §9.3 untuk RULE-004; §18.2/S C untuk RULE-007–009; §18.2–§18.3 untuk RULE-010/013/014; §18.4 untuk RULE-015–017; §18.5 untuk RULE-021/022; S E untuk RULE-018/027; **Proposed AC** untuk RULE-005/006/019/020/024–026/028; RULE-023 **Open D-B09**.

### 14.7 Reporting Requirements
| REP | SRS | FR | Status |
|---|---|---|---|
| REP-01 | §11 | FR-REP-002, FR-STK-001 | MVP, **Proposed AC** |
| REP-02 | §11 | FR-REP-002, FR-STK-002 | MVP, **Proposed AC** |
| REP-03 | §11 | FR-REP-001 | MVP, **Proposed AC** |
| REP-04 | §11 | FR-REP-001 | MVP, **Proposed AC** |
| REP-05 | §11 | FR-REP-001 | MVP, **Proposed AC** |
| REP-06 | §11 | FR-REP-003 | MVP, **Proposed AC** |
| REP-07 | §11 | FR-DASH-002 | MVP, **Proposed AC** |
| REP-08 | §11 | FR-AUD-001 | MVP, retensi **D-B16 Open**, export **D-B17 Deferred** |

### 14.8 Decision Log ke SRS
| Decision ID | Status BDL | SRS |
|---|---|---|
| D-B01 | Proposed | §3.5, §7.C, §9, §16 |
| D-B02 | Proposed | §7.C, §8.3–§8.4, §9 |
| D-B03 | Proposed | §7.E, §7.F, §8.3, §9 |
| D-B04 | Proposed | §7.D, §8.2, §9, §16 |
| D-B05 | Proposed | §7.C, §7.E, §8.4, §9 |
| D-B06 | Proposed | §7.C, §7.E, §9 |
| D-B07 | Proposed | §4, §7.F, §9 |
| D-B08 | Proposed | §7.L, §8.9, §12.8 |
| D-B09 | **Open** | §5.3, §7.H, §8.8, §15 |
| D-B10 | Proposed | §7.C, §7.J, §11, §14.7 |
| D-B11 | **Deferred** | §5.4, §16 |
| D-B12 | **Deferred** | §5.4, §16 |
| D-B13 | Proposed | §7.J, §14.2, §15 |
| D-B14 | Proposed | §8.9, §12.8, §15 |
| D-B15 | Proposed | §7.E, §7.F, §12.5, §15 |
| D-B16 | **Open** | §5.3, §7.L, §10.8, §12.7, §15 |
| D-B17 | **Deferred** | §5.4, §7.K, §11, §16 |
| D-T01 | Proposed | §5.2, §8.11, §15 |
| D-T02 | **Accepted** | §3.5, §5.1, §10.6 |
| D-T03 | **Accepted** | §3.5, §5.1, §6.1, §10.4 |
| D-T04 | Proposed | §5.2, §10.5, §15 |
| D-T05 | Proposed | §5.2, §7.A, §7.B, §10.1 |
| D-T06 | Proposed | §5.2, §7.E–§7.I, §7.L, §10.2 |
| D-T07 | Proposed | §5.2, §10.6–§10.9, §15 |
| D-T08 | Proposed | §5.2, §10.9, §15 |

Ringkasan status: Accepted 2 (D-T02, D-T03), Proposed 18, Open 2 (D-B09, D-B16), Deferred 3 (D-B11, D-B12, D-B17), Rejected 0.

### 14.9 Assumptions, Risks, dan Questions
| Kelompok | ID | SRS |
|---|---|---|
| Assumptions | ASM-01–ASM-10 | §3.7, §16; ASM-10 terkait D-B09 Open |
| Risks | RISK-01–RISK-10 | §3.6, §5.6, §12, §15, §17 |
| Open Questions | Q-01–Q-12 | §14.8, §15; Q-09 Open, Q-11/Q-12 Deferred |

---

## 15. UNRESOLVED REQUIREMENTS

Bagian ini mencatat seluruh item yang belum selesai menurut RTA §11, RTA §12, RTA §14, dan BDL. SRS tidak menyelesaikan keputusan secara sepihak.

### 15.1 Requirement gaps (12)
| Topik gap | Requirement terkait | Decision | Status |
|---|---|---|---|
| Opening Balance | RULE-019, RULE-020, ASM-06 | D-B14 | Proposed; FR detail belum ada |
| Cancel Draft | RULE-025, BRA §7.6 | D-B15 | Proposed; FR dan AC belum final |
| Double Posting Prevention | FR-IN-005, FR-OUT-005, RULE-024, RULE-027 | D-B15 | Proposed; AC §18.2 sebagian |
| Posted Reversal/Adjustment | FR-AUD-003, RULE-012, RULE-026 | D-B08 | Proposed; detail alur/AC belum ada |
| Stock Opname Location Protection | FR-OPN-001–006, RULE-023, ASM-10 | D-B09 | **Open** |
| Audit Log Retention | FR-AUD-001, REP-08, KPI-07 | D-B16 | **Open** |
| Backup and Restore | NFR-OPS-001, NFR-DOC-001 | D-T07 | Proposed |
| Requirement Acceptance Criteria | Seluruh FR, 19 NFR, 8 REP | D-B15, D-T08 | Belum disetujui |
| Requirement Detailed Specification | FR-DASH-001, FR-MD-004, RULE-005, RULE-009 | D-B05, D-B10, D-B13 | Proposed |
| Runtime Version List | NFR-COMP-001 | D-T02, D-T07 | D-T02 Accepted; D-T07 Proposed |
| Requirement ID Change Control | Q-01–Q-12 | D-T08 | Proposed |
| Implementation Gap | Seluruh 168 ID | D-T02–D-T06 | Repositori masih skeleton |

### 15.2 Requirement conflicts (13)
| Konflik | Decision | Status |
|---|---|---|
| FR-DASH-001 vs larangan penjumlahan satuan BRA §14.2 | D-B13 | Proposed |
| RULE-023/§7.5/ASM-10 vs status usulan AGENTS.md | D-B09 | **Open** |
| RULE-028 Asia/Jakarta vs `config/app.php` UTC | D-T01 | Proposed |
| BRA PHP 8.3+ vs `composer.json ^8.2` | D-T02 | **Accepted**, perubahan file belum |
| Blade+Bootstrap 5 vs scaffold Tailwind | D-T03 | **Accepted**, perubahan file belum |
| NFR-USE-002 responsive Bootstrap vs Tailwind scaffold | D-T03 | **Accepted** |
| `phpunit.xml` SQLite dinonaktifkan vs DB test terpisah | D-T04 | Proposed |
| FR-AUTH-003 vs skeleton tanpa middleware | D-T05 | Proposed |
| RULE-020/ASM-06 vs tidak ada FR opening balance | D-B14 | Proposed |
| RULE-025/§7.6 vs tidak ada FR cancel Draft | D-B15 | Proposed |
| §18.2 double posting vs tidak ada FR eksplisit | D-B15 | Proposed |
| BRA §11.5 data policies terbuka vs keputusan Proposed | D-B01–D-B08, D-B10 | Proposed |
| BDL Approved vs keputusan belum seluruhnya Accepted | — | Status tidak boleh dinaikkan |

### 15.3 Open questions
| ID | Pertanyaan | Status |
|---|---|---|
| Q-01 | Material number sebagai kode unik atau atribut terpisah | D-B01 Proposed |
| Q-02 | Semua barang memakai serial atau tidak | D-B02 Proposed |
| Q-03 | Serial unik global atau per item dan pabrikan | D-B03 Proposed |
| Q-04 | Kedalaman struktur lokasi | D-B04 Proposed |
| Q-05 | Kuantitas desimal atau bulat | D-B05 Proposed |
| Q-06 | Supplier wajib atau opsional | D-B06 Proposed |
| Q-07 | Approval outbound | D-B07 Proposed |
| Q-08 | Koreksi transaksi Posted | D-B08 Proposed |
| Q-09 | Proteksi lokasi saat opname | **D-B09 Open** |
| Q-10 | Minimum stock per item atau lokasi | D-B10 Proposed |
| Q-11 | Dukungan retur barang | **D-B11 Deferred** |
| Q-12 | Batch/lot/expiry tracking | **D-B12 Deferred** |

### 15.4 Pending decisions
- **Open:** D-B09, D-B16.
- **Proposed:** D-B01–D-B08, D-B10, D-B13–D-B15; D-T01, D-T04–D-T08.
- **Deferred:** D-B11, D-B12, D-B17.
- **Rejected:** tidak ada.

### 15.5 Proposed Acceptance Criteria yang menunggu persetujuan
- FR-AUTH-001, FR-AUTH-002, FR-USER-001; FR-MD-002, FR-MD-004, FR-MD-005, FR-MD-006; FR-WH-001.
- FR-IN-001 (cancel Draft), FR-IN-002, FR-IN-003, FR-IN-006; FR-OUT-002, FR-OUT-006.
- FR-STK-001–003; FR-DASH-001–002; seluruh FR-REP-001–004; FR-AUD-003.
- Seluruh REP-01–REP-08; NFR yang targetnya belum didefinisikan secara formal; AC detail untuk KPI-08.
- RULE-005, RULE-006, RULE-019, RULE-020, RULE-024, RULE-025, RULE-026, RULE-028.

---

## 16. SCOPE & ASSUMPTIONS

### 16.1 MVP Scope
Mengikuti BRA §5.1: login/logout dan pengelolaan pengguna dasar; role/permission dasar; master kategori dan satuan; master barang dengan kode/material number, nama, deskripsi, kategori, satuan, metode pelacakan, dan ambang minimum; satu gudang dan struktur lokasi; supplier referensi opsional; inbound; outbound; transfer antar lokasi dalam satu gudang; stock opname dan penyesuaian; saldo per barang/lokasi; kartu stok; dashboard ringkas dan laporan dasar; audit trail; data dummy/sintetis.

### 16.2 Future Scope
Mengikuti BRA §5.3: QR Code/barcode lokasi dan barang; approval workflow bertingkat; multi-warehouse dan transfer antar gudang; export Excel/PDF dengan format dapat dikonfigurasi; reorder suggestion; batch/lot tracking dan expiry date; integrasi procurement setelah kontrak antarmodul ditetapkan.

### 16.3 Deferred Features
| Fitur | ID | Alasan |
|---|---|---|
| Retur barang | D-B11, Q-11 | Di luar MVP |
| Batch/lot dan expiry | D-B12, Q-12 | Di luar MVP |
| Export Excel/PDF | D-B17, FR-REP-004 | Ditunda sampai laporan layar stabil |
| Bin/slot lokasi | D-B04 | Opsional dan future |
| Multi-warehouse operasional | BR-014, ASM-01 | Ekstensibilitas future |
| QR/barcode | BR-015, BRA §5.3 | Fitur lanjutan |
| Approval bertingkat | ASM-08 | MVP memakai Draft/Posted |
| Export audit report | REP-08, FR-REP-004 | Bergantung stabilitas laporan dan audit |

### 16.4 Approved Decisions
| ID | Keputusan |
|---|---|
| D-T02 | PHP minimum 8.3 |
| D-T03 | Frontend Blade + Bootstrap 5 |

### 16.5 Proposed Decisions
D-B01, D-B02, D-B03, D-B04, D-B05, D-B06, D-B07, D-B08, D-B10, D-B13, D-B14, D-B15; D-T01, D-T04, D-T05, D-T06, D-T07, D-T08.

### 16.6 Open Decisions
| ID | Topik |
|---|---|
| D-B09 | Proteksi lokasi saat sesi opname aktif; RULE-023 tidak boleh diimplementasikan sebelum disetujui |
| D-B16 | Retensi audit log dan transaksi |

### 16.7 Rejected Decisions
Tidak ada.

### 16.8 Project Assumptions
| ID | Asumsi | Catatan |
|---|---|---|
| ASM-01 | Prototype lokal satu gudang operasional | MVP |
| ASM-02 | Satu gudang memiliki banyak lokasi bertingkat | Terkait D-B04 Proposed |
| ASM-03 | Transaksi dibuat pengguna internal berrole | Terkait D-T05 Proposed |
| ASM-04 | Inbound/outbound manual, tanpa PO atau sistem lain | Sesuai BRA §5.2 |
| ASM-05 | Barang serialized dan non-serialized | Terkait D-B02 Proposed |
| ASM-06 | Opening balance lewat dokumen saldo awal | Terkait D-B14 Proposed |
| ASM-07 | Data demo dummy/sintetis | Wajib dipatuhi |
| ASM-08 | Draft dan Posted; approval bertingkat berikutnya | Terkait D-B07 Proposed |
| ASM-09 | Satu satuan dasar per item pada MVP | Terkait D-B05 Proposed |
| ASM-10 | Opname mengunci lokasi selama sesi aktif | **Terkait D-B09 Open — jangan diimplementasikan** |

---

## 17. CONCLUSION

SRS ini menyampaikan seluruh requirement BRA yang relevan ke dalam spesifikasi perilaku sistem untuk Laravel 12: 51 FR, 19 NFR, 28 RULE, dan 8 REP, dengan pelacakan ke 7 GOAL, 8 KPI, 15 BR, 168 ID BRA, 25 decision ID BDL, 12 gap, dan 13 konflik. Tidak ada requirement ID fiktif yang ditambahkan; usulan diberi penanda **usulan/Proposed**.

Dokumen ini **cukup sebagai dasar System Design** untuk modul-modul yang kebutuhannya sudah jelas, tetapi belum final secara keseluruhan karena:
- D-B09 dan D-B16 tetap **Open**;
- D-B11, D-B12, D-B17 tetap **Deferred**;
-18 keputusan tetap **Proposed**;
- hanya D-T02 dan D-T03 yang **Accepted**;
- sejumlah acceptance criteria masih **Proposed** dan perlu persetujuan.

Sebelum implementasi, pemilik proyek perlu menetapkan status final keputusan pada bagian 15, menyetujui FR dan AC tambahan untuk opening balance, cancel Draft, double posting, reversal/adjustment, dan detail laporan melalui change control sesuai D-T08, serta menggarisbawahi bahwa perubahan BRA, RTA, atau BDL tidak dilakukan dalam dokumen ini.

Setelah keputusan final tersedia, langkah berikutnya adalah menyusun Use Case Diagram, Activity Diagram, ERD, dan Data Dictionary sesuai BRA §25, lalu mengimplementasikan modul secara bertahap menurut BRA §22 dengan gerbang kualitas: tidak lanjut jika tes konsistensi stok modul sebelumnya belum lulus. Gunakan hanya data sintetis, jangan commit `.env`, dan jangan menjalankan perintah database sebelum lingkungan test disetujui.

**Akhir dokumen — SOFTWARE REQUIREMENTS SPECIFICATION v0.1 draft.**

---

**Validasi internal dokumen ini:**

| No | Pemeriksaan | Hasil |
|---|---|---|
| 1 | Seluruh 51 FR tercakup | Ya, §7.A–§7.L |
| 2 | Seluruh 19 NFR tercakup | Ya, §10 |
| 3 | Seluruh 28 RULE tercakup | Ya, §8 |
| 4 | Seluruh 8 REP tercakup | Ya, §11 |
| 5 | Seluruh 25 Decision ID dipetakan sesuai status | Ya, §5 dan §14.8 |
| 6 | Tidak ada requirement ID fiktif | Ya |
| 7 | Semua gap dan konflik tercatat | Ya, §15.1–§15.2 |
| 8 | Acceptance Criteria dapat diuji | Ya, §7, §13; yang belum ada ditandai Proposed |
| 9 | MVP dan Future Scope tidak tercampur | Ya, §7.K, §16 |
| 10 | Tidak ada perubahan source code atau konfigurasi | Ya |

