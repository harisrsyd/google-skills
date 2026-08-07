# 🚀 Google Skills - Challenge Lab Solutions

[![Google Cloud](https://img.shields.io/badge/Google_Cloud-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)](https://cloud.google.com/)
[![Qwiklabs](https://img.shields.io/badge/Qwiklabs-F4B400?style=for-the-badge&logo=qwiklabs&logoColor=white)](https://www.cloudskillsboost.google/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=for-the-badge)](https://github.com/)

Selamat datang di repository **Google Skills (formerly Qwiklabs) Challenge Lab Solutions**! Repository ini berisi dokumentasi, panduan langkah demi langkah, *script* otomatisasi, serta kode solusi untuk beberapa **Challenge Lab** pada jalur sertifikasi dan *Skill Badges* Google Cloud Platform (GCP).

---

## 📌 Tentang Challenge Lab

**Challenge Lab** pada Google Skills tidak menyediakan langkah-langkah detail (*step-by-step instructions*) seperti lab biasa. Sebaliknya, Anda diberikan skenario bisnis/teknis dengan beberapa *task* atau *goal* yang harus diselesaikan sendiri tanpa petunjuk rinci.

Repository ini bertujuan sebagai:
- **Referensi Pembelajaran**: Membantu memahami konsep dan perintah GCP yang dibutuhkan untuk menyelesaikan tantangan.
- **Troubleshooting Guide**: Solusi untuk kendala teknis atau kesalahan konfigurasi umum saat mengerjakan lab.
- **Automation Scripts**: Kumpulan *gcloud CLI scripts* dan *Terraform configurations* untuk mempercepat proses deployment.

---

## 📂 Struktur Repository

<!-- ```text
.
├── 📁 Infrastructure-&-DevOps/
│   ├── 📄 Deploy-and-Manage-Cloud-Environments-Challenge-Lab.md
│   ├── 📄 Set-Up-and-Configure-a-Cloud-Environment-Challenge-Lab.md
│   └── 📄 Automating-Infrastructure-with-Terraform-Challenge-Lab.md
├── 📁 Data-&-Analytics/
│   ├── 📄 Perform-Foundational-Data-ML-and-AI-Tasks-Challenge-Lab.md
│   └── 📄 Create-and-Manage-BigQuery-Databases-Challenge-Lab.md
├── 📁 Cloud-Development/
│   ├── 📄 Develop-Serverless-Applications-on-GCP-Challenge-Lab.md
│   └── 📄 Deploy-Kubernetes-Applications-on-GCP-Challenge-Lab.md
├── 📁 Security-&-Networking/
│   ├── 📄 Build-a-Secure-Google-Cloud-Network-Challenge-Lab.md
│   └── 📄 Ensure-Access-Control-and-Security-Challenge-Lab.md
├── 📁 scripts/
│   ├── 📄 gcloud-setup-helper.sh
│   └── 📄 terraform-quickstart.tf
└── 📄 README.md
``` -->

---

## 🏆 Daftar Solusi Challenge Lab

Berikut adalah daftar solusi lab yang tersedia dalam repository ini berdasarkan kategori *Skill Badge*:

### 🚀 Cloud Development & Serverless
| Skill Badge / Course Title | Challenge Lab Name | Solution & Guide |
| :--- | :--- | :---: |
| **The Basics of Google Cloud Compute** | *[ARC120] The Basics of Google Cloud Compute: Challenge Lab* | [Lihat Solusi](./The%20Basics%20of%20Google%20Cloud%20Compute/[ARC120]%20The%20Basics%20of%20Google%20Cloud%20Compute-Challenge%20Lab.md) |
| **Build Serverless Applications with Cloud Run Functions** | *[ARC104] Build Serverless Applications with Cloud Run Functions: Challenge Lab* | [Lihat Solusi](./Build%20Serverless%20Application%20With%20Cloud%20Run%20Functions/[ARC104]%20Build%20Serverless%20Applications%20with%20Cloud%20Run%20Functions-Challenge%20Lab.md) |

### ☁️ Core Infrastructure & DevOps
| Skill Badge / Course Title | Challenge Lab Name | Solution & Guide |
| :--- | :--- | :---: |
| **Implement CI/CD Pipelines on Google Cloud** | *[GSP393] Implement CI/CD Pipelines on Google Cloud: Challenge Lab* | [Lihat Solusi](./Implement%20CICD%20Pipelines%20on%20Google%20Cloud/[GSP393]%20Implement%20CICD%20Pipelines%20on%20Google%20Cloud-Challenge%20Lab.md) |
| **-** | *-* | [Lihat Solusi]() |

### 📊 AI, Data & Machine Learning
| Skill Badge / Course Title | Challenge Lab Name | Solution & Guide |
| :--- | :--- | :---: |
| **-** | *-* | [Lihat Solusi]() |

### 🏢 Others
| Skill Badge / Course Title | Challenge Lab Name | Solution & Guide |
| :--- | :--- | :---: |
| **Develop with App Script and AppSheet** | *[ARC126] Develop with App Script and AppSheet: Challenge Lab* | [Lihat Solusi](./Develop%20with%20App%20Script%20and%20AppSheet/[ARC126]%20Develop%20with%20App%20Script%20and%20AppSheet.md) |
| **-** | *-* | [Lihat Solusi]() |

---

## 🛠️ Cara Menggunakan Repository Ini

1. **Pilih Lab yang Ingin Diselesaikan**: Cari nama lab atau *Skill Badge* pada tabel di atas.
2. **Baca Kebutuhan & Variabel**: Setiap solusi menyertakan daftar variabel dinamis (seperti `PROJECT_ID`, `REGION`, `ZONE`, `INSTANCE_NAME`) yang perlu Anda sesuaikan dengan nilai unik pada sesi lab Anda.
3. **Gunakan Google Cloud Shell**:
   - Buka Cloud Shell di konsol Google Cloud.
   - Salin perintah `gcloud` atau *script* yang disediakan ke Cloud Shell.
   - Eksekusi langkah demi langkah dan pastikan setiap *task* memberikan output sukses sebelum menekan tombol **"Check my progress"**.

<!-- ### 💡 Tip Cepat Cloud Shell:
```bash
# Set variabel default agar tidak perlu ketik berulang kali
export REGION="us-central1"
export ZONE="us-central1-a"
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
``` -->

---

## ⚠️ Disclaimer & Kode Etik

- **Educational Purpose Only**: Repository ini dibuat **hanya untuk tujuan pendidikan dan pembelajaran**. 
- **Pahami, Jangan Cuma Copy-Paste**: Sangat disarankan untuk mempelajari konsep di balik setiap perintah daripada sekadar menyalin kode agar mendapatkan pemahaman mendalam tentang GCP.
- **Kepatuhan terhadap Terms of Service**: Pastikan Anda mematuhi [Google Cloud Skills Boost Terms of Service](https://www.cloudskillsboost.google/terms_of_service). Jangan menyalahgunakan sumber daya lab untuk aktivitas yang dilarang.

---

## 🤝 Kontribusi

Kontribusi selalu terbuka! Jika Anda menemukan solusi yang perlu diperbarui, bug pada *script*, atau ingin menambahkan solusi lab baru:

1. Fork repository ini.
2. Buat branch fitur baru (`git checkout -b feature/SolusiLabBaru`).
3. Commit perubahan Anda (`git commit -m 'Add solution for XYZ Challenge Lab'`).
4. Push ke branch tersebut (`git push origin feature/SolusiLabBaru`).
5. Buka **Pull Request**.

---

<p align="center">
  Dibuat dengan ❤️ untuk komunitas pembelajaran Google Cloud Platform.
</p>
