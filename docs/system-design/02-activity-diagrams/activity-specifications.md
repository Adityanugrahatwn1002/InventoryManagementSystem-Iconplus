# ACTIVITY DIAGRAM ANALYSIS & DESIGN — SPECIFICATIONS

## Inventory Management System

| Atribut | Nilai |
|---|---|
| Versi | 0.1 |
| Status artefak | Draft yang telah direview dan disetujui untuk disimpan |
| Status desain | **Provisional** untuk alur yang dipengaruhi keputusan Proposed/Open |
| Lokasi | `docs/system-design/02-activity-diagrams/activity-specifications.md` |
| Source diagram | 5 file `.puml` dalam direktori yang sama |
| Berlaku untuk | Proses bisnis yang telah disetujui pada tahap Use Case Diagram |

**Pernyataan batasan:** Dokumen ini adalah artefak desain untuk prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Proses bisnis yang dirujuk berasal dari BRA dan asumsi yang masih dapat ditinjau. Gunakan hanya data dummy/sintetis.

**AD-ID adalah ID artefak desain, bukan Requirement ID resmi BRA.** Tidak ada Requirement ID baru yang dibuat.

---

## 1. DOCUMENT INFORMATION

| Field | Isi |
|---|---|
| Document Title | Activity Diagram Analysis & Design — Specifications |
| Document ID | Akan ditetapkan setelah dokumen disetujui final |
| Version | 0.1 — Draft for Review, disetujui untuk disimpan |
| Owner | Pengembang proyek / pemilik proyek |
| Project | Inventory Management System |
| Project Type | Personal project / prototype pembelajaran PKL |
| Architecture Target | Laravel 12, PHP 8.3+, MySQL, Blade, Bootstrap 5 |
| Related Artifacts | `docs/system-design/01-use-case-diagram/` |
| Audit Condition | `AUD-01`–`AUD-11` masih menunggu review/persetujuan |

---

## 2. INTRODUCTION

Activity Diagram ini adalah turunan langsung dari Use Case Diagram yang telah disetujui. Use Case menjelaskan **tujuan pengguna**, sedangkan Activity Diagram menjelaskan **urutan aktivitas, swimlane, decision node, guard condition, alternative flow, dan exception flow** yang mendukung tujuan tersebut.

Kelima diagram yang disimpan berfokus pada proses inventory yang memiliki dampak langsung pada stock balance dan audit trail:

1. Stock Inbound.
2. Stock Outbound.
3. Stock Transfer.
4. Stock Opname.
5. Audit Trail & Reversal/Adjustment.

Proses lain yang tercakup dalam analisis induk — Authentication, User Management, Master Data, Warehouse & Location, Stock Balance/Card, serta Dashboard/Reporting — tetap dapat ditelusuri lewat Use Case dan FR, tetapi tidak dibuat sebagai file `.puml` terpisah dalam direktori ini sesuai ruang lingkup penyimpanan yang disetujui.

---

## 3. PURPOSE AND SCOPE

### Tujuan

- Menggambarkan alur Draft → Posted secara konsisten dengan BRA §7 dan §18.
- Menjaga invariant stok: Draft tidak mengubah saldo, posting atomik, tidak ada negative stock.
- Menampilkan swimlane aktor dan sistem sesuai BRA §12.
- Menyediakan guard condition yang bersumber dari RULE dan BDL.
- Menghubungkan setiap aktivitas ke UC-ID, FR-ID, RULE, dan Decision ID.

### In Scope

| Proses | UC | FR |
|---|---|---|
| Stock Inbound | UC-15, UC-16 | FR-IN-001–006, FR-AUD-004 |
| Stock Outbound | UC-17, UC-18 | FR-OUT-001–006, FR-AUD-004 |
| Stock Transfer | UC-19, UC-20 | FR-TRF-001–005, FR-AUD-004 |
| Stock Opname | UC-21–UC-23 | FR-OPN-001–006, FR-AUD-004 |
| Reversal/Adjustment & Audit Trail | UC-33, UC-34; UC-06 pendukung | FR-AUD-001–004; FR-USER-003 pendukung |

### Out of Scope

- Source code Laravel, migration, tabel MySQL, ERD, Data Dictionary final.
- Perubahan BRA, RTA, BDL, SRS, SRS Review, `AGENTS.md`, artefak Use Case, konfigurasi, atau package.
- Perintah database.
- Implementasi rekomendasi audit `AUD-01`–`AUD-11`.
- Location lock `D-B09`, Cancel Draft final `D-B15`, dan Opening Balance final `D-B14`.
- File hasil render PNG/SVG.

---

## 4. REFERENCE DOCUMENTS

| Dokumen | Peran | Bagian yang Dipakai |
|---|---|---|
| `AGENTS.md` | Batasan pengembangan | Aturan jumlah, business rules, kontrol usulan, alur kerja perubahan |
| `docs/01-business-requirements-analysis.md` | Sumber kebutuhan bisnis | §7 To-Be process, §9 FR, §10 RULE, §12 role matrix, §18 acceptance criteria |
| `docs/02-requirements-traceability-analysis.md` | Sumber traceability dan gap | §11 Requirement Gap Analysis, §13 role/coverage |
| `docs/03-business-decision-log.md` | Sumber status keputusan | §4 Business Decision Register, §5 Technical Decision Register |
| `docs/04-software-requirements-specification.md` | Sumber spesifikasi FR | Detail FR-IN, FR-OUT, FR-TRF, FR-OPN, FR-AUD |
| `docs/05-srs-review-validation.md` | Sumber audit dan readiness | `AUD-01`–`AUD-11`, READY WITH CONDITIONS |
| `docs/system-design/01-use-case-diagram/use-case-specifications.md` | Sumber Use Case | UC-15–UC-23, UC-33, UC-34, UC-06 |
| `docs/system-design/01-use-case-diagram/use-case-stock-transactions.puml` | Source Use Case transaksi | UC-15–UC-23 |
| `docs/system-design/01-use-case-diagram/use-case-monitoring-reporting.puml` | Source Use Case audit | UC-33, UC-34 |

---

## 5. ACTIVITY DIAGRAM OVERVIEW

| AD ID | Nama Proses | File | UC | FR | Aktor Swimlane | Status |
|---|---|---|---|---|---|---|
| AD-IN | Stock Inbound | `activity-inbound.puml` | UC-15, UC-16 | FR-IN-001–006, FR-AUD-004 | WO/IA, IA, System | Provisional |
| AD-OUT | Stock Outbound | `activity-outbound.puml` | UC-17, UC-18 | FR-OUT-001–006, FR-AUD-004 | WO/IA, IA, System | Provisional |
| AD-TRF | Stock Transfer | `activity-transfer.puml` | UC-19, UC-20 | FR-TRF-001–005, FR-AUD-004 | WO/IA, IA, System | Provisional |
| AD-OPN | Stock Opname | `activity-opname.puml` | UC-21–UC-23 | FR-OPN-001–006, FR-AUD-004 | IA/WO, WO, IA, System | Provisional — D-B09 Open |
| AD-REV | Reversal/Adjustment & Audit Trail | `activity-reversal-adjustment.puml` | UC-33, UC-34; UC-06 pendukung | FR-AUD-001–004; FR-USER-003 pendukung | SA/IA, IA, System | Provisional — D-B08 Proposed / D-B16 Open |

**Catatan:** `FR-AUD-004` adalah perilaku lintas transaksi, bukan Use Case mandiri dan bukan diagram mandiri. Ia direpresentasikan sebagai region `START TRANSACTION → COMMIT / ROLLBACK` pada AD-IN, AD-OUT, AD-TRF, AD-OPN, dan AD-REV.

---

## 6. ACTOR AND SWIMLANE DEFINITIONS

| Swimlane | Aktor / Komponen | Tanggung Jawab dalam Diagram | Kewenangan | Batasan | Sumber |
|---|---|---|---|---|---|
| `Warehouse Operator` | Warehouse Operator | Membuat dan merevisi Draft inbound/outbound/transfer; mengisi hitung fisik opname; memilih serial | C/R/U Draft dan opname; R master/lokasi/saldo | Posting **tidak default**; tidak mengotorisasi penyesuaian opname; tidak melihat audit penuh | BRA §6.2 C, §12; SRS §4 |
| `Inventory Administrator` | Inventory Administrator | Membuat Draft; mem-post transaksi; mengotorisasi penyesuaian opname; mengajukan reversal/adjustment; melihat audit terbatas | C/R/U Draft; P inbound/outbound/transfer/opname; R laporan; audit terbatas | Tidak mengubah dokumen Posted secara langsung | BRA §6.2 B, §12; UC-16/18/20/23/34 |
| `System Administrator` | System Administrator | Melihat audit log penuh; mendukung pengelolaan akun/role | R audit log penuh; C/R/U/D user/role di luar diagram ini | Tidak mem-post transaksi; tidak mengubah histori Posted | BRA §6.2 A, §12; UC-06/33 |
| `System` | Sistem | Memvalidasi master, qty, serial, saldo, permission; mengelola transaction; menulis movement, balance, status, dan audit | Menjalankan validasi backend dan atomic posting | Tidak boleh menulis saldo atau status secara parsial | RULE-011, RULE-018, RULE-027; FR-AUD-004 |
| `Manager / Viewer` | Manager / Viewer | Baca saldo, kartu stok, dashboard, laporan | R sesuai BRA §12 | Tidak muncul sebagai pelaku pada lima diagram transaksi karena tidak memiliki aksi tulis | BRA §6.2 D, §12 |

**Generalization antaraktor tidak digunakan.** Manager/Viewer tetap satu kelompok; Super Admin tidak dipakai; Supplier dan sistem eksternal bukan aktor.

---

## 7. ACTIVITY STOCK INBOUND SPECIFICATION

| Field | Isi |
|---|---|
| Activity Diagram ID | AD-IN |
| Nama Proses | Stock Inbound |
| File | `activity-inbound.puml` |
| Use Case | UC-15 Buat Draft inbound lengkap; UC-16 Post inbound |
| FR | FR-IN-001, FR-IN-002, FR-IN-003, FR-IN-004, FR-IN-005, FR-IN-006, FR-AUD-004 |
| Aktor | Warehouse Operator, Inventory Administrator, System |
| Preconditions | Barang dan lokasi tujuan aktif tersedia; akun pengguna aktif |
| Trigger | Pengguna membuat penerimaan; Admin memilih Post |
| Swimlane | `Warehouse Operator / Inventory Administrator`, `Inventory Administrator`, `System` |
| Business Rules | RULE-004, RULE-005, RULE-007–011, RULE-013, RULE-018, RULE-024, RULE-025, RULE-027 |
| Postconditions | Draft tersimpan tanpa perubahan saldo; setelah Post, saldo bertambah satu kali, movement `INBOUND` ada, status Posted, audit tercatat |
| Data | Nomor dokumen, tanggal, referensi sumber, barang, lokasi tujuan, kuantitas, serial, status, aktor, waktu |
| Traceability | BRA §7.2, §9.3, §18.2; SRS FR-IN-001–006; BDL D-B03, D-B05, D-B06, D-B15, D-T06 |

### Main Flow

1. WO/IA membuat header inbound bernomor unik `[FR-IN-001]`.
2. WO/IA menambah baris: barang, lokasi tujuan, kuantitas, serial `[FR-IN-002..004]`.
3. WO/IA mengisi referensi sumber jika tersedia `[FR-IN-006]`.
4. Sistem memvalidasi barang/lokasi aktif, kuantitas lebih dari 0, dan serial valid.
5. Sistem menyimpan **Draft**; saldo tidak berubah `[RULE-010]`.
6. IA mem-post dengan guard permission dan status masih Draft.
7. Sistem menjalankan transaction `[FR-AUD-004]`: tambah saldo, movement, serial, status, audit, commit.

### Alternative Flow

- Draft dapat direvisi sebelum posting; saldo tetap tidak berubah.
- Referensi supplier/source opsional `[D-B06 Proposed]`.
- Serial hanya diwajibkan untuk barang serialized.

### Exception Flow

- Validasi gagal → Draft ditolak.
- Tanpa permission atau dokumen sudah Posted → posting ditolak; saldo tidak digandakan.
- Error sebelum commit → rollback penuh; saldo, serial, status, dan movement tidak berubah.

### Decision Points & Guard Conditions

| Decision | Guard | Sumber |
|---|---|---|
| Validasi Draft | Barang/lokasi aktif, qty > 0, serial valid | RULE-003, RULE-004, RULE-008, RULE-009 |
| Draft direvisi | Pengguna memilih revisi sebelum Post | UC-15 alternative flow |
| Permission posting | Berwenang dan dokumen masih Draft | RULE-011, RULE-024, D-B15 |
| Error transaction | Gagal sebelum commit | RULE-027, FR-AUD-004, D-T06 |

---

## 8. ACTIVITY STOCK OUTBOUND SPECIFICATION

| Field | Isi |
|---|---|
| Activity Diagram ID | AD-OUT |
| Nama Proses | Stock Outbound |
| File | `activity-outbound.puml` |
| Use Case | UC-17 Buat Draft outbound lengkap; UC-18 Post outbound |
| FR | FR-OUT-001, FR-OUT-002, FR-OUT-003, FR-OUT-004, FR-OUT-005, FR-OUT-006, FR-AUD-004 |
| Aktor | Warehouse Operator, Inventory Administrator, System |
| Preconditions | Barang dan lokasi asal aktif; serial tersedia untuk barang serialized |
| Trigger | Pengguna membuat pengeluaran; Admin memilih Post |
| Swimlane | `Warehouse Operator / Inventory Administrator`, `Inventory Administrator`, `System` |
| Business Rules | RULE-004, RULE-007–011, RULE-014, RULE-018, RULE-024, RULE-025, RULE-027 |
| Postconditions | Draft tidak mengurangi saldo; setelah Post saldo berkurang, movement `OUTBOUND` ada, serial tidak tersedia, status Posted, audit tercatat |
| Data | Nomor, tanggal, tujuan/penerima/alasan, barang, lokasi asal, kuantitas, serial, saldo |
| Traceability | BRA §7.3, §9.4, §18.3; SRS FR-OUT-001–006; BDL D-B03, D-B07, D-B15, D-T06 |

### Main Flow

1. WO/IA membuat header outbound bernomor unik `[FR-OUT-001]`.
2. WO/IA memilih barang, lokasi asal, kuantitas, dan serial `[FR-OUT-002/004]`.
3. WO/IA mengisi tujuan, penerima, atau alasan `[FR-OUT-006]`.
4. Sistem memvalidasi master aktif dan serial tersedia di lokasi asal.
5. Sistem memastikan kuantitas lebih dari 0 dan tidak melebihi saldo `[FR-OUT-003]`.
6. Draft disimpan tanpa pengurangan saldo `[RULE-010]`.
7. IA mem-post; sistem menjalankan transaction: kurangi saldo, movement, serial, status, audit, commit.

### Alternative Flow

- Draft dapat direvisi sebelum posting.
- Serial yang sudah keluar ditolak tanpa inbound/retur yang sah.
- WO posting hanya jika permission final diberikan; status ini belum final `[UD-03 unresolved reference]`.

### Exception Flow

- Kuantitas melebihi saldo → posting ditolak; negative stock dilarang `[RULE-014]`.
- Serial tidak tersedia/duplikat → ditolak.
- Error transaction → rollback penuh.

### Decision Points & Guard Conditions

| Decision | Guard | Sumber |
|---|---|---|
| Validasi awal | Barang/lokasi asal aktif, serial tersedia | RULE-003, RULE-007, RULE-008 |
| Cek saldo | Kuantitas lebih dari 0 dan ≤ saldo | RULE-004, RULE-014, FR-OUT-003 |
| Permission posting | Berwenang dan masih Draft | RULE-011, D-B15 |
| Error transaction | Gagal sebelum commit | RULE-027, FR-AUD-004, D-T06 |

---

## 9. ACTIVITY STOCK TRANSFER SPECIFICATION

| Field | Isi |
|---|---|
| Activity Diagram ID | AD-TRF |
| Nama Proses | Stock Transfer |
| File | `activity-transfer.puml` |
| Use Case | UC-19 Buat Draft transfer; UC-20 Post transfer |
| FR | FR-TRF-001, FR-TRF-002, FR-TRF-003, FR-TRF-004, FR-TRF-005, FR-AUD-004 |
| Aktor | Warehouse Operator, Inventory Administrator, System |
| Preconditions | Lokasi asal dan tujuan aktif serta berbeda; saldo asal tersedia |
| Trigger | Pengguna membuat transfer; Admin memilih Post |
| Swimlane | `Warehouse Operator / Inventory Administrator`, `Inventory Administrator`, `System` |
| Business Rules | RULE-010, RULE-011, RULE-014–018, RULE-024, RULE-025, RULE-027 |
| Postconditions | Draft tidak mengubah saldo; setelah Post asal berkurang, tujuan bertambah, dua movement tercatat, total gabungan tetap |
| Data | Nomor, asal, tujuan, barang, kuantitas, serial, saldo sebelum/sesudah |
| Traceability | BRA §7.4, §9.5, §18.4; SRS FR-TRF-001–005; BDL D-T06 Proposed |

### Main Flow

1. WO/IA membuat header transfer bernomor unik `[FR-TRF-001]`.
2. WO/IA memilih asal dan tujuan yang berbeda `[FR-TRF-002]`.
3. WO/IA memilih barang, kuantitas, dan serial di lokasi asal `[FR-TRF-003]`.
4. Sistem memvalidasi lokasi aktif/berbeda dan saldo asal.
5. Draft disimpan tanpa mengubah saldo `[RULE-010]`.
6. IA mem-post; sistem menjalankan transaction:
   - kurangi asal + movement `TRANSFER_OUT`;
   - tambah tujuan + movement `TRANSFER_IN`;
   - update serial, status, audit;
   - commit jika total gabungan tetap `[FR-TRF-005]`.

### Alternative Flow

- Draft dapat direvisi sebelum posting.
- Transfer antar gudang bukan MVP.

### Exception Flow

- Lokasi sama/nonaktif → ditolak `[RULE-016]`.
- Saldo asal kurang → ditolak; negative stock dilarang `[RULE-014]`.
- Salah satu sisi gagal → kedua movement dibatalkan `[RULE-015]`.
- Total gabungan berubah → rollback dan flag inkonsistensi ledger.

### Decision Points & Guard Conditions

| Decision | Guard | Sumber |
|---|---|---|
| Lokasi valid | Asal dan tujuan berbeda serta aktif | RULE-016, FR-TRF-002 |
| Saldo valid | Saldo asal cukup dan serial valid | RULE-014, FR-TRF-003 |
| Permission posting | Berwenang dan masih Draft | RULE-011, D-B15 |
| Invariant total | Total sebelum = total sesudah | RULE-017, FR-TRF-005 |
| Error transaction | Gagal sebelum commit | RULE-015, RULE-027, D-T06 |

---

## 10. ACTIVITY STOCK OPNAME SPECIFICATION

| Field | Isi |
|---|---|
| Activity Diagram ID | AD-OPN |
| Nama Proses | Stock Opname |
| File | `activity-opname.puml` |
| Use Case | UC-21 Buka sesi opname dan rekam snapshot; UC-22 Catat hitung fisik dan selisih; UC-23 Post penyesuaian opname |
| FR | FR-OPN-001, FR-OPN-002, FR-OPN-003, FR-OPN-004, FR-OPN-005, FR-OPN-006, FR-AUD-004 |
| Aktor | Inventory Administrator, Warehouse Operator, System |
| Preconditions | Lokasi aktif tersedia; snapshot sistem dapat direkam |
| Trigger | Sesi opname dibuka; operator mengisi hitung fisik; IA mem-post penyesuaian |
| Swimlane | `Inventory Administrator / Warehouse Operator`, `Warehouse Operator`, `Inventory Administrator`, `System` |
| Business Rules | RULE-018, RULE-021, RULE-022, RULE-023, RULE-027 |
| Postconditions | Sesi dan snapshot tercatat; variance dihitung; penyesuaian Posted menghasilkan movement dan saldo mengikuti hasil fisik |
| Data | Lokasi, periode, snapshot, counted qty, serial, variance, alasan, aktor, waktu |
| Traceability | BRA §7.5, §9.6, §18.5; SRS FR-OPN-001–006; BDL D-B09 Open, D-T06 Proposed |

### Main Flow

1. IA/WO membuka sesi opname untuk lokasi dan periode `[FR-OPN-001]`.
2. Sistem merekam snapshot sistem `[FR-OPN-002]`.
3. Sistem menandai bahwa **D-B09 Open** dan tidak ada location lock final.
4. WO mengisi hasil hitung fisik dan serial jika berlaku `[FR-OPN-003]`.
5. Sistem menghitung `variance = Physical Quantity − Snapshot` `[RULE-021 / FR-OPN-004]`.
6. IA meninjau variance dan memastikan alasan diisi `[FR-OPN-005]`.
7. IA mem-post; sistem menjalankan transaction: movement `OPNAME_ADJUSTMENT`, update saldo, status, audit, commit `[FR-OPN-006]`.

### Alternative Flow

- Sesi Draft tetap tampil di laporan.
- Snapshot tetap tersimpan meski transaksi lain terjadi; mekanisme konkurensi belum final karena `D-B09` Open.
- WO tidak dapat mengotorisasi penyesuaian.

### Exception Flow

- Lokasi nonaktif → sesi ditolak.
- Nilai hitung fisik invalid → ditolak.
- Penyesuaian tanpa alasan/otorisasi → ditolak `[FR-OPN-005]`.
- Error transaction → rollback penuh `[RULE-027]`.

### Decision Points & Guard Conditions

| Decision | Guard | Sumber |
|---|---|---|
| Sesi valid | Lokasi aktif | FR-OPN-001, D-B09 Open |
| Hitung fisik valid | Nilai dan serial valid | FR-OPN-003, RULE-021 |
| Variance siap | Variance ditinjau dan alasan diisi | FR-OPN-005, RULE-022 |
| Otorisasi | Inventory Administrator berwenang | BRA §12, FR-OPN-005 |
| Error transaction | Gagal sebelum commit | RULE-027, FR-AUD-004, D-T06 |

**Catatan:** tidak ada aktivitas final untuk penguncian lokasi. RULE-023/D-B09 tetap Open dan tidak diimplementasikan dalam desain ini.

---

## 11. ACTIVITY REVERSAL & ADJUSTMENT SPECIFICATION

| Field | Isi |
|---|---|
| Activity Diagram ID | AD-REV |
| Nama Proses | Audit Trail & Reversal/Adjustment |
| File | `activity-reversal-adjustment.puml` |
| Use Case | UC-33 Tinjau audit log; UC-34 Koreksi dokumen Posted; UC-06 Tinjau metadata aksi kritis (pendukung) |
| FR | FR-AUD-001, FR-AUD-002, FR-AUD-003, FR-AUD-004; FR-USER-003 pendukung |
| Aktor | System Administrator, Inventory Administrator, System |
| Preconditions | Aksi kritis tercatat; dokumen Posted ditemukan; alasan koreksi disiapkan |
| Trigger | Admin membuka audit log atau mengajukan koreksi dokumen Posted |
| Swimlane | `System Administrator / Inventory Administrator`, `Inventory Administrator`, `System` |
| Business Rules | RULE-012, RULE-018, RULE-026, RULE-027 |
| Postconditions | Audit terbaca; dokumen awal tetap utuh; koreksi dapat ditelusuri ke dokumen awal |
| Data | Aktor, waktu, aksi, objek, referensi dokumen, alasan, tipe koreksi |
| Traceability | BRA §7.6, §9.8, §18.6; SRS FR-AUD-001–004; BDL D-B08 Proposed, D-B16 Open |

### Main Flow — Audit Log

1. SA/IA memilih mode **Lihat Audit Log**.
2. Sistem memeriksa permission sesuai BRA §12.
3. Sistem memfilter aktor, aksi, dan periode `[FR-AUD-001]`.
4. Sistem menampilkan aktor, aksi, objek, waktu, dan referensi.
5. Retensi tetap menunggu `D-B16` Open.

### Main Flow — Reversal/Adjustment

1. IA mencoba mengubah atau menghapus dokumen Posted.
2. Sistem menolak aksi langsung `[RULE-012 / FR-AUD-002]`.
3. Sistem mengarahkan ke reversal/adjustment `[FR-AUD-003]`.
4. IA mengajukan koreksi dengan dokumen awal, alasan, dan tipe koreksi.
5. Sistem memvalidasi referensi dan alasan.
6. Sistem menjalankan transaction `[FR-AUD-004]`: buat dokumen koreksi, movement pembalik/penyesuaian, audit, commit.
7. Dokumen awal tidak diubah.

### Alternative Flow

- IA melihat metadata/audit terbatas sesuai BRA §12.
- Koreksi dapat berupa reversal atau adjustment sesuai prinsip `D-B08` Proposed; detail alur belum final.
- Dokumen Draft tidak dibekukan di diagram ini karena detail Cancel Draft masih `D-B15` Proposed.

### Exception Flow

- Tanpa permission → akses audit ditolak.
- Edit/hapus langsung dokumen Posted → ditolak tanpa perubahan data.
- Koreksi tanpa referensi/alasan → ditolak.
- Error transaction → rollback penuh; dokumen awal dan saldo tidak berubah.

### Decision Points & Guard Conditions

| Decision | Guard | Sumber |
|---|---|---|
| Mode | Lihat Audit Log atau Koreksi Posted | UC-33, UC-34 |
| Permission audit | Sesuai BRA §12 | RULE-011, D-B16 Open |
| Status dokumen | Posted → tolak edit/hapus langsung | RULE-012, FR-AUD-002 |
| Koreksi valid | Referensi awal dan alasan lengkap | FR-AUD-003, RULE-026, D-B08 Proposed |
| Error transaction | Gagal sebelum commit | RULE-027, FR-AUD-004, D-T06 Proposed |

---

## 12. BUSINESS INVARIANTS

| No | Invariant | Sumber | Dijaga pada | Perlakuan dalam Diagram |
|---:|---|---|---|---|
| 1 | Draft tidak mengubah saldo | RULE-010, BRA §18.2/18.3 | AD-IN, AD-OUT, AD-TRF | Guard Draft disimpan; saldo belum berubah. |
| 2 | Posting dilakukan secara atomik | RULE-027, FR-AUD-004, D-T06 Proposed | AD-IN, AD-OUT, AD-TRF, AD-OPN, AD-REV | Region `START TRANSACTION → COMMIT / ROLLBACK`. |
| 3 | Tidak boleh negative stock | RULE-014, BRA §18.3, KPI-02 | AD-OUT, AD-TRF | Guard qty ≤ saldo; tolak sebelum transaksi. |
| 4 | Inbound menambah saldo | RULE-013, FR-IN-005 | AD-IN | Aksi tambah saldo lokasi tujuan saat posting. |
| 5 | Outbound mengurangi saldo | RULE-014, FR-OUT-005 | AD-OUT | Aksi kurangi saldo asal saat posting. |
| 6 | Transfer menghasilkan dua movement | RULE-015, FR-TRF-004 | AD-TRF | `TRANSFER_OUT` dan `TRANSFER_IN` dalam satu transaksi. |
| 7 | Total stok transfer tetap | RULE-017, FR-TRF-005 | AD-TRF | Decision invariant total sebelum = sesudah. |
| 8 | Opname menggunakan snapshot | RULE-021, FR-OPN-002 | AD-OPN | Sistem merekam snapshot saat sesi dibuka. |
| 9 | Variance = Physical Quantity − Snapshot | RULE-021, FR-OPN-004 | AD-OPN | Aksi hitung variance dengan rumus eksplisit. |
| 10 | Posted immutable | RULE-012, FR-AUD-002 | AD-REV | Edit/hapus langsung Posted ditolak. |
| 11 | Reversal/Adjustment dapat ditelusuri | RULE-026, FR-AUD-003 | AD-REV | Koreksi wajib memiliki referensi dokumen awal dan alasan. |

---

## 13. ALTERNATIVE FLOW AND EXCEPTION FLOW

| AD | Alternative Flow | Exception Flow | Status Terkait |
|---|---|---|---|
| AD-IN | Revisi Draft; referensi opsional; serial hanya untuk serialized | Validasi gagal; tanpa permission; post ulang; error transaction rollback | D-B03, D-B05, D-B06, D-B15, D-T06 Proposed |
| AD-OUT | Revisi Draft; WO posting hanya jika permission final | Saldo kurang; serial tidak tersedia; error transaction rollback | D-B03, D-B07, D-B15, D-T06; UD-03 unresolved |
| AD-TRF | Revisi Draft; transfer antar gudang bukan MVP | Lokasi sama/nonaktif; saldo kurang; satu sisi gagal; total berubah | D-B15, D-T06 Proposed |
| AD-OPN | Sesi Draft tampil di laporan; konkurensi belum final | Lokasi nonaktif; nilai invalid; tanpa alasan/otorisasi; error rollback | D-B09 Open, D-T06 Proposed |
| AD-REV | Audit terbatas; reversal atau adjustment; Draft tidak dibekukan di sini | Tanpa permission; edit/hapus Posted; tanpa referensi/alasan; error rollback | D-B08 Proposed, D-B16 Open, D-B15 Proposed |

---

## 14. REQUIREMENT TRACEABILITY MATRIX

| FR ID | UC | AD | RULE | Decision ID | Status | Scope |
|---|---|---|---|---|---|---|
| FR-IN-001 | UC-15 | AD-IN | RULE-010, RULE-024, RULE-025 | D-B15 | Proposed | MVP |
| FR-IN-002 | UC-15 | AD-IN | RULE-004 | — | — | MVP |
| FR-IN-003 | UC-15 | AD-IN | RULE-004, RULE-005 | D-B05 | Proposed | MVP |
| FR-IN-004 | UC-15 | AD-IN | RULE-008, RULE-009 | D-B03 | Proposed | MVP |
| FR-IN-005 | UC-16 | AD-IN | RULE-013, RULE-018, RULE-027 | D-B15, D-T06 | Proposed | MVP |
| FR-IN-006 | UC-15 | AD-IN | RULE-003 | D-B06 | Proposed | MVP |
| FR-OUT-001 | UC-17 | AD-OUT | RULE-010, RULE-024, RULE-025 | D-B07, D-B15 | Proposed | MVP |
| FR-OUT-002 | UC-17 | AD-OUT | RULE-004 | — | — | MVP |
| FR-OUT-003 | UC-18 | AD-OUT | RULE-014 | — | — | MVP |
| FR-OUT-004 | UC-17 | AD-OUT | RULE-007, RULE-008 | D-B03 | Proposed | MVP |
| FR-OUT-005 | UC-18 | AD-OUT | RULE-014, RULE-018, RULE-027 | D-B07, D-T06 | Proposed | MVP |
| FR-OUT-006 | UC-17 | AD-OUT | — | — | — | MVP |
| FR-TRF-001 | UC-19 | AD-TRF | RULE-010, RULE-024, RULE-025 | D-B15 | Proposed | MVP |
| FR-TRF-002 | UC-19 | AD-TRF | RULE-016 | — | — | MVP |
| FR-TRF-003 | UC-19 | AD-TRF | RULE-014, RULE-016 | — | — | MVP |
| FR-TRF-004 | UC-20 | AD-TRF | RULE-015, RULE-018, RULE-027 | D-T06 | Proposed | MVP |
| FR-TRF-005 | UC-20 | AD-TRF | RULE-017 | — | — | MVP |
| FR-OPN-001 | UC-21 | AD-OPN | RULE-021, RULE-023 | D-B09 | **Open** | MVP tanpa lock final |
| FR-OPN-002 | UC-21 | AD-OPN | RULE-021, RULE-023 | D-B09 | **Open** | MVP tanpa lock final |
| FR-OPN-003 | UC-22 | AD-OPN | RULE-021 | — | — | MVP |
| FR-OPN-004 | UC-22 | AD-OPN | RULE-021 | — | — | MVP |
| FR-OPN-005 | UC-23 | AD-OPN | RULE-022 | — | — | MVP |
| FR-OPN-006 | UC-23 | AD-OPN | RULE-018, RULE-022, RULE-027 | D-T06 | Proposed | MVP |
| FR-AUD-001 | UC-33 | AD-REV | RULE-018, RULE-027 | D-B16 | Open | MVP |
| FR-AUD-002 | UC-34 | AD-REV | RULE-012 | D-B08 | Proposed | MVP |
| FR-AUD-003 | UC-34 | AD-REV | RULE-012, RULE-026 | D-B08 | Proposed | MVP |
| FR-AUD-004 | UC-16, UC-18, UC-20, UC-23 | AD-IN, AD-OUT, AD-TRF, AD-OPN, AD-REV | RULE-018, RULE-027 | D-T06 | Proposed | Cross-cutting MVP |
| FR-USER-003 | UC-06 pendukung | AD-REV pendukung | RULE-018 | D-B16 | Open | Pendukung |

**Total:** 28 FR unik terpetakan (27 FR inti + 1 FR pendukung), 12 UC terkait (11 UC inti + UC-06 pendukung), 5 Activity Diagram, 0 Requirement ID baru.

---

## 15. BUSINESS RULES MAPPING

| RULE | Aturan | AD Terkait | Guard / Aktivitas |
|---|---|---|---|
| RULE-003 | Barang/lokasi nonaktif tidak dipakai transaksi baru | AD-IN, AD-OUT, AD-OPN | Validasi master aktif saat Draft/sesi. |
| RULE-004 | Kuantitas lebih dari 0 | AD-IN, AD-OUT, AD-TRF | Guard kuantitas pada validasi Draft. |
| RULE-005 | Quantity memakai presisi tetap | AD-IN, AD-OUT | Validasi kuantitas dan satuan; D-B05 Proposed. |
| RULE-007 | Satu serial hanya satu status/lokasi aktif | AD-IN, AD-OUT, AD-TRF | Validasi serial; update status serial saat posting. |
| RULE-008 | Jumlah serial = kuantitas bulat untuk serialized | AD-IN, AD-OUT, AD-TRF | Validasi serialized; D-B05 Proposed. |
| RULE-009 | Serial tidak didaftarkan dua kali | AD-IN, AD-OUT, AD-TRF | Guard serial valid/unik; D-B03 Proposed. |
| RULE-010 | Draft tidak mengubah saldo | AD-IN, AD-OUT, AD-TRF | Aktivitas eksplisit “saldo tidak berubah”. |
| RULE-011 | Posting hanya oleh berwenang | Semua AD | Guard permission backend; D-T05 Proposed. |
| RULE-012 | Posted immutable | AD-REV | Edit/hapus langsung ditolak. |
| RULE-013 | Inbound menambah saldo tujuan | AD-IN | Aksi tambah saldo saat posting. |
| RULE-014 | Outbound/transfer tidak boleh negative stock | AD-OUT, AD-TRF | Guard qty ≤ saldo; tolak sebelum transaction. |
| RULE-015 | Transfer dua movement dalam satu transaksi | AD-TRF | `TRANSFER_OUT` + `TRANSFER_IN`; rollback jika salah satu gagal. |
| RULE-016 | Lokasi asal/tujuan transfer berbeda dan aktif | AD-TRF | Guard lokasi valid. |
| RULE-017 | Total stok gabungan tetap | AD-TRF | Decision invariant total sebelum = sesudah. |
| RULE-018 | Perubahan saldo menghasilkan movement berreferensi | AD-IN, AD-OUT, AD-TRF, AD-OPN, AD-REV | Aktivitas movement + referensi dokumen. |
| RULE-021 | Variance = fisik − snapshot | AD-OPN | Aksi hitung variance. |
| RULE-022 | Penyesuaian memiliki alasan, pelaku, waktu, otorisasi | AD-OPN, AD-REV | Guard alasan dan aktivitas audit. |
| RULE-023 | Lock lokasi saat opname aktif | AD-OPN | **Tidak diimplementasikan**; D-B09 Open. |
| RULE-024 | Nomor dokumen unik | AD-IN, AD-OUT, AD-TRF | Header bernomor unik; guard post ulang. |
| RULE-025 | Batal Draft tanpa movement | AD-IN, AD-OUT, AD-TRF | Detail cancel Draft belum final; D-B15 Proposed. |
| RULE-026 | Koreksi Posted lewat reversal/adjustment berreferensi | AD-REV | Guard referensi + alasan; movement pembalik. |
| RULE-027 | Saldo dan status berhasil/gagal bersama | Semua AD | `START TRANSACTION`, `COMMIT`, `ROLLBACK`. |

---

## 16. DECISION STATUS MAPPING

Status berikut mengikuti BDL dan tidak diubah oleh artefak ini.

| Decision ID | Topik | Status | Dampak pada Activity Diagram |
|---|---|---|---|
| D-B01 | `item_code` vs `material_number` | Proposed | Tidak mengubah alur transaksi; master data di luar 5 diagram. |
| D-B02 | Tracking type per barang | Proposed | Menentukan apakah alur serial dipakai pada AD-IN/OUT/TRF. |
| D-B03 | Serial unik global MVP | Proposed | Guard serial pada AD-IN, AD-OUT, AD-TRF. |
| D-B04 | Hierarki lokasi | Proposed | Lokasi aktif divalidasi pada transaksi. |
| D-B05 | Presisi quantity & serialized integer | Proposed | Guard kuantitas dan serial. |
| D-B06 | Supplier opsional | Proposed | AD-IN referensi sumber opsional. |
| D-B07 | Draft/Posted outbound + permission | Proposed | Guard posting AD-OUT. |
| D-B08 | Reversal/adjustment | Proposed | Alur AD-REV provisional; detail belum final. |
| D-B09 | Location lock saat opname | **Open** | AD-OPN **tanpa location lock final**. |
| D-B10 | Minimum stock per item | Proposed | Tidak mengubah 5 diagram. |
| D-B11 | Retur barang | Deferred | Tidak ada alur return. |
| D-B12 | Batch / expiry | Deferred | Tidak ada alur batch/expiry. |
| D-B13 | Interpretasi dashboard | Proposed | Tidak mengubah 5 diagram. |
| D-B14 | Opening balance | Proposed | Tidak dibuat aktivitas opening balance. |
| D-B15 | Cancel Draft & double posting | Proposed | Guard “masih Draft” dipakai; cancel final belum ada. |
| D-B16 | Retensi audit | **Open** | AD-REV tidak menetapkan periode retensi. |
| D-B17 | Export Excel/PDF | Deferred | Tidak ada alur export. |
| D-T01 | Timezone Asia/Jakarta | Proposed | Aktivitas mencatat waktu; zona final belum ditetapkan. |
| D-T02 | Minimum PHP 8.3 | Accepted | Tidak mengubah alur. |
| D-T03 | Blade + Bootstrap 5 | Accepted | Tidak mengubah alur. |
| D-T04 | DB test terpisah | Proposed | Tidak mengubah alur. |
| D-T05 | Middleware/Policy/Gate | Proposed | Guard permission backend masih rancangan. |
| D-T06 | Transaction + lock saldo | Proposed | Region transaksi pada semua AD posting. |
| D-T07 | Backup/restore doc | Proposed | Tidak mengubah alur. |
| D-T08 | ID requirement baru butuh approval | Proposed | AD-ID bukan Requirement ID. |

**Rekap:** 18 Proposed, 2 Open, 3 Deferred, 2 Accepted. Total 25 Decision ID.

---

## 17. UNRESOLVED ACTIVITY DECISIONS

| Item | Topik | Sumber | Status | Dampak |
|---|---|---|---|---|
| UD-03 | Permission posting Warehouse Operator | BRA §12: “Sesuai permission; default tidak”; tidak ditemukan sebagai Decision ID di BRA/RTA/BDL | **Unresolved reference — bukan Decision ID resmi** | WO tidak diasumsikan memiliki hak posting final; guard tetap berwenang. |
| D-B09 | Location lock saat opname | BRA RULE-023, ASM-10, AGENTS.md | **Open** | Tidak ada location lock; AD-OPN hanya merekam snapshot. |
| D-B16 | Retensi audit log | BRA §11.5, BDL | **Open** | AD-REV tidak menetapkan periode retensi. |
| D-B08 | Detail reversal/adjustment | BDL | Proposed | AD-REV memakai prinsip referensi + alasan tanpa detail final. |
| D-B15 | Cancel Draft & double posting | RTA §11, BDL | Proposed | Tidak ada alur final cancel Draft; guard post ulang masih rancangan. |
| D-B14 | Opening balance | RTA §11, BDL | Proposed | Tidak dibuat aktivitas opening balance. |
| D-B17 | Export Excel/PDF | BDL | Deferred | Tidak ada aktivitas export dalam 5 diagram. |
| D-B13 | Interpretasi dashboard | BDL | Proposed | Di luar 5 diagram; tidak diubah. |
| D-T01 | Timezone aplikasi | BRA RULE-028 vs config UTC | Proposed | Waktu dicatat, zona final belum ditetapkan. |
| D-T05 | Auth middleware/Policy | BDL | Proposed | Guard permission tetap desain, bukan implementasi final. |
| D-T06 | Strategi transaction/lock | BDL | Proposed | Atomicitas dipetakan, strategi lock belum final. |
| AUD-01–AUD-11 | Temuan audit SRS Review | `docs/05-srs-review-validation.md` | Menunggu review/persetujuan | Rekomendasi audit belum diterapkan. |

---

## 18. FINAL REVIEW CHECKLIST

- [x] 5 file PlantUML tersedia sesuai lokasi yang disetujui.
- [x] `README.md` dan `activity-specifications.md` tersedia.
- [x] Setiap diagram memiliki `@startuml`, `@enduml`, judul, Initial Node, Action, Control Flow, Decision/Merge, dan Final Node.
- [x] Swimlane mencerminkan BRA §12; WO tidak melakukan posting secara default.
- [x] Seluruh diagram terhubung ke UC-ID dan FR-ID.
- [x] 28 FR unik terpetakan; tidak ada Requirement ID baru.
- [x] 12 UC terkait (11 inti + UC-06 pendukung) terpetakan; tidak ada Use Case baru.
- [x] Business invariants dipertahankan.
- [x] `FR-AUD-004` tetap cross-cutting, bukan UC/diagram mandiri.
- [x] D-B09 tetap Open; tidak ada location lock.
- [x] D-B16 tetap Open; tidak ada retensi final.
- [x] D-B08 tetap Proposed; detail koreksi belum final.
- [x] D-T06 tetap Proposed; atomicitas adalah rancangan.
- [x] Cancel Draft dan Opening Balance tidak dianggap requirement final.
- [x] `UD-03` dicatat sebagai unresolved reference, bukan Decision ID resmi.
- [x] AUD-01–AUD-11 belum diterapkan.
- [x] Tidak ada source code, migration, tabel database, ERD, Data Dictionary final, konfigurasi, package, atau perintah database.
- [x] State Machine Diagram belum dimulai.

---

**Batasan yang dipatuhi:** hanya tujuh artefak Activity Diagram yang dibuat di `docs/system-design/02-activity-diagrams/`. Use Case Diagram, BRA, RTA, BDL, SRS, SRS Review, `AGENTS.md`, dan seluruh source/configuration tidak diubah.
