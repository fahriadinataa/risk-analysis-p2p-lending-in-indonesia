# Risiko yang Tersembunyi di Balik Angka TWP90 2,6% 
**Membedah Konsentrasi Risiko Kredit pada Industri Fintech P2P Lending (LPBBTI) Indonesia**

**[Click here to download the full Power BI Dashboard](https://drive.google.com/file/d/1P_epljBDFlkhsvix-Jclwbm8-GSpLTvc/view?usp=sharing)**

## 1. Problem Statement

Per Desember 2024, industri Layanan Pendanaan Bersama Berbasis Teknologi Informasi (LPBBTI) atau yang lebih dikenal sebagai fintech P2P lending mencatatkan TWP90 (Tingkat Wanprestasi 90 hari) nasional sebesar 2,6%, angka yang terlihat sehat dan berada dalam batas wajar.

Namun, angka agregat ini menyembunyikan sesuatu.

Ketika dipecah berdasarkan kategori peminjam, **TWP90 pada segmen Badan Usaha ternyata mencapai lebih dari 9%, hampir 4 kali lipat dari rata-rata nasional**, dan portofolionya justru menyusut dari waktu ke waktu.

Kenapa rata-rata nasional bisa begitu jauh berbeda dari kondisi riil di level segmen? 

## 2. Dataset Overview

- **Sumber** : OJK - Statistik LPBBTI, Otoritas Jasa Keuangan (OJK) — Desember 2024  
- **Periode Analisis** : Desember 2023 – Desember 2024                     
- **Cakupan** : 97 penyelenggara fintech lending berizin OJK (90 konvensional, 7 syariah) 
- **Data Model** : Star Schema (8 Fact Table + 6 Dimension Table)

## 3. Objectives & Business Questions

- Bagaimana kesehatan finansial industri LPBBTI secara keseluruhan?
- Segmen peminjam mana yang menunjukkan pertumbuhan sehat, dan mana yang berisiko?
- Apakah risiko kredit tersebar merata secara geografis, atau terkonsentrasi di wilayah tertentu?
- Siapa yang paling menanggung dampak apabila risiko kredit ini terwujud menjadi gagal bayar?

## 4. Methodology

### Data Cleaning and Restructuring
Data mentah OJK berbentuk laporan (bukan format siap analisis) — kolom bulan tersebar horizontal (wide format), header bertingkat, dan subtotal region yang menyatu dengan data granular. Proses pembersihan dilakukan melalui Power Query, mencakup:

- Unpivot 26 kolom bulanan menjadi struktur long format
- Pemisahan section header dari baris detail menggunakan conditional column dan fill down
- Eliminasi baris subtotal/grand total yang dapat dihitung ulang, sekaligus mempertahankan baris yang secara struktural tampak seperti subtotal namun sebenarnya merupakan data mandiri (contoh: kategori "Luar Negeri" pada data lokasi)
- Pemisahan dimensi independen (Gender dan Kelompok Umur) menjadi fact table terpisah, mengingat kombinasi keduanya tidak pernah muncul dalam data sumber

### Penanganan Data Akumulatif (Year-to-Date)
Temuan penting dalam tahap eksplorasi: data Laba Rugi OJK bersifat kumulatif sejak Januari dan mengalami reset setiap awal tahun. Jika langsung dianalisis tanpa penyesuaian, data ini akan selalu menunjukkan tren "naik terus" — bukan mencerminkan performa bisnis aktual, melainkan sekadar efek akumulasi. Seluruh metrik turunan dari Laba Rugi (termasuk ROA dan ROE) diproses ulang menggunakan window function di MySQL untuk memperoleh nilai bulanan murni.

### Growth-vs-Risk Framework
Inti dari analisis ini adalah mengidentifikasi kombinasi pertumbuhan tinggi dengan risiko tinggi pada tiga dimensi: lokasi, kategori peminjam, dan demografi. Setiap segmen diklasifikasikan ke dalam empat kuadran berdasarkan growth outstanding dan TWP90, menggunakan nilai rata-rata industri sebagai garis pembagi.

### Tools
- **Power Query** : Transformasi struktur data mentah dari format laporan menjadi format analisis (unpivot, split, star schema).
- **MySQL** : Pembersihan data lanjutan, penghitungan de-akumulasi, growth rate (MoM/YoY), dan klasifikasi kuadran risiko menggunakan window function.  
- **Power BI** : Visualisasi interaktif serta perhitungan growth YoY dan proyeksi tren.
  
## 5. Key Findings

**Finding 1**  
Kesehatan makro industri membaik, namun ada sinyal di penghujung tahun
Total Aset tumbuh 22,6% YoY dan Laba Bersih melonjak 245,2% YoY. Struktur permodalan juga menguat secara konsisten sepanjang tahun — DER turun dari 1,03 ke 0,80, sementara Current Ratio naik dari 1,56 ke 2,25. Meski TWP90 nasional turun 11,1% secara tahunan, terdapat kenaikan 3,5% pada bulan terakhir yang menjadi sinyal awal dari temuan berikutnya.

**Finding 2**  
Dua portofolio yang bergerak berlawanan arah
Segmen Perseorangan menunjukkan pola sehat: outstanding tumbuh dan porsi bermasalah stagnan. Sebaliknya, segmen Badan Usaha menunjukkan portofolio yang mengecil sekaligus porsi macetnya membesar — kombinasi yang jauh lebih mengkhawatirkan dibanding sekadar TWP90 tinggi, karena mengindikasikan penyelenggara sudah mulai menahan penyaluran baru sementara portofolio lama belum tertangani.

**Finding 3**  
Risiko juga terkonsentrasi pada demografi tertentu
TWP90 meningkat seiring bertambahnya usia peminjam, dengan kelompok usia di atas 54 tahun menunjukkan pola yang serupa dengan segmen Badan Usaha: jumlah rekening menurun namun outstanding dan TWP90 justru meningkat.

**Finding 4**  
Risiko lokasi berkorelasi dengan kematangan pasar, bukan sekadar tinggi-rendah
Provinsi dengan penetrasi fintech lending yang masih baru menunjukkan growth tinggi dengan TWP90 rendah, kemungkinan besar karena portofolio yang belum cukup umur untuk menunjukkan risiko sesungguhnya. Sebaliknya, provinsi dengan pasar yang sudah mapan justru menunjukkan TWP90 lebih tinggi meski pertumbuhannya melambat — mencerminkan risiko yang lebih matang dan lebih dapat diandalkan untuk dianalisis.

**Finding 5**  
Basis pendanaan bertumpu pada institusi, namun eksposur ritel terus meluas
Institusi menguasai 92% dari total outstanding lender, dengan Bank Umum sebagai kontributor tunggal terbesar (58%). Meski demikian, jumlah rekening lender perorangan terus bertumbuh dari waktu ke waktu — mengindikasikan bahwa dampak risiko kredit, jika terwujud, akan semakin menyentuh masyarakat umum, bukan hanya institusi keuangan.

## 6. Business Recommendations

### Untuk Penyelenggara
**Evaluasi kriteria underwriting segmen Badan Usaha**  
Evaluasi kriteria underwriting segmen Badan Usaha
Mengingat pola portofolio yang mengecil sekaligus memburuk, diperlukan audit terhadap kriteria credit scoring pada segmen ini, disertai penguatan strategi collection untuk portofolio yang sudah berjalan sebelum semakin banyak yang jatuh ke kategori macet penuh.

**Perhatian khusus pada segmen usia di atas 54 tahun**  
Mengingat pola risikonya menyerupai segmen Badan Usaha, perlu investigasi lanjutan untuk memahami karakteristik peminjam pada kelompok ini secara lebih mendalam.

### Untuk Regulator
**Perkuat monitoring TWP90 pada level segmen, bukan hanya agregat nasional**  
Rata-rata nasional yang terlihat sehat dapat menyembunyikan konsentrasi risiko yang signifikan. Pelaporan wajib TWP90 berdasarkan kategori peminjam, sebagaimana yang sudah diterapkan untuk lokasi, akan memperkuat sistem deteksi dini.

**Pantau progresif provinsi dengan portofolio yang masih muda**   
TWP90 rendah pada provinsi berkembang berpotensi bersifat sementara. Diperlukan ambang batas pemantauan yang disesuaikan dengan usia penetrasi pasar, bukan disamakan dengan provinsi yang sudah mapan.

### Untuk Investor dan Lender
**Diversifikasi basis pendanaan institusi**  
Dominasi Bank Umum sebesar 58% dari total outstanding lender menciptakan risiko konsentrasi pada sisi pendanaan. Diversifikasi ke IKNB, dana pensiun, dan koperasi akan memperkuat ketahanan struktur pendanaan industri.

**Transparansi risiko bagi lender ritel**  
Seiring bertambahnya basis lender perorangan, penyelenggara maupun regulator perlu memastikan lender individu memahami profil risiko segmen yang mereka danai, bukan semata tergiur imbal hasil.

*All recommendations are designed to improve customer retention so the business is less dependent on constantly acquiring new buyers.*

## 7. Project Structure


## 8. Dashboard Preview
**Overview Industri**
<img width="1300" height="730" alt="Screenshot 2026-07-26 180720" src="https://github.com/user-attachments/assets/fa5f7b2f-886a-4a45-80dd-a13fdd682cfa" />

**Analisis Lender**
<img width="1301" height="728" alt="Screenshot 2026-07-26 180830" src="https://github.com/user-attachments/assets/b6dd00c5-0adf-434a-8796-81dcac0f2637" />

**Analisis Loan**
<img width="1200" height="673" alt="Screenshot 2026-07-26 183109" src="https://github.com/user-attachments/assets/044f781f-b32c-4787-b7bd-ea175e204839" />

## 9. Limitation
Beberapa keterbatasan data perlu diperhatikan dalam membaca analisis ini: breakdown TWP90 tidak tersedia pada level sektor ekonomi maupun UMKM/Non-UMKM, sehingga analisis pada kedua dimensi tersebut terbatas pada growth dan komposisi. Selain itu, ditemukan anomali pelaporan pada periode Juli hingga September 2024 pada beberapa metrik jumlah entitas, yang kemungkinan besar terkait dengan revisi data resmi OJK untuk periode Juni 2024 sebagaimana tercatat pada catatan kaki dataset. Detail lengkap tersedia pada dokumentasi data quality di repository ini.
Beberapa keterbatasan data perlu diperhatikan dalam membaca analisis ini: breakdown TWP90 tidak tersedia pada level sektor ekonomi maupun UMKM/Non-UMKM, sehingga analisis pada kedua dimensi tersebut terbatas pada growth dan komposisi. Selain itu, ditemukan anomali pelaporan pada periode Juli hingga September 2024 pada beberapa metrik jumlah entitas, yang kemungkinan besar terkait dengan revisi data resmi OJK untuk periode Juni 2024 sebagaimana tercatat pada catatan kaki dataset. Detail lengkap tersedia pada dokumentasi data quality di repository ini.
