# USE CASE DIAGRAM ANALYSIS & DESIGN — DOCUMENTATION INDEX

## Inventory Management System

| Atribut | Nilai |
|---|---|
| Lokasi artefak | `docs/system-design/01-use-case-diagram/` |
| Versi | 0.1 |
| Status artefak | Draft yang telah direview dan disetujui untuk disimpan |
| Status desain | **Provisional** — menunggu keputusan Open/Proposed/Deferred yang masih berlaku |
| Tahap | System Design — Use Case Diagram |
| Proyek | Personal project / prototype pembelajaran PKL |

**Pernyataan batasan:** Dokumen ini adalah artefak desain untuk prototype pembelajaran. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Gunakan hanya data dummy/sintetis.

---

## 1. Tujuan Dokumentasi

Direktori ini menyimpan rancangan Use Case Diagram yang telah ditampilkan dan disetujui untuk reviewer. Tujuannya adalah mendokumentasikan:

1. Aktor dan batas sistem.
2. Use Case beserta identitas artefaknya.
3. Hubungan aktor–Use Case.
4. Hubungan `include` dan `extend` yang beralasan.
5. Telusur kebutuhan fungsional (Functional Requirements/FR) ke Use Case.
6. Status desain yang masih bergantung pada keputusan bisnis atau teknis.

**UC-ID (UC-01 … UC-34) adalah ID artefak desain, bukan Requirement ID resmi BRA.** Tidak ada Requirement ID baru yang dibuat di direktori ini.

---

## 2. Daftar File

| File | Isi |
|---|---|
| `README.md` | Indeks artefak, tujuan, daftar diagram, aktor, sumber, cara pakai PlantUML, dan status provisional. |
| `use-case-overview.puml` | Diagram keseluruhan: 4 aktor, boundary sistem, dan 12 grup modul Use Case. |
| `use-case-auth-user.puml` | Diagram detail Authentication & User Management (UC-01–UC-06). |
| `use-case-master-warehouse.puml` | Diagram detail Master Data & Warehouse (UC-07–UC-14). |
| `use-case-stock-transactions.puml` | Diagram detail transaksi stok (UC-15–UC-23). |
| `use-case-monitoring-reporting.puml` | Diagram detail monitoring, reporting, dan audit (UC-24–UC-34). |
| `use-case-specifications.md` | Analisis aktor, matriks tanggung jawab, inventaris 34 Use Case, spesifikasi lengkap, relasi, traceability 51 FR, pemetaan aturan bisnis, pemetaan keputusan, unresolved items, dan checklist. |

---

## 3. Daftar Diagram

1. **Use Case Overview** — gambaran keseluruhan sistem dan hubungan aktor dengan kelompok Use Case.
2. **Authentication & User Management** — login, logout, akses terproteksi, akun pengguna, role/permission, dan metadata aksi kritis.
3. **Master Data & Warehouse** — kategori, satuan, barang, minimum stock, supplier referensi, gudang, lokasi, dan nonaktifisasi master.
4. **Inventory Transactions** — Draft dan posting inbound, outbound, transfer, serta alur stock opname.
5. **Inventory Monitoring & Reporting** — saldo, kartu stok, pencarian, dashboard, laporan, ekspor, audit log, dan koreksi dokumen Posted.

Kelima diagram memakai system boundary. Aktor berada di luar boundary dan Use Case berada di dalam boundary.

---

## 4. Penjelasan Aktor

| Aktor | Alias yang bersumber | Ringkasan |
|---|---|---|
| System Administrator | System Admin | Mengelola akun, role, dan data referensi; tidak mengubah histori Posted secara langsung. |
| Inventory Administrator | Inventory Admin | Mengelola master dan transaksi; memiliki hak posting sesuai matriks BRA §12. |
| Warehouse Operator | — | Membuat dan mengisi Draft transaksi serta hitung fisik opname. Hak posting **tidak default**. |
| Manager / Viewer | — | Aktor baca-saja untuk dashboard, saldo, kartu stok, dan laporan. |

Catatan konsistensi yang dipertahankan:

- **Super Admin tidak digunakan** karena nama tersebut tidak ditemukan di BRA, RTA, BDL, SRS, atau SRS Review.
- **Supplier bukan aktor interaktif**. Supplier hanya referensi opsional pada `FR-MD-006` / `D-B06` (Proposed).
- **Tidak ada aktor sistem eksternal** karena BRA §5.2 mengeluarkan integrasi ERP/procurement/API dari lingkup.
- **Manager / Viewer tetap satu kelompok aktor** sesuai BRA §12 dan BRA §6.2 D.
- Tidak ada generalization antaraktor karena hierarki role tidak dibuktikan oleh sumber.

---

## 5. Referensi Dokumen Sumber

Hierarki yang dipakai:

| Dokumen | Peran |
|---|---|
| `AGENTS.md` | Batasan pengembangan dan aturan kerja agen. |
| `docs/01-business-requirements-analysis.md` | Sumber kebutuhan bisnis, aktor, matriks peran, FR, RULE, dan asumsi. |
| `docs/02-requirements-traceability-analysis.md` | Sumber traceability FR, RULE, dan pemetaan peran. |
| `docs/03-business-decision-log.md` | Sumber status keputusan `D-B*` dan `D-T*`. |
| `docs/04-software-requirements-specification.md` | Sumber spesifikasi sistem dan karakteristik pengguna. |
| `docs/05-srs-review-validation.md` | Sumber audit `AUD-01`–`AUD-11` dan kondisi readiness. |

Dokumen sumber di atas **tidak diubah** oleh penyimpanan artefak ini.

---

## 6. Cara Menggunakan Source PlantUML

1. Buka file `.puml` dengan editor teks atau ekstensi PlantUML (misalnya Plotly PlantUML, PlantUML for VS Code, atau editor lain yang mendukung PlantUML).
2. Render diagram melalui preview ekstensi tersebut, atau gunakan PlantUML CLI/JAR:
   ```text
   java -jar plantuml.jar docs/system-design/01-use-case-diagram/use-case-overview.puml
   ```
3. Untuk seluruh file sekaligus:
   ```text
   java -jar plantuml.jar docs/system-design/01-use-case-diagram/*.puml
   ```
4. Source dapat juga ditempel ke server PlantUML atau layanan rendering lain yang mendukung syntax `@startuml` / `@enduml`.
5. Jangan mengubah isi diagram tanpa tinjauan ulang, karena status keputusan Open/Proposed/Deferred masih melekat pada desain.

Catatan: `*.puml` adalah source teks. File hasil render (PNG/SVG) tidak disimpan di direktori ini.

---

## 7. Status Desain Provisional

- Seluruh Use Case adalah **rancangan provisional**, bukan persetujuan implementasi final.
- `D-B09` tetap **Open**: tidak ada Use Case final untuk location lock saat stock opname aktif.
- `D-B16` tetap **Open**: tidak ada periode retensi audit final.
- `D-B13` tetap **Proposed**: dashboard hanya ringkasan per satuan dan dilarang menjumlahkan satuan berbeda.
- `D-B17` tetap **Deferred**: `UC-32` Ekspor Excel/PDF adalah Future Scope, bukan MVP.
- `D-T02` dan `D-T03` tetap **Accepted** sebagaimana BDL.
- `AUD-01`–`AUD-11` masih menunggu review/persetujuan dan **belum diterapkan**.
- Hak posting Warehouse Operator belum final. Label `UD-03` pada draft adalah **unresolved reference**, bukan Decision ID resmi BRA/RTA/BDL.

---

## 8. Ringkasan Traceability

- 4 aktor.
- 34 Use Case (`UC-01`–`UC-34`).
- 51/51 Functional Requirements terpetakan.
- `FR-AUD-004` bukan Use Case mandiri; merupakan perilaku lintas transaksi pada `UC-16`, `UC-18`, `UC-20`, dan `UC-23`.
- Tidak ada Requirement ID baru.
- 25 Decision ID dipertahankan sesuai BDL: 18 Proposed, 2 Open, 3 Deferred, 2 Accepted.

---

## 9. Unresolved Items Ringkas

| Item | Status | Dampak singkat |
|---|---|---|
| `UD-01` | Nama “Super Admin” belum bersumber | Aktor tidak ditambahkan. |
| `UD-02` | Pemisahan Manager vs Viewer belum diputus | Satu aktor `Manager / Viewer` dipertahankan. |
| `UD-03` | **Unresolved reference**, bukan Decision ID resmi | Hak posting Warehouse Operator belum final; tidak diasumsikan default. |
| `D-B09` | Open | Location lock tidak dibuat sebagai Use Case final. |
| `D-B16` | Open | Retensi audit belum ditetapkan. |
| `D-B13` | Proposed | Dashboard menunggu persetujuan. |
| `D-B17` | Deferred | Ekspor laporan bukan MVP. |
| `D-B08` | Proposed | Detail alur reversal/adjustment masih provisional. |
| `D-B14` | Proposed | Tidak ada Use Case opening balance baru. |
| `D-B15` | Proposed | Tidak ada Use Case final cancel Draft. |
| `D-T01` | Proposed | Zona waktu tampilan belum final. |
| `D-T05` | Proposed | Implementasi middleware/Policy/Gate belum final. |
| `D-T06` | Proposed | Strategi transaksi/lock saldo belum final. |
| `AUD-01`–`AUD-11` | Menunggu review | Rekomendasi audit belum diterapkan. |

Detail lengkap ada di `use-case-specifications.md`.
