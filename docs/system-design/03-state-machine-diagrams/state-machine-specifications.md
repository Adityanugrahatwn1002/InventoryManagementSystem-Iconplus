# STATE MACHINE DIAGRAM ANALYSIS & DESIGN — SPECIFICATIONS

## Inventory Management System

| Atribut | Nilai |
|---|---|
| Versi | 0.1 |
| Status artefak | Draft yang telah direview dan disetujui untuk disimpan |
| Status desain | **Provisional** untuk objek yang dipengaruhi keputusan Proposed/Open |
| Lokasi | `docs/system-design/03-state-machine-diagrams/state-machine-specifications.md` |
| Source diagram | 6 file `.puml` dalam direktori yang sama |
| Berlaku untuk | Lifecycle status objek yang telah disetujui pada tahap Use Case & Activity Diagram |

**Pernyataan batasan:** Dokumen ini adalah artefak desain untuk prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Proses bisnis yang dirujuk berasal dari BRA dan asumsi yang masih dapat ditinjau. Gunakan hanya data dummy/sintetis.

`SM-*`, `SMO-*`, `ST-*`, dan `EV-*` adalah **ID artefak desain**, bukan Requirement ID BRA. Tidak ada Requirement ID baru yang dibuat.

---

## 1. DOCUMENT INFORMATION

| Field | Isi |
|---|---|
| Document Title | State Machine Diagram Analysis & Design — Specifications |
| Document ID | Akan ditetapkan setelah dokumen disetujui final |
| Version | 0.1 — Draft for Review, disetujui untuk disimpan |
| Owner | Pengembang proyek / pemilik proyek |
| Project | Inventory Management System |
| Project Type | Personal project / prototype pembelajaran PKL |
| Architecture Target | Laravel 12, PHP 8.3+, MySQL, Blade, Bootstrap 5 |
| Related Artifacts | `docs/system-design/01-use-case-diagram/`, `docs/system-design/02-activity-diagrams/` |
| Audit Condition | `AUD-01`–`AUD-11` masih menunggu review/persetujuan |

---

## 2. INTRODUCTION

State Machine Diagram menggambarkan **state objek**, event yang memicu perpindahan, guard condition, dan action saat transisi. Use Case menjelaskan tujuan pengguna; Activity Diagram menjelaskan urutan aktivitas; State Machine Diagram menjelaskan **keadaan objek dan transisinya**.

Objek yang dimodelkan:

1. Inventory Transaction Document — Inbound (SM-01).
2. Inventory Transaction Document — Outbound (SM-02).
3. Inventory Transaction Document — Transfer (SM-03).
4. Stock Opname Session (SM-04).
5. Reversal/Adjustment Document (SM-05).

Satu file tambahan, `state-transaction-document.puml`, mendokumentasikan **pola umum** Draft → Posted dan bukan objek baru.

Kesimpulan review yang dipertahankan: Inbound, Outbound, dan Transfer memiliki topologi status yang sama, tetapi **guard dan action transisinya berbeda**, sehingga lifecycle dipisah menjadi tiga diagram. Tidak ada penggabungan lifecycle demi kesederhanaan.

---

## 3. PURPOSE AND SCOPE

### Tujuan

- Menggambarkan lifecycle `Draft` → `Posted` secara konsisten dengan BRA §7, §10, §18, dan §24.
- Menjaga invariant: Draft tidak mengubah saldo, posting atomik, rollback saat gagal, Posted immutable.
- Memisahkan dokumen koreksi dari dokumen asli yang immutable.
- Menyediakan guard dan action yang dapat ditelusuri ke UC, FR, RULE, Decision ID, dan Acceptance Criteria.

### In Scope

| Objek | UC | FR |
|---|---|---|
| Inbound Document | UC-15, UC-16 | FR-IN-001–006, FR-AUD-004 |
| Outbound Document | UC-17, UC-18 | FR-OUT-001–006, FR-AUD-004 |
| Transfer Document | UC-19, UC-20 | FR-TRF-001–005, FR-AUD-004 |
| Stock Opname Session | UC-21–UC-23 | FR-OPN-001–006, FR-AUD-004 |
| Reversal/Adjustment Document | UC-34; UC-33 konteks | FR-AUD-001–004 |

### Out of Scope

- Source code Laravel, migration, tabel MySQL, ERD, Data Dictionary final.
- Perubahan BRA, RTA, BDL, SRS, SRS Review, Use Case, Activity Diagram, `AGENTS.md`, konfigurasi, atau package.
- Perintah database.
- Implementasi rekomendasi `AUD-01`–`AUD-11`.
- State `Validated`, `Cancelled`, `Locked`, `Approved`, `Completed`, dan `Reversed` pada dokumen asli.
- Return (`D-B11`), Batch/Expiry (`D-B12`), Export (`D-B17`), Opening Balance (`D-B14`).
- File hasil render PNG/SVG.

---

## 4. REFERENCE DOCUMENTS

| Dokumen | Peran | Bagian yang Dipakai |
|---|---|---|
| `AGENTS.md` | Batasan pengembangan | Aturan bisnis, kontrol usulan, alur kerja perubahan |
| `docs/01-business-requirements-analysis.md` | Sumber kebutuhan bisnis | §7 To-Be process, §9 FR, §10 RULE, §12 role matrix, §18 AC, §24 glossary |
| `docs/02-requirements-traceability-analysis.md` | Sumber traceability dan gap | §11 Requirement Gap Analysis |
| `docs/03-business-decision-log.md` | Sumber status keputusan | §4 Business Decision Register, §5 Technical Decision Register, §8 Deferred, §9 Open |
| `docs/04-software-requirements-specification.md` | Sumber spesifikasi FR | Detail FR-IN, FR-OUT, FR-TRF, FR-OPN, FR-AUD |
| `docs/05-srs-review-validation.md` | Sumber audit dan readiness | `AUD-01`–`AUD-11`, READY WITH CONDITIONS, §8 Decision Status Audit |
| `docs/system-design/01-use-case-diagram/use-case-specifications.md` | Sumber Use Case | UC-15–UC-23, UC-33, UC-34 |
| `docs/system-design/02-activity-diagrams/activity-specifications.md` | Sumber Activity Diagram | AD-IN, AD-OUT, AD-TRF, AD-OPN, AD-REV |

---

## 5. OBJECT ANALYSIS

| Object ID | Object Name | Dasar status | Diagram | Status desain |
|---|---|---|---|---|
| SMO-00 | Inventory Transaction Document — pola umum | BRA §24 Draft/Posted; RULE-010/012/027 | `state-transaction-document.puml` | Referensi pola |
| SMO-01 | Inbound Document | FR-IN-001 Draft; FR-IN-005 Posted | `state-inbound.puml` | Provisional |
| SMO-02 | Outbound Document | FR-OUT-001 Draft; FR-OUT-005 Posted; D-B07 | `state-outbound.puml` | Provisional |
| SMO-03 | Transfer Document | FR-TRF-001 Draft; FR-TRF-004/005 Posted | `state-transfer.puml` | Provisional |
| SMO-04 | Stock Opname Session | FR-OPN-001 status sesi; UC-22 Draft session; FR-OPN-006 status Posted | `state-stock-opname.puml` | Provisional — D-B09 Open |
| SMO-05 | Reversal/Adjustment Document | FR-AUD-003 buat dokumen koreksi → posting movement | `state-correction-document.puml` | Provisional — D-B08 Proposed |

### 5.1 Perbandingan lifecycle transaksi

| Aspek | Inbound | Outbound | Transfer | Keputusan |
|---|---|---|---|---|
| State | Draft, Posted | Draft, Posted | Draft, Posted | Sama |
| Event Create/Post | Sama | Sama | Sama | Sama |
| Guard | Master aktif, qty > 0, serial valid | Master aktif, serial tersedia, qty ≤ saldo | Asal ≠ tujuan, aktif, saldo cukup | **Berbeda** |
| Action Post | +saldo tujuan, `INBOUND` | −saldo asal, `OUTBOUND` | −asal +tujuan, 2 movement, invariant total | **Berbeda** |

Karena guard dan action adalah bagian dari transisi, ketiganya **tidak disatukan**. Nama state sengaja dibuat identik agar konsisten dengan BRA §24.

### 5.2 Objek yang tidak dibuat diagram

Master Item dan Warehouse memiliki status aktif/nonaktif (FR-MD-003, FR-WH-003, RULE-003, AC §18.1), tetapi BRA/SRS **tidak mendefinisikan state machine lengkap** (event, guard, final state). Sesuai instruksi review, tidak dibuat diagram tambahan; status tersebut hanya menjadi guard pada SM-01–SM-04.

---

## 6. STATE DEFINITIONS

| State ID | Objek | Nama | Klasifikasi | Sumber |
|---|---|---|---|---|
| ST-IN-01 | SMO-01 | Draft | Terverifikasi | FR-IN-001, RULE-010, BRA §24 |
| ST-IN-02 | SMO-01 | Posted | Terverifikasi | FR-IN-005, RULE-012/013/027, BRA §24 |
| ST-OUT-01 | SMO-02 | Draft | Terverifikasi | FR-OUT-001, RULE-010 |
| ST-OUT-02 | SMO-02 | Posted | Terverifikasi | FR-OUT-005, RULE-012/014 |
| ST-TRF-01 | SMO-03 | Draft | Terverifikasi | FR-TRF-001, RULE-010 |
| ST-TRF-02 | SMO-03 | Posted | Terverifikasi | FR-TRF-004/005, RULE-015/017 |
| ST-OPN-01 | SMO-04 | Draft (sesi) | Terverifikasi | UC-21/22, FR-OPN-001–004 |
| ST-OPN-02 | SMO-04 | Posted (sesi) | Terverifikasi | FR-OPN-006, RULE-018/022/027 |
| ST-REV-01 | SMO-05 | Draft koreksi | **Provisional** | FR-AUD-003, D-B08 Proposed |
| ST-REV-02 | SMO-05 | Posted koreksi | **Provisional** | FR-AUD-003, RULE-026/027, D-B08 Proposed |

### 6.1 State yang sengaja tidak dibuat

| Kandidat | Keputusan | Alasan |
|---|---|---|
| Validated | Ditolak sebagai state | Tidak ada FR/BRA yang menetapkannya sebagai status; validasi adalah guard di dalam Post |
| Cancelled | Unresolved, bukan state final | RULE-025 & BRA §7.6 ada, tetapi D-B15 Proposed dan belum FR/AC final (RTA §11) |
| Locked | Tidak dibuat | RULE-023 D-B09 Open; BRA §7.5 menyebut kontrol usulan; AGENTS.md melarang implementasi sebelum persetujuan |
| Approved / Completed | Tidak dibuat | Tidak ada nilai status tersebut di BRA/SRS; otorisasi adalah guard (FR-OPN-005) |
| Reversed pada dokumen asli | Tidak dibuat | RULE-012 Posted immutable; koreksi adalah objek terpisah (SMO-05) |

---

## 7. EVENT DEFINITIONS

| Event ID | Event | Objek | Makna | Sumber |
|---|---|---|---|---|
| EV-01 | Create | SMO-01/02/03 | Membuat dokumen Draft bernomor unik | FR-IN-001, FR-OUT-001, FR-TRF-001 |
| EV-02 | Revise | SMO-01/02/03 | Mengubah Draft sebelum posting | UC-15/17/19 alternative |
| EV-03 | Post | SMO-01 | Mem-post inbound | UC-16, FR-IN-005 |
| EV-04 | Post | SMO-02 | Mem-post outbound | UC-18, FR-OUT-005 |
| EV-05 | Post | SMO-03 | Mem-post transfer | UC-20, FR-TRF-004 |
| EV-06 | Open | SMO-04 | Membuka sesi dan merekam snapshot | UC-21, FR-OPN-001/002 |
| EV-07 | Input | SMO-04 | Mengisi hitung fisik | UC-22, FR-OPN-003 |
| EV-08 | Ajukan | SMO-04 | Mengajukan penyesuaian dengan alasan | UC-23, FR-OPN-005 |
| EV-09 | Post | SMO-04 | Mem-post penyesuaian opname | UC-23, FR-OPN-006 |
| EV-10 | Post gagal / Ditolak | Semua | Validasi gagal, tanpa permission, atau error sebelum commit | RULE-011/027 |
| EV-11 | Edit/Hapus | SMO-01/02/03/05 | Upaya mengubah dokumen Posted | RULE-012, FR-AUD-002 |
| EV-12 | Create correction | SMO-05 | Membuat dokumen koreksi terpisah | UC-34, FR-AUD-003 |
| EV-13 | Cancel Draft | SMO-01/02/03 | **Tidak dimodelkan** sebagai transisi state | D-B15 Proposed |

---

## 8. GUARD CONDITIONS

| Guard | Ekspresi | Sumber | Status |
|---|---|---|---|
| Nomor unik | Nomor dokumen belum dipakai | RULE-024 | Terverifikasi |
| Master aktif | Barang/lokasi berstatus aktif | RULE-003, FR-WH-003 | Terverifikasi |
| Masih Draft | Status dokumen belum Posted | D-B15 Proposed | Rancangan |
| Permission | Aktor memiliki permission posting backend | RULE-011, D-T05 Proposed | Terverifikasi sebagai requirement |
| Qty positif | Kuantitas lebih dari nol | RULE-004 | Terverifikasi |
| Serial valid | Serial unik, jumlah sesuai kuantitas bulat | RULE-007–009, D-B03/D-B05 Proposed | Terverifikasi sebagai requirement |
| Saldo cukup | Kuantitas tidak melebihi saldo tersedia | RULE-014, FR-OUT-003 | Terverifikasi |
| Lokasi transfer valid | Asal dan tujuan berbeda serta aktif | RULE-016 | Terverifikasi |
| Total invariant | Total stok gabungan sebelum = sesudah | RULE-017, FR-TRF-005 | Terverifikasi |
| Sesi aktif | Sesi opname memiliki snapshot | FR-OPN-002 | Terverifikasi |
| Alasan & otorisasi | Alasan diisi dan Inventory Admin berwenang | RULE-022, FR-OPN-005 | Terverifikasi |
| Referensi koreksi | Dokumen awal dan alasan dicantumkan | FR-AUD-003, RULE-026 | D-B08 Proposed |
| Atomic | Semua langkah berhasil atau rollback penuh | RULE-027, FR-AUD-004 | D-T06 Proposed |

---

## 9. TRANSITION ACTIONS

| Aksi | Deskripsi | Sumber |
|---|---|---|
| Simpan header/baris | Draft dibuat tanpa menyentuh saldo | FR-IN-001, FR-OUT-001, FR-TRF-001 |
| Simpan ulang | Revisi Draft; saldo tetap | UC-15/17/19 |
| Snapshot sistem | Nilai saldo sistem direkam sekali dan stabil | FR-OPN-002 |
| Hitung variance | `variance = Physical Quantity − Snapshot` | RULE-021, FR-OPN-004 |
| Tambah saldo tujuan | Inbound Posting | RULE-013, FR-IN-005 |
| Kurangi saldo asal | Outbound Posting | RULE-014, FR-OUT-005 |
| Dua movement | `TRANSFER_OUT` + `TRANSFER_IN` dalam satu transaksi | RULE-015/017, FR-TRF-004/005 |
| Movement adjustment | `OPNAME_ADJUSTMENT`, saldo mengikuti hasil fisik | FR-OPN-006 |
| Movement pembalik | Koreksi menghasilkan movement baru | FR-AUD-003, RULE-026 |
| Audit | Mencatat aktor, waktu, aksi, referensi objek | FR-AUD-001, RULE-018/028 |
| COMMIT / ROLLBACK | Status, saldo, serial, movement, audit berhasil/gagal bersama | RULE-027, FR-AUD-004, D-T06 Proposed |
| Tolak | Aksi ditolak tanpa perubahan data | RULE-011/012 |

---

## 10. STATE INVARIANTS

| State | Invariant | Sumber | Jika dilanggar |
|---|---|---|---|
| Draft (SM-01/02/03) | Saldo tidak berubah; nomor unik; serial belum berpindah; belum ada movement | RULE-010, RULE-024, RULE-025 | Draft ditolak atau tetap tanpa efek saldo |
| Posted (SM-01/02/03) | Immutable; saldo berubah tepat satu kali; movement berreferensi ada; audit actor+waktu ada; status & saldo atomik | RULE-012/013/014/015/017/018/027 | Edit/hapus ditolak; error → rollback |
| Draft sesi opname | Snapshot stabil; variance = fisik − snapshot; saldo belum diubah; **tanpa location lock** | FR-OPN-002/004, RULE-021, D-B09 Open | Nilai invalid ditolak |
| Posted sesi opname | Saldo akhir = hasil fisik; movement `OPNAME_ADJUSTMENT` ada; alasan/pelaku/waktu ada | FR-OPN-006, RULE-018/022/027 | Rollback; tidak ada saldo parsial |
| Draft koreksi | Objek terpisah; referensi + alasan ada; dokumen asli tetap Posted | FR-AUD-003, RULE-012/026 | Koreksi ditolak |
| Posted koreksi | Movement baru; audit mempertahankan hubungan ke dokumen awal | RULE-026/027, FR-AUD-001 | Rollback |

---

## 11. LIFECYCLE SPECIFICATIONS

### 11.1 SM-01 — Inbound

- Initial: `[*] → Draft` saat Create dengan nomor unik dan master aktif.
- Draft self-loop: Revise; saldo tetap.
- Draft → Posted: Post dengan permission, qty > 0, serial valid, masih Draft; aksi menambah saldo tujuan, movement `INBOUND`, audit, commit.
- Draft self-loop: Post gagal → rollback penuh.
- Posted self-loop: Edit/Hapus ditolak.
- Final: `Posted → [*]` (record tetap tersimpan sebagai histori).

### 11.2 SM-02 — Outbound

- Sama seperti SM-01, dengan guard tambahan serial tersedia dan **kuantitas tidak melebihi saldo**.
- Action: mengurangi saldo asal, movement `OUTBOUND`.
- Post gagal: rollback tanpa negative stock.
- Hak posting WO belum final (`UD-03`).

### 11.3 SM-03 — Transfer

- Guard Create: asal dan tujuan berbeda serta aktif.
- Action Post: mengurangi asal + `TRANSFER_OUT`, menambah tujuan + `TRANSFER_IN`, serial, status, audit, commit.
- Guard tambahan: total gabungan tetap; jika gagal, kedua movement dibatalkan.

### 11.4 SM-04 — Stock Opname Session

- `[*] → Draft` saat Open dengan snapshot sistem.
- Draft self-loop: Input hitung fisik, hitung variance; Ajukan penyesuaian; Ditolak tanpa alasan/IA; Post gagal → rollback.
- Draft → Posted: movement `OPNAME_ADJUSTMENT`, saldo mengikuti hasil fisik, audit.
- **Tidak ada state Locked/Approved/Completed.** `D-B09` Open.

### 11.5 SM-05 — Reversal/Adjustment Document

- Dokumen asli Posted tidak memiliki transisi keluar; upaya edit/hapus ditolak.
- `[*] → Draft koreksi` saat Create dengan referensi dokumen awal, alasan, dan aktor IA.
- Draft koreksi self-loop: validasi gagal atau post gagal → rollback; asli tetap utuh.
- Draft koreksi → Posted koreksi: movement pembalik/penyesuaian + audit.
- Nama status bersifat provisional (`D-B08` Proposed).

---

## 12. REQUIREMENTS TRACEABILITY

| FR ID | UC | SM | RULE | Decision ID | AC |
|---|---|---|---|---|---|
| FR-IN-001 | UC-15 | SM-01 | RULE-010,024,025 | D-B15 Proposed | §18.2-1 |
| FR-IN-002 | UC-15 | SM-01 | RULE-004 | — | §18.2-1 |
| FR-IN-003 | UC-15 | SM-01 | RULE-004,005 | D-B05 Proposed | §18.2-1 |
| FR-IN-004 | UC-15 | SM-01 | RULE-007,008,009 | D-B03 Proposed | §18.2-4 |
| FR-IN-005 | UC-16 | SM-01 | RULE-011,013,018,024,027 | D-B15, D-T06 Proposed | §18.2-2,3,5 |
| FR-IN-006 | UC-15 | SM-01 | RULE-003 | D-B06 Proposed | §18.2 |
| FR-OUT-001 | UC-17 | SM-02 | RULE-010,024,025 | D-B07, D-B15 Proposed | §18.3-1 |
| FR-OUT-002 | UC-17 | SM-02 | RULE-004 | — | §18.3-1 |
| FR-OUT-003 | UC-18 | SM-02 | RULE-014 | — | §18.3-2 |
| FR-OUT-004 | UC-17 | SM-02 | RULE-007,008 | D-B03 Proposed | §18.3-4 |
| FR-OUT-005 | UC-18 | SM-02 | RULE-011,014,018,027 | D-B07, D-T06 Proposed | §18.3-3 |
| FR-OUT-006 | UC-17 | SM-02 | — | — | §18.3 |
| FR-TRF-001 | UC-19 | SM-03 | RULE-010,024,025 | D-B15 Proposed | §18.4-1 |
| FR-TRF-002 | UC-19 | SM-03 | RULE-016 | D-B04 Proposed | §18.4-1 |
| FR-TRF-003 | UC-19 | SM-03 | RULE-014,016 | — | §18.4-2 |
| FR-TRF-004 | UC-20 | SM-03 | RULE-015,018,027 | D-T06 Proposed | §18.4-3,5 |
| FR-TRF-005 | UC-20 | SM-03 | RULE-017 | — | §18.4-4 |
| FR-OPN-001 | UC-21 | SM-04 | RULE-021,023 | **D-B09 Open** | §18.5-1 |
| FR-OPN-002 | UC-21 | SM-04 | RULE-021 | D-B09 Open | §18.5-1 |
| FR-OPN-003 | UC-22 | SM-04 | RULE-004,005,021 | — | §18.5-2 |
| FR-OPN-004 | UC-22 | SM-04 | RULE-021 | — | §18.5-2 |
| FR-OPN-005 | UC-23 | SM-04 | RULE-022 | — | §18.5-3 |
| FR-OPN-006 | UC-23 | SM-04 | RULE-018,022,027 | D-T06 Proposed | §18.5-4 |
| FR-AUD-001 | UC-33 | SM-01–05 | RULE-018,027,028 | D-B16 Open, D-T01 Proposed | §18.6-2 |
| FR-AUD-002 | UC-34 | SM-01/02/03/05 | RULE-012 | D-B08 Proposed | §18.6-3 |
| FR-AUD-003 | UC-34 | SM-05 | RULE-012,026 | **D-B08 Proposed** | SRS AC Proposed |
| FR-AUD-004 | UC-16/18/20/23 | SM-01–05 | RULE-018,027 | D-T06 Proposed | §18.2-5, §18.4-5 |

**Rekap:** 27 FR unik, 10 UC lifecycle (+ UC-33 sebagai konteks audit), 6 diagram, 5 objek, 22 RULE, 0 Requirement ID baru.

---

## 13. BUSINESS RULES MAPPING

| RULE | Penerapan pada state machine |
|---|---|
| RULE-003 | Guard Create pada SM-01/02/04; master nonaktif ditolak |
| RULE-004 | Guard qty > 0 saat Draft dan input fisik opname |
| RULE-005 | Guard presisi kuantitas (D-B05 Proposed) |
| RULE-007–009 | Guard serial saat Draft dan Post; status serial berubah saat ke Posted |
| RULE-010 | Invariant Draft pada SM-01/02/03 — saldo tidak berubah |
| RULE-011 | Guard permission pada semua transisi Post/otorisasi |
| RULE-012 | Posted immutable; self-loop "edit/hapus ditolak" |
| RULE-013 | Action +saldo tujuan pada SM-01 |
| RULE-014 | Guard qty ≤ saldo pada SM-02/03; rollback tanpa negative stock |
| RULE-015 | Dua movement atomik pada SM-03 |
| RULE-016 | Guard asal ≠ tujuan & aktif pada SM-03 |
| RULE-017 | Guard total gabungan tetap pada SM-03 |
| RULE-018 | Setiap transisi ke Posted menghasilkan movement berreferensi |
| RULE-021 | Snapshot opname dan formula variance pada SM-04 |
| RULE-022 | Guard alasan & otorisasi IA pada SM-04/05 |
| RULE-023 | **Tidak diterapkan** — D-B09 Open; tidak ada state Locked |
| RULE-024 | Nomor unik saat Create; guard post ulang |
| RULE-025 | Pembatalan Draft tidak menghasilkan movement — belum dijadikan state, D-B15 Proposed |
| RULE-026 | SM-05 sebagai objek terpisah berreferensi + alasan |
| RULE-027 | `START TXN → COMMIT / ROLLBACK` pada semua transisi Post |
| RULE-028 | Audit mencatat waktu; zona final D-T01 Proposed |

---

## 14. DECISION STATUS MAPPING

Status berikut dibaca ulang dari BDL dan dikonfirmasi SRS Review §8. **Tidak ada status yang dinaikkan.**

| Decision ID | Status | Dampak |
|---|---|---|
| D-B03 | Proposed | Guard serial unik global |
| D-B04 | Proposed | Guard lokasi aktif/berbeda |
| D-B05 | Proposed | Guard kuantitas presisi & serialized integer |
| D-B06 | Proposed | Referensi sumber inbound opsional |
| D-B07 | Proposed | Draft/Posted outbound + permission |
| **D-B08** | **Proposed** | SM-05 provisional; detail koreksi belum final |
| **D-B09** | **Open** | SM-04 tanpa state Locked, tanpa hard lock, concurrency policy belum final |
| D-B10 | Proposed | Tidak memengaruhi 5 SM |
| **D-B11** | **Deferred** | Tidak ada state/transisi Return |
| **D-B12** | **Deferred** | Tidak ada state Batch/Expiry |
| D-B13 | Proposed | Tidak memengaruhi 5 SM |
| D-B14 | Proposed | Tidak ada aktivitas Opening Balance |
| **D-B15** | **Proposed** | Cancelled bukan state; guard anti-post-ulang rancangan |
| **D-B16** | **Open** | Tidak ada retention period audit |
| **D-B17** | **Deferred** | Tidak ada transisi Export |
| D-T01 | Proposed | Waktu dicatat; zona Asia/Jakarta belum final |
| D-T02 / D-T03 | Accepted | Tidak memengaruhi state |
| D-T04 | Proposed | Tidak memengaruhi state |
| D-T05 | Proposed | Guard permission backend rancangan |
| **D-T06** | **Proposed** | Region transaksi atomik pada semua Post |
| D-T07 / D-T08 | Proposed | Tidak memengaruhi state |

---

## 15. UNRESOLVED STATE DECISIONS

| Item | Status | Dampak |
|---|---|---|
| `Cancelled` sebagai state | **Unresolved — D-B15 Proposed** | Tidak digambarkan sebagai state final |
| Idempotensi Post ulang | **D-B15 Proposed** | Guard "masih Draft" dipakai, AC belum final |
| `Validated` sebagai state | **Ditolak sebagai state** | Tidak ada dasar BRA/SRS |
| Location lock / `Locked` | **D-B09 Open** | Tidak ada state Locked, hard lock, atau concurrency policy final |
| Status `Approved` / `Completed` opname | **Tidak bersumber** | Hanya Draft → Posted |
| Retensi audit | **D-B16 Open** | Tidak ada angka/periode |
| Detail koreksi & nama status | **D-B08 Proposed** | ST-REV-01/02 provisional |
| Hak posting Warehouse Operator | **UD-03 unresolved reference** | Bukan Decision ID resmi |
| Zona waktu timestamp | **D-T01 Proposed** | Waktu dicatat tanpa zona final |
| Strategi transaction/lock | **D-T06 Proposed** | Atomicitas sebagai desain, bukan implementasi final |
| `AUD-01`–`AUD-11` | Menunggu review | Belum diterapkan |

---

## 16. FINAL REVIEW CHECKLIST

- [x] 6 file PlantUML tersedia (1 pola umum + 5 spesialisasi objek).
- [x] `README.md`, `state-machine-specifications.md`, `state-transition-matrix.md` tersedia.
- [x] Setiap diagram memiliki satu `@startuml`/`@enduml`, judul, initial pseudostate, transisi ber-event dan guard, serta final pseudostate.
- [x] Draft tidak mengubah saldo pada SM-01/02/03/04.
- [x] Posting sukses mengubah saldo atomik dan menghasilkan movement.
- [x] Posting gagal melakukan rollback penuh.
- [x] Posted immutable; edit/hapus langsung ditolak.
- [x] Tidak ada state `Cancelled`, `Validated`, `Locked`, `Approved`, `Completed`, atau `Reversed` pada dokumen asli.
- [x] Reversal/Adjustment adalah dokumen terpisah yang mereferensikan dokumen awal.
- [x] `D-B08` Proposed, `D-B09` Open, `D-B16` Open, `D-B15` Proposed, `D-T06` Proposed dipertahankan.
- [x] `D-B11`, `D-B12`, `D-B17` berada di luar MVP.
- [x] 27 FR, 10 UC, 22 RULE, dan AC dapat ditelusuri pada matriks.
- [x] Konsisten dengan Use Case dan Activity Diagram yang telah disetujui.
- [x] Tidak ada Requirement ID baru.
- [x] Tidak ada source code, migration, database, ERD, Data Dictionary, konfigurasi, package, atau perintah database.

---

**Batasan yang dipatuhi:** hanya artefak dokumentasi State Machine yang dibuat di `docs/system-design/03-state-machine-diagrams/`. Dokumen sumber dan seluruh source/configuration tidak diubah.
