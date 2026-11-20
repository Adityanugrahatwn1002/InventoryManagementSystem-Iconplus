# BUSINESS REQUIREMENTS ANALYSIS (BRA)
## Inventory Management System
### ERP-Oriented Warehouse & Inventory Management Prototype

**Versi dokumen:** 1.0  
**Tanggal:** 20 November 2026  
**Status:** Draft baseline untuk proyek pribadi  
**Pemilik proyek:** Pengembang (siswa PKL)  
**Konteks:** Proyek pembelajaran mandiri selama PKL di PT Indonesia Comnets Plus (PLN Icon Plus)  
**Teknologi target:** Laravel 12, PHP 8.3+, MySQL, Blade, Bootstrap 5

> **Pernyataan status dan batasan:** Dokumen ini disusun untuk proyek pribadi/prototype pembelajaran. Dokumen ini bukan SOP, kebijakan, spesifikasi, atau representasi sistem resmi PT Indonesia Comnets Plus/PLN Icon Plus. Tidak ada alur kerja internal perusahaan yang diasumsikan telah disetujui. Proses bisnis dalam dokumen ini merupakan rancangan berdasarkan praktik umum pengelolaan persediaan dan asumsi desain yang perlu divalidasi. Gunakan hanya data dummy/sintetis kecuali penggunaan data lain telah memperoleh izin yang jelas.

---

# Pengantar: Apa itu BRA?

**Business Requirements Analysis (BRA)** adalah proses mengidentifikasi, menganalisis, mendokumentasikan, dan memvalidasi kebutuhan bisnis yang hendak diselesaikan oleh suatu solusi. Hasil analisis biasanya dituangkan dalam dokumen kebutuhan bisnis. Dalam praktik proyek, dokumen sejenis juga sering disebut **Business Requirements Document (BRD)**.

BRA menjawab pertanyaan bisnis seperti: masalah apa yang ingin ditangani, siapa yang membutuhkan solusi, hasil apa yang diharapkan, proses apa yang akan didukung, aturan apa yang wajib dipatuhi, serta bagaimana keberhasilan solusi akan diukur.

BRA berbeda dari dokumen desain teknis. BRA tidak perlu menentukan setiap nama class Laravel, nama route, struktur folder, atau detail implementasi SQL. Hal tersebut dibahas lebih lanjut dalam System Requirements Specification (SRS), System Design Document, ERD, dan technical design. Dokumen ini tetap menyebut batasan teknologi karena stack telah dipilih, tetapi kebutuhan bisnis harus tetap menjadi dasar keputusan teknis.

**Cara membaca dokumen:**
- **OBSERVED**: aktivitas yang pernah diamati langsung oleh pengembang selama PKL. Contoh yang telah disebutkan: pengecekan QR Code pada level rak serta pencatatan material number/serial number. Pengamatan tersebut tidak otomatis membuktikan seluruh SOP perusahaan.
- **GENERAL**: kebutuhan yang diusulkan berdasarkan praktik umum pengelolaan persediaan/gudang.
- **ASSUMPTION**: keputusan sementara untuk membuat prototype dapat dirancang dan diuji. Harus dikonfirmasi atau diubah jika ditemukan informasi yang lebih akurat.
- **MVP**: fitur minimum yang menjadi target versi pertama.
- **FUTURE**: fitur lanjutan yang tidak menghambat penyelesaian MVP.

# Ringkasan Eksekutif

Proyek ini bertujuan membangun aplikasi web **Inventory Management System** dengan pendekatan konsep ERP untuk membantu pencatatan dan pengendalian persediaan pada satu gudang yang mempunyai banyak area/lokasi/rak/level penyimpanan. Sistem dirancang agar dapat mengelola master data barang, lokasi penyimpanan, transaksi barang masuk, barang keluar, perpindahan lokasi, stock opname, saldo stok, riwayat pergerakan stok, serta laporan.

Versi pertama menggunakan pendekatan **single-warehouse, multi-location**: satu gudang operasional pada konfigurasi awal, dengan banyak lokasi penyimpanan di dalam gudang. Struktur data tetap menggunakan entitas gudang agar kemungkinan perluasan ke banyak gudang tidak mengharuskan perombakan total. Pengoperasian banyak gudang bukan sasaran MVP.

Sistem harus menjaga konsistensi stok. Perubahan stok hanya terjadi ketika transaksi yang valid diposting. Setiap perubahan harus menghasilkan catatan pergerakan stok yang dapat ditelusuri. Sistem tidak boleh mengizinkan stok negatif. Barang berserial harus memiliki identitas serial unik dan jumlah serial yang konsisten dengan kuantitas transaksi.

Aplikasi direncanakan menggunakan Laravel 12, PHP 8.3+, MySQL, Blade Template, Bootstrap 5, HTML, CSS, dan JavaScript. Lingkungan pengembangan menggunakan Laragon, Composer, Node.js/NPM, Visual Studio Code, Git, dan GitHub. OpenCode digunakan sebagai AI coding agent, tetapi keputusan kebutuhan, validasi hasil, pengujian, dan persetujuan perubahan tetap menjadi tanggung jawab pengembang.

**Hasil utama yang diharapkan:** prototype yang dapat didemonstrasikan dengan data dummy, mempunyai alur stok yang konsisten, hak akses dasar, jejak audit, laporan operasional, serta dokumentasi yang menjelaskan batasan dan asumsi sistem.

# 1. Informasi dan Kontrol Dokumen

| Atribut | Nilai |
|---|---|
| Nama dokumen | Business Requirements Analysis - Inventory Management System |
| Kode dokumen | BRA-IMS-001 |
| Versi | 1.0 |
| Tanggal | 18 November 2026 |
| Status | Draft baseline |
| Pemilik dokumen | Pengembang proyek |
| Jenis proyek | Proyek pribadi/prototype pembelajaran |
| Referensi proses perusahaan | Tidak tersedia; tidak ada SOP resmi yang menjadi dasar dokumen |
| Status validasi kebutuhan | Belum divalidasi oleh pemilik proses perusahaan |

## 1.1 Riwayat perubahan

| Versi | Tanggal | Perubahan | Penyusun |
|---|---|---|---|
| 1.0 | 18-11-2026 | Penyusunan baseline kebutuhan bisnis awal | Pengembang proyek |

## 1.2 Pihak yang dapat meninjau

| Pihak | Peran dalam proyek | Keterlibatan |
|---|---|---|
| Pengembang/siswa PKL | Pemilik dan pelaksana proyek | Menyusun kebutuhan, desain, implementasi, pengujian, dokumentasi |
| Pembimbing PKL | Pemberi arahan pembelajaran | Dapat memberi masukan dan meninjau hasil demonstrasi |
| Pengguna simulasi Admin Inventory | Aktor sistem | Menguji pengelolaan master data dan transaksi |
| Pengguna simulasi Warehouse Staff | Aktor sistem | Menguji penerimaan, pengeluaran, transfer, dan opname |
| Pengguna simulasi Manager/Viewer | Aktor sistem | Menguji dashboard dan laporan baca-saja |

Peran di atas adalah peran dalam prototype, bukan pernyataan mengenai struktur jabatan atau kewenangan resmi perusahaan.

# 2. Latar Belakang dan Konteks

Pengelolaan inventory memerlukan pencatatan yang konsisten atas identitas barang, jumlah persediaan, lokasi penyimpanan, dan perubahan stok. Jika pencatatan tersebar di beberapa file atau dilakukan tanpa jejak transaksi yang memadai, pengguna dapat mengalami kesulitan untuk mengetahui jumlah stok terkini, lokasi barang, riwayat pergerakan, dan penyebab selisih stok.

Selama kegiatan PKL, pengembang memperoleh konteks pembelajaran yang berkaitan dengan area Inventory, termasuk pengecekan QR Code pada level rak dan pencatatan material number/serial number pada label. Aktivitas tersebut menjadi inspirasi untuk memasukkan pengelolaan lokasi rak dan identitas barang ke dalam prototype. Namun, pengembang belum memperoleh SOP atau alur kerja resmi yang dapat digunakan untuk menetapkan proses internal perusahaan.

Oleh karena itu, proyek dirancang sebagai prototype mandiri untuk mempelajari dan menerapkan konsep pengelolaan inventory yang lazim, bukan untuk menggantikan aplikasi perusahaan atau menetapkan prosedur operasional perusahaan.

# 3. Pernyataan Masalah dan Peluang

## 3.1 Pernyataan masalah

Dalam konteks pembelajaran dan simulasi pengelolaan gudang, dibutuhkan satu aplikasi terpusat yang dapat menunjukkan hubungan antara master barang, lokasi penyimpanan, transaksi stok, saldo persediaan, dan laporan. Tanpa model data dan aturan transaksi yang konsisten, prototype berisiko hanya menjadi aplikasi CRUD yang mengubah angka stok tanpa riwayat yang dapat diaudit.

## 3.2 Peluang perbaikan

- Menyatukan master data dan transaksi inventory dalam satu aplikasi.
- Mengetahui jumlah stok berdasarkan barang dan lokasi penyimpanan.
- Menelusuri setiap penambahan, pengurangan, perpindahan, dan penyesuaian stok.
- Mendukung pencatatan material number dan serial number.
- Membandingkan stok sistem dengan hasil penghitungan fisik.
- Menyediakan laporan operasional untuk membantu demonstrasi dan evaluasi proyek.

## 3.3 Pernyataan solusi

Membangun aplikasi web modular yang menerapkan prinsip dasar inventory control: master data terkelola, transaksi berstatus, validasi kuantitas, pencatatan stock movement, saldo stok per lokasi, otorisasi berbasis peran, dan audit trail.

# 4. Tujuan Bisnis dan Indikator Keberhasilan

## 4.1 Tujuan

| ID | Tujuan | Hasil yang diharapkan |
|---|---|---|
| GOAL-01 | Memusatkan data master inventory | Pengguna mengelola barang, kategori, satuan, gudang, dan lokasi dari aplikasi yang sama |
| GOAL-02 | Mengendalikan perubahan stok | Stok berubah hanya melalui transaksi yang divalidasi dan diposting |
| GOAL-03 | Menelusuri pergerakan barang | Setiap perubahan stok memiliki referensi transaksi dan waktu pencatatan |
| GOAL-04 | Mengelola identitas barang | Material number dan serial number dapat dicatat sesuai jenis barang |
| GOAL-05 | Mendukung pemeriksaan fisik | Selisih antara stok sistem dan hitung fisik dapat dicatat dan ditindaklanjuti |
| GOAL-06 | Menyajikan informasi operasional | Dashboard dan laporan menampilkan informasi yang dapat difilter |
| GOAL-07 | Menerapkan kontrol akses | Aksi pengguna dibatasi berdasarkan peran dan dicatat |

## 4.2 Indikator keberhasilan proyek (target prototype)

Target berikut adalah kriteria demonstrasi yang diusulkan, bukan KPI perusahaan.

| ID | Indikator | Target penerimaan prototype |
|---|---|---|
| KPI-01 | Transaksi stok yang diposting memiliki stock movement | 100% pada skenario pengujian |
| KPI-02 | Stok negatif | 0 kasus pada seluruh skenario pengujian |
| KPI-03 | Konsistensi saldo | Saldo per barang-lokasi sama dengan hasil penjumlahan ledger pada skenario uji |
| KPI-04 | Validasi serial barang berserial | Jumlah serial valid sama dengan kuantitas transaksi |
| KPI-05 | Transfer | Total stok keseluruhan sebelum dan sesudah transfer tetap sama |
| KPI-06 | Otorisasi | Aksi tanpa hak akses ditolak pada skenario pengujian |
| KPI-07 | Audit trail | Transaksi posted menampilkan pelaku dan waktu pencatatan |
| KPI-08 | Dokumentasi | Kebutuhan, aturan bisnis, diagram, pengujian, dan panduan penggunaan tersedia |

# 5. Ruang Lingkup Proyek

## 5.1 Dalam ruang lingkup (MVP)

1. Login, logout, dan pengelolaan pengguna dasar.
2. Pengelolaan role dan permission dasar.
3. Master kategori barang dan satuan barang.
4. Master barang dengan kode barang/material number, nama, deskripsi, kategori, satuan, metode pelacakan, dan ambang stok minimum.
5. Master satu gudang operasional dan struktur lokasi penyimpanan.
6. Pencatatan supplier sebagai informasi referensi opsional untuk penerimaan barang.
7. Transaksi stock inbound.
8. Transaksi stock outbound.
9. Transaksi stock transfer antar lokasi dalam gudang yang sama.
10. Stock opname, pencatatan hasil hitung, selisih, dan penyesuaian melalui transaksi.
11. Saldo stok per barang dan lokasi.
12. Kartu stok/riwayat pergerakan barang.
13. Dashboard ringkas dan laporan dasar.
14. Audit trail untuk aktivitas penting.
15. Penggunaan data dummy/sintetis untuk pengujian dan demo.

## 5.2 Di luar ruang lingkup MVP

- Implementasi seluruh modul ERP perusahaan.
- Integrasi langsung dengan sistem ERP, procurement, finance, asset management, atau sistem internal perusahaan.
- Pembelian dan pengadaan lengkap, purchase requisition, purchase order, invoice, pembayaran, dan pencatatan akuntansi.
- Perhitungan nilai persediaan, costing FIFO, weighted average, standard cost, dan jurnal akuntansi.
- Pengelolaan aset tetap dan siklus hidup aset.
- Multi-company, multi-tenant, dan operasional multi-warehouse penuh.
- Integrasi API dengan sistem pihak ketiga atau sistem internal perusahaan.
- Aplikasi mobile native.
- Notifikasi email/WhatsApp otomatis.
- Peramalan permintaan dan optimasi persediaan berbasis machine learning.
- Work order, maintenance, dan manajemen proyek.
- Penggunaan data produksi perusahaan tanpa izin.

## 5.3 Fitur lanjutan (future)

- Pembuatan dan pemindaian QR Code/barcode lokasi serta barang.
- Approval workflow bertingkat.
- Multi-warehouse dan transfer antar gudang.
- Export Excel/PDF dengan format yang dapat dikonfigurasi.
- Reorder suggestion berdasarkan minimum stock dan histori konsumsi.
- Batch/lot tracking dan expiry date jika relevan dengan jenis barang.
- Integrasi procurement setelah kebutuhan dan kontrak antarmodul ditetapkan.

# 6. Pemangku Kepentingan dan Pengguna Sistem

## 6.1 Pemangku kepentingan

| Stakeholder | Kebutuhan/kepentingan | Tanggung jawab dalam proyek |
|---|---|---|
| Pengembang | Proyek dapat dibuat, diuji, dan dijelaskan | Mengambil keputusan desain dan menjaga dokumentasi |
| Pembimbing PKL | Hasil proyek relevan untuk pembelajaran | Memberi masukan jika berkenan |
| Admin Inventory (simulasi) | Mengelola data dan mengawasi transaksi | Menguji fungsi administrasi |
| Warehouse Staff (simulasi) | Mencatat aktivitas gudang dengan sederhana | Menguji alur operasional |
| Manager/Viewer (simulasi) | Memantau ringkasan dan laporan | Menguji fungsi baca-saja |

## 6.2 Definisi aktor

**A. System Administrator**  
Mengelola akun pengguna, role, konfigurasi aplikasi, dan data referensi sistem. Tidak boleh mengubah histori transaksi posted secara langsung.

**B. Inventory Administrator**  
Mengelola master data inventory, meninjau transaksi, menjalankan stock opname, dan melihat laporan. Dalam MVP dapat diberi kewenangan posting sesuai konfigurasi role.

**C. Warehouse Operator**  
Membuat draft penerimaan, pengeluaran, transfer, dan penghitungan fisik. Hak posting dapat dibatasi sesuai pengaturan role.

**D. Manager/Viewer**  
Melihat dashboard, saldo, kartu stok, serta laporan tanpa membuat atau mengubah transaksi.

> Role ini adalah model otorisasi prototype. Nama jabatan, pemisahan tugas, dan approval tidak menggambarkan struktur organisasi PLN Icon Plus.

# 7. Gambaran Proses Bisnis Target (To-Be)

Semua proses berikut merupakan alur rancangan prototype (GENERAL/ASSUMPTION).

## 7.1 Alur umum transaksi

1. Pengguna login.
2. Pengguna membuka modul transaksi.
3. Pengguna membuat dokumen berstatus Draft.
4. Pengguna mengisi header, lokasi, dan detail barang.
5. Sistem memvalidasi kelengkapan, status master data, kuantitas, serial number, dan ketersediaan stok jika relevan.
6. Pengguna melakukan aksi Post sesuai hak akses.
7. Sistem memproses posting dalam database transaction.
8. Sistem membuat stock movement dan memperbarui stock balance.
9. Sistem menandai dokumen menjadi Posted serta merekam pengguna dan waktu posting.
10. Pengguna dapat melihat dokumen dan kartu stok sebagai riwayat.

Jika proses posting gagal pada salah satu langkah, seluruh perubahan stok harus dibatalkan (rollback). Dokumen tidak boleh berada dalam keadaan setengah terposting.

## 7.2 Stock Inbound

**Tujuan:** mencatat barang yang diterima sehingga stok bertambah pada lokasi tujuan.

Alur:
1. Operator membuat dokumen penerimaan.
2. Operator mengisi tanggal transaksi, sumber penerimaan/referensi dokumen jika ada, dan catatan.
3. Operator menambahkan satu atau lebih item.
4. Untuk setiap item, operator memilih barang, lokasi tujuan, kuantitas, dan serial number jika barang berserial.
5. Sistem memvalidasi bahwa barang dan lokasi aktif, kuantitas lebih besar dari nol, serta serial number tidak duplikat/tidak sedang berada dalam stok.
6. Dokumen disimpan sebagai Draft.
7. Pengguna berwenang melakukan Post.
8. Sistem menambah saldo lokasi tujuan, membuat movement bertipe INBOUND, memperbarui status serial, dan mencatat audit trail.

**Hasil:** stok bertambah hanya setelah posting berhasil.

## 7.3 Stock Outbound

**Tujuan:** mencatat barang yang dikeluarkan sehingga stok berkurang pada lokasi asal.

Alur:
1. Operator membuat dokumen pengeluaran.
2. Operator mengisi tujuan/penerima atau alasan pengeluaran serta tanggal transaksi.
3. Operator memilih barang, lokasi asal, dan kuantitas.
4. Untuk barang berserial, operator memilih unit serial yang akan dikeluarkan.
5. Sistem memeriksa stok tersedia dan status serial.
6. Jika kuantitas melebihi stok tersedia, sistem menolak posting.
7. Pengguna berwenang melakukan Post.
8. Sistem mengurangi saldo, membuat movement OUTBOUND, mengubah status serial, dan mencatat audit trail.

**Hasil:** stok berkurang dan barang tidak lagi berstatus tersedia pada lokasi asal.

## 7.4 Stock Transfer

**Tujuan:** memindahkan barang antar lokasi penyimpanan tanpa mengubah total stok gudang.

Alur:
1. Operator membuat dokumen transfer.
2. Operator memilih lokasi asal dan lokasi tujuan yang berbeda.
3. Operator memilih barang dan kuantitas.
4. Untuk barang berserial, operator memilih serial number yang berada di lokasi asal.
5. Sistem memvalidasi stok dan lokasi.
6. Pengguna berwenang melakukan Post.
7. Sistem membuat dua movement dalam satu transaksi database: pengurangan pada lokasi asal dan penambahan pada lokasi tujuan.
8. Sistem memperbarui lokasi/status serial dan saldo kedua lokasi.

**Hasil:** saldo lokasi asal berkurang, lokasi tujuan bertambah, sedangkan total stok gabungan tetap.

## 7.5 Stock Opname

**Tujuan:** membandingkan kuantitas fisik dengan kuantitas sistem dan mencatat selisih secara terkendali.

Alur:
1. Admin membuat sesi opname untuk lokasi dan periode tertentu.
2. Sistem merekam snapshot kuantitas sistem pada saat sesi dimulai.
3. Operator memasukkan hasil hitung fisik per barang dan serial number jika berlaku.
4. Sistem menghitung selisih: kuantitas fisik dikurangi kuantitas sistem snapshot.
5. Selisih ditinjau oleh pengguna yang berwenang dan disertai alasan.
6. Setelah disetujui/diotorisasi sesuai konfigurasi prototype, sistem membuat transaksi penyesuaian.
7. Penyesuaian diposting sebagai stock movement bertipe OPNAME_ADJUSTMENT.
8. Sistem mencatat pelaku, waktu, dan alasan penyesuaian.

**Kontrol MVP yang diusulkan:** lokasi yang sedang menjalani opname tidak boleh menerima/mengeluarkan/memindahkan stok melalui transaksi lain sampai sesi opname selesai diposting atau dibatalkan. Alternatif yang lebih kompleks adalah perhitungan selisih yang memperhitungkan semua movement selama sesi opname; alternatif ini tidak wajib untuk MVP.

## 7.6 Pembatalan dan koreksi

- Dokumen Draft dapat diedit atau dihapus oleh role yang berwenang.
- Dokumen Posted tidak boleh dihapus atau diubah langsung.
- Kesalahan pada dokumen Posted dikoreksi dengan transaksi pembalik (reversal) atau transaksi penyesuaian yang memiliki referensi ke dokumen awal dan alasan koreksi.
- Fitur reversal dapat dibuat sederhana pada MVP, tetapi prinsip immutable history wajib dipertahankan.

# 8. Kebutuhan Bisnis (Business Requirements)

Kebutuhan bisnis menjelaskan hasil yang dibutuhkan, bukan detail kode.

| ID | Kebutuhan bisnis | Prioritas | Sumber/status |
|---|---|---|---|
| BR-001 | Sistem menyediakan satu sumber pencatatan master barang dan lokasi | Must | GENERAL |
| BR-002 | Sistem dapat menunjukkan saldo stok berdasarkan barang dan lokasi | Must | GENERAL |
| BR-003 | Penambahan stok harus dapat ditelusuri ke dokumen penerimaan | Must | GENERAL |
| BR-004 | Pengurangan stok harus dapat ditelusuri ke dokumen pengeluaran | Must | GENERAL |
| BR-005 | Perpindahan barang harus mempertahankan total stok gabungan | Must | GENERAL |
| BR-006 | Sistem mencegah transaksi yang menyebabkan stok negatif | Must | GENERAL |
| BR-007 | Setiap perubahan stok memiliki catatan pergerakan | Must | GENERAL |
| BR-008 | Sistem mendukung material number dan serial number | Must | OBSERVED + GENERAL |
| BR-009 | Sistem mendukung struktur gudang, area, rak, dan level | Must | OBSERVED + ASSUMPTION |
| BR-010 | Sistem mendukung perbandingan stok sistem dan stok fisik | Must | GENERAL |
| BR-011 | Pengguna hanya dapat menjalankan fungsi sesuai kewenangan | Must | GENERAL |
| BR-012 | Sistem menyediakan laporan saldo dan histori transaksi | Must | GENERAL |
| BR-013 | Aktivitas kritis pengguna dapat ditelusuri | Should | GENERAL |
| BR-014 | Sistem dapat dikembangkan menuju multi-warehouse | Could | ASSUMPTION |
| BR-015 | Sistem dapat dikembangkan untuk pemindaian QR/barcode | Could | OBSERVED + FUTURE |

# 9. Kebutuhan Fungsional (Functional Requirements)

Prioritas menggunakan MoSCoW: **Must** wajib untuk MVP, **Should** penting tetapi dapat menyusul jika waktu terbatas, **Could** fitur tambahan, **Won't (MVP)** tidak dikerjakan pada versi pertama.

## 9.1 Autentikasi dan pengguna

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-AUTH-001 | Sistem menyediakan login menggunakan akun terdaftar | Must | Akun valid dapat login; akun tidak valid ditolak |
| FR-AUTH-002 | Sistem menyediakan logout | Must | Sesi pengguna berakhir setelah logout |
| FR-AUTH-003 | Sistem membatasi halaman berdasarkan autentikasi | Must | Pengguna tanpa login diarahkan ke login |
| FR-USER-001 | Admin dapat membuat, melihat, mengubah, dan menonaktifkan pengguna | Must | Pengguna nonaktif tidak dapat login |
| FR-USER-002 | Admin dapat menetapkan role pengguna | Must | Hak menu dan aksi mengikuti role |
| FR-USER-003 | Sistem mencatat pengguna dan waktu aksi kritis | Should | Data pelaku/waktu terlihat pada detail transaksi |

## 9.2 Master data

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-MD-001 | Pengguna berwenang dapat mengelola kategori barang | Must | Kode/nama kategori tervalidasi |
| FR-MD-002 | Pengguna berwenang dapat mengelola satuan barang | Must | Satuan memiliki kode unik |
| FR-MD-003 | Pengguna berwenang dapat mengelola master barang | Must | Kode barang/material number unik dan field wajib tervalidasi |
| FR-MD-004 | Master barang menyimpan metode pelacakan | Must | Barang ditandai serialized atau non-serialized |
| FR-MD-005 | Master barang menyimpan minimum stock | Should | Sistem dapat menampilkan indikator stok minimum |
| FR-MD-006 | Pengguna berwenang dapat mengelola supplier referensi | Could | Data supplier dapat dipilih pada penerimaan |
| FR-WH-001 | Pengguna berwenang dapat mengelola gudang | Must | Kode gudang unik |
| FR-WH-002 | Pengguna berwenang dapat mengelola struktur area/rak/level/lokasi | Must | Kode lokasi unik dan memiliki parent yang valid |
| FR-WH-003 | Sistem dapat menonaktifkan barang/lokasi tanpa menghapus histori | Must | Data nonaktif tidak dipakai untuk transaksi baru |

## 9.3 Stock inbound

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-IN-001 | Pengguna dapat membuat dokumen inbound berstatus Draft | Must | Nomor dokumen dibuat unik |
| FR-IN-002 | Dokumen inbound dapat berisi beberapa baris barang | Must | Setiap baris memuat barang, lokasi, dan kuantitas |
| FR-IN-003 | Sistem memvalidasi kuantitas lebih besar dari nol | Must | Nilai nol/negatif ditolak |
| FR-IN-004 | Sistem memvalidasi serial number untuk barang berserial | Must | Jumlah serial valid sama dengan kuantitas dan tidak duplikat |
| FR-IN-005 | Pengguna berwenang dapat mem-post dokumen inbound | Must | Saldo bertambah satu kali setelah posting berhasil |
| FR-IN-006 | Sistem menyimpan referensi sumber penerimaan jika tersedia | Should | Referensi terlihat pada detail dokumen |

## 9.4 Stock outbound

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-OUT-001 | Pengguna dapat membuat dokumen outbound berstatus Draft | Must | Nomor dokumen unik |
| FR-OUT-002 | Dokumen outbound dapat berisi beberapa baris barang | Must | Tiap baris mempunyai lokasi asal dan kuantitas |
| FR-OUT-003 | Sistem memeriksa saldo tersedia sebelum posting | Must | Posting ditolak bila stok tidak cukup |
| FR-OUT-004 | Sistem memvalidasi serial yang dikeluarkan | Must | Serial harus berada di lokasi asal dan berstatus tersedia |
| FR-OUT-005 | Pengguna berwenang dapat mem-post dokumen outbound | Must | Saldo berkurang satu kali setelah posting berhasil |
| FR-OUT-006 | Sistem menyimpan tujuan/penerima/alasan pengeluaran | Must | Informasi tujuan tercatat pada dokumen |

## 9.5 Stock transfer

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-TRF-001 | Pengguna dapat membuat dokumen transfer berstatus Draft | Must | Nomor dokumen unik |
| FR-TRF-002 | Sistem mengharuskan lokasi asal dan tujuan berbeda | Must | Lokasi yang sama ditolak |
| FR-TRF-003 | Sistem memvalidasi ketersediaan stok pada lokasi asal | Must | Transfer melebihi saldo ditolak |
| FR-TRF-004 | Sistem membuat movement keluar dan masuk secara atomik | Must | Kegagalan salah satu sisi membatalkan seluruh transaksi |
| FR-TRF-005 | Sistem menjaga total stok gabungan saat transfer | Must | Total sebelum dan sesudah sama |

## 9.6 Stock opname

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-OPN-001 | Pengguna berwenang dapat membuat sesi opname | Must | Sesi memiliki lokasi, tanggal, dan status |
| FR-OPN-002 | Sistem merekam snapshot stok sistem saat sesi dimulai | Must | Nilai snapshot tidak berubah karena transaksi berikutnya |
| FR-OPN-003 | Operator dapat memasukkan hasil hitung fisik | Must | Nilai hitung tervalidasi |
| FR-OPN-004 | Sistem menghitung selisih stok | Must | Selisih = fisik - snapshot sistem |
| FR-OPN-005 | Penyesuaian memerlukan alasan dan kewenangan | Must | Penyesuaian tanpa alasan/otorisasi ditolak |
| FR-OPN-006 | Sistem membuat movement penyesuaian ketika sesi diposting | Must | Saldo mengikuti hasil penyesuaian dan histori tercatat |

## 9.7 Saldo, histori, dashboard, dan laporan

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-STK-001 | Sistem menampilkan saldo per barang dan lokasi | Must | Filter barang, gudang, lokasi tersedia |
| FR-STK-002 | Sistem menyediakan kartu stok | Must | Kartu stok menampilkan tanggal, tipe, referensi, masuk/keluar, dan saldo |
| FR-STK-003 | Sistem menyediakan pencarian dan filter transaksi | Must | Filter tanggal, nomor dokumen, tipe, status tersedia |
| FR-DASH-001 | Dashboard menampilkan ringkasan jumlah barang dan total kuantitas stok | Must | Angka berasal dari data transaksi/saldo |
| FR-DASH-002 | Dashboard menampilkan barang di bawah minimum stock | Should | Daftar mengikuti nilai minimum yang disetel |
| FR-REP-001 | Sistem menyediakan laporan inbound dan outbound | Must | Data dapat difilter menurut periode |
| FR-REP-002 | Sistem menyediakan laporan mutasi dan saldo stok | Must | Data konsisten dengan stock ledger |
| FR-REP-003 | Sistem menyediakan ringkasan hasil stock opname | Should | Menampilkan snapshot, hasil hitung, dan selisih |
| FR-REP-004 | Sistem dapat mengekspor laporan ke Excel/PDF | Could | Fitur ditambahkan setelah laporan layar stabil |

## 9.8 Audit dan kontrol transaksi

| ID | Kebutuhan fungsional | Prioritas | Kriteria ringkas |
|---|---|---|---|
| FR-AUD-001 | Sistem mencatat aksi create/update/post/cancel pada objek kritis | Should | Audit memuat aktor, waktu, aksi, dan referensi objek |
| FR-AUD-002 | Sistem mencegah perubahan langsung dokumen Posted | Must | Edit/hapus langsung ditolak |
| FR-AUD-003 | Sistem mengoreksi transaksi Posted melalui reversal/adjustment | Should | Koreksi memiliki alasan dan referensi transaksi awal |
| FR-AUD-004 | Posting transaksi dijalankan secara atomik | Must | Tidak ada saldo berubah sebagian jika terjadi error |

# 10. Aturan Bisnis (Business Rules)

Aturan berikut wajib diterapkan pada logika backend, bukan hanya melalui validasi tampilan.

| ID | Aturan bisnis |
|---|---|
| RULE-001 | Setiap barang harus mempunyai kode unik yang tidak boleh digunakan barang lain. |
| RULE-002 | Setiap kategori, satuan, gudang, dan kode lokasi harus unik dalam ruang lingkupnya. |
| RULE-003 | Barang dan lokasi nonaktif tidak dapat digunakan dalam transaksi baru. |
| RULE-004 | Kuantitas transaksi harus lebih besar dari nol. |
| RULE-005 | Kuantitas disimpan menggunakan tipe numerik presisi tetap (misalnya DECIMAL), bukan floating point. |
| RULE-006 | Barang non-serialized dilacak berdasarkan kuantitas. |
| RULE-007 | Barang serialized dilacak per unit serial; satu serial hanya boleh mempunyai satu status/lokasi aktif pada satu waktu. |
| RULE-008 | Untuk barang serialized, jumlah serial yang dicatat harus sama dengan kuantitas transaksi dan kuantitas harus bilangan bulat. |
| RULE-009 | Serial number tidak boleh didaftarkan dua kali untuk unit yang sama. Kebijakan format serial dapat dikonfigurasi. |
| RULE-010 | Transaksi Draft tidak mengubah saldo stok. |
| RULE-011 | Posting hanya dapat dilakukan oleh pengguna yang mempunyai permission sesuai. |
| RULE-012 | Dokumen yang sudah Posted tidak boleh diubah atau dihapus langsung. |
| RULE-013 | Inbound menambah saldo lokasi tujuan ketika posting berhasil. |
| RULE-014 | Outbound mengurangi saldo lokasi asal ketika posting berhasil dan tidak boleh menyebabkan stok negatif. |
| RULE-015 | Transfer mengurangi lokasi asal dan menambah lokasi tujuan dalam satu transaksi database. |
| RULE-016 | Lokasi asal dan tujuan transfer harus berbeda dan aktif. |
| RULE-017 | Total stok gabungan tidak berubah akibat transfer internal. |
| RULE-018 | Setiap perubahan saldo harus menghasilkan stock movement yang mempunyai referensi dokumen. |
| RULE-019 | Saldo stok tidak boleh diedit langsung melalui form master barang. Koreksi dilakukan lewat transaksi pembukaan saldo atau penyesuaian yang beralasan. |
| RULE-020 | Saldo opening balance harus dicatat melalui dokumen khusus/penyesuaian awal, bukan dengan memasukkan angka tanpa jejak. |
| RULE-021 | Selisih opname dihitung sebagai hasil fisik dikurangi snapshot sistem. |
| RULE-022 | Penyesuaian opname harus mempunyai alasan, pelaku, waktu, dan otorisasi. |
| RULE-023 | Selama sesi opname aktif pada lokasi tertentu, transaksi yang dapat mengubah stok lokasi tersebut diblokir pada MVP. |
| RULE-024 | Nomor dokumen transaksi harus unik dan tidak dapat digunakan ulang. |
| RULE-025 | Pembatalan dokumen Draft tidak menghasilkan stock movement. |
| RULE-026 | Koreksi transaksi Posted dilakukan melalui reversal atau adjustment yang dapat ditelusuri ke dokumen awal. |
| RULE-027 | Perubahan stok dan perubahan status dokumen harus berhasil atau gagal bersama-sama (atomic). |
| RULE-028 | Semua waktu transaksi disimpan konsisten; tampilan menggunakan zona waktu yang ditetapkan aplikasi (default proyek: Asia/Jakarta). |

# 11. Kebutuhan Data dan Entitas Bisnis

Bagian ini mendefinisikan kebutuhan informasi secara konseptual. Ini belum merupakan ERD final atau daftar migration Laravel.

## 11.1 Entitas master

| Entitas | Data penting | Catatan |
|---|---|---|
| User | nama, email/username, password hash, status | Data akun aplikasi |
| Role/Permission | nama role, permission | Mengatur akses fitur dan aksi |
| Warehouse | kode, nama, alamat/deskripsi, status | MVP mengoperasikan satu gudang |
| Warehouse Location | kode lokasi, nama, tipe, parent, status | Mewakili area/rak/level/bin sesuai kebutuhan |
| Category | kode, nama, deskripsi, status | Klasifikasi barang |
| Unit | kode satuan, nama, precision | Contoh: unit, pcs, meter, roll |
| Item | kode internal/material number, nama, deskripsi, kategori, satuan, tracking type, minimum stock, status | Master barang |
| Supplier | kode, nama, kontak, status | Referensi opsional penerimaan |
| Item Serial | item, serial number, status, lokasi saat ini | Hanya untuk barang serialized |

## 11.2 Entitas transaksi

| Entitas | Data penting | Catatan |
|---|---|---|
| Stock Document | nomor, tipe, tanggal, status, pembuat, pemosting, waktu posting, catatan | Header inbound/outbound/transfer/adjustment |
| Stock Document Line | dokumen, item, lokasi asal/tujuan, kuantitas, satuan | Detail transaksi |
| Stock Movement | item, lokasi, quantity delta, tipe movement, referensi dokumen, waktu, aktor | Ledger pergerakan stok |
| Stock Balance | item, lokasi, quantity on hand | Saldo terkini; hasil yang harus konsisten dengan ledger |
| Stock Opname | nomor sesi, lokasi, waktu snapshot, status, pembuat, peninjau | Header pemeriksaan fisik |
| Stock Opname Line | item, snapshot qty, counted qty, variance, alasan | Detail hitung dan selisih |
| Audit Log | aktor, aksi, objek, nilai penting, waktu | Jejak aktivitas kritis |

## 11.3 Relasi konseptual utama

- Satu kategori dapat memiliki banyak barang; satu barang memiliki satu kategori utama pada MVP.
- Satu satuan dapat digunakan oleh banyak barang.
- Satu gudang memiliki banyak lokasi penyimpanan.
- Struktur lokasi dapat bertingkat: Gudang → Area → Rack → Level → Bin/Slot. Level yang benar-benar dibutuhkan harus ditetapkan dari observasi; tidak semua tingkat wajib digunakan.
- Satu dokumen transaksi memiliki banyak baris detail.
- Satu baris detail dapat menghasilkan satu atau lebih stock movement, tergantung tipe transaksi dan pelacakan serial.
- Satu item dapat memiliki banyak serial number jika metode pelacakannya serialized.
- Saldo stok dihitung untuk kombinasi barang dan lokasi.
- Satu sesi opname memiliki banyak baris pemeriksaan.

## 11.4 Prinsip saldo dan ledger

**Stock Movement adalah jejak perubahan stok. Stock Balance adalah saldo terkini yang digunakan untuk pembacaan cepat.** Keduanya harus dijaga konsisten.

Contoh ledger non-serialized:

| Waktu | Tipe | Lokasi | Delta | Saldo setelah transaksi |
|---|---|---|---:|---:|
| 01 Nov | Opening Balance | Rack A / Level 1 | +100 | 100 |
| 03 Nov | Inbound | Rack A / Level 1 | +20 | 120 |
| 05 Nov | Outbound | Rack A / Level 1 | -15 | 105 |
| 06 Nov | Transfer Out | Rack A / Level 1 | -10 | 95 |
| 06 Nov | Transfer In | Rack B / Level 2 | +10 | 10 |

Total stok sebelum transfer adalah 105; total stok setelah transfer adalah 105. Nilai `quantity_delta` disimpan sebagai angka positif untuk penambahan dan negatif untuk pengurangan. Untuk transfer dibuat dua baris movement yang terkait ke dokumen transfer yang sama.

**Rumus konseptual:**

`Saldo(item, lokasi) = jumlah seluruh quantity_delta untuk item dan lokasi tersebut`

`Total stok item = jumlah saldo item di seluruh lokasi aktif`

Untuk sistem yang memakai stock reservation, rumus available stock dapat menjadi `on_hand - reserved`. Reservation tidak termasuk MVP; karena itu available stock pada MVP sama dengan on_hand.

## 11.5 Kebijakan data yang belum diputuskan

- Apakah material number sama dengan kode internal barang atau merupakan atribut eksternal terpisah.
- Apakah serial number selalu wajib untuk jenis barang tertentu atau hanya pada barang tertentu.
- Apakah nomor serial unik global atau unik dalam kombinasi item dan pabrikan.
- Apakah batch/lot number diperlukan.
- Apakah kuantitas pecahan diizinkan untuk semua satuan atau hanya satuan tertentu.
- Apakah supplier wajib pada inbound atau hanya opsional.
- Apakah stok minimum ditetapkan per item atau per lokasi.
- Berapa lama audit log dan transaksi disimpan.

Untuk MVP, gunakan keputusan eksplisit pada konfigurasi/desain dan dokumentasikan jika keputusan tersebut hanya asumsi.

# 12. Matriks Peran dan Hak Akses

Simbol: **C** = Create, **R** = Read, **U** = Update, **P** = Post/approve, **D** = Delete Draft/Nonaktifkan. Matriks ini merupakan usulan awal prototype.

| Modul/aksi | System Admin | Inventory Admin | Warehouse Operator | Manager/Viewer |
|---|---:|---:|---:|---:|
| Kelola pengguna dan role | C/R/U/D | - | - | - |
| Lihat master data | R | R | R | R |
| Kelola master barang/kategori/satuan | R/U | C/R/U/D | - | - |
| Kelola warehouse/location | R/U | C/R/U/D | R | R |
| Buat draft inbound/outbound/transfer | - | C/R/U | C/R/U | - |
| Post inbound/outbound/transfer | - | P | Sesuai permission; default tidak | - |
| Buat dan isi stock opname | - | C/R/U | C/R/U | - |
| Otorisasi/post penyesuaian opname | - | P | - | - |
| Lihat saldo dan kartu stok | R | R | R | R |
| Lihat laporan | R | R | R terbatas | R |
| Lihat audit log | R | R terbatas | - | - |

Prinsip pemisahan tugas: jika memungkinkan, pengguna yang membuat transaksi tidak harus menjadi satu-satunya pengguna yang mem-post atau menyetujui transaksi. Approval bertingkat dapat menjadi fitur lanjutan, tetapi permission untuk posting tetap harus dibedakan.

# 13. Kebutuhan Non-Fungsional (Non-Functional Requirements)

Target berikut ditujukan untuk aplikasi prototype dan perlu disesuaikan jika aplikasi kelak digunakan secara nyata.

| ID | Aspek | Kebutuhan/target |
|---|---|---|
| NFR-SEC-001 | Authentication | Password disimpan menggunakan hashing bawaan Laravel; tidak disimpan sebagai teks biasa |
| NFR-SEC-002 | Authorization | Backend memvalidasi permission; menyembunyikan tombol di UI saja tidak cukup |
| NFR-SEC-003 | Web security | Menggunakan proteksi CSRF, validasi server-side, escaping output Blade, dan query aman melalui ORM/query binding |
| NFR-SEC-004 | Session | Sesi dikelola dengan konfigurasi aman dan logout mengakhiri sesi |
| NFR-SEC-005 | Data exposure | Aplikasi demo tidak menggunakan data rahasia/operasional perusahaan tanpa izin |
| NFR-INT-001 | Data integrity | Perubahan dokumen, movement, serial, dan saldo dilakukan secara atomik dalam database transaction |
| NFR-INT-002 | Concurrency | Posting mengunci atau memvalidasi saldo secara transaksional agar dua permintaan bersamaan tidak menghasilkan stok negatif |
| NFR-INT-003 | Traceability | Setiap movement mempunyai referensi dokumen, waktu, dan aktor |
| NFR-PERF-001 | Performance | Pada dataset uji 1.000 item dan 5.000 stock movement, halaman utama dan daftar umum ditargetkan tampil dalam ≤3 detik pada lingkungan lokal normal; target ini bukan SLA produksi |
| NFR-PERF-002 | Pagination | Daftar barang/transaksi menggunakan pagination dan filter agar dataset besar tidak dimuat seluruhnya sekaligus |
| NFR-USE-001 | Usability | Form menggunakan label, validasi, pesan kesalahan yang jelas, dan konfirmasi sebelum posting |
| NFR-USE-002 | Responsive | Tampilan tetap dapat digunakan pada desktop dan layar tablet/ponsel melalui Bootstrap responsive layout |
| NFR-MNT-001 | Maintainability | Struktur kode mengikuti konvensi Laravel; business logic tidak ditumpuk di Blade |
| NFR-MNT-002 | Testability | Logika stok kritis mempunyai automated tests, terutama inbound, outbound, transfer, opname, dan serial |
| NFR-REL-001 | Reliability | Jika posting gagal, sistem melakukan rollback dan tidak meninggalkan saldo parsial |
| NFR-OPS-001 | Backup | Database demo dapat di-backup dan dipulihkan dengan prosedur terdokumentasi |
| NFR-COMP-001 | Compatibility | Aplikasi dikembangkan dan diuji pada versi PHP, Laravel, MySQL, Node, dan browser yang didokumentasikan |
| NFR-LOG-001 | Logging | Error teknis dicatat melalui mekanisme logging aplikasi tanpa membocorkan password/token |
| NFR-DOC-001 | Documentation | README, setup lokal, data dummy, role, aturan bisnis, dan panduan demo disertakan |

# 14. Kebutuhan Antarmuka dan Informasi

## 14.1 Navigasi utama yang diusulkan

- Dashboard
- Master Data
  - Barang
  - Kategori
  - Satuan
  - Supplier
- Warehouse
  - Gudang
  - Lokasi/Rak/Level
- Inventory Transactions
  - Stock Inbound
  - Stock Outbound
  - Stock Transfer
  - Stock Opname
- Inventory Reports
  - Stock Summary
  - Stock Card
  - Inbound Report
  - Outbound Report
  - Transfer Report
  - Opname Report
- User & Access Management
- Audit Log
- Settings

Menu yang tidak diizinkan untuk role tertentu tidak ditampilkan, tetapi pemeriksaan permission tetap dilakukan di backend.

## 14.2 Informasi dashboard

- Total item aktif.
- Total kuantitas on-hand (dengan catatan satuan berbeda tidak boleh dijumlahkan seolah satu unit yang sama; lebih baik tampilkan per kategori/satuan atau jumlah SKU).
- Jumlah SKU dengan stok di bawah minimum.
- Jumlah transaksi inbound dan outbound pada periode terpilih.
- Daftar transaksi terbaru.
- Daftar stok per lokasi.

**Catatan penting:** Jangan menjumlahkan `100 pcs + 20 meter + 5 roll` menjadi satu angka “total unit” karena satuannya berbeda. Gunakan jumlah SKU atau ringkasan terpisah per satuan.

## 14.3 Standar tampilan transaksi

Setiap halaman transaksi setidaknya menampilkan:
- Nomor dokumen.
- Tanggal transaksi.
- Status dokumen.
- Pembuat dan pemosting.
- Detail item, lokasi, dan kuantitas.
- Catatan/referensi.
- Tombol aksi sesuai status dan permission.
- Riwayat perubahan status.

# 15. Kebutuhan Laporan

| ID | Laporan | Kolom/informasi minimum | Filter |
|---|---|---|---|
| REP-01 | Stock Summary | Kode barang, nama, kategori, gudang, lokasi, saldo, satuan, minimum stock | Barang, kategori, lokasi |
| REP-02 | Stock Card | Tanggal, nomor dokumen, tipe movement, masuk, keluar, saldo berjalan, aktor | Barang, lokasi, periode |
| REP-03 | Inbound Report | Nomor dokumen, tanggal, sumber, item, lokasi, qty, status, pemosting | Periode, status, item |
| REP-04 | Outbound Report | Nomor dokumen, tanggal, tujuan, item, lokasi, qty, status, pemosting | Periode, tujuan, item |
| REP-05 | Transfer Report | Nomor dokumen, tanggal, lokasi asal, lokasi tujuan, item, qty, status | Periode, lokasi, item |
| REP-06 | Stock Opname Report | Nomor sesi, lokasi, snapshot qty, counted qty, variance, alasan, status | Periode, lokasi, status |
| REP-07 | Low Stock Report | Barang, lokasi (jika minimum per lokasi), saldo, minimum, selisih | Kategori, lokasi |
| REP-08 | Audit Activity Report | Aktor, aksi, objek, waktu, referensi | Aktor, aksi, periode |

# 16. Asumsi, Batasan, dan Ketergantungan

## 16.1 Asumsi

| ID | Asumsi |
|---|---|
| ASM-01 | MVP dijalankan sebagai prototype lokal dengan satu gudang operasional |
| ASM-02 | Satu gudang mempunyai banyak lokasi penyimpanan yang dapat disusun bertingkat |
| ASM-03 | Transaksi dibuat oleh pengguna internal aplikasi dengan role yang ditetapkan |
| ASM-04 | Inbound dan outbound dicatat manual; belum ada integrasi purchase order atau sistem lain |
| ASM-05 | Barang dibedakan menjadi serialized dan non-serialized |
| ASM-06 | Opening balance dimasukkan melalui dokumen saldo awal agar tetap mempunyai jejak |
| ASM-07 | Data untuk demo bersifat dummy/sintetis |
| ASM-08 | Aturan posting pada MVP menggunakan Draft dan Posted; approval bertingkat adalah pengembangan berikutnya |
| ASM-09 | Satu satuan dasar ditetapkan untuk setiap item pada MVP; konversi antar satuan belum menjadi fitur inti |
| ASM-10 | Stock opname mengunci lokasi selama sesi aktif untuk mencegah movement bersamaan |

## 16.2 Batasan

- Pengembang mengerjakan proyek sebagai pembelajaran mandiri dengan waktu dan sumber daya terbatas.
- Tidak tersedia dokumen SOP atau akses resmi terhadap rancangan sistem internal perusahaan.
- Proyek tidak memiliki otorisasi untuk mengintegrasikan atau mengubah sistem perusahaan.
- Aplikasi pertama kali ditujukan untuk lingkungan lokal Laragon.
- Kebenaran alur bisnis hanya dapat diklaim sebatas asumsi dan skenario yang telah diuji.

## 16.3 Ketergantungan

- PHP versi yang kompatibel dengan Laravel 12.
- Composer dan dependency Laravel.
- MySQL yang dikelola melalui Laragon.
- Node.js dan NPM untuk asset build jika digunakan.
- Git dan GitHub untuk version control.
- Ketersediaan data dummy untuk pengujian.
- OpenCode sebagai alat bantu coding, bukan sumber kebenaran kebutuhan.

# 17. Risiko dan Mitigasi

| ID | Risiko | Dampak | Mitigasi |
|---|---|---|---|
| RISK-01 | Asumsi alur bisnis berbeda dari proses nyata | Fitur tidak relevan | Labeli asumsi, dokumentasikan, dan minta masukan pembimbing bila memungkinkan |
| RISK-02 | Lingkup proyek terlalu besar | MVP tidak selesai | Prioritaskan kebutuhan Must; tunda integrasi ERP dan fitur lanjutan |
| RISK-03 | AI menghasilkan kode tanpa memahami aturan stok | Bug integritas data | Berikan AGENTS.md, review kode, dan automated tests |
| RISK-04 | Saldo stok tidak konsisten dengan ledger | Laporan salah | Gunakan transaksi database, constraint, locking, dan rekonsiliasi ledger |
| RISK-05 | Duplikasi serial number | Identitas unit tidak valid | Unique constraint dan validasi status serial di backend |
| RISK-06 | Dua pengguna mem-post transaksi bersamaan | Stok negatif/over-issue | Lock row atau mekanisme concurrency control saat posting |
| RISK-07 | Dokumen posted dapat diedit/dihapus | Audit trail rusak | Immutable posted documents; koreksi melalui reversal/adjustment |
| RISK-08 | Data internal perusahaan digunakan tanpa izin | Risiko kerahasiaan | Gunakan data sintetis; jangan unggah data internal ke AI/cloud tanpa izin |
| RISK-09 | Ketergantungan berlebihan pada OpenCode | Pengembang tidak memahami sistem | Review diff, minta penjelasan, uji manual, dan tulis dokumentasi sendiri |
| RISK-10 | Perubahan kebutuhan tidak tercatat | Scope creep | Gunakan change log dan revisi dokumen dengan versioning |

# 18. Kriteria Penerimaan (Acceptance Criteria)

## 18.1 Master data

- Pengguna berwenang dapat membuat item dengan kode unik.
- Sistem menolak kode item duplikat.
- Item nonaktif tetap dapat dilihat dalam histori tetapi tidak dapat dipakai untuk transaksi baru.
- Lokasi mempunyai kode unik dan relasi parent yang valid.

## 18.2 Inbound

- Membuat Draft tidak mengubah saldo.
- Mem-post inbound menambah saldo sesuai kuantitas.
- Posting dokumen yang sama untuk kedua kalinya ditolak atau tidak menghasilkan perubahan ganda.
- Barang serialized tidak dapat diposting jika jumlah serial tidak sama dengan kuantitas.
- Kegagalan saat posting tidak boleh menyisakan perubahan saldo parsial.

## 18.3 Outbound

- Membuat Draft tidak mengurangi saldo.
- Outbound dengan kuantitas melebihi saldo ditolak.
- Outbound berhasil mengurangi saldo dan membuat movement.
- Serial yang sudah keluar tidak dapat dikeluarkan lagi tanpa inbound/retur yang sah.

## 18.4 Transfer

- Transfer dengan lokasi asal sama dengan tujuan ditolak.
- Transfer dengan stok tidak cukup ditolak.
- Transfer berhasil mengurangi saldo asal dan menambah saldo tujuan.
- Total stok seluruh lokasi tidak berubah.
- Jika salah satu proses gagal, seluruh transfer dibatalkan.

## 18.5 Stock opname

- Sistem menyimpan snapshot saat sesi dibuat/dimulai.
- Sistem menghitung variance dengan rumus fisik dikurangi snapshot.
- Penyesuaian tanpa alasan atau hak akses ditolak.
- Posting penyesuaian menghasilkan movement dan mengubah saldo.

## 18.6 Otorisasi dan audit

- Pengguna tanpa permission tidak dapat mengakses aksi melalui URL langsung.
- Setiap posting mencatat pengguna dan waktu.
- Dokumen Posted tidak dapat diedit/hapus langsung.
- Aksi yang ditolak tidak mengubah saldo.

# 19. Skenario Uji Bisnis Minimum

Gunakan dataset kecil yang hasil akhirnya dapat dihitung manual.

**Skenario A - Inbound dan outbound**
1. Stok awal item A pada lokasi L1 = 0.
2. Post inbound 10 unit ke L1; saldo harus menjadi 10.
3. Post outbound 4 unit dari L1; saldo harus menjadi 6.
4. Coba outbound 7 unit; sistem harus menolak dan saldo tetap 6.

**Skenario B - Transfer**
1. Saldo item A pada L1 = 10 dan L2 = 2.
2. Transfer 3 unit dari L1 ke L2.
3. Hasil: L1 = 7, L2 = 5, total = 12.

**Skenario C - Serial number**
1. Item B bertipe serialized.
2. Inbound kuantitas 2 harus berisi dua serial unik.
3. Inbound dengan satu serial saja harus ditolak.
4. Outbound hanya dapat memilih serial yang berstatus tersedia di lokasi asal.
5. Serial yang sudah dikeluarkan tidak dapat dikeluarkan kembali.

**Skenario D - Stock opname**
1. Snapshot sistem item A pada L1 = 8.
2. Hasil hitung fisik = 7.
3. Variance = -1.
4. Setelah adjustment -1 diposting, saldo = 7 dan movement adjustment tercatat.

**Skenario E - Kegagalan atomik**
1. Simulasikan error ketika posting setelah validasi tetapi sebelum seluruh movement selesai.
2. Pastikan saldo, serial, dan status dokumen tidak mengalami perubahan parsial.

# 20. Pemetaan Kebutuhan ke Modul

| Kelompok kebutuhan | Modul utama | Dokumen desain lanjutan |
|---|---|---|
| BR-001, BR-008 | Master Data Barang | ERD, Data Dictionary |
| BR-002, BR-009 | Warehouse & Location | Location Hierarchy Diagram |
| BR-003, BR-007 | Stock Inbound | Inbound Activity Diagram |
| BR-004, BR-006 | Stock Outbound | Outbound Activity Diagram |
| BR-005 | Stock Transfer | Transfer Activity Diagram |
| BR-010 | Stock Opname | Opname Activity Diagram |
| BR-011 | Authentication & Authorization | Use Case, Role-Permission Matrix |
| BR-012, BR-013 | Dashboard, Reporting, Audit | Report Specification, Audit Data Design |
| BR-014, BR-015 | Future Integration & QR/Barcode | Future Architecture Note |

# 21. Pertanyaan Terbuka yang Perlu Diputuskan

Pertanyaan ini tidak menghalangi penyusunan baseline, tetapi harus dijawab sebelum desain database dan alur transaksi dibekukan.

| ID | Pertanyaan | Rekomendasi awal untuk MVP |
|---|---|---|
| Q-01 | Apakah material number merupakan kode unik barang atau atribut terpisah? | Simpan `item_code` internal dan `material_number` sebagai field terpisah jika sumbernya berbeda; jika identik, dokumentasikan pemetaan |
| Q-02 | Apakah semua barang memakai serial number? | Tidak; tetapkan tracking type per item: serialized/non-serialized |
| Q-03 | Apakah serial number wajib unik global? | Terapkan unique constraint global untuk MVP, kecuali domain data membuktikan perlu aturan lain |
| Q-04 | Seberapa dalam struktur lokasi? | Mulai dari warehouse-area-rack-level; bin/slot opsional |
| Q-05 | Apakah kuantitas boleh desimal? | Ikuti precision satuan; serialized wajib bilangan bulat |
| Q-06 | Apakah inbound harus terkait supplier? | Supplier opsional pada MVP |
| Q-07 | Apakah outbound harus memakai approval? | Draft-Posted dengan permission posting; approval bertingkat future |
| Q-08 | Bagaimana koreksi posted transaction? | Reversal atau adjustment dengan referensi dokumen awal |
| Q-09 | Bagaimana lokasi opname dilindungi dari transaksi paralel? | Kunci lokasi selama sesi opname aktif pada MVP |
| Q-10 | Apakah minimum stock ditentukan per item atau per lokasi? | Per item untuk MVP; perluas ke item-lokasi jika dibutuhkan |
| Q-11 | Apakah retur barang perlu didukung? | Tunda; rancang sebagai tipe transaksi terpisah setelah alur outbound stabil |
| Q-12 | Apakah perlu batch/lot/expiry tracking? | Tidak termasuk MVP; evaluasi menurut jenis barang |

# 22. Rekomendasi Prioritas Implementasi

## Tahap 1 - Fondasi
- Setup Laravel 12 dan koneksi MySQL.
- Authentication dan role/permission.
- Master kategori, satuan, barang.
- Master gudang dan lokasi.
- Migration, relasi model, factory, dan seeder data dummy.

## Tahap 2 - Mesin transaksi stok
- Desain stock document dan stock document lines.
- Desain stock movement dan stock balance.
- Opening balance.
- Stock inbound.
- Stock outbound.
- Automated tests untuk stok negatif dan posting ganda.

## Tahap 3 - Pergerakan lokasi dan opname
- Stock transfer dengan dua movement atomik.
- Serial number lifecycle.
- Stock opname, snapshot, variance, dan adjustment.

## Tahap 4 - Operasional dan informasi
- Dashboard.
- Stock summary dan stock card.
- Laporan inbound/outbound/transfer/opname.
- Audit log.

## Tahap 5 - Penyempurnaan
- QR Code/barcode.
- Export Excel/PDF.
- Approval workflow.
- Backup/restore dan deployment.

**Gerbang kualitas:** jangan lanjut ke tahap berikutnya jika tes konsistensi stok tahap sebelumnya belum lulus.

# 23. Panduan Menggunakan Dokumen Ini dengan OpenCode

Dokumen BRA ini merupakan sumber konteks bisnis. OpenCode harus diminta membaca dokumen sebelum membuat perubahan besar. Jangan meminta AI membuat semua modul sekaligus.

## 23.1 Instruksi kerja untuk OpenCode

1. Baca `docs/01-business-requirements-analysis.md` sampai selesai.
2. Ringkas tujuan, batasan, aktor, modul MVP, business rules, dan pertanyaan terbuka.
3. Tandai konflik atau kebutuhan yang belum didefinisikan.
4. Jangan mengarang SOP atau menyatakan sistem sebagai sistem resmi perusahaan.
5. Jangan mengimplementasikan fitur di luar scope tanpa meminta persetujuan pengembang.
6. Usulkan rancangan teknis dan jelaskan konsekuensi setiap keputusan.
7. Implementasikan per modul dan sertakan automated tests.
8. Setelah mengubah kode, jelaskan file yang berubah, alasan, cara pengujian, serta risiko yang belum ditangani.

## 23.2 Prompt awal untuk OpenCode (Plan mode)

```text
Baca dokumen docs/01-business-requirements-analysis.md secara menyeluruh.

Tugas Anda hanya melakukan analisis dan perencanaan. Jangan mengubah file kode,
jangan menjalankan migration, dan jangan membuat fitur terlebih dahulu.

Buat hasil analisis yang berisi:
1. Ringkasan tujuan dan batasan proyek.
2. Daftar aktor, role, dan permission.
3. Daftar modul MVP dan dependensi antar modul.
4. Daftar business rules yang paling berpengaruh terhadap integritas stok.
5. Daftar entitas data dan relasi konseptual.
6. Pertanyaan terbuka, konflik, dan asumsi yang perlu diputuskan.
7. Rekomendasi urutan implementasi.
8. Risiko teknis untuk transaksi stok, serial number, concurrency, dan stock opname.

Jangan menganggap dokumen ini sebagai SOP resmi perusahaan.
Jangan menambahkan kebutuhan yang tidak tercantum tanpa menandainya sebagai usulan.
Tunggu persetujuan saya sebelum melakukan implementasi.
```

## 23.3 Prompt review setelah satu modul selesai

```text
Review modul yang baru dikerjakan terhadap dokumen BRA.

Periksa:
- Kesesuaian requirement dan business rules.
- Validasi di backend dan authorization.
- Integritas stock movement dan stock balance.
- Potensi duplicate posting, negative stock, dan race condition.
- Automated tests serta kasus batas (edge cases).
- Apakah ada perubahan scope atau asumsi baru.

Jangan mengubah kode sebelum menyampaikan temuan dan rencana perbaikan.
```

# 24. Persetujuan dan Status Validasi

Karena ini proyek pribadi, persetujuan formal perusahaan tidak diasumsikan. Status setiap bagian dapat ditandai berikut:

| Bagian | Status saat dokumen dibuat |
|---|---|
| Konteks proyek pribadi | Ditetapkan oleh pengembang |
| Stack teknologi | Ditetapkan oleh pengembang |
| Kebutuhan umum inventory | Usulan baseline |
| Alur inbound/outbound/transfer/opname | Asumsi prototype, belum divalidasi |
| Struktur gudang/rak/level | Terinspirasi dari pengamatan terbatas, perlu pemodelan lebih lanjut |
| Kebijakan serial number | Asumsi prototype |
| Role dan approval | Usulan prototype |
| Kebijakan operasional perusahaan | Tidak diketahui/tidak diklaim |

**Catatan persetujuan:** Perubahan terhadap dokumen harus dicatat pada riwayat perubahan. Jika pembimbing memberikan masukan, catat sebagai masukan/review dan ubah kebutuhan hanya setelah dipahami dampaknya.

# 25. Glosarium

| Istilah | Definisi |
|---|---|
| ERP | Enterprise Resource Planning; pendekatan sistem terintegrasi untuk berbagai proses bisnis organisasi |
| Inventory | Persediaan barang/material yang dikelola organisasi |
| WMS | Warehouse Management System; sistem untuk mendukung operasi gudang dan lokasi penyimpanan |
| SKU | Stock Keeping Unit; identitas item yang digunakan untuk pengelolaan persediaan |
| Material Number | Nomor identifikasi material; makna dan format spesifik harus ditentukan oleh pemilik data |
| Serial Number | Identitas unik untuk satu unit barang tertentu |
| Stock Inbound | Transaksi penerimaan yang menambah persediaan |
| Stock Outbound | Transaksi pengeluaran yang mengurangi persediaan |
| Stock Transfer | Perpindahan barang antar lokasi/gudang |
| Stock Opname | Penghitungan fisik untuk dibandingkan dengan catatan sistem |
| Stock Movement | Catatan perubahan kuantitas persediaan |
| Stock Balance | Saldo kuantitas persediaan pada suatu barang dan lokasi |
| On-hand | Kuantitas yang tercatat berada pada lokasi/gudang |
| Available Stock | Kuantitas yang tersedia untuk digunakan; pada MVP sama dengan on-hand karena reservation belum dibuat |
| Draft | Dokumen yang masih dapat diedit dan belum mengubah stok |
| Posted | Dokumen yang telah diproses dan mengubah saldo serta ledger |
| Reversal | Transaksi pembalik untuk mengoreksi transaksi yang sudah diposting |
| Audit Trail | Jejak yang merekam siapa melakukan apa dan kapan |
| MVP | Minimum Viable Product; versi pertama dengan fitur inti yang dapat diuji |
| BRA | Business Requirements Analysis; proses/dokumen analisis kebutuhan bisnis |
| BRD | Business Requirements Document; dokumen yang merangkum kebutuhan bisnis |
| SRS | Software/System Requirements Specification; spesifikasi kebutuhan sistem yang lebih rinci |
| ERD | Entity Relationship Diagram; diagram entitas dan hubungan data |
| CRUD | Create, Read, Update, Delete |
| RBAC | Role-Based Access Control; kontrol akses berbasis peran |

---

## Penutup

Dokumen BRA versi 1.0 menetapkan baseline kebutuhan untuk membangun prototype Inventory Management System berbasis konsep ERP dengan fokus pada satu gudang dan banyak lokasi penyimpanan. Dokumen ini sengaja membatasi ruang lingkup agar implementasi dapat diselesaikan dan diuji dengan baik.

Sebelum coding dimulai, langkah berikutnya adalah mengubah kebutuhan ini menjadi artefak desain sistem: **Use Case Diagram, Activity Diagram, ERD, Data Dictionary, dan spesifikasi alur transaksi**. Setelah artefak tersebut konsisten dengan BRA, barulah implementasi Laravel 12 dilakukan secara bertahap.

**Akhir dokumen - BRA-IMS-001, Versi 1.0**
