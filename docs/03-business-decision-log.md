# BUSINESS DECISION LOG

## 1. Document Information

| Atribut | Nilai |
|---|---|
| Document title | Business Decision Log — Inventory Management System |
| Document ID | Akan ditetapkan setelah dokumen disetujui |
| Version | 0.1 — Approved |
| Date | 22 November 2026 |
| Owner | Pengembang proyek / pemilik proyek |
| Status | Approved — setelah final review |
| Project type | Proyek pribadi / prototype pembelajaran PKL |
| Related sources | `AGENTS.md`; `docs/01-business-requirements-analysis.md`; Requirements Traceability Analysis; Business Decision Analysis |

**Pernyataan status proyek:** Dokumen ini merupakan catatan keputusan desain proyek pribadi. Bukan SOP, kebijakan, spesifikasi resmi, atau representasi sistem PT Indonesia Comnets Plus/PLN Icon Plus. Seluruh keputusan tetap dapat ditinjau dan disetujui oleh pemilik proyek.

---

## 2. Purpose and Scope

Tujuan dokumen ini adalah mencatat keputusan bisnis dan teknis yang diambil selama tahap System Analysis agar dapat ditelusuri ke BRA dan dipakai sebagai dasar System Design.

Ruang lingkup dokumen ini:

- Mencatat seluruh keputusan D-B01–D-B17 dan D-T01–D-T08.
- Menjelaskan status keputusan berdasarkan definisi pada bagian 3.
- Mencatat konflik requirement, gap requirement, dan usulan penyelesaiannya.
- Menyediakan kandidat baseline MVP yang akan berlaku setelah keputusan diberi status `Accepted`.
- Menjaga agar BRA tidak diubah tanpa persetujuan eksplisit.

Dokumen ini tidak mencakup:

- Implementasi source code.
- Migration atau perubahan database.
- Perubahan konfigurasi Laravel.
- Perubahan BRA.
- Pelaksanaan command database.

---

## 3. Decision Status Definitions

| Status | Definisi | Catatan penggunaan |
|---|---|---|
| Proposed | Rekomendasi atau usulan yang belum disetujui pemilik proyek | Status awal untuk seluruh keputusan kecuali ditentukan lain dalam dokumen ini |
| Accepted | Keputusan sudah disetujui secara eksplisit oleh pemilik proyek | Hanya boleh dicatat jika pemilik proyek menyatakan persetujuan final |
| Rejected | Keputusan tidak digunakan dan tidak boleh menjadi dasar desain | Perlu dicatat alasan penolakan |
| Deferred | Keputusan sengaja ditunda ke fase lanjutan | Tidak diimplementasikan pada MVP |
| Open | Masih memerlukan pembahasan atau keputusan | Tidak boleh dianggap disetujui |

**Catatan penting:** Rekomendasi AI tidak otomatis menjadi `Accepted`. Pemilik proyek berubah status keputusan secara eksplisit.

---

## 4. Business Decision Register

| Decision ID | Related Requirement ID | Problem | Available Options | Proposed Solution | Reason | Impact | Status |
|---|---|---|---|---|---|---|---|
| D-B01 | Q-01; FR-MD-003; RULE-001 | Material Number dan Item Code dapat dianggap sama atau terpisah | Satu kode gabungan; dua field terpisah; field terpisah plus mapping | Pisahkan `item_code` internal dan `material_number` eksternal | Sesuai usulan pemilik proyek dan rekomendasi BRA Q-01 | BP: master barang lebih jelas; DB: dua field unik; LAR: validasi FormRequest; LOGIC: uniqueness terpisah; AUTH: Inventory Admin; TEST: uji duplikat tiap field | Proposed |
| D-B02 | Q-02; FR-MD-004; ASM-05; RULE-006–RULE-008 | Tidak semua barang memerlukan serial tracking | Semua serialized; tracking per item; tracking per transaksi | Tracking type ditentukan per barang: serialized atau non-serialized | Sesuai usulan pemilik proyek, BRA Q-02, dan ASM-05 | BP: alur serial hanya untuk item tertentu; DB: field tracking type; LAR: service serial terpisah; LOGIC: qty dan serial dibedakan; AUTH: Inventory Admin; TEST: dua skenario tracking | Proposed |
| D-B03 | Q-03; RULE-007–RULE-009; KPI-04 | Scope keunikan serial belum final | Unik global; unik per item; unik per item dan pabrikan | Serial Number unik secara global untuk MVP | Sesuai usulan pemilik proyek dan rekomendasi BRA Q-03 | BP: serial tidak dipakai ulang; DB: unique constraint global; LAR: validasi serialized centralized; LOGIC: status/lokasi serial unik; AUTH: Operator memilih serial tersedia; TEST: duplikat serial ditolak | Proposed |
| D-B04 | Q-04; BR-009; FR-WH-002; ASM-02; §11.3 | Kedalaman struktur lokasi belum final | Warehouse only; Warehouse > Area > Rack > Level; tambah Bin di MVP | Warehouse > Area > Rack > Level untuk MVP; Bin menjadi fitur lanjutan | Sesuai usulan pemilik proyek dan rekomendasi BRA Q-04 | BP: lokasi lebih terstruktur; DB: hierarki parent-child; LAR: model relasi parent; LOGIC: transaksi memakai level aktif; AUTH: Inventory Admin; TEST: parent invalid dan kode duplikat | Proposed |
| D-B05 | Q-05; RULE-004–RULE-005; RULE-008; FR-IN-003 | Presisi quantity dan serialized integer belum final | Semua integer; semua desimal; presisi per satuan | Quantity mengikuti presisi satuan; serialized quantity wajib integer | Sesuai usulan pemilik proyek, BRA Q-05, RULE-005, dan RULE-008 | BP: qty mengikuti satuan; DB: DECIMAL dengan precision; LAR: cast dan validation; LOGIC: serial count sama dengan qty bulat; AUTH: Operator/Inventory Admin; TEST: desimal, nol, negatif, mismatch serial | Proposed |
| D-B06 | Q-06; FR-IN-006; FR-MD-006; ASM-04 | Supplier wajib atau opsional pada inbound | Wajib; opsional; tanpa supplier | Supplier bersifat opsional pada inbound | Sesuai usulan pemilik proyek dan BRA Q-06 | BP: inbound tidak diblokir tanpa supplier; DB: nullable relation; LAR: conditional validation; LOGIC: supplier hanya referensi; AUTH: Inventory Admin/Operator; TEST: inbound dengan dan tanpa supplier | Proposed |
| D-B07 | Q-07; FR-OUT-001–FR-OUT-005; ASM-08; RULE-010–RULE-012 | Model persetujuan outbound belum final | Draft-Posted plus permission; approval bertingkat; auto-post | Outbound memakai Draft dan Posted dengan permission posting | Sesuai usulan pemilik proyek, BRA Q-07, dan ASM-08 | BP: pembuat dan pemosting dibedakan; DB: status, posted_by, posted_at; LAR: Policy/Gate; LOGIC: Draft tidak ubah saldo; AUTH: permission posting eksplisit; TEST: unauthorized post ditolak | Proposed |
| D-B08 | Q-08; FR-AUD-003; RULE-012; RULE-026 | Koreksi transaksi Posted belum final | Edit langsung; reversal; adjustment; reversal/adjustment berreferensi | Posted transaction dikoreksi melalui Reversal/Adjustment yang merujuk dokumen awal | Sesuai usulan pemilik proyek, BRA Q-08, RULE-012, dan RULE-026 | BP: koreksi tetap audit-abel; DB: relasi dokumen koreksi; LAR: action/service khusus; LOGIC: Posted immutable; AUTH: Inventory Admin; TEST: edit langsung ditolak dan reversal menghasilkan movement | Proposed |
| D-B09 | Q-09; RULE-023; ASM-10; FR-OPN-001–FR-OPN-006 | Lock lokasi saat opname aktif belum disetujui | Hard lock DB; soft status block; reconcile movement; tanpa lock | Analisis risiko dilakukan; rekomendasi sementara adalah soft status block hanya setelah persetujuan | BRA menyebut proposed control dan asumsi; pemilik proyek belum menyetujui | BP: dapat memblokir transaksi lain; DB: status sesi opname; LAR: policy concurrency; LOGIC: movement lokasi diblokir atau direkonsiliasi; AUTH: override perlu keputusan; TEST: concurrent post dan opname aktif | Open |
| D-B10 | Q-10; FR-MD-005; FR-DASH-002; REP-07 | Scope Minimum Stock belum final | Per item; per lokasi; keduanya | Minimum Stock berlaku per item untuk MVP | Sesuai usulan pemilik proyek dan BRA Q-10 | BP: low stock sederhana; DB: field minimum di item; LAR: query dashboard/report; LOGIC: ambang per SKU; AUTH: Inventory Admin; TEST: item di bawah minimum muncul | Proposed |
| D-B11 | Q-11; §5.2; RISK-02 | Retur barang belum diputus untuk MVP | Masuk MVP; tunda; tipe transaksi terpisah | Return berada di luar MVP | Sesuai instruksi pemilik proyek dan BRA Q-11/RISK-02 | BP: fokus pada modul MVP; DB: tanpa tipe return; LAR: tanpa modul return; LOGIC: tanpa reverse outbound khusus; AUTH: tanpa permission return; TEST: tidak diuji pada MVP | Deferred |
| D-B12 | Q-12; §5.2; §5.3 | Batch atau expiry tracking belum diputus | Masuk MVP; future; evaluasi per jenis barang | Batch dan Expiry berada di luar MVP | Sesuai instruksi pemilik proyek dan BRA Q-12 | BP: tidak memperluas traceability; DB: tanpa tabel batch; LAR: tanpa modul batch; LOGIC: serial cukup untuk identitas unit; AUTH: tanpa permission batch; TEST: tidak diuji pada MVP | Deferred |
| D-B13 | FR-DASH-001; §14.2; KPI-08 | Dashboard “total kuantitas stok” konflik dengan larangan menjumlahkan satuan berbeda | Total qty gabungan; jumlah SKU; ringkasan per satuan | Tampilkan jumlah SKU dan ringkasan per satuan, bukan total qty lintas satuan | §14.2 BRA eksplisit melarang penjumlahan satuan berbeda; solusi masih usulan | BP: dashboard tidak menyesatkan; DB: agregasi per unit; LAR: query dashboard; LOGIC: tidak menjumlahkan unit berbeda; AUTH: semua role pembaca; TEST: dua satuan tetap terpisah | Proposed |
| D-B14 | RULE-020; ASM-06; §22 tahap 2 | Opening balance belum punya Functional Requirement detail | Input langsung di master; dokumen opening balance; adjustment biasa | Opening balance memakai dokumen khusus atau penyesuaian awal; detail FR perlu ditambahkan ke BRA setelah disetujui | RULE-020 dan ASM-06 menetapkan prinsip, tetapi FR belum tersedia | BP: saldo awal punya jejak; DB: tipe dokumen/movement; LAR: action khusus; LOGIC: saldo tidak diedit manual; AUTH: Inventory Admin; TEST: opening balance menghasilkan movement | Proposed |
| D-B15 | RULE-024–RULE-026; §7.6; §18.2 | Cancel Draft dan double posting belum punya FR dan acceptance criteria eksplisit | Masuk FR transaksi; FR terpisah; hanya aturan teknis | Masukkan ke desain modul transaksi dengan acceptance criteria eksplisit | RULE-024/025 dan AC §18.2 harus dapat diverifikasi | BP: Draft dapat dibatalkan, Posted tidak; DB: status dan nomor unik; LAR: posting guard; LOGIC: tolak post kedua; AUTH: role berwenang; TEST: cancel Draft tanpa movement dan double posting ditolak | Proposed |
| D-B16 | FR-AUD-001; §11.5; NFR-LOG-001 | Retensi audit log dan transaksi belum diputus | Tanpa retensi; retensi tetap; konfigurabel | Masih memerlukan keputusan pemilik proyek | §11.5 menyebutkan topik ini terbuka | BP: jangkauan histori belum final; DB: kebijakan retensi belum final; LAR: audit service belum final; LOGIC: Posted immutable tetap berlaku; AUTH: akses audit belum final; TEST: belum bisa distandardisasi | Open |
| D-B17 | FR-REP-004; §5.3; RISK-02 | Export Excel/PDF belum dirinci dan bukan target MVP | Implementasi di MVP; setelah laporan stabil; future | Export Excel/PDF ditunda sampai laporan layar stabil | Sesuai instruksi pemilik proyek dan BRA Could/Future | BP: tidak memperluas MVP; DB: tidak ada format export; LAR: tidak ada exporter dulu; LOGIC: fokus konsistensi data; AUTH: role laporan; TEST: tidak diuji pada MVP | Deferred |

---

## 5. Technical Decision Register

| Decision ID | Related Requirement ID | Problem | Available Options | Proposed Solution | Reason | Impact | Status |
|---|---|---|---|---|---|---|---|
| D-T01 | RULE-028; NFR-COMP-001; `config/app.php` | Timezone BRA Asia/Jakarta vs config UTC | UTC; Asia/Jakarta; timezone per user | Gunakan Asia/Jakarta sebagai timezone aplikasi prototype | Sesuai usulan pemilik proyek dan RULE-028 | BP: waktu transaksi konsisten; DB: timestamp konsisten; LAR: config setelah approval; LOGIC: waktu ledger; AUTH: seluruh actor; TEST: assertion timezone | Proposed |
| D-T02 | NFR-COMP-001; BRA target PHP 8.3+; `composer.json ^8.2` | Versi PHP minimum tidak konsisten | PHP 8.2; PHP 8.3; latest LTS | PHP minimum 8.3 | Pemilik proyek memilih PHP 8.3+ | BP: tidak langsung; DB: tidak langsung; LAR: constraint runtime; LOGIC: kompatibilitas fitur PHP; AUTH: tidak langsung; TEST: matrix versi PHP | Accepted |
| D-T03 | NFR-USE-002; BRA stack Blade+Bootstrap 5; scaffold Tailwind | Frontend target berbeda dari scaffold | Tailwind; Bootstrap 5; keduanya | Blade + Bootstrap 5 sebagai frontend MVP | Pemilik proyek memilih Blade dan Bootstrap 5 | BP: UI sesuai target; DB: tidak langsung; LAR: resources/views dan asset pipeline; LOGIC: logic tidak di Blade; AUTH: menu sesuai role; TEST: responsif dan alur form | Accepted |
| D-T04 | AGENTS.md; `phpunit.xml`; NFR-MNT-002 | Tes dapat memakai DB lokal `.env` | Pakai `.env`; SQLite in-memory; DB tes terpisah | Gunakan database terpisah untuk automated testing | Sesuai usulan pemilik proyek dan AGENTS.md | BP: tidak mengganggu demo; DB: DSN test terpisah; LAR: environment test setelah approval; LOGIC: RefreshDatabase; AUTH: test permission; TEST: isolasi data dan repeatable | Proposed |
| D-T05 | FR-AUTH-003; NFR-SEC-002; KPI-06 | Auth dan authorization belum ada di skeleton | Middleware global; Policy per model; Gate per aksi | Middleware auth plus Policy/Gate permission di backend | BRA melarang authorization hanya lewat UI | BP: halaman terlindungi; DB: users/roles/permissions; LAR: middleware dan policy; LOGIC: pengecekan di service; AUTH: matriks C/R/U/P/D; TEST: URL langsung ditolak | Proposed |
| D-T06 | NFR-INT-001–NFR-INT-002; NFR-REL-001; RISK-04; RISK-06 | Atomicitas dan concurrency posting perlu strategi | Transaction saja; row lock; optimistic lock | Database transaction plus validasi/lock saldo saat posting | RULE-027, RISK-04, RISK-06 | BP: rollback saat gagal; DB: transaction dan lock; LAR: service/action layer; LOGIC: tidak ada partial stock; AUTH: hanya berwenang; TEST: Skenario E dan concurrent post | Proposed |
| D-T07 | NFR-OPS-001; NFR-COMP-001; NFR-DOC-001 | Prosedur backup dan daftar versi belum didokumentasi | Tanpa prosedur; README singkat; runbook terpisah | Dokumentasikan backup/restore dan versi runtime di dokumentasi teknis | NFR-OPS-001 dan NFR-COMP-001 mensyaratkan | BP: operasional demo jelas; DB: prosedur dump/restore; LAR: catatan environment; LOGIC: tidak langsung; AUTH: System Admin; TEST: verifikasi restore | Proposed |
| D-T08 | Q-01–Q-12; §20–§22 | ID requirement baru untuk gap belum boleh dibuat tanpa persetujuan | Tambah ID di BRA; catatan desain saja; abaikan gap | Ajukan penambahan atau revisi ID BRA terlebih dahulu jika gap menjadi requirement | AGENTS.md melarang mengubah BRA tanpa persetujuan | BP: scope terkendali; DB: tidak langsung; LAR: tidak langsung; LOGIC: tidak ada aturan diam-diam; AUTH: pengembang sebagai approver; TEST: traceability tetap valid | Proposed |

---

## 6. Conflict Resolution

| Conflict | Source | Proposed Resolution | Current Status |
|---|---|---|---|
| FR-DASH-001 vs larangan §14.2 | FR-DASH-001; §14.2 | Tampilkan jumlah SKU dan ringkasan per satuan; revisi interpretasi FR-DASH-001 setelah persetujuan | Proposed |
| RULE-023 status ganda | RULE-023; §7.5; ASM-10; Q-09 | Tandai sebagai proposed control; jangan implementasi sebelum persetujuan | Open |
| RULE-028 vs `config/app.php` | RULE-028; `config/app.php` | Tetapkan Asia/Jakarta; terapkan lewat perubahan config setelah approval | Proposed via D-T01 |
| PHP 8.3+ vs `composer.json ^8.2` | BRA; `composer.json` | Naikkan minimum ke PHP 8.3 | Accepted via D-T02 |
| Blade+Bootstrap 5 vs Tailwind scaffold | BRA; AGENTS.md; Vite config | Tetapkan Blade+Bootstrap 5; Tailwind sebagai scaffold non-final | Accepted via D-T03 |
| NFR-USE-002 Bootstrap responsive vs scaffold Tailwind | NFR-USE-002; repo | Gunakan Bootstrap 5 sebagai target UI | Accepted via D-T03 |
| `phpunit.xml` override SQLite dikomentari vs aman test DB | AGENTS.md; `phpunit.xml` | DB test terpisah; atur environment test setelah approval | Proposed via D-T04 |
| FR-AUTH-003 vs skeleton | FR-AUTH-003; `bootstrap/app.php` | Middleware auth dan policy di desain | Proposed via D-T05 |
| RULE-020/ASM-06 vs tidak ada FR opening balance | RULE-020; ASM-06 | Tambahkan FR opening balance setelah disetujui | Proposed via D-B14 |
| RULE-025/§7.6 vs tidak ada FR cancel Draft | RULE-025; §7.6 | Masukkan cancel Draft ke modul transaksi | Proposed via D-B15 |
| §18.2 double posting vs tidak ada FR eksplisit | FR-IN-005; FR-OUT-005; RULE-024 | Tambahkan state guard dan acceptance criteria idempotent posting | Proposed via D-B15 |

---

## 7. Approved MVP Baseline

### Keputusan yang sudah Accepted

| Decision ID | Keputusan | Status | Catatan |
|---|---|---|---|
| D-T02 | PHP minimum 8.3 | Accepted | Dipilih oleh pemilik proyek |
| D-T03 | Frontend Blade + Bootstrap 5 | Accepted | Dipilih oleh pemilik proyek |

### Kandidat Baseline — belum Accepted

| Group | Decision IDs | Candidate Baseline | Note |
|---|---|---|---|
| Master data | D-B01, D-B02, D-B04, D-B05 | Item code dan material number terpisah; tracking per item; hierarki lokasi empat level; qty per presisi satuan | Menunggu acceptance |
| Serial tracking | D-B03 | Serial global unique untuk MVP | Menunggu acceptance |
| Supplier | D-B06 | Supplier opsional pada inbound | Menunggu acceptance |
| Transaksi | D-B07, D-B08, D-B15 | Draft-Posted; Reversal/Adjustment; cancel Draft dan double posting punya AC | Menunggu acceptance; D-B15 masih perlu detail AC |
| Opname | D-B09 | Lock lokasi belum disetujui | Open |
| Minimum stock | D-B10 | Minimum stock per item | Menunggu acceptance |
| Return dan Batch/Expiry | D-B11, D-B12 | Di luar MVP | Deferred |
| Dashboard | D-B13 | SKU count dan ringkasan per satuan | Menunggu acceptance karena konflik FR-DASH-001 vs §14.2 |
| Opening balance | D-B14 | Dokumen khusus opening balance | Menunggu penetapan FR detail |
| Audit | D-B16 | Retensi audit belum diputus | Open |
| Export | D-B17 | Export Excel/PDF ditunda | Deferred |
| Timezone | D-T01 | Asia/Jakarta | Menunggu acceptance |
| Testing | D-T04 | Database tes terpisah | Menunggu acceptance |
| Auth & concurrency | D-T05, D-T06 | Middleware/Policy dan transaction plus lock | Menunggu acceptance |
| Ops | D-T07, D-T08 | Backup docs dan revisi BRA untuk gap ID | Menunggu acceptance |

---

## 8. Deferred Features

| Feature | Related ID | Reason | Status |
|---|---|---|---|
| Export Excel/PDF | D-B17; FR-REP-004 | Ditunda sampai laporan layar stabil | Deferred |
| Return barang | D-B11; Q-11; §5.2 | Di luar MVP menurut BRA Q-11 | Deferred |
| Batch/lot dan expiry | D-B12; Q-12; §5.2 | Di luar MVP menurut BRA Q-12 | Deferred |
| Bin atau slot lokasi | D-B04; Q-04 | Bin bersifat opsional dan future | Proposed sebagai bagian dari D-B04 |
| Multi-warehouse operasional | BR-014; ASM-01 | Ekstensibilitas future, bukan MVP | Deferred berdasarkan BRA |
| QR/barcode | BR-015; §5.3 | Fitur lanjutan | Deferred berdasarkan BRA |
| Approval workflow bertingkat | ASM-08; §5.3 | MVP memakai Draft/Posted dengan permission | Deferred berdasarkan BRA |
| Export audit report | REP-08; FR-REP-004 | Bergantung pada kestabilan laporan dan audit | Deferred |

---

## 9. Open Decisions

| Open Item | Related ID | Reason | Required Action |
|---|---|---|---|
| Stock Opname location lock | D-B09; Q-09; RULE-023; ASM-10 | Masih memerlukan analisis risiko dan persetujuan | Putuskan antara soft status block, reconcile movement, atau tanpa lock |
| Audit retention | D-B16; §11.5 | BRA menyebut retensi belum diputus | Tetapkan periode retensi dan akses audit |
| Status final D-B01–D-B08, D-B10, D-B13, D-B14, D-B15 | Business Decision Register | Masih Proposed | Tetapkan Accepted atau Rejected |
| Status final D-T01, D-T04, D-T05, D-T06, D-T07, D-T08 | Technical Decision Register | Masih Proposed | Tetapkan Accepted atau Rejected |
| Detail FR Opening Balance | D-B14; RULE-020 | Aturan ada, FR belum tersedia | Ajukan penambahan FR ke BRA setelah disetujui |
| Acceptance criteria Cancel Draft dan Double Posting | D-B15; RULE-024–RULE-026; §18.2 | Gap acceptance criteria | Tetapkan AC sebelum System Design final |
| Interpretasi dashboard | D-B13; FR-DASH-001; §14.2 | Konflik internal BRA | Setujui SKU count dan ringkasan per satuan |
| Siapa boleh membatalkan atau mengesampingkan sesi opname | D-B09; ASM-10 | Terkait otorisasi opname | Tentukan aturan role sebelum desain concurrency |
| Versi runtime pendukung | D-T02; NFR-COMP-001 | PHP sudah Accepted; daftar versi lain belum | Dokumentasikan Laravel, MySQL, Node, browser |
| Prosedur backup/restore | D-T07; NFR-OPS-001 | Belum ada runbook | Tulis prosedur di dokumentasi teknis |

---

## 10. Impact on System Design

| Area | Dampak keputusan | Catatan |
|---|---|---|
| Business Process | Draft/Posted menjadi state utama; koreksi Posted via reversal/adjustment; opname masih Open; dashboard memakai SKU/per satuan jika disetujui | D-B07, D-B08, D-B09, D-B13, D-B15 |
| Database Design | `item_code` dan `material_number` terpisah; tracking type per item; serial unique global; hierarki Warehouse > Area > Rack > Level; DECIMAL; supplier nullable; status dan waktu posting; timezone Asia/Jakarta jika D-T01 diterima | D-B01–D-B06, D-B10, D-T01 |
| Laravel Architecture | PHP 8.3 dan Blade+Bootstrap 5 sudah menjadi baseline; middleware auth, Policy/Gate, service/action transaction, environment test terpisah masih Proposed | D-T02 Accepted; D-T03 Accepted; D-T04–D-T06 Proposed |
| Inventory Business Logic | RULE-001–RULE-028 tetap menjadi dasar; RULE-023 tidak final; opening balance dan double posting perlu detail requirement/AC; serial logic mengikuti tracking type | D-B02, D-B03, D-B08, D-B09, D-B14, D-B15 |
| User Authorization | Matriks C/R/U/P/D BRA dipakai; permission posting dibedakan dari draft; Inventory Admin mengelola master dan posting; override opname belum diputus | D-B07, D-B08, D-B09, D-T05 |
| Testing Strategy | Database test terpisah; automated tests inbound, outbound, transfer, opname, serial; tambah test double posting, cancel Draft, reversal, serial global, dan timezone | D-T04, D-T06; Skenario A–E BRA; NFR-MNT-002 |

---

## 11. Change History

| Version | Date | Change | Author | Approval |
|---|---|---|---|---|
| 0.1 | 22 November 2026 | Initial draft prepared from BRA, AGENTS.md, Requirements Traceability Analysis, and Business Decision Analysis; final status audit applied: D-B11 dan D-B12 Deferred, D-T02 dan D-T03 Accepted, D-B09 dan D-B16 Open, D-B17 Deferred, keputusan lain Proposed | AI assistant | Approved by owner after final review |
