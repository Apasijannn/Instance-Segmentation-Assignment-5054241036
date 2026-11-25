# Instance Segmentation pada Lingkungan Indoor Nyata

Tugas Evaluasi CPMK 3 – Visi Komputer (EA234503), Institut Teknologi Sepuluh Nopember.
Muhammad Dayyan Ghazanfar Latief · NRP 5054241036 · Rekayasa Kecerdasan Artifisial

Repo ini membandingkan tiga model instance segmentation yang dilatih pada [Panoptic Indoor Segmentation v5](https://universe.roboflow.com/panopticindoor/panoptic-indoor-segmentation), lalu menguji model tersebut di test set asli dan di 44 foto ruangan dari kamera HP sendiri. Variabel eksperimen dari digit terakhir NRP (6) adalah **augmentasi**: tanpa, default, dan rancangan sendiri.

| Model | Keluarga | Library |
| --- | --- | --- |
| YOLO11s-seg | One-stage CNN | Ultralytics |
| YOLOv8s-seg | One-stage CNN | Ultralytics |
| Mask R-CNN R50-FPN v2 | Two-stage CNN | torchvision |

## Isi repo

```
.
├── notebooks/        Notebook yang dijalankan di Kaggle, lengkap dengan output-nya
│   ├── eda_panoptic.ipynb     EDA dataset dan perbandingan dengan foto HP (EDA-Panoptic v1)
│   ├── training.ipynb         Training 3 model + eksperimen augmentasi (train_instance_seg v4)
│   └── testing.ipynb          Evaluasi test set asli vs HP, AP per kelas/foto, analisis kegagalan (Viskom-Testing v17)
├── results/
│   ├── training/     Log training asli (results.csv, results.png, kurva), waktu GPU, konfigurasi kelas
│   └── testing/      Tabel hasil, AP per kelas dan per foto, grafik augmentasi, galeri prediksi,
│                     5 foto gagal (failure_cases/) dan eksperimen pembuktian (failure_experiment/)
├── evidence/         Bukti test set HP: folder Drive, album HP, anotasi Roboflow, EXIF, foto kondisi sulit
├── docs/
│   ├── laporan_instance_segmentation_5054241036.pdf   Laporan penelitian
│   ├── logbook.md                                     Logbook: hipotesis, entri kegiatan, waktu GPU, keputusan, deklarasi AI
│   └── img/                                           Gambar yang dipakai logbook dan laporan
└── demo/
    ├── demo_inferensi.ipynb   Inferensi foto baru dengan 3 model berdampingan (jalan di CPU lokal)
    └── foto_*.jpg             Foto baru yang tidak dipakai saat training/testing
```

## Hasil singkat (mask mAP50-95, testing v17)

| Model | Test set asli | Test set HP |
| --- | --- | --- |
| YOLO11s-seg | 0,1004 | 0,1225 |
| YOLOv8s-seg | 0,1036 | 0,1475 |
| Mask R-CNN R50-FPN v2 | 0,1015 | 0,1076 |

Kondisi tanpa augmentasi memberi skor tertinggi untuk ketiga model. Pembahasan lengkap ada di [laporan](docs/laporan_instance_segmentation_5054241036.pdf), dan prosesnya tercatat di [logbook](docs/logbook.md).

## Ringkasan project

1. **Data latih:** Panoptic Indoor Segmentation v5, 397 gambar latih, 166 validasi, dan 359 test. Kelas yang salah ketik digabung sehingga 74 kelas menjadi 72.
2. **Test set sendiri:** 44 foto ruangan dari Samsung Galaxy S24 Ultra (679 objek, 50 kelas), dianotasi polygon di Roboflow. Foto mencakup kondisi sulit: cahaya redup, backlight, sudut ekstrem, dan objek bertumpuk.
3. **Training:** ketiga model dilatih di Kaggle (Tesla T4) dengan input 640 px dan batas 1 jam GPU per kondisi, masing-masing untuk tiga kondisi augmentasi.
4. **Domain shift:** ketiga model mencapai mask mAP50-95 sekitar 0,10 di test set asli dan lebih tinggi di foto HP (0,108–0,148). Kenaikan ini dipengaruhi kelas test asli yang tidak ada di data latih serta komposisi kelas foto HP, bukan bukti model lebih baik di HP.
5. **Augmentasi:** kondisi tanpa augmentasi justru terbaik untuk ketiga model, sedangkan augmentasi rancangan sendiri tidak unggul berarti dibanding default.
6. **Analisis kegagalan:** pada lima foto tersulit, kegagalan utama berasal dari kelas langka (misalnya `toilet`, 2 objek latih) yang tertukar dengan kelas lain, serta cahaya redup, backlight, dan sudut ekstrem. Menaikkan input ke 960 px dengan retina mask hanya menambah 0,004 mAP50-95, jadi resolusi mask bukan penyebab utama.
7. **Kesimpulan:** perbaikan yang paling menjanjikan adalah menambah contoh latih untuk kelas langka dan kondisi sulit, bukan menaikkan resolusi atau memperkuat augmentasi.
