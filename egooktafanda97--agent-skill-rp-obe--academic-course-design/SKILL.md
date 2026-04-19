---
name: academic-course-design
description: Skill for generating OBE-based academic course documents (RP) in Word format with micro-teaching integration. Use when this capability is needed.
metadata:
  author: egooktafanda97
---

# Academic Course Design Skill

Skill untuk menyusun dokumen **Rencana Pembelajaran (RP)** berbasis **OBE (Outcome-Based Education)** dengan integrasi **keterampilan mengajar mikro** dalam format Word (.DOCX).

## Capabilities

- **Generate RP DOCX**: Buat RP dalam format Word dengan template presisi
- **OBE-Mikro Terpadu**: Integrasi PBL 5 Fase + Micro-Teaching Skills
- **Dynamic Phases**: Support unlimited learning phases (auto-insert rows)
- **Hybrid Generation**: User-provided content OR auto-generate from reference

## Key Features

### 1. OBE Framework
- Constructive Alignment (Sub-CPMK → Indikator → Bukti → Asesmen)
- Format ABCD untuk outcomes
- Matriks Keselarasan wajib

### 2. PBL 5 Fase
- F1: Orientasi Masalah
- F2: Organisasi
- F3: Investigasi
- F4: Karya/Presentasi
- F5: Analisis/Evaluasi
- + Pendahuluan (Apersepsi) + Penutup (Closing)

### 3. Micro-Teaching Skills
- Membuka (apersepsi, motivasi, acuan, kaitan)
- Menjelaskan
- Bertanya (dasar + lanjut/pelacak)
- Mengelola kelas
- Menutup (rangkuman, evaluasi, tindak lanjut)

### 4. Asesmen
- Rubrik produk/artefak
- Rubrik performansi
- Kuis dengan ketuntasan (≥75)
- Remedial/Pengayaan

## Usage

```bash
python scripts/generate_rp.py templates/TEMPLATE_RP.docx data.json output.docx
```

### JSON Input Example

```json
{
  "MATA_KULIAH": "Desain Proyek",
  "PERTEMUAN": "3",
  "TOPIK": "Manajemen Proyek I",
  "STUDI_KASUS": "Aplikasi Pemesanan Kafe via QR",
  "REFERENCE_FOLDER": "/path/to/reference"
}
```

## Directory Structure

```
academic_course_design/
├── SKILL.md              # This file
├── scripts/
│   └── generate_rp.py    # Main generator (dynamic phases)
├── templates/
│   └── TEMPLATE_RP.docx  # Official template
├── prompts/
│   ├── rp_docx_filler.md        # DOCX filling rules
│   └── rp_obe_mikro_master.md   # OBE-Mikro framework
├── examples/
│   └── desain_project_p3.json   # Sample input
└── resources/
```

## Reference Prompts

1. **rp_docx_filler.md**: Aturan pengisian template DOCX
2. **rp_obe_mikro_master.md**: Framework lengkap OBE + Mikro + PBL
3. **rubrik_generator_prompt.md**: Prompt untuk generate rubrik penilaian

---

## Rubrik Generator

Skill tambahan untuk generate **Rubrik Penilaian** dari RP OBE yang sudah ada.

### Quick Start (Cara Mudah)

📖 **[BACA PANDUAN PENGGUNAAN DI SINI](PANDUAN_PENGGUNAAN.md)**

Jalankan satu perintah untuk membuat RP Lengkap + Rubrik:
```bash
python scripts/generate_complete_rp.py examples/desain_project_p2.json output/
```

## Features

- **Extract Kisi-Kisi**: Otomatis extract indikator evaluasi dari RP
- **Generate Rubrik MC**: Buat rubrik multiple choice dengan kategori kognitif (C1-C6)
- **Generate Rubrik Tugas**: Buat rubrik tugas dengan kriteria penilaian standar
- **Merge Documents**: Gabungkan RP + Rubrik menjadi 1 dokumen dengan section breaks

### Usage

```bash
python scripts/generate_rubrik.py <rp_path> <output_dir>
```

### Example

```bash
python scripts/generate_rubrik.py examples/RP.docx ./output/
```

### Output Structure
```
┌─────────────────────────────────┐
│  Section 1: RP OBE (Landscape)  │
├─────────────────────────────────┤
│  Section 2: Rubrik MC (Portrait)│
├─────────────────────────────────┤
│  Section 3: Rubrik Tugas        │
│  (Portrait)                     │
└─────────────────────────────────┘
```

---

## Related Skills

- **[word_excel_automation](../word_excel_automation/SKILL.md)**: Best practices for Word & Excel manipulation (python-docx, openpyxl)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/egooktafanda97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
