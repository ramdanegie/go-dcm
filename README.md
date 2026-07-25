# go-dcm (DICOM Conversion API)

Golang REST API Wrapper untuk utilitas konversi DICOM yang memberdayakan spesifikasi command-line dari **DCMTK**.
Aplikasi ini memungkinkan klien mengkonversi file gambar (JPEG, BMP) maupun dokumen (PDF, CDA, STL, MTL, OBJ) menjadi format enkapsulasi `.dcm` yang resmi dan tervalidasi menggunakan HTTP Request (`multipart/form-data`) yang sangat mudah digunakan tanpa harus mengutak-atik eksekusi bash secara manual.

## ⚠️ Prasyarat Instalasai (Prerequisites)

Karena aplikasi Golang ini bertindak sebagai jembatan eksekusi *wrapper* (`os/exec`), perangkat server yang menjalankannya **DIWAJIBKAN** sudah memasang dependensi berikut di dalam _Environment System Variables_ (PATH):

1. **Golang** (v1.18+ direkomendasikan)
   - Digunakan untuk menjalankan web server `chi`.
2. **DCMTK (DICOM Toolkit)**
   - Anda wajib memasang binary kompilasi DCMTK (dapat diunduh rilis siap-pakainya untuk Windows/Linux pada [situs asli DCMTK](https://dicom.offis.de/dcmtk.php.en)).
   - Pastikan *executable* utilitas **`img2dcm`** dan **`dcmencap`** dapat dipanggil dari terminal/command prompt mana pun secara global (`img2dcm --version`).

## 🚀 Cara Menjalankan

1. Lakukan instalasi dependency *routes*:
   ```bash
   go mod tidy
   ```
2. Jalankan aplikasi Main server Golang:
   ```bash
   go run main.go
   ```
3. Server akan berjalan pada alamat standard **http://localhost:8080**.

## 🔌 API Endpoint Tersedia

* **`POST /api/v1/convert/img2dcm`**: Konversi JPEG/BMP ke target SOP class seperti *Secondary Capture* atau *Visible Light*.
* **`POST /api/v1/convert/pdf2dcm`**: Konversi (Enkapsulasi) *PDF/Clinical Document Architecture* dengan injeksi metadata ke format pembungkus standar DICOM.

Anda bisa menggunakan Postman atau langsung mengimpor bundel [openapi.yaml](openapi.yaml) yang disertakan pada direktori.

### 📝 Contoh Payload \`parameters\` (Basic Metadata)

> [!NOTE]
> Parameter `study_instance_uid` dan `series_instance_uid` bersifat opsional. Jika parameter ini dikosongkan (atau `"generate_uids": true` dikirimkan), maka UID baru untuk Study dan Series akan di-generate secara otomatis.

**Konversi Gambar (\`/img2dcm\`)**
Sertakan File JPEG/BMP pada form \`file\`, dan berikan text di form \`parameters\` sbb:
```json
{
  "output_sop_class": "vl-photo",
  "encounter_id": "12345-abcde-67890",
  "study_date": "20240420",
  "study_time": "120000",
  "study_instance_uid": "1.2.3.4.5.6.7",
  "series_instance_uid": "1.2.3.4.5.6.7.1",
  "generate_uids": true,
  "keys": [
    "PatientName=Doe^John",
    "PatientID=123456789",
    "PatientBirthDate=19900101",
    "PatientSex=M",
    "AccessionNumber=ACC001",
    "Modality=DOC"
  ]
}
```

**Konversi Dokumen/PDF (\`/pdf2dcm\`)**
Sertakan Dokumen PDF pada form \`file\`, dan berikan text di form \`parameters\` sbb:
```json
{
  "filetype": "pdf",
  "title": "Hasil Pemeriksaan Laboratorium",
  "patient_name": "Doe^John",
  "patient_id": "123456789",
  "patient_birthdate": "19900101",
  "patient_sex": "M",
  "manufacturer": "NAMA_RS/KLINIK",
  "manufacturer_model": "Sistem Informasi Rekam Medis",
  "encounter_id": "12345-abcde-67890",
  "study_date": "20240420",
  "study_time": "120000",
  "study_instance_uid": "1.2.3.4.5.6.7",
  "series_instance_uid": "1.2.3.4.5.6.7.1",
  "generate_uids": true,
  "keys": [
    "AccessionNumber=ACC002",
    "Modality=DOC"
  ]
}
```

---

### Credits
Credit to **Jaisyullah Rafiul Islam**
Contribute for **Transformation and Digitalization Team Ministry of Health Indonesia**
