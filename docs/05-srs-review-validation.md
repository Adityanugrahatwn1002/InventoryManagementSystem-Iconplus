# SRS REVIEW & VALIDATION

## Inventory Management System — Hasil Audit Dokumen Requirement

## 1. DOCUMENT INFORMATION

| Atribut | Nilai |
|---|---|
| Document Title | SRS Review & Validation |
| Document ID | Akan ditetapkan setelah dokumen disetujui |
| Version | 0.1 - Draft for Review |
| Date | 24 November 2026 |
| Owner | Pengembang proyek / pemilik proyek |
| Document Status | Draft - menunggu review dan persetujuan |
| Project Type | Proyek pribadi / prototype pembelajaran PKL. |
| Object of Audit | `docs/04-software-requirements-specification.md` v0.1 — Draft for review |
| Audit Result | READY WITH CONDITIONS |
| Related Documents | `AGENTS.md`; BRA v1.0; RTA v0.1; BDL v0.1; SRS v0.1; hasil audit sebelumnya |

**Pernyataan status proyek:** Dokumen ini mendokumentasikan hasil audit untuk proyek pribadi/prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Proses bisnis yang dirujuk adalah rancangan berdasarkan BRA dan asumsi yang masih dapat ditinjau. Gunakan hanya data dummy/sintetis.

**Pernyataan batasan audit:** Audit ini bersifat **read-only**. Tidak ada koreksi yang diterapkan pada SRS, BRA, RTA, BDL, source code, migration, database, atau konfigurasi. Seluruh rekomendasi pada dokumen ini menunggu persetujuan pengembang.

---

## 2. AUDIT OBJECTIVE AND SCOPE

**Tujuan audit:**
- Menilai **completeness, consistency, accuracy**, dan **readiness for System Design** dari SRS.
- Memverifikasi jumlah dan identitas requirement secara individual, bukan hanya total.
- Memastikan status keputusan, batas MVP/Future, aturan bisnis stok, dan acceptance criteria tidak bertentangan dengan sumber yang disetujui.
- Mendokumentasikan temuan untuk ditinjau pengembang sebelum ada perubahan apa pun.

**Lingkup:**
- SRS v0.1 sebagai objek audit.
- BRA, RTA, BDL, dan `AGENTS.md` sebagai sumber kebenaran pembanding.
- Hasil SRS Review & Validation sebelumnya sebagai ringkasan temuan yang didokumentasikan di sini.

**Di luar lingkup:**
- Koreksi otomatis pada SRS atau dokumen sumber lain.
- Implementasi kode, migration, perubahan database, konfigurasi, atau paket.
- Penetapan keputusan yang masih Open, Proposed, atau Deferred.

**Metode:** Pemeriksaan silang terhadap ID, jumlah, field, status, rujukan, dan rantai traceability; verifikasi individual untuk 51 FR, 19 NFR, 28 RULE, 8 REP, 25 Decision ID, dan 168 BRA ID.

---

## 3. SOURCE DOCUMENTS

| No | Sumber | Peran dalam audit | Bagian yang dipakai |
|---:|---|---|---|
| 1 | `AGENTS.md` | Batasan kerja, aturan bisnis kritis, kontrol usulan | Sumber kebenaran dan batasan; kontrol D-B09; data dummy; testing |
| 2 | `docs/01-business-requirements-analysis.md` | Sumber requirement utama (BRA v1.0) | §4.1–§4.2, §5, §7.5, §8–§10, §11.5, §12–§13, §15–§19, §21–§23 |
| 3 | `docs/02-requirements-traceability-analysis.md` | Pemetaan dan jumlah dasar (RTA v0.1) | §5.3, §6, §7.1–§7.10, §8–§9, §11–§14 |
| 4 | `docs/03-business-decision-log.md` | Status keputusan (BDL v0.1) | §4–§5, §6–§9 |
| 5 | `docs/04-software-requirements-specification.md` | Objek audit (SRS v0.1) | §1–§17 |
| 6 | Hasil SRS Review & Validation sebelumnya | Ringkasan temuan yang didokumentasikan | AUD-01–AUD-11, readiness assessment |

---

## 4. REQUIREMENT COMPLETENESS RESULTS

| Kategori | Jumlah | Hasil verifikasi |
|---|---:|---|
| Functional Requirements | **51** | 51 ID di SRS sama persis dengan BRA §9.1–§9.8; 12 modul (A–L) |
| Non-Functional Requirements | **19** | 19 kolom di SRS §10.1–§10.9; 8 field per NFR |
| Business Rules | **28** | RULE-001–RULE-028 di SRS §8 |
| Reporting Requirements | **8** | REP-01–REP-08 di SRS §11 |
| Decision IDs | **25** | D-B01–D-B17 dan D-T01–D-T08 di SRS §14.8 |
| RTA Requirement IDs | **168** | 7 GOAL + 8 KPI + 15 BR + 51 FR + 19 NFR + 28 RULE + 8 REP + 10 ASM + 10 RISK + 12 Q |
| Requirement gaps | **12** | SRS §15.1 = RTA §11 |
| Conflicts | **13** | SRS §15.2 = RTA §12 |

**Distribusi FR per modul:** A Authentication 3, B User Management 3, C Master Data 6, D Warehouse & Location 3, E Stock Inbound 6, F Stock Outbound 6, G Stock Transfer 5, H Stock Opname 6, I Stock Balance & Stock Card 3, J Dashboard 2, K Reporting 4, L Audit Trail 4.

**Distribusi NFR:** SEC 5, INT 3, REL 1, PERF 2, USE 2, MNT 2, COMP 1, OPS 1, LOG 1, DOC 1.

**Verifikasi field:**
- Seluruh **51 FR** memuat tepat **19 field**: Requirement ID, Requirement Name, Description, Source Reference, Requirement Status, Implementation Priority, MVP / Future Scope, Related User Roles, Preconditions, Trigger, Input Data, Business Process, Validation Rules, Business Rules, Authorization Requirements, Expected Output, Alternative Flow, Error Handling, Acceptance Criteria.
- **Tidak ada field FR yang benar-benar kosong.**
- Penanda `— (tidak ada RULE khusus)` pada FR-OUT-006, FR-STK-003, FR-REP-004 = **not applicable**, sesuai RTA §7.4.
- Penanda `belum final`, `menunggu D-Bxx`, `Open`, `Tidak tersedia pada MVP` = **undetermined / menunggu persetujuan**, bukan asumsi tersembunyi.
- Satu inkonsistensi teks pada SRS §7: "Format 20 field" vs 19 field aktual → **AUD-07**.
- SRS memuat 160 dari 168 BRA ID secara eksplisit; RISK-02–RISK-09 hanya tercakup lewat rentang → **AUD-04**.

---

## 5. FUNCTIONAL REQUIREMENTS AUDIT

**Hasil positif:**
- 51 ID FR SRS identik dengan 51 ID FR BRA; tidak ada ID fiktif atau hilang.
- Prioritas Must/Should/Could sesuai BRA, termasuk FR-MD-005, FR-DASH-002, FR-REP-003, FR-REP-004, FR-AUD-003.
- Perilaku stok sesuai BRA §9: Draft netral; posting inbound/outbound mengubah saldo + movement; transfer dua movement atomik dengan total tetap; opname snapshot/variance/alasan/movement; serial unik dan count-consistent; audit mencatat aktor/waktu, Posted immutable, reversal berreferensi.
- RULE-023 pada FR-OPN-001/002 tetap **Open D-B09** dan tidak diimplementasikan.
- FR-DASH-001 tidak menyelesaikan konflik D-B13 secara sepihak.

**Temuan terkait:** AUD-02, AUD-05, AUD-07, AUD-09.

---

## 6. NON-FUNCTIONAL REQUIREMENTS AUDIT

**Hasil positif:**
- Seluruh 19 NFR BRA §13 hadir dengan 8 field lengkap di SRS §10.
- Target mengikuti BRA, tanpa standar buatan:
  - NFR-PERF-001: ≤3 detik pada 1.000 item dan 5.000 movement, eksplisit **bukan SLA produksi**.
  - NFR-INT-002 `0 kasus stok negatif` dan NFR-INT-003 `100% movement memiliki referensi` berasal dari KPI-02/KPI-01.
  - NFR-COMP-001: PHP minimum 8.3 = **Accepted D-T02**; versi lain belum final.
- Status NFR konsisten dengan BDL: D-T03 Accepted pada NFR-USE-002, D-T04 Proposed pada NFR-MNT-002, D-B16 Open pada NFR-LOG-001.
- Tidak ada komitmen SLA, retensi, atau standar keamanan produksi yang ditambahkan.

**Temuan terkait:** AUD-03, AUD-10.

---

## 7. BUSINESS RULES AUDIT

| Area aturan | Status audit |
|---|---|
| RULE-010 / RULE-025 — Draft tidak mengubah stok; batal Draft tanpa movement | RULE-010 aktif; RULE-025 masih gap **D-B15** |
| RULE-013 / RULE-014 / RULE-018 / RULE-027 — posting, movement berreferensi, atomik | Konsisten di FR-IN-005, FR-OUT-005, FR-TRF-004, FR-AUD-004 |
| RULE-014 — tidak ada stok negatif | Dijaga di FR-OUT-003/005, FR-TRF-003, S A, KPI-02 |
| RULE-015–017 — transfer beda lokasi aktif, dua movement, total tetap | Konsisten di FR-TRF-002/004/005, S B, S E |
| RULE-007–009 — serial unik, satu status/lokasi, jumlah = qty bulat | Konsisten; unique global masih **D-B03 Proposed** |
| RULE-021 / RULE-022 — opname snapshot dan variance = fisik − snapshot | Konsisten; S D |
| RULE-012 / RULE-026 — Posted immutable, reversal/adjustment beralasan | Konsisten; detail **D-B08 Proposed** |
| RULE-019 / RULE-020 — saldo tidak diedit manual; opening balance berjejak | Prinsip ada, **FR khusus belum ada — D-B14** |
| RULE-023 — lock lokasi opname | **Open D-B09**, tidak diimplementasikan |
| RULE-028 — timezone Asia/Jakarta | **D-T01 Proposed**; konflik `config/app.php` UTC tercatat |

**Simpulan:** Tidak ditemukan kontradiksi antara RULE dan FR. Yang ada adalah gap yang memang sudah ditandai SRS, bukan keputusan diam-diam.

**Temuan terkait:** AUD-02.

---

## 8. DECISION STATUS AUDIT

| Status | Jumlah | Decision ID | Kesesuaian dengan BDL |
|---|---:|---|---|
| Accepted | 2 | D-T02 (PHP minimum 8.3), D-T03 (Blade + Bootstrap 5) | Sesuai |
| Proposed | 18 | D-B01–D-B08, D-B10, D-B13–D-B15; D-T01, D-T04–D-T08 | Sesuai |
| Open | 2 | D-B09, D-B16 | Sesuai |
| Deferred | 3 | D-B11, D-B12, D-B17 | Sesuai |
| Rejected | 0 | — | Sesuai |

- D-B09 tidak dinaikkan menjadi Accepted meski RULE-023 aktif di BRA.
- D-B16 tidak menghasilkan angka retensi final.
- D-B11, D-B12, D-B17 tidak masuk MVP.
- D-B13 tetap Proposed meski SRS memakai interpretasi SKU count + ringkasan per satuan.
- SRS §5.1–§5.5, §14.8, §15.4, dan §16.4–§16.7 saling konsisten.

**Tidak ada temuan pada bagian ini.**

---

## 9. ACCEPTANCE CRITERIA AUDIT

- **24 FR** berlabel **Proposed Acceptance Criteria**.
- **27 FR** tanpa label Proposed, tetapi setelah diperiksa individual seluruhnya dapat ditelusur ke **BRA §18.1–§18.6** dan/atau **Skenario A–E BRA §19**. Tidak ada AC buatan AI yang tidak diberi label.
- Skenario A–E di SRS §13.4 sesuai BRA §19: inbound 0→10→6→tolak 7; transfer 10+2→7+5 total 12; serial 2 unik; opname 8−7=−1; rollback atomik.
- AC KPI-01–KPI-08 mengikuti target BRA §4.2.
- AC RULE-023 sengaja tidak aktif karena D-B09 Open.
- §15.5 mencakup seluruh FR berlabel Proposed, termasuk yang ditulis dengan rentang (`FR-STK-001–003`, `FR-DASH-001–002`, `seluruh FR-REP-001–004`) — bukan omission.

**Temuan terkait:** AUD-03, AUD-08, AUD-09, AUD-10, AUD-11.

---

## 10. MVP AND FUTURE SCOPE AUDIT

| Fitur | Status BDL | Representasi SRS | Hasil |
|---|---|---|---|
| Export Excel/PDF | D-B17 Deferred | §16.2/§16.3 Future; FR-REP-004 di luar MVP; REP-08 tanpa export | Benar, catatan AUD-05 |
| Retur barang | D-B11 Deferred | §16.3 di luar MVP | Benar |
| Batch/lot & expiry | D-B12 Deferred | §16.3 di luar MVP | Benar |
| Multi-warehouse, QR/barcode, approval bertingkat | Future/Deferred | §16.2/§16.3 | Benar |
| Lock lokasi opname | D-B09 Open | Tidak diimplementasikan | Benar |
| Retensi audit | D-B16 Open | Tidak ada angka retensi | Benar |
| Dashboard | D-B13 Proposed | Konflik dicatat, tidak diputus | Benar |

**Simpulan:** Tidak ada fitur Deferred yang diaktifkan pada MVP. Satu catatan pembukuan pada AUD-05.

---

## 11. REQUIREMENTS TRACEABILITY AUDIT

Rantai **BRA → RTA → BDL → SRS → Acceptance Criteria** dinyatakan di SRS §14.2 dan dijalankan konsisten:

- §14.1 jumlah dasar sama dengan RTA §5.3: 168 BRA ID.
- §14.2 GOAL/KPI dan §14.3 BR dipetakan per ID, cocok dengan RTA §7.1–§7.3.
- §14.4 memetakan 51 FR per modul dan per ID.
- §10 memetakan 19 NFR per kolom; §8 memetakan 28 RULE per baris; §11 memetakan 8 REP; §14.8 memetakan 25 Decision ID; §15.3 memetakan 12 Q; §16.8 memetakan 10 ASM.
- §15.1 dan §15.2 mereproduksi 12 gap RTA §11 dan 13 konflik RTA §12 secara lengkap.

**Kesenjangan:** RISK hanya muncul sebagai rentang `RISK-01–RISK-10` di §14.1 dan §14.9; sebagian unresolved item RTA §14 tidak masuk §15 → **AUD-04** dan **AUD-06**.

---

## 12. AUDIT FINDINGS

### AUD-01

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-01 |
| **Priority** | Critical |
| **Source Location** | SRS §17 baris 1942–1949; SRS §15.4 baris 1869–1873; BDL §9 baris 159–172 |
| **Related Requirement ID** | D-B09, D-B16, D-B13, D-B14, D-B15, D-B08, D-T01, D-T04–D-T08 |
| **Finding Description** | SRS menyatakan "cukup sebagai dasar System Design", tetapi bersamaan mencatat 2 Open, 18 Proposed, dan sejumlah FR/AC gap. Pernyataan itu hanya benar untuk modul stabil, bukan seluruh sistem. |
| **Impact** | Desain concurrency opname, retensi audit, timezone, opening balance, dan reversal dapat dibekukan di atas keputusan yang belum disetujui; biaya revisi desain tinggi. |
| **Recommendation** | Nyatakan cakupan readiness per modul; minta approval D-B09/D-B16 terlebih dahulu, lalu keputusan Proposed yang mengikat desain (D-B08, D-B13, D-B14, D-B15, D-T01, D-T04–D-T06). |
| **Approval Status** | Belum disetujui — tidak ada tindakan yang diambil; menunggu keputusan pemilik proyek. |

### AUD-02

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-02 |
| **Priority** | Critical |
| **Source Location** | SRS §8.9 baris 1338–1340; SRS §12.5 baris 1564–1565; SRS §15.1 baris 1823–1825; BDL §9 baris 167–168 |
| **Related Requirement ID** | RULE-020, RULE-024, RULE-025, RULE-027; D-B14, D-B15; FR-IN-001, FR-IN-005, FR-OUT-001, FR-OUT-005, FR-TRF-001, FR-TRF-004 |
| **Finding Description** | Prinsip opening balance, cancel Draft, dan idempotent posting ada, tetapi tidak ada FR/AC final. SRS sudah menandainya tepat sebagai gap, namun kontrak perilakunya belum dispesifikasi. |
| **Impact** | System Design berisiko menghilangkan doc-type opening, status guard Draft→Posted, atau constraint nomor unik; menyerang RULE-020/024/025/027, KPI-02, dan kewajiban automated test NFR-MNT-002. |
| **Recommendation** | Ajukan FR dan AC baru melalui change control BRA sesuai D-T08 sebelum design freeze modul inbound/outbound/transfer/opname. |
| **Approval Status** | Belum disetujui — tidak ada FR baru yang dibuat; menunggu change control BRA. |

### AUD-03

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-03 |
| **Priority** | Critical |
| **Source Location** | SRS §13.2–§13.5; SRS §15.5 baris 1875–1880; RTA §11 baris 666 |
| **Related Requirement ID** | 24 FR berlabel Proposed; 19 NFR; 8 REP; KPI-08 |
| **Finding Description** | BRA hanya menyediakan 6 kelompok AC formal (§18.1–§18.6). SRS menambahkan Proposed AC untuk FR serta AC ringkas untuk NFR/REP, tetapi semuanya masih menunggu persetujuan. |
| **Impact** | Test case tidak dapat distandardisasi; validasi hasil tidak bisa menjadi gerbang kualitas yang sama untuk semua modul. |
| **Recommendation** | Setujui AC tambahan lewat change control BRA; pertahankan label Proposed sampai disetujui. |
| **Approval Status** | Belum disetujui — label Proposed dipertahankan apa adanya. |

### AUD-04

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-04 |
| **Priority** | Medium |
| **Source Location** | SRS §14.1 baris 1685; SRS §14.9 baris 1811; bandingkan RTA §7.9 baris 541–554 |
| **Related Requirement ID** | RISK-01–RISK-10, khususnya RISK-02–RISK-09 |
| **Finding Description** | SRS hanya menulis rentang `RISK-01–RISK-10`; tidak ada tabel risiko→mitigasi→bagian SRS. Delapan ID hanya tercakup implisit. |
| **Impact** | Tidak dapat diverifikasi bahwa mitigasi RISK-03, RISK-05, RISK-06, RISK-08, RISK-09 benar-benar masuk desain; klaim cakupan 168 ID kurang dapat dibuktikan untuk kelompok ini. |
| **Recommendation** | Tambahkan sub-bagian pada §14.9 dengan satu baris per RISK-01…RISK-10. |
| **Approval Status** | Belum disetujui — tidak ada penambahan tabel. |

### AUD-05

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-05 |
| **Priority** | Medium |
| **Source Location** | SRS §7.K baris 1169–1188; SRS §14.4 baris 1743–1745; SRS §17 baris 1940 |
| **Related Requirement ID** | FR-REP-004, D-B17, REP-08 |
| **Finding Description** | FR-REP-004 sudah benar berstatus Future Scope dan tidak termasuk MVP, tetapi rekap §14.4 dan kalimat §17 tetap menyebut 51 FR tanpa memisahkan 50 MVP + 1 Future. |
| **Impact** | Pembaca desain dapat mengira seluruh 51 FR harus masuk MVP; potensi scope creep export. |
| **Recommendation** | Tulis `51 FR = 50 MVP + 1 Future (FR-REP-004/D-B17)` pada rekap dan kesimpulan. |
| **Approval Status** | Belum disetujui — angka dan status SRS tidak diubah. |

### AUD-06

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-06 |
| **Priority** | Medium |
| **Source Location** | SRS §15 baris 1818; RTA §14 baris 840; BDL §9 baris 170 |
| **Related Requirement ID** | GOAL-01, GOAL-05, D-B09 |
| **Finding Description** | §15 menyatakan mencatat seluruh item belum selesai menurut RTA §11, §12, §14, dan BDL, tetapi tidak memuat RTA §14 "GOAL-01 dan GOAL-05 tidak ada KPI langsung" serta tidak eksplisit memuat pertanyaan BDL "siapa boleh membatalkan/mengesampingkan sesi opname". |
| **Impact** | Register unresolved tidak lengkap; coverage gap goal–KPI dan otorisasi override opname dapat terlewat saat design review. |
| **Recommendation** | Tambahkan dua baris ke §15.1/§15.4 dengan status mengikuti RTA/BDL, tanpa memutusnya. |
| **Approval Status** | Belum disetujui — §15 tidak diubah. |

### AUD-07

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-07 |
| **Priority** | Low |
| **Source Location** | SRS §7 baris 179 |
| **Related Requirement ID** | Seluruh 51 FR |
| **Finding Description** | Teks tertulis "Format 20 field dipakai untuk seluruh 51 FR", padahal tiap blok berisi tepat 19 field sesuai daftar yang ditentukan. |
| **Impact** | Pembaca mungkin mencari field ke-20 yang tidak ada; mengganggu kepercayaan terhadap struktur audit. |
| **Recommendation** | Ubah menjadi "Format 19 field". |
| **Approval Status** | Belum disetujui — teks SRS tidak diubah. |

### AUD-08

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-08 |
| **Priority** | Low |
| **Source Location** | SRS §7.H blok FR-OPN-002; BRA §9.6 baris 382 |
| **Related Requirement ID** | FR-OPN-002 |
| **Finding Description** | AC ditandai berasal dari BRA §18.5, tetapi pernyataan "nilai snapshot tidak berubah karena transaksi berikutnya" ada di kriteria ringkas BRA §9.6. BRA §18.5 hanya menyebut snapshot disimpan. |
| **Impact** | Kesalahan sitasi; tidak mengubah perilaku, tetapi melemahkan jejak AC. |
| **Recommendation** | Ubah rujukan menjadi `BRA §9.6 + §18.5`. |
| **Approval Status** | Belum disetujui — rujukan tidak diubah. |

### AUD-09

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-09 |
| **Priority** | Low |
| **Source Location** | SRS §7.E blok FR-IN-001 |
| **Related Requirement ID** | FR-IN-001, D-B15 |
| **Finding Description** | Satu field Acceptance Criteria berisi kriteria BRA §18.2 sekaligus fragmen "**Proposed Acceptance Criteria** menunggu D-B15" untuk cancel Draft. |
| **Impact** | Sulit membedakan bagian AC yang sudah bersumber BRA dan bagian yang masih usulan. |
| **Recommendation** | Pisahkan menjadi dua baris: `BRA §18.2 — …` dan `Proposed AC (cancel Draft, D-B15) — …`. |
| **Approval Status** | Belum disetujui — AC tidak dipisahkan. |

### AUD-10

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-10 |
| **Priority** | Low |
| **Source Location** | SRS §13.5 baris 1657–1666 |
| **Related Requirement ID** | NFR-SEC-001–005, NFR-INT-001–003, NFR-USE-001–002, NFR-MNT-001–002 |
| **Finding Description** | Beberapa NFR digabung dalam satu baris dengan beberapa kriteria, bukan satu baris per ID. |
| **Impact** | Verifikasi per NFR kurang granular, meski tiap NFR sudah memiliki 8 field lengkap di §10. |
| **Recommendation** | Ubah menjadi satu baris per NFR bila diperlukan untuk test matrix. |
| **Approval Status** | Belum disetujui — tabel §13.5 tidak diubah. |

### AUD-11

| Field | Nilai |
|---|---|
| **Finding ID** | AUD-11 |
| **Priority** | Low |
| **Source Location** | SRS §7.K baris 1188 |
| **Related Requirement ID** | FR-REP-004, D-B17 |
| **Finding Description** | AC berbunyi "Export hanya boleh diuji setelah D-B17 tidak lagi Deferred; pada MVP tidak ada kriteria penerimaan export." Ini pernyataan status, bukan kriteria uji Given–When–Then. |
| **Impact** | Jika D-B17 berubah, tidak ada dasar pengujian export; untuk MVP memang disengaja, tetapi tetap tercatat sebagai AC yang belum ada. |
| **Recommendation** | Tandai sebagai `AC ditunda — D-B17 Deferred` di §15.5 dan buat GWT hanya setelah keputusan berubah. |
| **Approval Status** | Belum disetujui — AC tetap apa adanya. |

---

## 13. RECOMMENDED CORRECTIONS

| Finding ID | Prioritas | Koreksi yang disarankan | Target yang diusulkan | Status |
|---|---|---|---|---|
| AUD-01 | Critical | Batasi klaim readiness per modul; minta approval Open/Proposed decision yang mengikat desain | SRS §17, §15.4 | Belum disetujui |
| AUD-02 | Critical | Ajukan FR+AC opening balance, cancel Draft, double-posting via change control BRA/D-T08 | BRA → RTA → BDL → SRS | Belum disetujui |
| AUD-03 | Critical | Setujui AC tambahan untuk FR/NFR/REP; pertahankan label Proposed sampai disetujui | BRA §18, SRS §13, §15.5 | Belum disetujui |
| AUD-04 | Medium | Tambah pemetaan individual RISK-01–RISK-10 di §14.9 | SRS §14.9 | Belum disetujui |
| AUD-05 | Medium | Tulis `51 FR = 50 MVP + 1 Future (FR-REP-004)` | SRS §14.4, §17 | Belum disetujui |
| AUD-06 | Medium | Tambah GOAL-01/GOAL-05 coverage gap dan otorisasi override opname ke §15 | SRS §15.1, §15.4 | Belum disetujui |
| AUD-07 | Low | `20 field` → `19 field` | SRS §7 | Belum disetujui |
| AUD-08 | Low | Rujukan AC FR-OPN-002 → BRA §9.6 + §18.5 | SRS §7.H | Belum disetujui |
| AUD-09 | Low | Pisahkan AC BRA dan Proposed pada FR-IN-001 | SRS §7.E | Belum disetujui |
| AUD-10 | Low | Satu baris AC per NFR di §13.5 | SRS §13.5 | Belum disetujui |
| AUD-11 | Low | Tandai AC FR-REP-004 sebagai "ditunda — D-B17 Deferred" | SRS §7.K, §15.5 | Belum disetujui |

**Pernyataan penting:** Seluruh koreksi pada tabel ini adalah **usulan**. SRS, BRA, RTA, BDL, source code, migration, database, dan konfigurasi belum diubah sama sekali. AUD-01 sampai AUD-11 **belum otomatis diperbaiki**.

---

## 14. FINAL READINESS ASSESSMENT

# READY WITH CONDITIONS

SRS v0.1 **dapat dipakai sebagai dasar bagi sebagian modul untuk mulai dirancang secara provisional**, tetapi **belum seluruh sistem siap untuk final design**.

Modul yang dapat dirancang secara provisional antara lain struktur master data, autentikasi/otorisasi dasar, saldo dan kartu stok, laporan layar, serta sebagian besar alur inbound/outbound/transfer/opname — selama penanda Proposed dihormati dan tidak diubah menjadi keputusan final.

Kondisi yang masih mengikat:

- **D-B09 tetap Open** — proteksi lokasi saat sesi opname aktif belum disetujui; RULE-023 tidak boleh diimplementasikan.
- **D-B16 tetap Open** — retensi audit log dan transaksi belum diputus; tidak boleh ada angka retensi final.
- **18 keputusan Proposed tetap membutuhkan persetujuan pemilik proyek** — statusnya tidak boleh dinaikkan menjadi Accepted tanpa persetujuan.
- **Fitur Deferred tidak boleh masuk MVP** — D-B11, D-B12, dan D-B17 tetap di luar MVP.
- **AC masih sebagian Proposed** — belum dapat dijadikan gerbang verifikasi final.
- **AUD-01 sampai AUD-11 belum otomatis diperbaiki** — seluruhnya masih menunggu tinjauan dan persetujuan pengembang.

Integritas stok utuh: Draft netral; posting atomik dan menghasilkan movement berreferensi; tidak ada negative stock; transfer menjaga total dan membuat dua movement dalam satu transaksi; serial unik dan count-consistent; opname = fisik − snapshot; Posted immutable dengan reversal/adjustment berreferensi dan beralasan.

---

## 15. NEXT STEPS

1. Tinjau dan setujui draft dokumen ini terlebih dahulu sebelum disimpan sebagai `docs/05-srs-review-validation.md`.
2. Putuskan D-B09 dan D-B16 sebelum desain concurrency opname dan retensi audit dibekukan.
3. Tetapkan status final keputusan Proposed yang mengikat desain, terutama D-B08, D-B13, D-B14, D-B15, D-T01, D-T04–D-T06.
4. Ajukan FR/AC tambahan untuk opening balance, cancel Draft, dan double-posting melalui change control BRA sesuai D-T08.
5. Setujui AC tambahan untuk FR, NFR, dan REP; pertahankan label Proposed sampai ada persetujuan.
6. Terapkan koreksi AUD-01–AUD-11 hanya setelah persetujuan eksplisit, dan hanya pada dokumen yang ditentukan.
7. Mulai System Design secara provisional untuk modul yang sudah stabil; gunakan gerbang kualitas BRA §22–§23: jangan lanjut modul berikutnya jika tes konsistensi stok modul sebelumnya belum lulus.
8. Gunakan hanya data sintetis, jangan commit `.env`, dan jangan menjalankan perintah database sebelum lingkungan test disetujui.

---

*Akhir dokumen — SRS REVIEW & VALIDATION v0.1 draft.*
