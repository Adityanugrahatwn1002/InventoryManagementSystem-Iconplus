# ACTIVITY DIAGRAM ANALYSIS & DESIGN — DOCUMENTATION INDEX

## Inventory Management System

| Atribut | Nilai |
|---|---|
| Lokasi artefak | `docs/system-design/02-activity-diagrams/` |
| Versi | 0.1 |
| Status artefak | Draft yang telah direview dan disetujui untuk disimpan |
| Status desain | **Provisional** untuk alur yang dipengaruhi keputusan Proposed/Open |
| Tahap | System Design — Activity Diagram |
| Project Type | Personal project / prototype pembelajaran PKL |

**Pernyataan batasan:** Dokumen ini adalah artefak desain untuk prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Gunakan hanya data dummy/sintetis.

---

## 1. Tujuan Dokumentasi

Direktori ini menyimpan rancangan Activity Diagram yang telah ditampilkan dan disetujui. Tujuannya adalah mendokumentasikan:

1. Alur aktivitas transaksi stok dari Draft hingga Posted.
2. Swimlane aktor dan sistem sesuai matriks otorisasi BRA §12.
3. Decision node dan guard condition yang bersumber dari business rules.
4. Alternative flow dan exception flow, termasuk rollback transaksi.
5. Keterhubungan Activity Diagram dengan Use Case, Functional Requirement, Business Rule, dan Decision ID.

**AD-ID adalah ID artefak desain, bukan Requirement ID resmi BRA.** Tidak ada Requirement ID baru yang dibuat.

---

## 2. Daftar Activity Diagram

| File | Diagram | Use Case | FR Utama | Deskripsi Singkat |
|---|---|---|---|---|
| `activity-inbound.puml` | Stock Inbound | UC-15, UC-16 | FR-IN-001–006, FR-AUD-004 | Draft penerimaan, validasi barang/lokasi/qty/serial, lalu posting atomik yang menambah saldo dan movement `INBOUND`. |
| `activity-outbound.puml` | Stock Outbound | UC-17, UC-18 | FR-OUT-001–006, FR-AUD-004 | Draft pengeluaran, validasi serial dan saldo, lalu posting atomik yang mengurangi saldo tanpa negative stock. |
| `activity-transfer.puml` | Stock Transfer | UC-19, UC-20 | FR-TRF-001–005, FR-AUD-004 | Draft transfer antar lokasi aktif berbeda, lalu posting atomik dua movement dengan total stok gabungan tetap. |
| `activity-opname.puml` | Stock Opname | UC-21–UC-23 | FR-OPN-001–006, FR-AUD-004 | Sesi dan snapshot, hitung fisik, variance = fisik − snapshot, otorisasi IA, lalu posting penyesuaian atomik. |
| `activity-reversal-adjustment.puml` | Audit Trail & Reversal/Adjustment | UC-33, UC-34; UC-06 pendukung | FR-AUD-001–004 | Penolakan edit/hapus langsung Posted, koreksi via reversal/adjustment berreferensi dan beralasan, serta peninjauan audit log. |

Kelima diagram memakai `@startuml` / `@enduml`, Initial Node (`start`), Final Node (`stop`), swimlane, decision node, alternative flow, dan exception flow.

---

## 3. Relasi dengan Use Case Diagram

Activity Diagram ini merupakan turunan langsung dari artefak di `docs/system-design/01-use-case-diagram/`:

| Activity Diagram | Use Case Diagram Source |
|---|---|
| Stock Inbound | `use-case-stock-transactions.puml` — UC-15, UC-16 |
| Stock Outbound | `use-case-stock-transactions.puml` — UC-17, UC-18 |
| Stock Transfer | `use-case-stock-transactions.puml` — UC-19, UC-20 |
| Stock Opname | `use-case-stock-transactions.puml` — UC-21, UC-22, UC-23 |
| Reversal/Adjustment | `use-case-monitoring-reporting.puml` — UC-33, UC-34; `use-case-auth-user.puml` — UC-06 pendukung |

Use Case menjawab **tujuan pengguna**; Activity Diagram menjawab **urutan aktivitas, guard, dan exception**. Tidak ada Use Case baru yang dibuat.

---

## 4. Referensi Dokumen Sumber

| Dokumen | Peran |
|---|---|
| `AGENTS.md` | Batasan pengembangan dan aturan kerja agen. |
| `docs/01-business-requirements-analysis.md` | Sumber proses target BRA §7, aturan bisnis §10, matriks peran §12, dan kriteria penerimaan §18. |
| `docs/02-requirements-traceability-analysis.md` | Sumber traceability dan requirement gap §11. |
| `docs/03-business-decision-log.md` | Sumber status keputusan `D-B*` dan `D-T*`. |
| `docs/04-software-requirements-specification.md` | Sumber detail FR dan validation rules. |
| `docs/05-srs-review-validation.md` | Sumber audit `AUD-01`–`AUD-11` dan kondisi readiness. |
| `docs/system-design/01-use-case-diagram/` | Sumber Use Case yang telah disetujui. |

Dokumen sumber di atas **tidak diubah** oleh penyimpanan artefak ini.

---

## 5. Cara Membaca Source PlantUML

1. Buka file `.puml` dengan editor teks atau ekstensi PlantUML.
2. Render melalui preview ekstensi, atau gunakan PlantUML CLI/JAR:
   ```text
   java -jar plantuml.jar docs/system-design/02-activity-diagrams/activity-inbound.puml
   ```
3. Untuk seluruh diagram:
   ```text
   java -jar plantuml.jar docs/system-design/02-activity-diagrams/*.puml
   ```
4. Baris yang diawali tanda kutip tunggal (`'`) adalah komentar referensi UC, FR, RULE, dan Decision ID.
5. `start` adalah Initial Node; `stop` adalah Activity Final Node.
6. `|Nama|` adalah swimlane.
7. `if (...) then (...)` / `else (...)` / `endif` adalah Decision Node dan merge implisit.
8. File hasil render (PNG/SVG) **tidak disimpan** pada tahap ini.

---

## 6. Catatan Status Provisional

- `D-B08` tetap **Proposed** — detail reversal/adjustment belum final.
- `D-B09` tetap **Open** — **tidak ada location lock** pada alur opname.
- `D-B16` tetap **Open** — tidak ada periode retensi audit final.
- `D-T06` tetap **Proposed** — transaction/lock saldo masih rancangan, bukan implementasi final.
- `D-B15` tetap **Proposed** — Cancel Draft dan double-posting guard belum menjadi requirement/AC final.
- `D-B14` tetap **Proposed** — Opening Balance tidak dibuat sebagai requirement final atau aktivitas mandiri.
- Hak posting Warehouse Operator belum final; label `UD-03` adalah **unresolved reference**, bukan Decision ID resmi BRA/RTA/BDL.
- `AUD-01`–`AUD-11` masih menunggu review/persetujuan dan **belum diterapkan**.

---

## 7. Ringkasan Invariant yang Dijaga

- Draft tidak mengubah saldo.
- Posting dilakukan secara atomik: saldo, status, serial, movement, dan audit berhasil atau gagal bersama-sama.
- Tidak boleh ada negative stock.
- Inbound menambah saldo lokasi tujuan.
- Outbound mengurangi saldo lokasi asal.
- Transfer menghasilkan dua movement dalam satu transaksi.
- Total stok gabungan transfer tetap sama.
- Opname menggunakan snapshot sistem.
- Variance = Physical Quantity − Snapshot.
- Dokumen Posted immutable.
- Reversal/Adjustment dapat ditelusuri ke dokumen awal dan memiliki alasan.

Detail lengkap ada di `activity-specifications.md`.
