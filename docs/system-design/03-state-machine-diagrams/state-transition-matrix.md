# STATE TRANSITION MATRIX — Inventory Management System

| Atribut | Nilai |
|---|---|
| Versi | 0.1 |
| Status artefak | Draft yang telah direview dan disetujui untuk disimpan |
| Lokasi | `docs/system-design/03-state-machine-diagrams/state-transition-matrix.md` |
| Source diagram | `state-transaction-document.puml`, `state-inbound.puml`, `state-outbound.puml`, `state-transfer.puml`, `state-stock-opname.puml`, `state-correction-document.puml` |

**Pernyataan batasan:** Artefak desain untuk prototype pembelajaran, bukan SOP/kebijakan resmi PT Indonesia Comnets Plus/PLN Icon Plus. ID `SM-*`, `SMO-*`, `ST-*`, dan `EV-*` adalah ID artefak desain, bukan Requirement ID BRA.

---

## 1. MATRIKS TRANSISI — SMO-01 INBOUND (SM-01)

| Object | Current State | Trigger Event | Guard Condition | Action | Next State | Related UC | Related FR | Related RULE | Decision Status | Related AC |
|---|---|---|---|---|---|---|---|---|---|---|
| SMO-01 | Initial | Create [EV-01] | Nomor unik RULE-024; barang & lokasi aktif RULE-003 | Simpan header, baris, referensi sumber; saldo tidak diubah | Draft | UC-15 | FR-IN-001–004, FR-IN-006 | RULE-003,004,010,024 | D-B03/05/06/15 Proposed | §18.2-1 |
| SMO-01 | Draft | Revise [EV-02] | Masih Draft | Simpan ulang baris; saldo tidak diubah | Draft | UC-15 | FR-IN-001–004 | RULE-010 | D-B15 Proposed | §18.2-1 |
| SMO-01 | Draft | Post [EV-03] | Permission RULE-011; master aktif; qty > 0; serial valid; masih Draft | Transaksi atomik: +saldo tujuan, movement INBOUND, update serial, status, audit, COMMIT | Posted | UC-16 | FR-IN-005, FR-AUD-004, FR-AUD-001 | RULE-011,013,018,024,027 | D-B15, D-T06 Proposed | §18.2-2,5; §18.6 |
| SMO-01 | Draft | Post gagal [EV-10] | Tanpa permission / validasi gagal / error | ROLLBACK penuh; saldo, status, serial tetap | Draft | UC-16 | FR-IN-005, FR-AUD-004 | RULE-011,024,027 | D-B15, D-T06 Proposed | §18.2-3 |
| SMO-01 | Posted | Edit/Hapus [EV-11] | Status Posted | Tolak; dokumen tetap utuh | Posted | UC-34 (konteks) | FR-AUD-002 | RULE-012 | D-B08 Proposed | §18.6 |
| SMO-01 | Posted | Final [EV-*] | Lifecycle status selesai | Record tetap tersimpan sebagai histori | Final | UC-16 | FR-IN-005 | RULE-012,018 | — | §18.2 |

## 2. MATRIKS TRANSISI — SMO-02 OUTBOUND (SM-02)

| Object | Current State | Trigger Event | Guard Condition | Action | Next State | Related UC | Related FR | Related RULE | Decision Status | Related AC |
|---|---|---|---|---|---|---|---|---|---|---|
| SMO-02 | Initial | Create [EV-01] | Nomor unik RULE-024; barang & lokasi asal aktif | Simpan baris dan serial tersedia; saldo tidak berkurang | Draft | UC-17 | FR-OUT-001,002,004,006 | RULE-003,004,010,024 | D-B03/07/15 Proposed | §18.3-1 |
| SMO-02 | Draft | Revise [EV-02] | Masih Draft | Simpan ulang; saldo tidak berkurang | Draft | UC-17 | FR-OUT-001,002 | RULE-010 | D-B07/15 Proposed | §18.3-1 |
| SMO-02 | Draft | Post [EV-04] | Permission; serial tersedia; **qty ≤ saldo**; masih Draft | Transaksi atomik: −saldo asal, movement OUTBOUND, serial, status, audit, COMMIT | Posted | UC-18 | FR-OUT-003,005, FR-AUD-004, FR-AUD-001 | RULE-011,014,018,027 | D-B07, D-T06 Proposed | §18.3-2,3; §18.6 |
| SMO-02 | Draft | Post gagal [EV-10] | Saldo kurang / serial invalid / tanpa permission / error | ROLLBACK penuh; tolak tanpa negative stock | Draft | UC-18 | FR-OUT-003,005 | RULE-011,014,027 | D-B07, D-T06 Proposed | §18.3-2 |
| SMO-02 | Posted | Edit/Hapus [EV-11] | Status Posted | Tolak; dokumen tetap utuh | Posted | UC-34 (konteks) | FR-AUD-002 | RULE-012 | D-B08 Proposed | §18.6 |
| SMO-02 | Posted | Final [EV-*] | Lifecycle status selesai | Record tetap tersimpan sebagai histori | Final | UC-18 | FR-OUT-005 | RULE-012,018 | — | §18.3 |

## 3. MATRIKS TRANSISI — SMO-03 TRANSFER (SM-03)

| Object | Current State | Trigger Event | Guard Condition | Action | Next State | Related UC | Related FR | Related RULE | Decision Status | Related AC |
|---|---|---|---|---|---|---|---|---|---|---|
| SMO-03 | Initial | Create [EV-01] | Nomor unik; asal ≠ tujuan; keduanya aktif RULE-016 | Simpan baris; saldo kedua lokasi belum berubah | Draft | UC-19 | FR-TRF-001–003 | RULE-003,010,016,024 | D-B04/15 Proposed | §18.4-1 |
| SMO-03 | Draft | Revise [EV-02] | Masih Draft | Simpan ulang; saldo tetap | Draft | UC-19 | FR-TRF-001–003 | RULE-010 | D-B15 Proposed | §18.4 |
| SMO-03 | Draft | Post [EV-05] | Permission; saldo asal cukup; serial di asal; masih Draft | Transaksi atomik: −asal + TRANSFER_OUT, +tujuan + TRANSFER_IN, serial, status, audit, COMMIT | Posted | UC-20 | FR-TRF-004,005, FR-AUD-004, FR-AUD-001 | RULE-011,014,015,016,017,018,027 | D-T06 Proposed | §18.4-3,4 |
| SMO-03 | Draft | Post gagal [EV-10] | Lokasi sama/nonaktif; saldo kurang; total berubah; error | ROLLBACK kedua movement | Draft | UC-20 | FR-TRF-004,005 | RULE-014,015,016,017,027 | D-T06 Proposed | §18.4-1,2,5 |
| SMO-03 | Posted | Edit/Hapus [EV-11] | Status Posted | Tolak; dokumen tetap utuh | Posted | UC-34 (konteks) | FR-AUD-002 | RULE-012 | D-B08 Proposed | §18.6 |
| SMO-03 | Posted | Final [EV-*] | Lifecycle status selesai | Total stok gabungan tetap; record tersimpan | Final | UC-20 | FR-TRF-005 | RULE-015,017,018 | — | §18.4-4 |

## 4. MATRIKS TRANSISI — SMO-04 STOCK OPNAME SESSION (SM-04)

| Object | Current State | Trigger Event | Guard Condition | Action | Next State | Related UC | Related FR | Related RULE | Decision Status | Related AC |
|---|---|---|---|---|---|---|---|---|---|---|
| SMO-04 | Initial | Open [EV-06] | Lokasi aktif; tanggal wajib FR-OPN-001 | Buat sesi dan rekam snapshot sistem | Draft | UC-21 | FR-OPN-001,002 | RULE-021,023 | **D-B09 Open** (tanpa lock) | §18.5-1 |
| SMO-04 | Draft | Input [EV-07] | Kuantitas valid RULE-004/005 | Simpan hitung fisik; variance = fisik − snapshot | Draft | UC-22 | FR-OPN-003,004 | RULE-004,005,021 | D-B09 Open | §18.5-2 |
| SMO-04 | Draft | Ajukan [EV-08] | Alasan diisi; Inventory Admin RULE-022 | Validasi otorisasi; siapkan penyesuaian | Draft | UC-23 | FR-OPN-005 | RULE-022 | D-B09 Open | §18.5-3 |
| SMO-04 | Draft | Ditolak [EV-10] | Tanpa alasan / bukan IA | Tolak; saldo tidak berubah | Draft | UC-23 | FR-OPN-005 | RULE-022 | D-B09 Open | §18.5-3 |
| SMO-04 | Draft | Post [EV-09] | IA berwenang; alasan ada; variance tersedia | Transaksi atomik: movement OPNAME_ADJUSTMENT, saldo = hasil fisik, status sesi, audit, COMMIT | Posted | UC-23 | FR-OPN-006, FR-AUD-004, FR-AUD-001 | RULE-018,021,022,027 | D-T06 Proposed; D-B09 Open | §18.5-4; §18.6 |
| SMO-04 | Draft | Post gagal [EV-10] | Error sebelum commit | ROLLBACK penuh; saldo tidak parsial | Draft | UC-23 | FR-OPN-006 | RULE-027 | D-T06 Proposed | §18.5-4 |
| SMO-04 | Posted | Final [EV-*] | Lifecycle sesi selesai | Movement dan audit tetap tersimpan | Final | UC-23 | FR-OPN-006 | RULE-018,022 | — | §18.5-4 |

## 5. MATRIKS TRANSISI — SMO-05 REVERSAL/ADJUSTMENT DOCUMENT (SM-05, PROVISIONAL)

| Object | Current State | Trigger Event | Guard Condition | Action | Next State | Related UC | Related FR | Related RULE | Decision Status | Related AC |
|---|---|---|---|---|---|---|---|---|---|---|
| SMO-05 | Posted (dokumen asli) | Edit/Hapus [EV-11] | Status asli Posted | Tolak; **tidak ada transisi status pada dokumen asli** | Posted (asli) | UC-34 | FR-AUD-002 | RULE-012 | D-B08 Proposed | §18.6 |
| SMO-05 | Initial | Create [EV-12] | Referensi dokumen awal + alasan lengkap; IA | Buat dokumen koreksi terpisah; saldo belum diubah | Draft koreksi | UC-34 | FR-AUD-003 | RULE-012,026 | **D-B08 Proposed** | SRS AC Proposed |
| SMO-05 | Draft koreksi | Validasi gagal [EV-10] | Referensi/alasan/otorisasi kurang | Tolak; dokumen asli tetap utuh | Draft koreksi | UC-34 | FR-AUD-003 | RULE-026 | D-B08 Proposed | SRS AC Proposed |
| SMO-05 | Draft koreksi | Post [EV-12] | IA berwenang; referensi & alasan ada | Transaksi atomik: movement pembalik/penyesuaian, audit, COMMIT | Posted koreksi | UC-34 | FR-AUD-003, FR-AUD-004, FR-AUD-001 | RULE-018,026,027 | D-B08, D-T06 Proposed | SRS AC Proposed; §18.6 |
| SMO-05 | Draft koreksi | Post gagal [EV-10] | Error sebelum commit | ROLLBACK penuh; dokumen asli tetap utuh | Draft koreksi | UC-34 | FR-AUD-003, FR-AUD-004 | RULE-026,027 | D-B08, D-T06 Proposed | SRS AC Proposed |
| SMO-05 | Posted koreksi | Edit/Hapus [EV-11] | Status Posted | Tolak; dokumen koreksi tetap utuh | Posted koreksi | UC-34 | FR-AUD-002 | RULE-012 | D-B08 Proposed | §18.6 |
| SMO-05 | Posted koreksi | Final [EV-*] | Lifecycle koreksi selesai | Audit mempertahankan hubungan ke dokumen awal | Final | UC-34, UC-33 konteks | FR-AUD-001,003 | RULE-026 | D-B08 Proposed | §18.6 |

---

## 6. REKAPITULASI

| Item | Jumlah |
|---|---:|
| Objek dimodelkan (kecuali pola umum SMO-00) | 5 |
| Diagram PlantUML | 6 (1 pola umum + 5 spesialisasi) |
| State pada diagram | 12 node (2 per diagram) |
| Transisi pada diagram | 37 (24 transaksi + 7 opname + 6 koreksi) |
| FR unik terpetakan | 27 |
| UC lifecycle terpetakan | 10 (+ UC-33 sebagai konteks audit) |
| RULE terpetakan | 22 |
| Requirement ID baru | 0 |

## 7. CATATAN TRANSISI YANG SENGAJA TIDAK DIBUAT

| Transisi | Alasan |
|---|---|
| Draft → Validated | Tidak ada FR/BRA yang menetapkan `Validated` sebagai state tersimpan |
| Draft → Cancelled | D-B15 Proposed; FR/AC cancel Draft belum final (RTA §11) |
| Draft sesi → Locked | D-B09 Open; RULE-023 belum disetujui; tidak ada hard lock |
| Draft sesi → Approved / Completed | Tidak bersumber dari BRA/SRS |
| Posted → Reversed (dokumen asli) | RULE-012 immutable; koreksi memakai objek terpisah SMO-05 |
| Apapun → Return / Batch / Export | D-B11, D-B12, D-B17 Deferred, di luar MVP |
