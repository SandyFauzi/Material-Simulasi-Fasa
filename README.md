# Fe-Ni Material Simulator — v6.0
**Simulasi Material Paduan Besi-Nikel · Fisika Fisis Terpadu**

Simulator berbasis browser (single-file HTML) yang mensimulasikan perilaku termodinamika dan mekanik paduan Fe-Ni secara real-time. Semua visualisasi — diagram fasa, mikrostruktur 3D, dan uji tarik — diperbarui seketika setiap parameter diubah.

---

## Tata Cara Penggunaan

### 1. Membuka Aplikasi
Buka file `material-simulator-v6.html` langsung di browser modern (Chrome, Edge, Firefox, atau Safari). Tidak memerlukan instalasi, server lokal, atau koneksi internet (setelah font Google di-cache).

### 2. Panel Kontrol (Kiri)
Semua parameter diatur dari panel kiri menggunakan slider dan tombol. Setiap perubahan langsung memperbarui ketiga visualisasi secara real-time.

### 3. Tiga Panel Visualisasi
| Panel | Letak | Isi |
|---|---|---|
| **Mikrostruktur 3D** | Tengah | Kisi kristal 3D interaktif — drag memutar, scroll zoom |
| **Diagram Fasa Fe-Ni** | Kanan Atas | Kurva liquidus/solidus, titik kondisi, tie-line |
| **Uji Tarik A vs B** | Kanan Bawah | Perbandingan spesimen Material A dan Material B |

### 4. Panel Keterangan (Kiri Bawah)
Tiga tab — **Fasa**, **Mikro**, **Mekanik** — menampilkan penjelasan fisika yang berubah dinamis sesuai kondisi saat ini (termasuk tekanan).

### 5. Toggle Tema
Tombol 🌙 / ☀️ di pojok kanan atas untuk beralih antara mode gelap dan terang.

---

## Parameter dan Pengaruhnya

### 🌡️ 1. Suhu Termal
| | |
|---|---|
| **Unit** | °C (derajat Celsius) |
| **Rentang** | 600 °C — 1600 °C |
| **Default** | 900 °C |
| **Step** | 10 °C |

**Pengaruh fisika:**
- Menentukan **fase material** berdasarkan diagram fasa (Solid α, dua fase α+L, atau Liquid)
- Semakin tinggi suhu → kekuatan luluh (σ_y) turun secara non-linear (power-law: `1 − (T/T_solidus)^2.2`)
- Di atas suhu liquidus → material mencair, semua kekuatan mekanik = 0
- Di visualisasi 3D: amplitudo vibrasi atom meningkat proporsional dengan `T/T_solidus`
- Mempengaruhi keuletan (ductility): material lebih ulet pada suhu lebih tinggi

**Efek pada diagram fasa:**
- Posisi vertikal titik merah (state point) bergerak naik/turun
- Saat titik melewati kurva liquidus → region berubah dari padat ke cair
- Saat titik di antara liquidus–solidus → tie-line dan Aturan Tuas aktif

---

### ⚗️ 2. Komposisi Ni (wt%)
| | |
|---|---|
| **Unit** | % berat Ni |
| **Rentang** | 0% (Fe murni) — 100% (Ni murni) |
| **Default** | 35% |
| **Step** | 1% |

**Pengaruh fisika:**
- Menggeser posisi **horizontal** titik state pada diagram fasa
- Mempengaruhi suhu liquidus dan solidus: Fe murni T_liq = 1538°C; Ni murni T_liq = 1455°C
- Meningkatkan %Ni → *solid-solution strengthening* → σ_y naik (`base = 130 + C × 0.7 MPa`)
- Komposisi penting secara praktis:
  - **36% Ni** → Invar (koefisien ekspansi termal sangat rendah)
  - **80% Ni** → Permalloy (permeabilitas magnetik sangat tinggi)
- Warna atom dalam visualisasi 3D mencerminkan rasio Fe (abu-biru) : Ni (cyan)

---

### 🔵 3. Tekanan *(parameter baru v6.0)*
| | |
|---|---|
| **Unit** | GPa (Gigapascal) |
| **Rentang** | 0.1 GPa — 20 GPa |
| **Default** | 0.1 GPa |
| **Step** | 0.1 GPa |
| **Referensi** | 0.1 GPa ≈ 1000 atm (mendekati tekanan permukaan bumi); 20 GPa ≈ kondisi inti bumi |

**Pengaruh fisika:**

**a) Diagram Fasa — efek Clausius-Clapeyron:**
Logam umumnya mengembang saat mencair (ΔV_lebur > 0), sehingga peningkatan tekanan menaikkan titik lebur:
```
dT/dP ≈ 15–20 °C/GPa  (bergantung komposisi)
```
Formula yang diimplementasikan:
```
T_liquidus(c, P) = T_liq_ambient(c) + (P − 0.1) × (15 + c × 0.05)
T_solidus(c, P)  = T_sol_ambient(c) + (P − 0.1) × (15 + c × 0.05)
```
Ini berarti pada P = 10 GPa, titik lebur naik sekitar +135°C dari kondisi ambien.

**b) Visualisasi diagram — apa yang berubah:**
- Kurva liquidus dan solidus bergeser **ke atas** (suhu lebih tinggi) seiring kenaikan tekanan
- Saat P > 1 GPa: **kurva referensi ambien** (garis putus-putus transparan) ditampilkan sebagai pembanding
- Badge tekanan di pojok kanan bawah diagram menampilkan nilai P dan pergeseran suhu lebur (`+X°C`)
- Label region berubah berdasarkan tekanan (lihat poin c)

**c) Transisi fase ε-Fe pada P ≥ 13 GPa:**
Pada tekanan ≥ 13 GPa, besi mengalami transisi polimorfis struktural:
```
α-Fe (BCC, kubik berpusat badan) → ε-Fe (HCP, hexagonal close-packed)
```
Efek yang ditampilkan:
- Region solid bawah diberi warna overlay hijau
- Garis batas ε-Fe ditampilkan dengan label
- Label region solid berubah: `Solid α (Fe-Ni)` → `Solid α + ε-Fe (HCP)`
- Status tekanan di panel stat menampilkan `(ε-Fe)`
- Teks keterangan tab Fasa berubah menjelaskan transisi BCC→HCP

**d) Kekuatan mekanik — pressure hardening:**
```
σ_y × pMod,  di mana  pMod = 1 + (P − 0.1) × 0.015
```
Kenaikan yield strength ~1.5% per GPa. Pada 20 GPa: pMod ≈ 1.30 (kenaikan ~30%).

**e) Teks keterangan tab Fasa:**
Berubah dinamis mencerminkan tekanan saat ini, pergeseran suhu lebur, dan kondisi ε-Fe jika berlaku.

---

### 🔩 4. Jenis Cacat Kristal
| | |
|---|---|
| **Pilihan** | 6 jenis (tombol toggle) |
| **Default** | Dislokasi |

| Cacat | Faktor YS | Faktor Keuletan | Penjelasan Fisika |
|---|---|---|---|
| **Sempurna** | ×1.00 | ×1.00 | Kisi ideal, tidak ada hambatan slip |
| **Vacancy** | ×0.92 | ×1.05 | Kekosongan kisi, melemahkan ikatan |
| **Interstisial** | ×1.50 | ×0.50 | Atom C/N di celah oktahedral, distorsi tetragonal ekstrem (mekanisme martensite) |
| **Substitusional** | ×1.25 | ×0.78 | Atom Ni gantikan Fe, solid-solution strengthening |
| **Dislokasi** | ×0.85 | ×1.35 | Setengah bidang atom ekstra, slip plastis mudah |
| **Batas Butir** | ×1.85 | ×0.88 | Dua orientasi kristal bertemu, Hall-Petch: σ_y = σ₀ + k·d⁻½ |

**Pengaruh pada visualisasi 3D:**
- Vacancy → lingkaran putus-putus merah di posisi kosong
- Interstisial → atom merah kecil di celah kisi
- Substitusional → atom hijau besar menggantikan posisi Fe
- Dislokasi → baris atom hilang + garis slip merah + label vektor Burgers `b →`
- Batas Butir → bidang semi-transparan kuning memisahkan dua domain kristal

---

### ⬆️ 5. Beban Tarik
| | |
|---|---|
| **Unit** | MPa (Megapascal) |
| **Rentang** | 0 — 400 MPa |
| **Default** | 0 MPa |
| **Step** | 1 MPa |

**Pengaruh fisika:**

| Kondisi | Perilaku |
|---|---|
| `0 < Load < σ_y` | Deformasi **elastis** — material kembali saat beban dilepas |
| `σ_y < Load < σ_u` | Deformasi **plastis** permanen — necking mulai terbentuk |
| `Load ≥ σ_u` dan Ductility > 32% | **Patah Ulet** (cup-and-cone) |
| `Load ≥ σ_u` dan Ductility ≤ 32% | **Patah Getas** (permukaan datar, tanpa peringatan) |
| Material sudah cair | Beban tidak ditahan |

**Panel uji tarik:** menampilkan spesimen Material A (kiri) dan Material B (kanan) di bawah beban yang sama. Necking, patah, dan regangan ε ditampilkan secara visual.

---

### 🔵 6. Material B — Pembanding (Preset)
| | |
|---|---|
| **Pilihan** | 6 preset material |
| **Default** | Ferit Lunak |

| Preset | Komposisi | Kondisi | Sifat Khas |
|---|---|---|---|
| **Ferit Lunak** | Fe-2%Ni | Dislokasi · 600°C | Sangat ulet, kekuatan rendah |
| **Baja Keras** | Fe-5%Ni | Interstisial · 100°C | Sangat getas, kekuatan tinggi |
| **Invar 36** | Fe-36%Ni | Sempurna · 200°C | Ekspansi termal sangat rendah |
| **Permalloy** | Fe-80%Ni | Substitusi · 400°C | Permeabilitas magnetik tinggi |
| **Hall-Petch** | Fe-20%Ni | Batas Butir · 200°C | Kuat + semi-getas |
| **Vacancy·Panas** | Fe-50%Ni | Vacancy · 1100°C | Sangat lunak, dekat titik lebur |

Beban tarik yang sama (slider Beban Tarik) diaplikasikan ke kedua material secara bersamaan.

---

## Batasan Parameter

| Parameter | Min | Max | Default | Unit |
|---|---|---|---|---|
| Suhu Termal | 600 | 1600 | 900 | °C |
| Komposisi Ni | 0 | 100 | 35 | wt% |
| Tekanan | 0.1 | 20 | 0.1 | GPa |
| Beban Tarik | 0 | 400 | 0 | MPa |

**Catatan batasan fisika:**
- Suhu di bawah 600°C tidak disimulasikan (transformasi martensitik Fe-Ni memerlukan model terpisah)
- Tekanan di atas 20 GPa tidak disimulasikan (memerlukan model potensial interatomik ab initio)
- Beban tarik 400 MPa adalah batas uji tarik simulator; paduan nyata bisa mencapai 1000+ MPa
- Model Tliq/Tsol adalah aproksimasi linear-kuadratik; diagram fasa Fe-Ni nyata lebih kompleks di bawah 400°C

---

## Landasan Fisika yang Diimplementasikan

| Konsep | Implementasi |
|---|---|
| **Diagram Fasa Fe-Ni** | Tliq = 1538 − 0.83·c; Tsol = 1538 − 2.1·c + 0.013·c² |
| **Clausius-Clapeyron** | dT/dP = 15–20 °C/GPa untuk Fe-Ni |
| **Transisi ε-Fe** | BCC→HCP pada P ≥ 13 GPa |
| **Aturan Tuas** | W_L = (C_α − C₀)/(C_α − C_L); W_α = 1 − W_L |
| **Pressure Hardening** | σ_y × (1 + (P−0.1) × 0.015) |
| **Hall-Petch** | σ_y = σ₀ + k·d⁻½ (dimodelkan via faktor batas butir) |
| **Solid-Solution Strengthening** | σ_y ∝ 130 + 0.7·C_Ni |
| **Pelunakan Termal** | tMod = max(0.08, 1 − (T/T_solidus)^2.2) |
| **Patah Ulet/Getas** | Ductility threshold 32% |
| **Hukum Hooke** | σ = E·ε (region elastis) |

---

## Kompatibilitas Browser

| Browser | Status |
|---|---|
| Chrome / Edge (Chromium) | ✅ Terbaik |
| Firefox | ✅ Baik |
| Safari | ✅ Baik |
| Mobile (Chrome/Safari) | ✅ Responsif |

Tidak memerlukan koneksi internet setelah font Google Fonts ter-cache. Tidak ada dependensi eksternal atau framework JavaScript.

---

*Fe-Ni Material Simulator v6.0 — Pengantar Material Fungsional & Maju*
