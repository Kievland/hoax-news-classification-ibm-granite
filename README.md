# Proyek Klasifikasi Berita Hoax dengan IBM Granite

Capstone Project untuk kelas Data Classification and Summarization oleh HACKTIV8.

## Latar Belakang (Project Overview)
Penyebaran misinformasi dan berita hoax di Indonesia semakin mengkhawatirkan dan dapat memecah belah masyarakat. Maka dari itu diperlukan sebuah cara untuk membantu publik mengidentifikasi konten yang berpotensi hoax secara cepat.

## Dataset
Proyek ini memanfaatkan dataset publik **Deteksi Berita Hoaks Indo** yang tersedia di Kaggle. Untuk menciptakan sebuah lingkungan pengujian yang realistis dan seimbang, dataset final dirakit dengan menggabungkan beberapa sumber data yang memiliki karakteristik berbeda.
### Sumber Data
Dataset ini terdiri dari dua kategori utama:
* **Sumber Data Fakta (Label 0):** Berita-berita ini diambil dari beberapa portal berita nasional terkemuka di Indonesia, yaitu:
    * CNN Indonesia
    * Kompas
    * Detikcom
* **Sumber Data Hoax (Label 1):** Berita-berita ini bersumber dari situs inisiatif pengecekan fakta (fact-checking) tepercaya:
    * TurnBackHoax.id
### Strategi Penggabungan Data
Penggabungan sumber-sumber ini merupakan langkah strategis untuk membangun model yang tangguh. Alasannya adalah:
1.  **Menciptakan Keberagaman:** Dengan mengambil berita fakta dari tiga media besar yang berbeda, model dapat belajar mengenali berbagai macam gaya penulisan jurnalistik yang valid dan profesional.
2.  **Memastikan Kualitas Label:** Menggunakan TurnBackHoax sebagai sumber data hoax memastikan bahwa artikel yang dilabeli 'hoax' adalah konten yang sudah terverifikasi sebagai misinformasi.
3.  **Simulasi Dunia Nyata:** Kombinasi antara berita jurnalistik asli dan berita hoax yang terverifikasi menciptakan dataset yang seimbang dan mencerminkan tantangan nyata dalam membedakan informasi yang benar dan salah di internet.
Setelah proses penggabungan dan pembersihan, dataset final yang digunakan dalam proyek ini terdiri dari **24.675 artikel**, dengan komposisi **12.646 berita fakta (51.2%)** dan **12.029 berita hoax (48.8%)**.


## Proses Analisis
Proses analisis dalam proyek ini dilakukan melalui beberapa tahapan sistematis, mulai dari persiapan data hingga evaluasi performa model.
### 1. Pengumpulan & Persiapan Data
Tahap awal berfokus pada perakitan dataset yang solid dan seimbang dari berbagai sumber. Prosesnya meliputi:
* **Identifikasi Sumber:** Data dikumpulkan dari dataset publik "Deteksi Berita Hoaks Indo". Sumber berita Fakta (label `0`) diidentifikasi berasal dari media kredibel (CNN, Detik, Kompas), sementara sumber berita Hoax (label `1`) berasal dari situs pengecekan fakta (TurnBackHoax).
* **Penggabungan Data:** Setiap sumber data terdiri dari dua file: file `RAW` yang berisi teks lengkap berita dan file `Summarized` yang berisi label. Kedua file ini digabungkan (`merge`) berdasarkan URL unik untuk menciptakan dataset yang utuh per sumber.
* **Konsolidasi Dataset:** Semua data dari sumber Fakta dan Hoax kemudian disatukan (`concat`) menjadi satu DataFrame akhir. Hasilnya adalah sebuah dataset yang seimbang berisi **24.675 artikel**, siap untuk digunakan dalam proses klasifikasi.

### 2. Model & Platform
Infrastruktur teknis yang digunakan dalam proyek ini adalah sebagai berikut:
* **Model:** Model AI yang digunakan adalah **`ibm-granite/granite-3.0-8b-instruct`**, sebuah Large Language Model (LLM) yang dioptimalkan untuk tugas instruksional seperti klasifikasi dan peringkasan.
* **Platform:**
    * **Google Colab:** Digunakan sebagai lingkungan pengembangan utama untuk menulis dan mengeksekusi kode Python.
    * **Replicate:** Digunakan sebagai platform penyedia API untuk mengakses dan berinteraksi dengan model IBM Granite.

### 3. Prompt Engineering
Untuk mendapatkan output yang optimal dari model, dilakukan proses penyempurnaan prompt.
* **Prompt Awal:** Pengujian dimulai dengan prompt sederhana yang hanya meminta klasifikasi "Hoax" atau "Fakta".
* **Prompt Terstruktur (Final):** Prompt kemudian disempurnakan untuk meminta output dalam format **JSON** yang terstruktur. Model diinstruksikan untuk memberikan jawaban dengan dua kunci: `{"klasifikasi": "...", "alasan": "..."}`. Pendekatan ini tidak hanya memberikan label klasifikasi, tetapi juga wawasan kualitatif mengenai alasan di balik keputusan model, serta memastikan output yang konsisten dan mudah diolah secara terprogram.

### 4. Evaluasi Model
Performa model diukur secara kuantitatif melalui metodologi berikut:
* **Sampling:** Sebuah sampel acak sebanyak **100 artikel** diambil dari dataset final. Penggunaan `random_state` memastikan bahwa sampel ini dapat direproduksi untuk konsistensi pengujian. Sampel yang dihasilkan terdistribusi secara seimbang (55 berita Fakta dan 45 berita Hoax).
* **Klasifikasi Otomatis:** Sebuah *loop* dibuat untuk memproses setiap artikel dalam sampel. Setiap artikel dikirim ke model melalui API, dan hasilnya (label prediksi dan alasan) disimpan. Jeda 1 detik diterapkan antar panggilan API untuk menjaga stabilitas.
* **Metrik Performa:** Hasil prediksi dari model kemudian dibandingkan dengan label asli menggunakan metrik standar, yaitu **Akurasi** untuk melihat performa keseluruhan, serta **Laporan Klasifikasi** (Presisi, Recall, F1-Score) untuk menganalisis performa model pada setiap kelas secara lebih mendalam.




## Hasil & Temuan (Insight & Findings)

Evaluasi terhadap 100 sampel data acak menunjukkan performa model yang menjanjikan, dengan beberapa karakteristik yang menarik untuk dianalisis lebih dalam.

### 1. Akurasi Model
Model AI berhasil mencapai akurasi keseluruhan sebesar **82.83%**. Artinya, dari 99 artikel yang berhasil diproses dalam sampel uji, model mampu memberikan klasifikasi yang benar untuk 82 artikel di antaranya.

### 2. Laporan Klasifikasi
Untuk memahami performa model secara lebih rinci pada setiap kelas, berikut adalah laporan klasifikasi detailnya:
 
**Penjelasan Laporan:**
* **Sangat Efektif Mendeteksi Hoax:** Model ini menunjukkan kekuatan utamanya dalam mengidentifikasi berita hoax, dengan skor **Recall untuk kelas Hoax mencapai 96%**. Ini berarti, jika ada berita hoax, model ini memiliki probabilitas 96% untuk berhasil menangkapnya.
* **Cenderung Terlalu "Curiga":** Kekuatan di atas diimbangi dengan kecenderungan model untuk bersikap terlalu "sensitif". Skor **Precision untuk kelas Hoax yang hanya 74%** menandakan bahwa ketika model menebak sesuatu sebagai "Hoax", ada kemungkinan 26% tebakannya salah (berita fakta yang dituduh hoax). Hal ini juga tercermin pada skor **Recall untuk kelas Fakta yang lebih rendah (72%)**, yang menunjukkan bahwa model melewatkan atau salah mengklasifikasikan 28% dari berita fakta.

### 3. Analisis Kesalahan
Analisis kualitatif pada kasus-kasus di mana model melakukan kesalahan memberikan wawasan lebih dalam mengenai karakteristiknya:

* ***False Positive* (Fakta Dibilang Hoax):** Ini adalah jenis kesalahan yang paling sering dilakukan oleh model. Alasan utamanya adalah model cenderung terkecoh oleh **berita fakta yang menggunakan judul bombastis, membahas topik yang sangat kontroversial, atau memiliki gaya penulisan yang sensasional**. Meskipun isinya valid, ciri-ciri tersebut mirip dengan pola yang biasa ditemukan pada berita hoax, sehingga model salah mengklasifikasikannya.

* ***False Negative* (Hoax Dibilang Fakta):** Meskipun lebih jarang terjadi, ini adalah jenis kesalahan yang paling berbahaya. Kesalahan ini umumnya terjadi pada **berita hoax yang ditulis dengan sangat rapi, meniru struktur dan gaya bahasa jurnalistik profesional, serta mencatut nama atau kutipan tokoh terkenal secara meyakinkan**. Kualitas tulisan yang tinggi ini membuat AI kesulitan untuk membedakannya dari berita fakta yang asli.

## Kesimpulan & Rekomendasi

### Kesimpulan
Proyek ini telah berhasil mengembangkan dan mengevaluasi sebuah sistem klasifikasi berita hoax berbahasa Indonesia dengan memanfaatkan kekuatan Large Language Model (LLM), yaitu **IBM Granite**. Dengan merakit dataset yang seimbang dari berbagai sumber media kredibel dan situs pengecekan fakta, model ini berhasil mencapai tingkat akurasi yang solid sebesar **82.83%** pada pengujian sampel.

Temuan utama dari proyek ini adalah karakteristik model yang cenderung memprioritaskan **sensitivitas tinggi terhadap hoax (Recall 96%)**. Meskipun sangat efektif dalam menjaring konten misinformasi, hal ini menghasilkan *trade-off* berupa kecenderungan *False Positive*, di mana sejumlah berita fakta ikut terklasifikasi sebagai hoax. Proyek ini membuktikan bahwa LLM memiliki potensi besar sebagai alat bantu untuk memerangi misinformasi, namun performanya harus dianalisis secara mendalam di luar metrik akurasi semata untuk memahami perilaku dan biasnya.

### Rekomendasi
Untuk pengembangan proyek ini di masa depan, beberapa langkah dapat ditempuh untuk meningkatkan performa dan fungsionalitasnya:
1.  **Penyempurnaan Prompt Lanjutan:** Melakukan eksperimen lebih lanjut pada *prompt* dengan menambahkan instruksi yang lebih spesifik untuk mengurangi kecenderungan *False Positive*. Misalnya, dengan menambahkan aturan seperti "Klasifikasikan sebagai Hoax hanya jika Anda memiliki tingkat keyakinan yang sangat tinggi".
2.  **Tuning Parameter Model:** Mengeksplorasi penyesuaian parameter model seperti `top_p` dan `temperature` untuk melihat dampaknya terhadap "kreativitas" dan "kehati-hatian" model dalam membuat keputusan klasifikasi.
3.  **Pengembangan Aplikasi Prototipe:** Mengembangkan model ini menjadi sebuah aplikasi sederhana (misalnya, *web app* atau *browser extension*) di mana pengguna dapat memasukkan URL atau teks berita untuk mendapatkan klasifikasi secara *real-time*. Hal ini akan menjadi demonstrasi praktis dari kegunaan model.
## Cara Menjalankan
Berikan instruksi singkat jika orang lain mau mencoba notebook lo. Misal: "Untuk menjalankan notebook ini, Anda memerlukan API Token dari Replicate yang disimpan di Google Colab Secrets dengan nama `REPLICATE_API_TOKEN`."
