# STATE MACHINE DIAGRAM ANALYSIS & DESIGN — DOCUMENTATION INDEX

## Inventory Management System

| Atribut | Nilai |
|---|---|
| Lokasi artefak | `docs/system-design/03-state-machine-diagrams/` |
| Versi | 0.1 |
| Status artefak | Draft yang telah direview dan disetujui untuk disimpan |
| Status desain | **Provisional** untuk objek yang dipengaruhi keputusan Proposed/Open |
| Tahap | System Design — State Machine Diagram |
| Project Type | Personal project / prototype pembelajaran PKL |

**Pernyataan batasan:** Dokumen ini adalah artefak desain untuk prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Gunakan hanya data dummy/sintetis.

---

## 1. Tujuan Dokumentasi

Direktori ini menyimpan rancangan State Machine Diagram yang telah ditampilkan dan disetujui. Tujuannya adalah mendokumentasikan:

1. Lifecycle status objek inventory: Draft → Posted.
2. Event, guard condition, dan transition action yang mengubah status.
3. State invariant yang menjaga konsistensi stock balance dan audit trail.
4. Pemisahan dokumen koreksi dari dokumen Posted yang immutable.
5. Keterhubungan setiap state dan transisi ke UC-ID, FR-ID, RULE, Decision ID, dan Acceptance Criteria.

`SM-*`, `SMO-*`, `ST-*`, dan `EV-*` adalah **ID artefak desain**, bukan Requirement ID BRA. Tidak ada Requirement ID baru yang dibuat.

---

## 2. Penjelasan State Machine Diagram

State Machine Diagram menggambarkan **keadaan (state) suatu objek**, bagaimana objek berpindah keadaan akibat **event**, syarat perpindahan (**guard**), dan perilaku saat perpindahan (**action**).

Perbedaan dengan diagram sebelumnya:

| Diagram | Fokus |
|---|---|
| Use Case | Tujuan pengguna terhadap sistem |
| Activity Diagram | Urutan aktivitas, swimlane, dan kontrol alur |
| **State Machine Diagram** | **Status objek dan transisinya** |

Kesimpulan review yang dipertahankan:

- Inbound, Outbound, dan Transfer memiliki **topologi status yang sama** (`Draft` → `Posted`), tetapi **guard dan action transisinya berbeda**. Oleh karena itu lifecycle dipisah menjadi tiga diagram spesialisasi, bukan disatukan demi kesederhanaan.
- `Validated`, `Cancelled`, `Locked`, `Approved`, `Completed`, dan `Reversed` pada dokumen asli **tidak dijadikan state** karena tidak memiliki dasar requirement final.

---

## 3. Objek yang Dimodelkan

| Object ID | Nama objek | Diagram | Status desain |
|---|---|---|---|
| SMO-00 | Inventory Transaction Document — pola umum | `state-transaction-document.puml` | Referensi pola; bukan objek tambahan |
| SMO-01 | Inventory Transaction Document — Inbound | `state-inbound.puml` | Provisional (`D-B15`, `D-T06` Proposed) |
| SMO-02 | Inventory Transaction Document — Outbound | `state-outbound.puml` | Provisional (`D-B07`, `D-B15`, `D-T06` Proposed) |
| SMO-03 | Inventory Transaction Document — Transfer | `state-transfer.puml` | Provisional (`D-T06` Proposed) |
| SMO-04 | Stock Opname Session | `state-stock-opname.puml` | Provisional — `D-B09` **Open** |
| SMO-05 | Reversal/Adjustment Document | `state-correction-document.puml` | Provisional — `D-B08` **Proposed** |

Master Item dan Warehouse **tidak dibuat diagram tambahan**: BRA/SRS hanya mendefinisikan status aktif/nonaktif (FR-MD-003, FR-WH-003, RULE-003, AC §18.1), bukan state machine lengkap dengan event, guard, dan final state.

---

## 4. Daftar Diagram

| File | Diagram | Use Case | FR Utama |
|---|---|---|---|
| `state-transaction-document.puml` | Pola umum Draft → Posted | UC-15–UC-20 | FR-IN/OUT/TRF, FR-AUD-001/004 |
| `state-inbound.puml` | Inbound Document | UC-15, UC-16 | FR-IN-001–006, FR-AUD-004 |
| `state-outbound.puml` | Outbound Document | UC-17, UC-18 | FR-OUT-001–006, FR-AUD-004 |
| `state-transfer.puml` | Transfer Document | UC-19, UC-20 | FR-TRF-001–005, FR-AUD-004 |
| `state-stock-opname.puml` | Stock Opname Session | UC-21–UC-23 | FR-OPN-001–006, FR-AUD-004 |
| `state-correction-document.puml` | Reversal/Adjustment Document | UC-34; UC-33 konteks audit | FR-AUD-001–004 |

Dokumen pendukung:

- `state-machine-specifications.md` — spesifikasi lengkap 16 bagian.
- `state-transition-matrix.md` — matriks seluruh transisi.

---

## 5. Referensi Dokumen Sumber

| Dokumen | Peran |
|---|---|
| `AGENTS.md` | Batasan pengembangan dan aturan kerja agen. |
| `docs/01-business-requirements-analysis.md` | BRA §7 proses target, §10 RULE, §12 matriks peran, §18 acceptance criteria, §24 glossary Draft/Posted. |
| `docs/02-requirements-traceability-analysis.md` | Requirement gap §11 (cancel Draft, reversal, lock opname, retensi audit). |
| `docs/03-business-decision-log.md` | Status keputusan `D-B*` dan `D-T*`. |
| `docs/04-software-requirements-specification.md` | Detail FR-IN, FR-OUT, FR-TRF, FR-OPN, FR-AUD. |
| `docs/05-srs-review-validation.md` | Audit `AUD-01`–`AUD-11`, READY WITH CONDITIONS. |
| `docs/system-design/01-use-case-diagram/` | Use Case yang telah disetujui. |
| `docs/system-design/02-activity-diagrams/` | Activity Diagram yang telah disetujui. |

Dokumen sumber di atas **tidak diubah** oleh penyimpanan artefak ini.

---

## 6. Cara Menggunakan PlantUML

1. Buka file `.puml` dengan editor teks atau ekstensi PlantUML.
2. Render melalui preview ekstensi, atau CLI/JAR:
   ```text
   java -jar plantuml.jar docs/system-design/03-state-machine-diagrams/state-inbound.puml
   ```
3. Untuk seluruh diagram:
   ```text
   java -jar plantuml.jar docs/system-design/03-state-machine-diagrams/*.puml
   ```
4. Baris diawali tanda kutip tunggal (`'`) adalah komentar referensi UC/FR/RULE/Decision.
5. `[*]` adalah pseudostate awal/akhir; `state "Nama" as alias` mendefinisikan state.
6. Format transisi: `Sumber --> Tujuan : Event [guard] / action`.
7. File hasil render (PNG/SVG) **tidak disimpan** pada tahap ini.

---

## 7. Catatan Desain Provisional

- `D-B08` tetap **Proposed** — detail reversal/adjustment dan nama status koreksi belum final.
- `D-B09` tetap **Open** — **tidak ada state Locked**, tidak ada hard lock, concurrency policy belum ditetapkan.
- `D-B16` tetap **Open** — tidak ada periode retensi audit.
- `D-B15` tetap **Proposed** — `Cancelled` tidak dijadikan state final; anti-post-ulang masih rancangan.
- `D-T06` tetap **Proposed** — region transaksi atomik adalah desain, bukan implementasi final.
- `D-B11`, `D-B12`, `D-B17` tetap **Deferred** — tidak ada state Return, Batch/Expiry, atau Export.
- Hak posting Warehouse Operator (`UD-03`) bukan Decision ID resmi dan belum final.
- `AUD-01`–`AUD-11` menunggu review/persetujuan dan **belum diterapkan**.

---

## 8. Ringkasan Invariant yang Dijaga

- Draft tidak mengubah Stock Balance.
- Posting memperbarui stok secara atomik dan menghasilkan stock movement.
- Posting gagal melakukan rollback penuh.
- Dokumen Posted immutable; tidak ada edit/hapus langsung.
- Reversal/Adjustment adalah dokumen koreksi terpisah yang mereferensikan dokumen awal dan memiliki alasan.
- Opname memakai snapshot; variance = Physical Quantity − Snapshot; penyesuaian memerlukan alasan dan otorisasi Inventory Administrator.

Detail lengkap ada di `state-machine-specifications.md` dan `state-transition-matrix.md`.
