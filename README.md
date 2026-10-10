# n8n AI Video Generator Workflow

Repositori ini berisi workflow otomasi **n8n** untuk **Praktikum Modul 06 - Topik 3: Pembuatan AI Video Generator Otomatis** menggunakan model AI generatif video dari **Kie AI (Grok Imagine Video)** dan penyimpanan otomatis ke **Google Drive**.

---

## 📋 Deskripsi Proyek

Workflow ini memungkinkan pengguna untuk menghasilkan konten video otomatis berbasis gambar dan prompt teks melalui formulir web, memantau proses render video secara asynchronous, mengunduh file video yang telah selesai, dan menyimpannya secara terstruktur ke Google Drive.

---

## 🛠️ Alur & Komponen Node Workflow

1. **Submit Text Prompt** (`n8n-nodes-base.formTrigger`):
   - Menyediakan form trigger publik dengan input:
     - `prompt` (Deskripsi adegan video yang ingin dihasilkan)
     - `image` (URL gambar referensi)
     - `api_key` (Kunci otorisasi API Kie AI)
2. **Kirim Video Request** (`n8n-nodes-base.httpRequest`):
   - Mengirim request POST pembuatan task ke endpoint `https://api.kie.ai/api/v1/jobs/createTask`.
   - Menggunakan model `grok-imagine-video-1-5-preview` dengan durasi 8 detik, resolusi 480p, dan aspect ratio otomatis.
3. **Wait** (`n8n-nodes-base.wait`):
   - Memberikan jeda 20 detik untuk proses rendering video di server Kie AI.
4. **Get Status** (`n8n-nodes-base.httpRequest`):
   - Melakukan polling GET ke `https://api.kie.ai/api/v1/jobs/recordInfo` menggunakan `taskId` yang diperoleh dari respon sebelumnya.
5. **Check Jika Generate Selesai** (`n8n-nodes-base.if`):
   - Memeriksa kondisi apakah `data.state` bernilai `success`.
6. **Parse Video URL** (`n8n-nodes-base.code`):
   - Mengurai objek JSON respons untuk mengekstrak URL unduhan file video (`videoUrl`).
7. **Download Video** (`n8n-nodes-base.httpRequest`):
   - Mengunduh file video sebagai binary stream (`responseFormat: file`).
8. **Upload file to Google Drive** (`n8n-nodes-base.googleDrive`):
   - Mengunggah file video langsung ke folder Google Drive tujuan dengan format penamaan dinamis: `video_yyyyMMdd_HHmmss.mp4`.

---

## 📁 Berkas Proyek

- [`Tugas 3 - AI Video Generator-Ariq Jamhari.json`](./Tugas%203%20-%20AI%20Video%20Generator-Ariq%20Jamhari.json): File workflow n8n lengkap.

---

## 🚀 Panduan Import ke n8n

1. Buka instance **n8n** Anda.
2. Masuk ke menu **Workflows**.
3. Klik menu **Options** (ikon titik tiga di kanan atas kanvas) lalu pilih **Import from File...** (atau salin isi berkas JSON dan paste langsung ke kanvas n8n).
4. Pilih file [`Tugas 3 - AI Video Generator-Ariq Jamhari.json`](./Tugas%203%20-%20AI%20Video%20Generator-Ariq%20Jamhari.json).
5. Konfigurasikan kredensial yang dibutuhkan:
   - **Google Drive OAuth2 API**: Pada node *Upload file to Google Drive* (sesuaikan folder tujuan jika perlu).
6. Aktifkan workflow (`Active / Publish`) dan gunakan URL form publik dari node *Submit Text Prompt*.
