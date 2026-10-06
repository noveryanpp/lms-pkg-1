# PROJECT BRIEF

Learning Management System

Dokumen ini memberikan gambaran mengenai ruang lingkup solusi, fitur yang direncanakan, serta alur penggunaan sistem sebagai dasar penyelarasan pemahaman antara tim pengembang dan pihak terkait. Masukan dan revisi dari seluruh pemangku kepentingan sangat diharapkan untuk memastikan solusi yang dikembangkan sesuai dengan kebutuhan dan tujuan organisasi.

# RINGKASAN PROYEK

Dokumen ini menjelaskan rencana pengembangan Learning Management System, yang dirancang untuk mengatasi tantangan pelaksanaan pelatihan yang selama ini mengharuskan peserta hadir secara langsung di lokasi tertentu. Kondisi tersebut sering kali menimbulkan kendala berupa tingginya biaya perjalanan, keterbatasan waktu, serta berkurangnya aksesibilitas bagi peserta yang berada di wilayah yang jauh dari lokasi pelatihan.

Tujuan dari proyek ini adalah menyediakan platform pembelajaran digital yang memungkinkan proses pelatihan, distribusi materi, evaluasi, dan pemantauan perkembangan peserta dilakukan secara daring dan terpusat. Dengan demikian, peserta dapat mengikuti pelatihan dari lokasi masing ~~-~~ masing tanpa mengurangi kualitas proses pembelajaran.

Pendekatan yang digunakan berfokus pada penyediaan pengalaman belajar yang mudah diakses, terstruktur, dan fleksibel, sehingga dapat mendukung kebutuhan pelatihan dalam skala yang lebih luas. Melalui implementasi LMS ini, organisasi diharapkan dapat meningkatkan efisiensi penyelenggaraan pelatihan, memperluas jangkauan peserta, mengurangi biaya operasional, serta mempermudah pengelolaan dan pelaporan aktivitas pembelajaran.

# TANTANGAN & SOlusi

#### Keterbatasan Akses dan Fleksibilitas Pelatihan Tatap Muka

Pelaksanaan pelatihan yang mengharuskan peserta hadir secara langsung di lokasi tertentu dapat membatasi akses bagi peserta yang berada di luar wilayah pelatihan atau memiliki keterbatasan waktu untuk melakukan perjalanan. Kondisi ini berpotensi mengurangi jangkauan program pelatihan serta meningkatkan biaya yang harus dikeluarkan oleh peserta maupun penyelenggara. Untuk mengatasi hal tersebut, diperlukan platform pembelajaran daring yang memungkinkan peserta mengikuti pelatihan dari mana saja secara lebih fleksibel.

#### Pengelolaan Materi dan Proses Pembelajaran yang Belum Terpusat

Materi pembelajaran, aktivitas pelatihan, serta proses evaluasi sering kali dikelola melalui berbagai media yang berbeda. Hal ini dapat menyulitkan peserta dalam mengakses materi dan menyulitkan penyelenggara dalam memastikan proses pembelajaran berjalan secara konsisten. Melalui Learning Management System (LMS), seluruh aktivitas pembelajaran dapat dikelola dalam satu platform yang terintegrasi dan lebih mudah digunakan.

#### Monitoring dan Evaluasi Peserta yang Kurang Efisien

Pemantauan perkembangan peserta serta proses evaluasi hasil pelatihan dapat menjadi lebih kompleks seiring bertambahnya jumlah peserta. Tanpa sistem yang terstruktur, proses monitoring dan pelaporan memerlukan waktu yang lebih banyak dan berpotensi menimbulkan ketidaktepatan data. Untuk mendukung pengelolaan yang lebih efektif, sistem akan menyediakan fitur pelacakan progres, evaluasi pembelajaran, serta pelaporan yang dapat diakses secara terpusat.

#### Efisiensi Operasional dan Skalabilitas Program Pelatihan

Seiring berkembangnya program pelatihan, kebutuhan pengelolaan peserta, distribusi materi, dan penerbitan sertifikat akan semakin meningkat. Jika dilakukan secara manual, proses tersebut dapat menambah beban operasional dan membatasi kemampuan organisasi untuk menjangkau lebih banyak peserta. Dengan implementasi LMS, berbagai proses administrasi dapat diotomatisasi sehingga program pelatihan dapat berkembang dengan lebih efisien dan terukur.

# TARGET PENGGUNA

## Peserta Pelatihan

Peserta merupakan pengguna utama platform yang mengikuti program pembelajaran. Peserta dapat mengakses materi, mengikuti proses pembelajaran dan evaluasi, memantau perkembangan belajar, serta memperoleh sertifikat setelah memenuhi persyaratan yang ditentukan.

## Instruktur

Instruktur berperan dalam menyusun dan menyampaikan materi pembelajaran kepada peserta. Melalui platform, instruktur dapat mengelola materi pelatihan, mendukung proses evaluasi, serta memantau perkembangan peserta selama mengikuti program.

## Administrator Platform

Administrator Platform bertanggung jawab terhadap pengelolaan operasional sistem secara keseluruhan. Peran ini mencakup pengelolaan pengguna, program pelatihan, konten pembelajaran, pengaturan sistem, serta monitoring aktivitas yang berlangsung di dalam platform.

## Administrator Lembaga

Administrator Lembaga bertanggung jawab terhadap operasional sertifikasi kompetensi. Peran ini mencakup pengelolaan proses sertifikasi, data peserta yang mengikuti sertifikasi, administrasi asesmen, serta pengelolaan status dan penerbitan sertifikat sesuai dengan ketentuan lembaga.

# ALUR PENGGUNA

## Peserta

<!-- Start of picture text -->

Daftar → Login → Pilih Kursus → Belajar →<br>Asesmen → Lihat Progress → Lulus → Sertifikat

## Instruktur

Login → Buat Kursus → Unggah Materi →<br>Buat Asesmen → Publish → Pantau Progress

## Administrator Platform

Login → Kelola Pengguna → Kelola Kursus →<br>Monitoring Aktivitas → Laporan

## Administrator Lembaga

Login → Lihat Data Peserta → Verifikasi →<br>Kelola Status Sertifikasi → Menerbitkan Sertifikat<br>

<!-- End of picture text -->

# OPSI SOLUSI

## Opsi 1 – Platform Website

### Kelebihan

- Dapat diakses langsung dari browser

- Implementasi lebih cepat

- Biaya pengembangan relatif lebih rendah

- Perubahan (update) dapat langsung diterapkan

- Mudah ditemukan melalui mesin pencari seperti Google

### Pertimbangan

- Notifikasi sebatas browser dan email

- Konten lebih mudah diakses atau disalin oleh pihak tidak berwenang

- Pembatasan jumlah perangkat yang menggunakan akun yang sama lebih sulit

- Membutuhkan koneksi internet untuk sebagian besar aktivitas

## Opsi 2 – Mobile Application

### Kelebihan

- Pengalaman lebih optimal pada perangkat mobile

- Dukungan push notification yang lebih efektif untuk meningkatkan engagement pengguna

- Keamanan konten lebih baik

- Dapat menerapkan pembatasan perangkat yang menggunakan akun yang sama

### Pertimbangan

- Memerlukan proses publikasi ke Google Play Store atau Apple Store

- Biaya pengembangan relatif lebih tinggi

- Pengembangan membutuhkan waktu lebih lama karna memerlukan proses review aplikasi dari Google

- Perubahan (update) mengharuskan pengguna untuk memperbarui aplikasi ke versi terbaru

- Memungkinan untuk implementasi fitur offline

## Rekomendasi Pendekatan

Jika fokus utama adalah pengalaman belajar dan engagement pengguna, Mobile App dapat menjadi platform utama. Jika prioritasnya adalah kecepatan implementasi, fleksibilitas, dan efisiensi pengembangan awal, Website dapat menjadi pilihan yang lebih sesuai.

Apabila ingin menggabungkan kedua kebutuhan tersebut, pendekatan Mobile App sebagai platform utama + Website Landing Page dapat dipertimbangkan. Website berfungsi sebagai media informasi dan discovery melalui mesin pencari, sementara seluruh aktivitas pembelajaran dilakukan melalui Mobile App.

# RUANG LINGKUP

Ruang lingkup proyek mencakup tahapan utama yang diperlukan untuk merancang, membangun, menguji, dan memastikan kesiapan platform sebelum digunakan oleh pengguna. Setiap tahap dilakukan secara bertahap untuk memastikan solusi yang dikembangkan sesuai dengan kebutuhan bisnis, dapat direalisasikan secara teknis, serta memiliki kualitas dan performa yang baik saat digunakan.

## Lingkup Pekerjaan

#### Research & Analysis

Melakukan riset terkait kemungkinan implementasi fitur serta menganalisis kebutuhan bisnis dan teknis proyek.

#### Design

Merancang alur, UI/UX, struktur sistem, serta perencanaan pengembangan berdasarkan hasil analisis.

#### Development

Mengembangkan aplikasi beserta fitur dan integrasi sesuai dengan scope yang telah disepakati.

#### Testing

Melakukan pengujian untuk memastikan seluruh fungsi aplikasi berjalan sesuai kebutuhan dan memperbaiki masalah yang ditemukan.

Deployment Monitoring & Mempersiapkan dan Maintenance melakukan deployment Dilakukan selama 3 bulan aplikasi agar siap pertama untuk digunakan oleh memastikan aplikasi pengguna. berjalan dengan lancar.

# ROADMAP

Roadmap pengembangan dibagi menjadi beberapa fase untuk memastikan platform dapat diluncurkan dengan fitur inti terlebih dahulu, kemudian dikembangkan secara bertahap berdasarkan kebutuhan pengguna dan perkembangan bisnis. Fitur pada Phase 2 dan Phase 3 bersifat indikatif dan dapat disesuaikan berdasarkan hasil evaluasi, kebutuhan pengguna, serta perkembangan bisnis setelah Phase 1 berjalan.

## phase 1: Core Platform

Fokus: membangun fondasi dan fitur utama agar platform dapat digunakan untuk menjalankan proses pembelajaran dan sertifikasi. Cakupan:

Akses Pengguna Manajemen Kursus Asesmen & Evaluasi Sertifikasi Dashboard Administrasi

## phase 2: Growth

Fokus: meningkatkan pengalaman pengguna, engagement, serta efektivitas pengelolaan pembelajaran. Cakupan:

Push Notification Rating & Review Kursus Ruang Diskusi

## phase 3: Scale & Expansion

Fokus: mendukung pertumbuhan platform, perluasan layanan, dan pengembangan model bisnis. Cakupan:

Membership Paket Kursus Multi ~~-~~ language Integrasi Sistem Eksternal

# PHASE 1

Phase 1 berfokus pada pembangunan fondasi utama Learning Management System yang memungkinkan proses pembelajaran dan sertifikasi dapat dilakukan secara terstruktur dalam satu platform. Fase ini mencakup fitur ~~-~~ fitur inti yang dibutuhkan agar platform dapat mulai digunakan oleh peserta, instruktur, administrator platform, dan administrator lembaga.

## feature 1: Akses Pengguna

Menyediakan mekanisme registrasi, login, profil pengguna, serta pengaturan hak akses berdasarkan peran pengguna. Cakupan:

Registrasi & Login Profil Pengguna Manajemen Akses

## feature 2: Manajemen Kursus

Menyediakan sistem untuk mengelola dan menyelenggarakan program pembelajaran secara terstruktur.

Cakupan:

Daftar & detail kursus Struktur materi Modul Video Pengelolaan kursus

## feature 3: Asesmen & Evaluasi

Menyediakan sistem untuk mengelola dan menyelenggarakan program pembelajaran secara terstruktur.

Cakupan:

Pre ~~-~~ Test & Post ~~-~~ Test Quiz Hasil Evaluasi Riwayat Asesmen

## feature 4: Sertifikasi

Mendukung proses administrasi sertifikasi setelah peserta memenuhi persyaratan yang telah ditentukan.

Cakupan:

Data sertifikasi peserta Penerbitan sertifikat digital Riwayat sertifikasi

## feature 5: Dashboard

Menyediakan informasi yang membantu pengguna melihat aktivitas dan perkembangan pembelajaran.

Peserta:

Kursus yang diikuti Progress pembelajaran Hasil assessment Sertifikat Admin/Instruktur: Data kursus & peserta Ringkasan aktivitas pembelajaran Hasil asesmen

Admin/Instruktur:

## feature 6: Administrasi

Menyediakan fungsi administrasi untuk mendukung pengelolaan platform dan program pelatihan.

Cakupan:

<!-- Start of picture text -->

Pengelolaan pengguna Pengelolaan kursus & materi Pengelolaan assessment<br>Pengelolaan data sertifikasi Monitoring aktivitas platform<br>Pengelolaan data & laporan dasar<br><!-- End of picture text -->

# TIMELINE PHASE 1

Timeline bersifat estimasi dan dapat menyesuaikan berdasarkan hasil finalisasi scope, feedback, kesiapan materi dari pihak client, serta kebutuhan teknis selama proses development.

#### Week 1 Research & Analysis

Analisis kebutuhan, validasi fitur, dan finalisasi scope Phase 1

#### Week 2 Design & Planning

User flow, UI/UX design, struktur sistem, dan technical planning

#### Week 3-6 Development

Pengembangan fitur phase 1

#### Week 7 Testing

Functional testing, perbaikan bug, dan refinement berdasarkan hasil testing

#### Week 8 Deployment & Handover

Deployment ke environment production, konfigurasi awal, dan handover

# Penutup

Dokumen Project Brief ini disusun sebagai gambaran awal mengenai kebutuhan, tujuan, ruang lingkup, serta arah pengembangan platform. Detail fitur, alur, timeline, dan kebutuhan teknis masih dapat disesuaikan berdasarkan hasil diskusi dan validasi bersama sebelum proses development dimulai.

Kami menyarankan agar pengembangan dilakukan secara bertahap dengan menyelesaikan Phase 1 – Core Platform terlebih dahulu. Pendekatan ini memungkinkan platform inti untuk digunakan dan divalidasi sebelum pengembangan fitur lanjutan pada Phase 2 dan Phase 3 dilakukan.

Dengan demikian, kebutuhan pengembangan dapat dievaluasi berdasarkan penggunaan nyata dan feedback dari pengguna, sehingga pengembangan fitur berikutnya dapat dilakukan secara lebih terarah dan sesuai dengan prioritas bisnis.

Setelah dokumen ini ditinjau, langkah berikutnya adalah melakukan pembahasan bersama untuk memvalidasi prototype, finalisasi scope Phase 1, menentukan pilihan platform, serta menyesuaikan timeline pengembangan. Setelah scope dan kebutuhan telah disepakati, pembahasan mengenai estimasi biaya pengembangan dapat dilakukan sebagai tahap berikutnya.

Catatan: Timeline dan ruang lingkup yang tercantum dalam dokumen ini merupakan estimasi awal dan dapat berubah berdasarkan hasil finalisasi kebutuhan, perubahan scope, serta kebutuhan teknis yang ditemukan selama proses analisis.
