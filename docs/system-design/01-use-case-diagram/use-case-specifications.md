# USE CASE DIAGRAM ANALYSIS & DESIGN — SPECIFICATIONS

## Inventory Management System

| Atribut | Nilai |
|---|---|
| Versi | 0.1 |
| Status artefak | Draft yang telah direview dan disetujui untuk disimpan |
| Status desain | **Provisional** |
| Lokasi | `docs/system-design/01-use-case-diagram/use-case-specifications.md` |
| Berlaku untuk | 5 source diagram di direktori yang sama |
| Project Type | Personal project / prototype pembelajaran PKL |

**Pernyataan status proyek:** Dokumen ini mendokumentasikan rancangan Use Case untuk prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Proses bisnis yang dirujuk berasal dari BRA dan asumsi yang masih dapat ditinjau. Gunakan hanya data dummy/sintetis.

**Pernyataan identitas:** UC-ID (UC-01 … UC-34) adalah **ID artefak desain**, bukan Requirement ID resmi BRA. Tidak ada Requirement ID baru yang dibuat.

---

## 1. ACTOR ANALYSIS

| Actor Name | Actor Description | Responsibilities | Authorized Capabilities | Restricted Capabilities | Related Functional Requirements | Source References |
|---|---|---|---|---|---|---|
| **System Administrator** (alias：System Admin) | Mengelola akun, role, konfigurasi aplikasi, dan data referensi sistem. Tidak boleh mengubah histori transaksi Posted langsung. | Mengelola pengguna/role; menjaga data referensi; membaca master, saldo, laporan, dan audit log. | C/R/U/D pada pengguna dan role; R/U pada master barang/kategori/satuan dan warehouse/location; R pada saldo, kartu stok, laporan, dan audit log. | Tidak membuat Draft transaksi; tidak mem-post transaksi; tidak mengubah histori Posted; tidak mengubah saldo manual. | FR-AUTH-001–003, FR-USER-001/002/003, FR-MD-001–003 (R/U), FR-WH-001/002 (R/U), FR-STK-001–003 (R), FR-REP-001–003 (R), FR-AUD-001 (R) | BRA §6.2 A; BRA §12; SRS §4; RTA §13.5 |
| **Inventory Administrator** (alias：Inventory Admin) | Mengelola master data inventory, meninjau transaksi, menjalankan stock opname, dan melihat laporan. Dalam MVP dapat diberi kewenangan posting. | Mengelola master dan lokasi; membuat/meninjau Draft; mem-post transaksi; mengotorisasi penyesuaian opname; melihat laporan dan audit terbatas. | C/R/U/D master; C/R/U Draft inbound/outbound/transfer/opname; P posting transaksi dan penyesuaian opname; R saldo/kartu/laporan; R terbatas audit log. | Tidak mengubah histori Posted secara langsung; koreksi Posted wajib lewat reversal/adjustment; tidak melewati validasi backend. | FR-MD-001–006, FR-WH-001–003, FR-IN-001–006, FR-OUT-001–006, FR-TRF-001–005, FR-OPN-001–006, FR-STK-001–003, FR-REP-001–003, FR-AUD-001–003, FR-AUTH-003 | BRA §6.2 B; BRA §12; SRS §4; RTA §13.5 |
| **Warehouse Operator** | Membuat draft penerimaan, pengeluaran, transfer, dan mengisi hitung fisik opname. | Membuat dan mengisi Draft; memilih serial; mencatat counted qty; melihat master/lokasi/saldo/laporan terbatas. | C/R/U pada Draft inbound/outbound/transfer dan opname; R master dan lokasi; R saldo/kartu; R terbatas laporan. | **Posting tidak default** — hanya "sesuai permission" yang belum didefinisikan final; tidak mengotorisasi penyesuaian opname; tidak mengelola pengguna/role; tidak melihat audit log. | FR-IN-001–004/006, FR-OUT-001/002/004/006, FR-TRF-001–003, FR-OPN-001–004, FR-MD-001–003 (R), FR-WH-001–003 (R), FR-STK-001/002 (R) | BRA §6.2 C; BRA §12; SRS §4; RTA §13.5 |
| **Manager / Viewer** | Melihat dashboard, saldo, kartu stok, dan laporan tanpa membuat atau mengubah transaksi. | Memantau ringkasan operasional dan hasil laporan. | R pada master, lokasi, saldo, kartu stok, dashboard, dan laporan. | Tidak membuat/mengubah/mem-post transaksi; tidak mengelola master; tidak melihat audit log; tidak mengakses aksi kritis. | FR-AUTH-003, FR-MD-001–003 (R), FR-WH-001/002 (R), FR-STK-001–003 (R), FR-DASH-001/002, FR-REP-001–004 (R) | BRA §6.2 D; BRA §12; SRS §4; RTA §13.5 |

**Catatan konsistensi nama:**

- BRA §6.2 memakai **System Administrator** dan **Inventory Administrator**; BRA §12 memakai **System Admin** dan **Inventory Admin** dalam tabel yang sama → keduanya diperlakukan sebagai alias yang terbukti dari dokumen yang sama, bukan asumsi baru.
- **"Super Admin" tidak ditemukan** di BRA, RTA, BDL, SRS, maupun SRS Review. Nama ini **tidak dipakai** dalam desain. Jika diperlukan, harus diputuskan terlebih dahulu → lihat UD-01.
- **Manager/Viewer adalah satu kolom** di BRA §12 dan satu aktor di BRA §6.2 D. Tidak ada bukti pemisahan permission antara Manager dan Viewer → lihat UD-02.
- **Tidak ada aktor Supplier** dan **tidak ada sistem eksternal**: BRA §5.2 secara eksplisit mengeluarkan integrasi ERP/procurement/API. Supplier hanya data referensi opsional (FR-MD-006, D-B06 Proposed), bukan aktor interaktif.

---

## 2. ACTOR RESPONSIBILITY MATRIX

Berdasarkan BRA §12 (C=Create, R=Read, U=Update, P=Post/Approve, D=Delete Draft/Nonaktifkan):

| Modul / Aksi | UC Terkait | System Admin | Inventory Admin | Warehouse Operator | Manager/Viewer |
|---|---|---:|---:|---:|---:|
| Kelola pengguna dan role | UC-04, UC-05 | C/R/U/D | — | — | — |
| Lihat master data | UC-07–UC-13 | R | R | R | R |
| Kelola master barang/kategori/satuan | UC-07–UC-09 | R/U | C/R/U/D | — | — |
| Kelola warehouse/location | UC-12, UC-13 | R/U | C/R/U/D | R | R |
| Buat draft inbound/outbound/transfer | UC-15, UC-17, UC-19 | — | C/R/U | C/R/U | — |
| Post inbound/outbound/transfer | UC-16, UC-18, UC-20 | — | P | Sesuai permission; **default tidak** (UD-03) | — |
| Buat dan isi stock opname | UC-21, UC-22 | — | C/R/U | C/R/U | — |
| Otorisasi/post penyesuaian opname | UC-23 | — | P | — | — |
| Lihat saldo dan kartu stok | UC-24, UC-25, UC-26 | R | R | R | R |
| Lihat laporan | UC-29–UC-32 | R | R | R terbatas | R |
| Lihat audit log | UC-06, UC-33 | R | R terbatas | — | — |

**Prinsip BRA §12 yang dipertahankan:** pembuat dan pemosting sebaiknya berbeda; approval bertingkat adalah future (ASM-08). Matriks ini adalah **usulan awal prototype**, bukan SOP resmi.

**Catatan UD-03:** label `UD-03` muncul pada rancangan yang disetujui untuk menandai permission posting Warehouse Operator. Pemeriksaan terhadap BRA, RTA, dan BDL tidak menemukan `UD-03` sebagai Decision ID. Oleh karena itu `UD-03` diperlakukan sebagai **unresolved reference**, bukan Decision ID resmi. Hak posting Warehouse Operator tetap **belum final** dan tidak diubah dari matriks BRA §12.

---

## 3. USE CASE INVENTORY

| UC ID | Use Case Name | Modul | FR IDs | RULE Terkait | Roles | Decision IDs | Scope | Decision Status |
|---|---|---|---|---|---|---|---|---|
| UC-01 | Log In dengan akun terdaftar | Authentication | FR-AUTH-001 | RULE-011 | Semua | D-T05 | MVP | Proposed |
| UC-02 | Akhiri sesi (Log Out) | Authentication | FR-AUTH-002 | — | Semua | D-T05 | MVP | Proposed |
| UC-03 | Akses fungsi terproteksi | Authorization | FR-AUTH-003 | RULE-011 | Semua | D-T05 | MVP | Proposed |
| UC-04 | Kelola akun pengguna | User Mgmt | FR-USER-001 | RULE-011 | SA | D-T05 | MVP | Proposed |
| UC-05 | Tetapkan role/permission pengguna | User Mgmt | FR-USER-002 | RULE-011 | SA | D-T05 | MVP | Proposed |
| UC-06 | Tinjau metadata aksi kritis | User Mgmt | FR-USER-003 | RULE-018 | SA, IA | D-B16 | MVP | Open (retensi) |
| UC-07 | Kelola kategori barang | Master Data | FR-MD-001 | RULE-002 | IA, SA(R/U) | — | MVP | — |
| UC-08 | Kelola satuan barang | Master Data | FR-MD-002 | RULE-002, RULE-005 | IA, SA(R/U) | D-B05 | MVP | Proposed |
| UC-09 | Kelola master barang dan metode pelacakan | Master Data | FR-MD-003, FR-MD-004 | RULE-001, RULE-003, RULE-006–008 | IA, SA(R/U) | D-B01, D-B02, D-B03 | MVP | Proposed |
| UC-10 | Tetapkan ambang minimum stock | Master Data | FR-MD-005 | — | IA | D-B10 | MVP | Proposed |
| UC-11 | Kelola referensi supplier | Master Data | FR-MD-006 | RULE-003 | IA | D-B06 | MVP | Proposed |
| UC-12 | Kelola gudang | Warehouse | FR-WH-001 | RULE-002 | IA, SA(R/U) | D-B04 | MVP | Proposed |
| UC-13 | Kelola struktur lokasi | Warehouse | FR-WH-002 | RULE-002, RULE-003, RULE-016 | IA, SA(R/U), WO(R) | D-B04 | MVP | Proposed |
| UC-14 | Nonaktifkan master tanpa hapus histori | Warehouse | FR-WH-003 | RULE-003 | IA | — | MVP | — |
| UC-15 | Buat Draft inbound lengkap | Inbound | FR-IN-001–004, FR-IN-006 | RULE-004, 005, 007–010, 024, 025 | IA, WO | D-B03, D-B05, D-B06, D-B15 | MVP | Proposed |
| UC-16 | Post inbound | Inbound | FR-IN-005 (+FR-AUD-004) | RULE-013, 018, 024, 027 | IA | D-B15, D-T06 | MVP | Proposed |
| UC-17 | Buat Draft outbound lengkap | Outbound | FR-OUT-001, 002, 004, 006 | RULE-004, 007–010, 024, 025 | IA, WO | D-B03, D-B07, D-B15 | MVP | Proposed |
| UC-18 | Post outbound | Outbound | FR-OUT-003, 005 (+FR-AUD-004) | RULE-014, 018, 024, 027 | IA | D-B07, D-T06 | MVP | Proposed |
| UC-19 | Buat Draft transfer | Transfer | FR-TRF-001–003 | RULE-010, 014, 016, 024, 025 | IA, WO | D-B15 | MVP | Proposed |
| UC-20 | Post transfer | Transfer | FR-TRF-004, 005 (+FR-AUD-004) | RULE-015, 017, 018, 027 | IA | D-T06 | MVP | Proposed |
| UC-21 | Buka sesi opname dan rekam snapshot | Opname | FR-OPN-001, 002 | RULE-021, RULE-023 | IA, WO | D-B09 | MVP | **Open** |
| UC-22 | Catat hitung fisik dan selisih | Opname | FR-OPN-003, 004 | RULE-021 | IA, WO | D-B09 | MVP | **Open** |
| UC-23 | Post penyesuaian opname | Opname | FR-OPN-005, 006 (+FR-AUD-004) | RULE-018, 021, 022, 027 | IA | D-T06, D-B09 | MVP | **Open** |
| UC-24 | Lihat saldo stok | Balance & Card | FR-STK-001 | RULE-018 | Semua peran | D-B10 | MVP | Proposed |
| UC-25 | Lihat kartu stok | Balance & Card | FR-STK-002 | RULE-018 | Semua peran | — | MVP | — |
| UC-26 | Cari/filter transaksi | Balance & Card | FR-STK-003 | — | Semua peran | — | MVP | — |
| UC-27 | Lihat ringkasan dashboard | Dashboard | FR-DASH-001 | RULE-005 | Semua pembaca | D-B13 | MVP | Proposed |
| UC-28 | Lihat daftar barang di bawah minimum | Dashboard | FR-DASH-002 | — | IA, Manager/Viewer | D-B10 | MVP | Proposed |
| UC-29 | Hasilkan laporan inbound/outbound | Reporting | FR-REP-001 | RULE-018 | Semua (WO terbatas) | D-B17 (export hanya) | MVP | Proposed |
| UC-30 | Hasilkan laporan mutasi/saldo | Reporting | FR-REP-002 | RULE-018 | Semua peran | — | MVP | — |
| UC-31 | Hasilkan ringkasan opname | Reporting | FR-REP-003 | RULE-021, 022 | IA, Manager/Viewer | — | MVP | — |
| UC-32 | Ekspor laporan ke Excel/PDF | Reporting | FR-REP-004 | — | IA, Manager/Viewer | D-B17 | **Future — bukan MVP** | **Deferred** |
| UC-33 | Tinjau audit log | Audit Trail | FR-AUD-001 | RULE-018, 027 | SA, IA terbatas | D-B16 | MVP | Open (retensi) |
| UC-34 | Koreksi dokumen Posted | Audit Trail | FR-AUD-002, FR-AUD-003 | RULE-012, 026 | IA | D-B08 | MVP | Proposed |

**Cakupan FR:** 51/51 FR terpetakan. FR-AUD-004 tidak menjadi UC mandiri karena merupakan perilaku atomik lintas UC-16, UC-18, UC-20, dan UC-23; ia dicatat dalam traceability pada keempat UC tersebut.

---

## 4. USE CASE SPECIFICATION

### UC-01 — Log In dengan akun terdaftar
| Field | Isi |
|---|---|
| Use Case ID | UC-01 |
| Use Case Name | Log In dengan akun terdaftar |
| Description | Pengguna membuktikan identitas untuk memperoleh sesi aplikasi. |
| Primary Actor | Semua peran |
| Supporting Actor | — |
| Related FR | FR-AUTH-001 |
| Related RULE | RULE-011 |
| Related Decisions | D-T05 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Akun aktif tersedia dalam data dummy. |
| Trigger | Pengguna mengirim form login. |
| Main Success Scenario | Sistem memvalidasi kredensial → membuat sesi → mengarahkan ke halaman sesuai role. |
| Alternative Flow | Kredensial salah → pesan generik, sesi tidak dibuat. |
| Exception Flow | Akun nonaktif → login ditolak. |
| Postconditions | Sesi aktif; akses tercatat untuk audit. |

### UC-02 — Akhiri sesi
| Field | Isi |
|---|---|
| Use Case ID | UC-02 |
| Use Case Name | Akhiri sesi (Log Out) |
| Description | Pengguna mengakhiri sesi kerja. |
| Primary Actor | Semua peran |
| Supporting Actor | — |
| Related FR | FR-AUTH-002 |
| Related RULE | — |
| Related Decisions | D-T05 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Sesi aktif. |
| Trigger | Pengguna memilih Log Out. |
| Main Success Scenario | Sesi diakhiri → halaman terproteksi tidak lagi dapat diakses. |
| Alternative Flow | Sesi habis otomatis → arahkan ke login. |
| Exception Flow | Gagal mengakhiri sesi → sesi dibersihkan oleh mekanisme aplikasi. |
| Postconditions | Sesi tidak aktif. |

### UC-03 — Akses fungsi terproteksi
| Field | Isi |
|---|---|
| Use Case ID | UC-03 |
| Use Case Name | Akses fungsi terproteksi |
| Description | Sistem memvalidasi sesi dan permission di backend sebelum aksi dijalankan. |
| Primary Actor | Semua peran |
| Supporting Actor | — |
| Related FR | FR-AUTH-003 |
| Related RULE | RULE-011 |
| Related Decisions | D-T05 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Permintaan dikirim ke endpoint terproteksi. |
| Trigger | Pengguna mengakses URL atau aksi yang memerlukan izin. |
| Main Success Scenario | Backend memeriksa sesi dan permission → aksi diizinkan. |
| Alternative Flow | Peran dengan izin terbatas mendapat aksi baca-saja. |
| Exception Flow | Tanpa sesi → redirect login; tanpa permission → ditolak, saldo tidak berubah. |
| Postconditions | Hanya aksi berizin yang dijalankan. |

### UC-04 — Kelola akun pengguna
| Field | Isi |
|---|---|
| Use Case ID | UC-04 |
| Use Case Name | Kelola akun pengguna |
| Description | Administrator mengelola siklus akun aplikasi. |
| Primary Actor | System Administrator |
| Supporting Actor | — |
| Related FR | FR-USER-001 |
| Related RULE | RULE-011, RULE-018 |
| Related Decisions | D-T05 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Admin terautentikasi dan berpermission. |
| Trigger | Admin memilih kelola pengguna. |
| Main Success Scenario | Buat/ubah/nonaktifkan akun → simpan → catat aktor dan waktu. |
| Alternative Flow | Akun dinonaktifkan tetap tersedia dalam histori. |
| Exception Flow | Email duplikat atau permission kurang → ditolak. |
| Postconditions | Data akun berubah; aksi tercatat. |

### UC-05 — Tetapkan role/permission
| Field | Isi |
|---|---|
| Use Case ID | UC-05 |
| Use Case Name | Tetapkan role/permission pengguna |
| Description | Administrator menentukan hak akses sesuai matriks BRA §12. |
| Primary Actor | System Administrator |
| Supporting Actor | — |
| Related FR | FR-USER-002 |
| Related RULE | RULE-011 |
| Related Decisions | D-T05 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Akun dan role tersedia. |
| Trigger | Admin mengubah role pengguna. |
| Main Success Scenario | Pilih role → simpan → permission diterapkan di backend. |
| Alternative Flow | Role diubah tanpa mengubah histori transaksi. |
| Exception Flow | Role tidak dikenal atau permission kurang → ditolak. |
| Postconditions | Hak akses pengguna berubah. |

### UC-06 — Tinjau metadata aksi kritis
| Field | Isi |
|---|---|
| Use Case ID | UC-06 |
| Use Case Name | Tinjau metadata aksi kritis |
| Description | Admin melihat siapa dan kapan aksi kritis dilakukan. |
| Primary Actor | System Administrator |
| Supporting Actor | Inventory Administrator (terbatas) |
| Related FR | FR-USER-003 |
| Related RULE | RULE-018 |
| Related Decisions | D-B16 (Open) |
| Priority | Should |
| Scope | MVP |
| Preconditions | Aksi kritis telah tercatat. |
| Trigger | Admin membuka detail aktivitas. |
| Main Success Scenario | Sistem menampilkan aktor, aksi, objek, dan waktu. |
| Alternative Flow | IA melihat metadata terbatas sesuai BRA §12. |
| Exception Flow | Retensi belum final → jangkauan histori tidak ditetapkan SRS. |
| Postconditions | Metadata terbaca tanpa mengubah data. |

### UC-07 — Kelola kategori barang
| Field | Isi |
|---|---|
| Use Case ID | UC-07 |
| Use Case Name | Kelola kategori barang |
| Description | Admin mengelola klasifikasi barang. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | System Administrator (R/U) |
| Related FR | FR-MD-001 |
| Related RULE | RULE-002 |
| Related Decisions | — |
| Priority | Must |
| Scope | MVP |
| Preconditions | Admin berwenang. |
| Trigger | Admin mengelola kategori. |
| Main Success Scenario | Buat/ubah kategori dengan kode unik → simpan. |
| Alternative Flow | Kategori dinonaktifkan, histori tetap terbaca. |
| Exception Flow | Kode duplikat → ditolak. |
| Postconditions | Master kategori diperbarui. |

### UC-08 — Kelola satuan barang
| Field | Isi |
|---|---|
| Use Case ID | UC-08 |
| Use Case Name | Kelola satuan barang |
| Description | Admin mengelola satuan dan presisi terkait. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | System Administrator (R/U) |
| Related FR | FR-MD-002 |
| Related RULE | RULE-002, RULE-005 |
| Related Decisions | D-B05 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Admin berwenang. |
| Trigger | Admin mengelola satuan. |
| Main Success Scenario | Buat/ubah satuan dengan kode unik → simpan. |
| Alternative Flow | Presisi mengikuti kebijakan yang belum final. |
| Exception Flow | Kode duplikat → ditolak. |
| Postconditions | Master satuan diperbarui. |

### UC-09 — Kelola master barang dan metode pelacakan
| Field | Isi |
|---|---|
| Use Case ID | UC-09 |
| Use Case Name | Kelola master barang dan metode pelacakan |
| Description | Admin mengelola identitas barang serta menentukan tracking serialized/non-serialized. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | System Administrator (R/U) |
| Related FR | FR-MD-003, FR-MD-004 |
| Related RULE | RULE-001, RULE-003, RULE-006–009 |
| Related Decisions | D-B01, D-B02, D-B03 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Kategori dan satuan tersedia. |
| Trigger | Admin membuat/mengubah barang. |
| Main Success Scenario | Isi kode internal, material number, nama, kategori, satuan, tracking type → simpan. |
| Alternative Flow | Barang non-serialized dilacak per kuantitas. |
| Exception Flow | Kode duplikat atau perubahan tracking type setelah ada transaksi → ditolak/menunggu kebijakan tambahan. |
| Postconditions | Master barang dan tracking type tersimpan. |

### UC-10 — Tetapkan ambang minimum stock
| Field | Isi |
|---|---|
| Use Case ID | UC-10 |
| Use Case Name | Tetapkan ambang minimum stock |
| Description | Admin menetapkan nilai minimum untuk indicator low stock. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-MD-005 |
| Related RULE | — |
| Related Decisions | D-B10 (Proposed) |
| Priority | Should |
| Scope | MVP |
| Preconditions | Barang tersedia. |
| Trigger | Admin mengatur minimum. |
| Main Success Scenario | Tetapkan nilai minimum per item → simpan → dashboard membaca nilai. |
| Alternative Flow | Minimum per lokasi belum termasuk MVP. |
| Exception Flow | Nilai invalid → ditolak. |
| Postconditions | Ambang minimum tersimpan. |

### UC-11 — Kelola referensi supplier
| Field | Isi |
|---|---|
| Use Case ID | UC-11 |
| Use Case Name | Kelola referensi supplier |
| Description | Admin mengelola data supplier sebagai referensi opsional penerimaan. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-MD-006 |
| Related RULE | RULE-003 |
| Related Decisions | D-B06 (Proposed) |
| Priority | Could |
| Scope | MVP sebagai referensi opsional |
| Preconditions | Admin berwenang. |
| Trigger | Admin mengelola supplier. |
| Main Success Scenario | Buat/ubah kode, nama, kontak, status → simpan. |
| Alternative Flow | Inbound tanpa supplier tetap valid. |
| Exception Flow | Kode duplikat → ditolak. |
| Postconditions | Referensi supplier tersedia. |

### UC-12 — Kelola gudang
| Field | Isi |
|---|---|
| Use Case ID | UC-12 |
| Use Case Name | Kelola gudang |
| Description | Admin mengelola entitas gudang operasional. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | System Administrator (R/U) |
| Related FR | FR-WH-001 |
| Related RULE | RULE-002 |
| Related Decisions | D-B04 (Proposed) |
| Priority | Must |
| Scope | MVP — satu gudang operasional |
| Preconditions | Admin berwenang. |
| Trigger | Admin mengelola gudang. |
| Main Success Scenario | Buat/ubah gudang dengan kode unik → simpan. |
| Alternative Flow | Multi-warehouse operasional bukan MVP. |
| Exception Flow | Kode duplikat → ditolak. |
| Postconditions | Data gudang diperbarui. |

### UC-13 — Kelola struktur lokasi
| Field | Isi |
|---|---|
| Use Case ID | UC-13 |
| Use Case Name | Kelola struktur lokasi |
| Description | Admin menyusun hierarki Warehouse > Area > Rack > Level. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | System Administrator (R/U), Warehouse Operator (R) |
| Related FR | FR-WH-002 |
| Related RULE | RULE-002, RULE-003, RULE-016 |
| Related Decisions | D-B04 (Proposed) |
| Priority | Must |
| Scope | MVP (Bin future) |
| Preconditions | Gudang tersedia; parent valid. |
| Trigger | Admin membuat/mengubah lokasi. |
| Main Success Scenario | Isi kode, nama, tipe, parent → validasi hierarki → simpan. |
| Alternative Flow | Lokasi nonaktif tetap tampil di histori. |
| Exception Flow | Parent tidak ada atau kode duplikat → ditolak. |
| Postconditions | Struktur lokasi diperbarui. |

### UC-14 — Nonaktifkan master tanpa hapus histori
| Field | Isi |
|---|---|
| Use Case ID | UC-14 |
| Use Case Name | Nonaktifkan master tanpa hapus histori |
| Description | Admin menonaktifkan barang/lokasi agar tidak dipakai transaksi baru. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-WH-003 |
| Related RULE | RULE-003 |
| Related Decisions | — |
| Priority | Must |
| Scope | MVP |
| Preconditions | Master aktif dan berhistori. |
| Trigger | Admin menonaktifkan data. |
| Main Success Scenario | Ubah status为非aktif → transaksi baru menolak data → histori tetap terbaca. |
| Alternative Flow | Reaktivasi mengikuti kewenangan admin. |
| Exception Flow | Data dipakai Draft aktif → evaluasi lanjutan belum diputus. |
| Postconditions | Status nonaktif tersimpan; histori utuh. |

### UC-15 — Buat Draft inbound lengkap
| Field | Isi |
|---|---|
| Use Case ID | UC-15 |
| Use Case Name | Buat Draft inbound lengkap |
| Description | Pengguna membuat dokumen penerimaan berisi baris, kuantitas, serial, dan referensi sumber. |
| Primary Actor | Warehouse Operator |
| Supporting Actor | Inventory Administrator |
| Related FR | FR-IN-001, FR-IN-002, FR-IN-003, FR-IN-004, FR-IN-006 |
| Related RULE | RULE-004, 005, 007–010, 024, 025 |
| Related Decisions | D-B03, D-B05, D-B06, D-B15 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Barang dan lokasi aktif tersedia. |
| Trigger | Pengguna membuat penerimaan. |
| Main Success Scenario | Buat header bernomor unik → tambah baris → validasi qty > 0 → isi serial jika serialized → simpan Draft. |
| Alternative Flow | Supplier/reference opsional; Draft dapat direvisi. |
| Exception Flow | Serial mismatch/duplikat atau qty nol → ditolak; batal Draft belum punya AC final (D-B15). |
| Postconditions | Draft tersimpan; **saldo tidak berubah**. |

### UC-16 — Post inbound
| Field | Isi |
|---|---|
| Use Case ID | UC-16 |
| Use Case Name | Post inbound |
| Description | Admin berwenang mem-post penerimaan sehingga saldo bertambah dan movement tercatat. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-IN-005, FR-AUD-004 |
| Related RULE | RULE-011, 013, 018, 024, 027 |
| Related Decisions | D-B15, D-T06 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Draft lengkap dan valid; permission posting ada. |
| Trigger | Admin mem-post Draft. |
| Main Success Scenario | Validasi → ubah status Posted → tambah saldo lokasi tujuan → buat movement → catat pemosting dan waktu, semuanya atomik. |
| Alternative Flow | Posting ulang ditolak/tidak menggandakan saldo. |
| Exception Flow | Error sebelum movement selesai → rollback penuh. |
| Postconditions | Saldo bertambah satu kali; movement berreferensi ada. |

### UC-17 — Buat Draft outbound lengkap
| Field | Isi |
|---|---|
| Use Case ID | UC-17 |
| Use Case Name | Buat Draft outbound lengkap |
| Description | Pengguna membuat dokumen pengeluaran berisi baris, serial, dan informasi tujuan. |
| Primary Actor | Warehouse Operator |
| Supporting Actor | Inventory Administrator |
| Related FR | FR-OUT-001, FR-OUT-002, FR-OUT-004, FR-OUT-006 |
| Related RULE | RULE-004, 007–010, 024, 025 |
| Related Decisions | D-B03, D-B07, D-B15 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Barang dan lokasi asal aktif. |
| Trigger | Pengguna membuat pengeluaran. |
| Main Success Scenario | Buat header unik → tambah baris → pilih serial tersedia di lokasi asal → isi tujuan/penerima/alasan → simpan Draft. |
| Alternative Flow | Draft dapat direvisi sebelum posting. |
| Exception Flow | Serial tidak tersedia atau duplikat → ditolak; batal Draft belum final (D-B15). |
| Postconditions | Draft tersimpan; **saldo tidak berkurang**. |

### UC-18 — Post outbound
| Field | Isi |
|---|---|
| Use Case ID | UC-18 |
| Use Case Name | Post outbound |
| Description | Admin berwenang mem-post pengeluaran setelah memeriksa saldo. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-OUT-003, FR-OUT-005, FR-AUD-004 |
| Related RULE | RULE-011, 014, 018, 024, 027 |
| Related Decisions | D-B07, D-T06 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Draft valid; permission ada. |
| Trigger | Admin mem-post Draft. |
| Main Success Scenario | Cek saldo → status Posted → kurangi saldo asal → movement OUTBOUND → catat aktor/waktu secara atomik. |
| Alternative Flow | WO dapat mem-post hanya jika permission diberikan (UD-03). |
| Exception Flow | Saldo kurang → posting ditolak, saldo tetap; error lain → rollback. |
| Postconditions | Saldo berkurang; tidak ada stok negatif. |

### UC-19 — Buat Draft transfer
| Field | Isi |
|---|---|
| Use Case ID | UC-19 |
| Use Case Name | Buat Draft transfer |
| Description | Pengguna membuat perpindahan antar lokasi aktif yang berbeda. |
| Primary Actor | Warehouse Operator |
| Supporting Actor | Inventory Administrator |
| Related FR | FR-TRF-001, FR-TRF-002, FR-TRF-003 |
| Related RULE | RULE-010, 014, 016, 024, 025 |
| Related Decisions | D-B15 (Proposed) |
| Priority | Must |
| Scope | MVP — satu gudang |
| Preconditions | Lokasi asal dan tujuan aktif. |
| Trigger | Pengguna membuat transfer. |
| Main Success Scenario | Buat header unik → pilih asal/tujuan berbeda → validasi qty terhadap saldo asal → simpan Draft. |
| Alternative Flow | Draft dapat direvisi. |
| Exception Flow | Lokasi sama/nonaktif atau stok kurang → ditolak. |
| Postconditions | Draft tersimpan; saldo belum berubah. |

### UC-20 — Post transfer
| Field | Isi |
|---|---|
| Use Case ID | UC-20 |
| Use Case Name | Post transfer |
| Description | Admin mem-post transfer yang menghasilkan dua movement dalam satu transaksi. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-TRF-004, FR-TRF-005, FR-AUD-004 |
| Related RULE | RULE-015, 017, 018, 027 |
| Related Decisions | D-T06 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Draft valid; saldo asal cukup. |
| Trigger | Admin mem-post Draft. |
| Main Success Scenario | Kurangi asal → tambah tujuan → dua movement → status Posted → total gabungan tetap, atomik. |
| Alternative Flow | Transfer antar gudang bukan MVP. |
| Exception Flow | Salah satu sisi gagal → kedua movement dibatalkan. |
| Postconditions | Total stok gabungan tidak berubah. |

### UC-21 — Buka sesi opname dan rekam snapshot
| Field | Isi |
|---|---|
| Use Case ID | UC-21 |
| Use Case Name | Buka sesi opname dan rekam snapshot |
| Description | Pengguna memulai sesi pemeriksaan fisik dan sistem menyimpan snapshot stok sistem. |
| Primary Actor | Warehouse Operator |
| Supporting Actor | Inventory Administrator |
| Related FR | FR-OPN-001, FR-OPN-002 |
| Related RULE | RULE-021, RULE-023 |
| Related Decisions | D-B09 (**Open**) |
| Priority | Must |
| Scope | MVP — **tanpa location lock final** |
| Preconditions | Lokasi tersedia. |
| Trigger | Pengguna membuka sesi opname. |
| Main Success Scenario | Buat sesi berisi lokasi, tanggal, status → sistem menyimpan snapshot. |
| Alternative Flow | Snapshot tetap tersimpan meski transaksi lain terjadi (mekanisme konkurensi belum final). |
| Exception Flow | **Tidak ada use case final untuk penguncian lokasi** — D-B09 Open. |
| Postconditions | Sesi dan snapshot tercatat. |

### UC-22 — Catat hitung fisik dan selisih
| Field | Isi |
|---|---|
| Use Case ID | UC-22 |
| Use Case Name | Catat hitung fisik dan selisih |
| Description | Operator mengisi hasil hitung fisik dan sistem menghitung variance. |
| Primary Actor | Warehouse Operator |
| Supporting Actor | Inventory Administrator |
| Related FR | FR-OPN-003, FR-OPN-004 |
| Related RULE | RULE-021 |
| Related Decisions | D-B09 (Open) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Sesi aktif dengan snapshot. |
| Trigger | Operator mengisi counted qty. |
| Main Success Scenario | Simpan counted qty → hitung variance = fizik − snapshot → tampilkan hasil. |
| Alternative Flow | Draft session tetap tampil di laporan. |
| Exception Flow | Nilai invalid → ditolak. |
| Postconditions | Baris opname memuat snapshot, counted, variance. |

### UC-23 — Post penyesuaian opname
| Field | Isi |
|---|---|
| Use Case ID | UC-23 |
| Use Case Name | Post penyesuaian opname |
| Description | Admin mengotorisasi penyesuaian sehingga saldo mengikuti hasil fisik dan movement tercatat. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-OPN-005, FR-OPN-006, FR-AUD-004 |
| Related RULE | RULE-018, 021, 022, 027 |
| Related Decisions | D-T06 (Proposed), D-B09 (Open) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Variance dihitung; alasan diisi. |
| Trigger | Admin mem-post penyesuaian. |
| Main Success Scenario | Validasi alasan dan otorisasi → ubah saldo → movement OPNAME_ADJUSTMENT → catat aktor/waktu atomik. |
| Alternative Flow | Penyesuaian tanpa alasan ditolak. |
| Exception Flow | Error → rollback; lock lokasi tidak diterapkan sebelum D-B09 disetujui. |
| Postconditions | Saldo = hasil fisik; movement berreferensi ada. |

### UC-24 — Lihat saldo stok
| Field | Isi |
|---|---|
| Use Case ID | UC-24 |
| Use Case Name | Lihat saldo stok |
| Description | Pengguna melihat saldo per barang dan lokasi. |
| Primary Actor | Semua peran pembaca |
| Supporting Actor | — |
| Related FR | FR-STK-001 |
| Related RULE | RULE-018 |
| Related Decisions | D-B10 (Proposed) |
| Priority | Must |
| Scope | MVP |
| Preconditions | Saldo tersedia. |
| Trigger | Pengguna membuka halaman saldo. |
| Main Success Scenario | Terapkan filter barang/gudang/lokasi → tampilkan saldo dan satuan. |
| Alternative Flow | Filter tanpa hasil → pesan kosong. |
| Exception Flow | Data tidak berizin → ditolak backend. |
| Postconditions | Tidak ada perubahan data. |

### UC-25 — Lihat kartu stok
| Field | Isi |
|---|---|
| Use Case ID | UC-25 |
| Use Case Name | Lihat kartu stok |
| Description | Pengguna menelusuri riwayat pergerakan per barang. |
| Primary Actor | Semua peran pembaca |
| Supporting Actor | — |
| Related FR | FR-STK-002 |
| Related RULE | RULE-018 |
| Related Decisions | — |
| Priority | Must |
| Scope | MVP |
| Preconditions | Movement tersedia. |
| Trigger | Pengguna membuka kartu stok. |
| Main Success Scenario | Tampilkan tanggal, nomor dokumen, tipe, masuk/keluar, saldo berjalan, aktor. |
| Alternative Flow | Filter periode diterapkan. |
| Exception Flow | Inkonsistensi ledger ditandai untuk tinjauan. |
| Postconditions | Riwayat terbaca. |

### UC-26 — Cari/filter transaksi
| Field | Isi |
|---|---|
| Use Case ID | UC-26 |
| Use Case Name | Cari dan filter transaksi |
| Description | Pengguna mencari dokumen berdasarkan kriteria tertentu. |
| Primary Actor | Semua peran pembaca |
| Supporting Actor | — |
| Related FR | FR-STK-003 |
| Related RULE | — |
| Related Decisions | — |
| Priority | Must |
| Scope | MVP |
| Preconditions | Dokumen tersedia. |
| Trigger | Pengguna menerapkan filter. |
| Main Success Scenario | Filter tanggal/nomor/tipe/status → tampilkan hasil. |
| Alternative Flow | Kombinasi filter tanpa hasil → pesan kosong. |
| Exception Flow | Filter invalid → pesan validasi. |
| Postconditions | Daftar transaksi tersaring. |

### UC-27 — Lihat ringkasan dashboard
| Field | Isi |
|---|---|
| Use Case ID | UC-27 |
| Use Case Name | Lihat ringkasan dashboard |
| Description | Pengguna melihat ringkasan jumlah barang dan saldo per satuan. |
| Primary Actor | Semua peran pembaca |
| Supporting Actor | — |
| Related FR | FR-DASH-001 |
| Related RULE | RULE-005 |
| Related Decisions | D-B13 (Proposed) |
| Priority | Must |
| Scope | MVP — interpretasi provisional |
| Preconditions | Data saldo dan master tersedia. |
| Trigger | Pengguna membuka dashboard. |
| Main Success Scenario | Agregasi per satuan/SKU → tampilkan ringkasan. |
| Alternative Flow | Low stock dapat ditampilkan sebagai bagian dashboard (UC-28). |
| Exception Flow | **Dilarang menjumlahkan satuan berbeda** menjadi satu angka total unit. |
| Postconditions | Ringkasan per satuan tampil; resolusi akhir menunggu D-B13. |

### UC-28 — Lihat daftar barang di bawah minimum
| Field | Isi |
|---|---|
| Use Case ID | UC-28 |
| Use Case Name | Lihat daftar barang di bawah minimum |
| Description | Pengguna melihat item yang saldonya berada di bawah ambang minimum. |
| Primary Actor | Manager/Viewer |
| Supporting Actor | Inventory Administrator |
| Related FR | FR-DASH-002 |
| Related RULE | — |
| Related Decisions | D-B10 (Proposed) |
| Priority | Should |
| Scope | MVP |
| Preconditions | Minimum stock ditetapkan (UC-10). |
| Trigger | Pengguna membuka daftar low stock. |
| Main Success Scenario | Bandingkan saldo dengan minimum → tampilkan daftar. |
| Alternative Flow | Minimum per lokasi belum MVP. |
| Exception Flow | Minimum belum disetel → item tidak masuk daftar. |
| Postconditions | Daftar low stock tampil. |

### UC-29 — Hasilkan laporan inbound/outbound
| Field | Isi |
|---|---|
| Use Case ID | UC-29 |
| Use Case Name | Hasilkan laporan inbound/outbound |
| Description | Pengguna melihat transaksi penerimaan dan pengeluaran sesuai filter. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | WO terbatas, Manager/Viewer, System Administrator |
| Related FR | FR-REP-001 |
| Related RULE | RULE-018 |
| Related Decisions | D-B17 (hanya untuk export) |
| Priority | Must |
| Scope | MVP layar; export Future |
| Preconditions | Dokumen tersedia. |
| Trigger | Pengguna membuka laporan. |
| Main Success Scenario | Filter periode/status/item → tampilkan tabel sesuai REP-03/REP-04. |
| Alternative Flow | Tanpa data → pesan kosong. |
| Exception Flow | Error query dicatat tanpa data sensitif. |
| Postconditions | Data laporan konsisten dengan dokumen. |

### UC-30 — Hasilkan laporan mutasi/saldo
| Field | Isi |
|---|---|
| Use Case ID | UC-30 |
| Use Case Name | Hasilkan laporan mutasi dan saldo stok |
| Description | Pengguna melihat saldo dan mutasi yang konsisten dengan ledger. |
| Primary Actor | Semua peran pembaca |
| Supporting Actor | — |
| Related FR | FR-REP-002 |
| Related RULE | RULE-018 |
| Related Decisions | — |
| Priority | Must |
| Scope | MVP |
| Preconditions | Movement dan saldo tersedia. |
| Trigger | Pengguna membuka laporan. |
| Main Success Scenario | Agregasi ledger → tampilkan stock summary dan stock card. |
| Alternative Flow | Filter barang/lokasi/periode. |
| Exception Flow | Inkonsistensi ditandai. |
| Postconditions | Saldo laporan = penjumlahan delta. |

### UC-31 — Hasilkan ringkasan opname
| Field | Isi |
|---|---|
| Use Case ID | UC-31 |
| Use Case Name | Hasilkan ringkasan hasil stock opname |
| Description | Pengguna melihat snapshot, counted qty, variance, dan status sesi. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | Manager/Viewer |
| Related FR | FR-REP-003 |
| Related RULE | RULE-021, RULE-022 |
| Related Decisions | — |
| Priority | Should |
| Scope | MVP |
| Preconditions | Sesi opname tersedia. |
| Trigger | Pengguna membuka laporan opname. |
| Main Success Scenario | Query sesi dan baris → tampilkan ringkasan. |
| Alternative Flow | Sesi Draft tetap tampil. |
| Exception Flow | Error query dicatat. |
| Postconditions | Variance = counted − snapshot. |

### UC-32 — Ekspor laporan ke Excel/PDF
| Field | Isi |
|---|---|
| Use Case ID | UC-32 |
| Use Case Name | Ekspor laporan ke Excel/PDF |
| Description | Pengguna mengekspor laporan dalam format berkas. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | Manager/Viewer |
| Related FR | FR-REP-004 |
| Related RULE | — |
| Related Decisions | D-B17 (**Deferred**) |
| Priority | Could |
| Scope | **Future — bukan MVP** |
| Preconditions | Laporan layar stabil dan D-B17 tidak lagi Deferred. |
| Trigger | Permintaan export (tidak tersedia di MVP). |
| Main Success Scenario | Belum dispesifikasikan; hanya dapat dirancang setelah persetujuan. |
| Alternative Flow | — |
| Exception Flow | Permintaan export pada MVP ditolak/tidak disediakan. |
| Postconditions | Tidak ada perubahan pada MVP. |

### UC-33 — Tinjau audit log
| Field | Isi |
|---|---|
| Use Case ID | UC-33 |
| Use Case Name | Tinjau audit log |
| Description | Admin melihat jejak aktivitas kritis pada objek sistem. |
| Primary Actor | System Administrator |
| Supporting Actor | Inventory Administrator (terbatas) |
| Related FR | FR-AUD-001 |
| Related RULE | RULE-018, RULE-027 |
| Related Decisions | D-B16 (**Open**) |
| Priority | Should |
| Scope | MVP |
| Preconditions | Aktivitas kritis tercatat. |
| Trigger | Admin membuka audit log. |
| Main Success Scenario | Filter aktor/aksi/periode → tampilkan aktor, aksi, objek, waktu, referensi. |
| Alternative Flow | IA melihat data terbatas sesuai BRA §12. |
| Exception Flow | Periode retensi belum diputus → tidak ada angka final. |
| Postconditions | Audit terbaca tanpa perubahan. |

### UC-34 — Koreksi dokumen Posted
| Field | Isi |
|---|---|
| Use Case ID | UC-34 |
| Use Case Name | Koreksi dokumen Posted |
| Description | Admin melakukan koreksi transaksi yang sudah Posted tanpa mengubah dokumen asal. |
| Primary Actor | Inventory Administrator |
| Supporting Actor | — |
| Related FR | FR-AUD-002, FR-AUD-003 |
| Related RULE | RULE-012, RULE-026 |
| Related Decisions | D-B08 (Proposed) |
| Priority | Should |
| Scope | MVP dengan prinsip immutable; detail alur provisional |
| Preconditions | Dokumen Posted tersedia; alasan koreksi disiapkan. |
| Trigger | Admin mengajukan koreksi. |
| Main Success Scenario | Buat reversal/adjustment → referensikan dokumen awal → isi alasan → movement pembalik tercatat. |
| Alternative Flow | Koreksi dapat berupa adjustment sesuai keputusan D-B08. |
| Exception Flow | Edit/hapus langsung dokumen Posted ditolak. |
| Postconditions | Dokumen awal tidak berubah; koreksi dapat ditelusuri. |

---

## 5. USE CASE RELATIONSHIP ANALYSIS

| No | Dari → Ke | Jenis | Justifikasi | Status |
|---:|---|---|---|---|
| 1 | UC-04, UC-05, UC-06 → UC-03 | `<<include>>` | Perilaku wajib: setiap aksi administrasi harus lolos validasi backend (RULE-011). | Stabil |
| 2 | UC-15/17/19 → UC-03 | `<<include>>` | Pembuatan Draft hanya untuk peran berizin (BRA §12). | Stabil |
| 3 | UC-16/18/20/23 → UC-03 | `<<include>>` | Posting wajib diverifikasi permission di backend. | Stabil |
| 4 | UC-29/30/31/33 → UC-03 | `<<include>>` | Akses laporan dan audit dibatasi per role. | Stabil |
| 5 | UC-16/18/20/23 → FR-AUD-004 | Bukan UC terpisah | Atomicitas adalah perilaku lintas UC, bukan tujuan pengguna; dicatat dalam traceability. | Stabil |
| 6 | UC-32 → UC-29/30/31 | `<<extend>>` | Export adalah perilaku tambahan kondisional pada laporan layar. | **Pending — D-B17 Deferred, bukan MVP** |
| 7 | UC-34 → UC-16/18/20/23 | `<<extend>>` | Koreksi Posted hanya terjadi kondisional setelah posting berhasil. | **Pending — D-B08 Proposed** |
| 8 | UC-14 → UC-09 / UC-13 | `<<extend>>` | Nonaktifitas master adalah aksi lanjutan kondisional pada pengelolaan master/lokasi. | Stabil |
| 9 | UC-28 → UC-27 | Asosiasi tampilan (bukan include) | Low stock merupakan informasi dashboard, tetapi bukan perilaku bersama yang dipaksakan; keduanya use case tersendiri. | Stabil |
| 10 | Generalization antaraktor | **Tidak digunakan** | BRA §12 tidak menunjukkan hierarki role; "Super Admin" tidak bersumber. | Lihat UD-01 |
| 11 | Location lock UC | **Tidak dibuat** | D-B09 Open; RULE-023 tidak boleh diimplementasikan sebagai UC final. | Pending |
| 12 | Retention period UC | **Tidak dibuat** | D-B16 Open. | Pending |

**Aturan yang dipegang:** `include` hanya untuk validasi akses yang memang wajib dan digunakan bersama; `extend` hanya untuk perilaku kondisional; tidak ada relationship dekoratif.

---

## 6. REQUIREMENT TRACEABILITY MATRIX

UC-ID di sini adalah artefak desain; FR/RULE/Decision-ID tetap mengikuti BRA/RTA/BDL.

| FR ID | Use Case | RULE | Decision ID | Status | Scope |
|---|---|---|---|---|---|
| FR-AUTH-001 | UC-01 | RULE-011 | D-T05 | Proposed | MVP |
| FR-AUTH-002 | UC-02 | — | D-T05 | Proposed | MVP |
| FR-AUTH-003 | UC-03 | RULE-011 | D-T05 | Proposed | MVP |
| FR-USER-001 | UC-04 | RULE-011, 018 | D-T05 | Proposed | MVP |
| FR-USER-002 | UC-05 | RULE-011 | D-T05 | Proposed | MVP |
| FR-USER-003 | UC-06 | RULE-018 | D-B16 | Open | MVP |
| FR-MD-001 | UC-07 | RULE-002 | — | — | MVP |
| FR-MD-002 | UC-08 | RULE-002, 005 | D-B05 | Proposed | MVP |
| FR-MD-003 | UC-09 | RULE-001, 003 | D-B01 | Proposed | MVP |
| FR-MD-004 | UC-09 | RULE-006–008 | D-B02, D-B03 | Proposed | MVP |
| FR-MD-005 | UC-10 | — | D-B10 | Proposed | MVP |
| FR-MD-006 | UC-11 | RULE-003 | D-B06 | Proposed | MVP |
| FR-WH-001 | UC-12 | RULE-002 | D-B04 | Proposed | MVP |
| FR-WH-002 | UC-13 | RULE-002, 003, 016 | D-B04 | Proposed | MVP |
| FR-WH-003 | UC-14 | RULE-003 | — | — | MVP |
| FR-IN-001 | UC-15 | RULE-010, 024, 025 | D-B15 | Proposed | MVP |
| FR-IN-002 | UC-15 | RULE-004 | — | — | MVP |
| FR-IN-003 | UC-15 | RULE-004, 005 | D-B05 | Proposed | MVP |
| FR-IN-004 | UC-15 | RULE-008, 009 | D-B03 | Proposed | MVP |
| FR-IN-005 | UC-16 | RULE-013, 018, 027 | D-B15, D-T06 | Proposed | MVP |
| FR-IN-006 | UC-15 | — | D-B06 | Proposed | MVP |
| FR-OUT-001 | UC-17 | RULE-010, 024, 025 | D-B07, D-B15 | Proposed | MVP |
| FR-OUT-002 | UC-17 | RULE-004 | — | — | MVP |
| FR-OUT-003 | UC-18 | RULE-014 | D-T06 | Proposed | MVP |
| FR-OUT-004 | UC-17 | RULE-007, 008 | D-B03 | Proposed | MVP |
| FR-OUT-005 | UC-18 | RULE-014, 018, 027 | D-B07, D-T06 | Proposed | MVP |
| FR-OUT-006 | UC-17 | — | — | — | MVP |
| FR-TRF-001 | UC-19 | RULE-010, 024, 025 | D-B15 | Proposed | MVP |
| FR-TRF-002 | UC-19 | RULE-016 | — | — | MVP |
| FR-TRF-003 | UC-19 | RULE-014, 016 | — | — | MVP |
| FR-TRF-004 | UC-20 | RULE-015, 027 | D-T06 | Proposed | MVP |
| FR-TRF-005 | UC-20 | RULE-017 | — | — | MVP |
| FR-OPN-001 | UC-21 | RULE-021, 023 | D-B09 | **Open** | MVP |
| FR-OPN-002 | UC-21 | RULE-021, 023 | D-B09 | **Open** | MVP |
| FR-OPN-003 | UC-22 | RULE-021 | — | — | MVP |
| FR-OPN-004 | UC-22 | RULE-021 | — | — | MVP |
| FR-OPN-005 | UC-23 | RULE-022 | — | — | MVP |
| FR-OPN-006 | UC-23 | RULE-022, 027 | D-T06 | Proposed | MVP |
| FR-STK-001 | UC-24 | RULE-018 | D-B10 | Proposed | MVP |
| FR-STK-002 | UC-25 | RULE-018 | — | — | MVP |
| FR-STK-003 | UC-26 | — | — | — | MVP |
| FR-DASH-001 | UC-27 | RULE-005 | D-B13 | Proposed | MVP |
| FR-DASH-002 | UC-28 | — | D-B10 | Proposed | MVP |
| FR-REP-001 | UC-29 | RULE-018 | D-B17 (export) | Deferred untuk export | MVP layar |
| FR-REP-002 | UC-30 | RULE-018 | — | — | MVP |
| FR-REP-003 | UC-31 | RULE-021, 022 | — | — | MVP |
| FR-REP-004 | UC-32 | — | D-B17 | **Deferred** | **Future** |
| FR-AUD-001 | UC-33 | RULE-018, 027 | D-B16 | Open | MVP |
| FR-AUD-002 | UC-34 | RULE-012 | D-B08 | Proposed | MVP |
| FR-AUD-003 | UC-34 | RULE-026 | D-B08 | Proposed | MVP |
| FR-AUD-004 | UC-16, UC-18, UC-20, UC-23 | RULE-027 | D-T06 | Proposed | MVP |

**Total:** 51/51 FR terpetakan; 34 UC; 0 requirement ID baru.

---

## 7. BUSINESS RULES MAPPING

Pemetaan ini merangkum aturan bisnis BRA yang muncul pada inventaris, spesifikasi, dan analisis relasi di atas. Tidak ada aturan baru yang ditambahkan.

| RULE | Ringkasan aturan | Use Case / Perlakuan desain | Status / Catatan |
|---|---|---|---|
| RULE-001 | Kode barang unik. | UC-09 | Proposed via D-B01 untuk pemisahan `item_code` dan `material_number`. |
| RULE-002 | Kode kategori, satuan, gudang, dan lokasi unik dalam ruang lingkupnya. | UC-07, UC-08, UC-12, UC-13 | Dipertahankan pada master data. |
| RULE-003 | Barang/lokasi nonaktif tidak dipakai transaksi baru. | UC-09, UC-11, UC-13, UC-14 | Dipertahankan; histori tetap terbaca. |
| RULE-004 | Quantity transaksi lebih besar dari nol. | UC-15, UC-17 | Validasi draft. |
| RULE-005 | Quantity memakai tipe presisi tetap, bukan floating point. | UC-08, UC-15, UC-27 | D-B05 Proposed; dashboard tidak menjumlahkan satuan berbeda. |
| RULE-006 | Barang non-serialized dilacak per kuantitas. | UC-09 | D-B02 Proposed. |
| RULE-007 | Barang serialized per unit serial; satu serial hanya satu status/lokasi aktif. | UC-09, UC-15, UC-17 | D-B03 Proposed untuk unique global di MVP. |
| RULE-008 | Jumlah serial sama dengan kuantitas bulat untuk serialized. | UC-09, UC-15, UC-17 | D-B05 Proposed. |
| RULE-009 | Serial tidak boleh didaftarkan dua kali untuk unit yang sama. | UC-09, UC-15, UC-17 | D-B03 Proposed. |
| RULE-010 | Draft tidak mengubah saldo. | UC-15, UC-17, UC-19 | Stabil; postcondition eksplisit. |
| RULE-011 | Posting hanya oleh pengguna berpermission. | UC-01, UC-03, UC-04, UC-05, UC-16, UC-18, UC-20 | D-T05 Proposed untuk implementasi backend. |
| RULE-012 | Dokumen Posted tidak diubah/dihapus langsung. | UC-34 | D-B08 Proposed. |
| RULE-013 | Inbound menambah saldo lokasi tujuan saat posting. | UC-16 | Atomik dengan movement. |
| RULE-014 | Outbound mengurangi saldo asal dan tidak boleh negative stock. | UC-18, UC-19 | Stabil; posting ditolak saat saldo kurang. |
| RULE-015 | Transfer mengurangi asal dan menambah tujuan dalam satu transaksi. | UC-20 | D-T06 Proposed untuk strategi transaksi/lock. |
| RULE-016 | Lokasi asal dan tujuan transfer berbeda dan aktif. | UC-13, UC-19 | Stabil. |
| RULE-017 | Total stok gabungan tidak berubah akibat transfer internal. | UC-20 | Stabil. |
| RULE-018 | Setiap perubahan saldo menghasilkan stock movement berreferensi dokumen. | UC-04, UC-06, UC-16, UC-18, UC-20, UC-23, UC-24, UC-25, UC-29, UC-30, UC-33 | Stabil; FR-AUD-004 menjadi perilaku lintas UC. |
| RULE-019 | Saldo tidak diedit langsung lewat form master barang. | Tidak ada UC khusus | D-B14 Proposed; detail FR/AC belum ada. Lihat UD-09. |
| RULE-020 | Opening balance wajib lewat dokumen/penyesuaian awal, bukan angka tanpa jejak. | Tidak ada UC khusus | D-B14 Proposed; tidak dibuat UC baru. Lihat UD-09. |
| RULE-021 | Selisih opname = hasil fisik − snapshot sistem. | UC-21, UC-22, UC-23, UC-31 | Stabil. |
| RULE-022 | Penyesuaian opname memerlukan alasan, pelaku, waktu, dan otorisasi. | UC-23, UC-31 | Stabil; otorisasi IA. |
| RULE-023 | Lock lokasi selama sesi opname aktif. | UC-21/UC-22 hanya merekam snapshot | **Open — D-B09**; tidak ada Use Case final location lock. |
| RULE-024 | Nomor dokumen unik dan tidak reused. | UC-15, UC-16, UC-17, UC-18, UC-19 | Stabil. |
| RULE-025 | Batal Draft tidak menghasilkan stock movement. | UC-15, UC-17, UC-19 | D-B15 Proposed; tidak ada UC final cancel Draft. |
| RULE-026 | Koreksi Posted via reversal/adjustment yang merujuk dokumen awal. | UC-34 | D-B08 Proposed. |
| RULE-027 | Saldo dan status dokumen berhasil/gagal bersama-sama. | UC-16, UC-18, UC-20, UC-23, UC-33 | D-T06 Proposed; FR-AUD-004 dicatat pada empat UC posting. |
| RULE-028 | Waktu transaksi konsisten dengan zona aplikasi. | UC-16, UC-18, UC-20, UC-23 | D-T01 Proposed; zona final belum ditetapkan. |

---

## 8. DECISION STATUS MAPPING

Status berikut mengikuti BDL dan tidak diubah oleh artefak desain ini.

| Decision ID | Topik | Status | Dampak pada Use Case |
|---|---|---|---|
| D-B01 | Pemisahan `item_code` dan `material_number` | Proposed | UC-09 |
| D-B02 | Tracking type per barang | Proposed | UC-09 |
| D-B03 | Serial unik global untuk MVP | Proposed | UC-09, UC-15, UC-17 |
| D-B04 | Hierarki lokasi Warehouse > Area > Rack > Level | Proposed | UC-12, UC-13 |
| D-B05 | Presisi quantity dan serialized integer | Proposed | UC-08, UC-15 |
| D-B06 | Supplier opsional pada inbound | Proposed | UC-11, UC-15 |
| D-B07 | Draft/Posted outbound dengan permission | Proposed | UC-17, UC-18 |
| D-B08 | Koreksi Posted via reversal/adjustment | Proposed | UC-34 |
| D-B09 | Lock lokasi saat opname aktif | **Open** | UC-21–UC-23 provisional; tidak ada UC location lock final. |
| D-B10 | Minimum stock per item | Proposed | UC-10, UC-24, UC-28 |
| D-B11 | Retur barang | **Deferred** | Tidak ada UC return; bukan MVP. |
| D-B12 | Batch / expiry tracking | **Deferred** | Tidak ada UC batch/expiry; bukan MVP. |
| D-B13 | Interpretasi dashboard | Proposed | UC-27; dilarang menjumlahkan satuan berbeda. |
| D-B14 | Opening balance | Proposed | Tidak ada UC opening balance baru. |
| D-B15 | Cancel Draft dan double posting | Proposed | Guard posting pada UC-16/18/20; tidak ada UC final cancel Draft. |
| D-B16 | Retensi audit log dan transaksi | **Open** | UC-06, UC-33; tidak ada angka retensi final. |
| D-B17 | Export Excel/PDF | **Deferred** | UC-32 Future Scope, bukan MVP. |
| D-T01 | Timezone Asia/Jakarta | Proposed | UC-16/18/20/23 mencatat waktu; zona final menunggu keputusan. |
| D-T02 | Minimum PHP 8.3 | **Accepted** | Tidak mengubah desain Use Case. |
| D-T03 | Frontend Blade + Bootstrap 5 | **Accepted** | Tidak mengubah desain Use Case. |
| D-T04 | Database terpisah untuk testing | Proposed | Tidak mengubah desain Use Case. |
| D-T05 | Middleware auth plus Policy/Gate | Proposed | UC-03 sebagai include pada aksi terproteksi. |
| D-T06 | Transaction plus validasi/lock saldo | Proposed | UC-16/18/20/23 atomic; FR-AUD-004. |
| D-T07 | Dokumentasi backup dan versi runtime | Proposed | Tidak mengubah desain Use Case. |
| D-T08 | Penambahan ID requirement baru butuh persetujuan | Proposed | UC-ID tetap artefak desain, bukan Requirement ID. |

**Rekap status:** 18 Proposed, 2 Open, 3 Deferred, 2 Accepted. Total 25 Decision ID.

---

## 9. UNRESOLVED DESIGN DECISIONS

| ID | Topik | Sumber | Status | Dampak pada Use Case Design |
|---|---|---|---|---|
| UD-01 | Nama "Super Admin" | Tidak ada di BRA/RTA/BDL/SRS | Belum dibuktikan | Tidak dipakai; konfirmasi sebelum menambah aktor |
| UD-02 | Pemisahan Manager vs Viewer | BRA §12 satu kolom; BRA §6.2 D satu aktor | Belum diputus | UC-24–UC-31 memakai satu aktor Manager/Viewer |
| UD-03 | Permission posting Warehouse Operator | BRA §12: "Sesuai permission; default tidak". Label `UD-03` **tidak ditemukan sebagai Decision ID** di BRA, RTA, atau BDL. | **Unresolved reference — bukan Decision ID resmi** | UC-18/UC-20 provisional untuk WO; permission tidak diasumsikan final; matriks BRA §12 tidak diubah. |
| UD-04 | D-B09 — location lock saat opname | BRA §10 RULE-023; ASM-10; AGENTS.md | **Open** | Tidak ada UC final location lock; UC-21–UC-23 provisional |
| UD-05 | D-B16 — retensi audit log | BRA §11.5; BDL §9 | **Open** | UC-06/UC-33 tidak menetapkan masa retensi |
| UD-06 | D-B13 — interpretasi dashboard | BRA §9.7 vs §14.2 | Proposed | UC-27 hanya ringkasan per satuan; tidak menjumlahkan satuan berbeda |
| UD-07 | D-B17 — export Excel/PDF | BDL §8 | **Deferred** | UC-32 bukan MVP; `<<extend>>` bersifat kondisional |
| UD-08 | D-B08 — detail reversal/adjustment | BDL §4 | Proposed | UC-34 provisional pada alur koreksi |
| UD-09 | D-B14 — FR opening balance | RTA §11; BDL §9 | Proposed, FR belum ada | Tidak dibuat UC opening balance |
| UD-10 | D-B15 — cancel Draft & double posting | RTA §11; BDL §9 | Proposed, AC belum final | Tidak dibuat UC final cancel Draft; guard posting masih dalam UC-16/18/20 |
| UD-11 | D-T01 — timezone Asia/Jakarta | BRA RULE-028 vs config UTC | Proposed | UC-16/18/20/23 mencatat waktu, tapi zona final belum ditetapkan |
| UD-12 | AUD-01–AUD-11 | SRS Review | Menunggu review/persetujuan | Rekomendasi audit **belum diterapkan**; desain bersifat provisional |
| UD-13 | D-T05 — middleware/Policy/Gate | BDL §5 | Proposed | UC-03 menjadi include, tetapi implementasi belum final |
| UD-14 | D-T06 — transaction + lock saldo | BDL §5 | Proposed | Atomicitas pada UC-16/18/20/23 masih rancangan |

**Catatan verifikasi UD-03:** pencarian eksplisit pada `docs/01-business-requirements-analysis.md`, `docs/02-requirements-traceability-analysis.md`, dan `docs/03-business-decision-log.md` tidak menemukan `UD-03` sebagai Decision ID. Kemunculan `AUD-03` pada dokumen audit bukan merupakan `UD-03`. Oleh karena itu, `UD-03` dipertahankan hanya sebagai unresolved reference dan tidak menambah jumlah Decision ID resmi.

---

## 10. FINAL REVIEW CHECKLIST

- [x] 4 aktor diidentifikasi dari BRA §6.2 dan BRA §12; "Super Admin" tidak dipakai karena tidak bersumber.
- [x] Supplier dan sistem eksternal **tidak** dijadikan aktor; BRA §5.2 mengeluarkan integrasi.
- [x] Manager / Viewer dipertahankan sebagai satu kelompok aktor.
- [x] 12 modul FR tercakup dalam 34 use case.
- [x] Seluruh **51 FR** terpetakan ke UC; tidak ada requirement ID baru.
- [x] 28 RULE dirujuk pada UC yang relevan; RULE-023 tidak dijadikan UC final.
- [x] 25 Decision ID tidak diubah statusnya; 2 Open, 18 Proposed, 3 Deferred, 2 Accepted dipertahankan.
- [x] UC-ID dinyatakan sebagai artefak desain, bukan Requirement ID.
- [x] Include hanya untuk validasi wajib/bersama; extend hanya untuk perilaku kondisional.
- [x] Generalization tidak dipakai karena tidak ada bukti hierarki role.
- [x] System boundary, aktor di luar boundary, dan use case di dalam boundary hadir di kelima diagram.
- [x] D-B09, D-B16, D-B13, D-B17 dipertahankan sesuai statusnya.
- [x] Fitur Deferred (UC-32) tidak masuk MVP.
- [x] FR-AUD-004 tetap cross-cutting pada UC-16, UC-18, UC-20, dan UC-23.
- [x] `UD-03` dicatat sebagai unresolved reference, bukan Decision ID resmi.
- [x] Business Rules Mapping dan Decision Status Mapping tercantum.
- [x] AUD-01–AUD-11 diperlakukan sebagai kondisi review, bukan perubahan yang sudah diterapkan.
- [x] Desain hanya provisional untuk modul yang stabil.
- [x] Penyimpanan artefak telah disetujui; Activity Diagram belum dimulai.

---

**Batasan yang dipatuhi:** tidak ada source code Laravel, migration, tabel database, ERD, perubahan BRA/RTA/BDL/SRS/SRS Review/AGENTS.md, konfigurasi, package, atau perintah database. Hanya tujuh artefak Use Case yang dibuat di `docs/system-design/01-use-case-diagram/`.
