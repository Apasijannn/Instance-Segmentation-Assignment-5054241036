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

## Menjalankan ulang

**Training dan testing (Kaggle)**

1. Settings: Accelerator GPU T4, Internet On; Add-ons → Secrets: `ROBOFLOW_API_KEY`.
2. `notebooks/training.ipynb` → Save & Run All. Output versi sebelumnya bisa dijadikan input agar model yang sudah selesai tidak dilatih ulang.
3. `notebooks/testing.ipynb` → tambahkan output training sebagai input, lalu Save & Run All.

**Demo (lokal, CPU)**

1. Unduh bobot dari output notebook training di Kaggle ke `demo/weights/`: `yolo11s_seg.pt`, `yolov8s_seg.pt`, dan `mask_rcnn_r50.pt`. Ketiganya berasal dari `CV_InstanceSeg/runs/<model>/`.
2. `pip install ultralytics torch torchvision pandas matplotlib`
3. Buka `demo/demo_inferensi.ipynb`, lalu Run All.

Bobot model (`*.pt`) dan foto asli HP (berisi EXIF/lokasi) sengaja tidak disimpan di repo.

## Catatan

- Draf anotasi foto HP dibuat dari prediksi model, lalu dikoreksi manual, sehingga skor HP bisa terlalu optimistis.
- Delapan kelas test set asli tidak ada di data latih; setiap kondisi dilatih dengan satu seed.
- Penggunaan AI dideklarasikan di logbook dan laporan.
