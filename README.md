# VA & Pentest Repository

> **Bahasa Indonesia** | [English](#english)

Repo ini digunakan untuk menyimpan, mengelola, dan melacak semua hasil **Vulnerability Assessment (VA)** dan **Penetration Testing (Pentest)** — mulai dari data mentah, bukti temuan, catatan analisa, hingga laporan akhir dan retest remediasi.

Repo ini dirancang agar **mudah digunakan oleh junior** sekalipun, dengan aturan yang jelas dan struktur yang konsisten.

---

## Apa itu repo ini dan untuk apa?

Dalam sebuah proyek keamanan (VA / Pentest), kita menghasilkan banyak file:
- Output dari tools (nmap, Burp Suite, Nessus, Nuclei, dll)
- Screenshot / bukti temuan
- Catatan kerja harian
- Laporan untuk klien
- Bukti bahwa remediasi sudah diperbaiki (retest)

Kalau semua file ini tersebar di desktop, email, atau folder acak — kita akan sulit melacak apa yang sudah dilakukan, sulit diaudit, dan rawan kehilangan bukti.

**Repo ini menyelesaikan masalah tersebut** dengan struktur yang teratur dan aturan wajib yang harus diikuti oleh semua anggota tim.

---

## Struktur Folder

```
va-pentest-repo/
├── 01-scope/              ← Apa yang boleh dan tidak boleh diuji
├── 02-asset-inventory/    ← Daftar semua aset (server, website, aplikasi)
├── 03-data-collection/    ← Output mentah dari tools + hasil olahan
├── 04-worknotes/          ← Catatan harian tim (non-rahasia)
├── 05-findings/           ← SEMUA temuan keamanan + buktinya
├── 06-analysis/           ← Analisis: rating risiko, mapping CWE/OWASP/MITRE
├── 07-reporting/          ← Draft & final report, slides, deliverables
├── 08-retest/             ← Bukti bahwa temuan sudah diperbaiki
├── 99-archive/            ← File yang sudah tidak relevan / deprecated
└── templates/             ← Template siap pakai (scope, finding, retest)
```

### Penjelasan Detail

| Folder | Fungsi | Untuk siapa? | Isi apa? |
|---|---|---|---|
| `01-scope/` | Menentukan batasan pengujian — apa yang boleh diuji, apa yang tidak, siapa yang bisa dihubungi | Pentester, PM, klien | `scope.md` (tujuan & jenis pengujian), `in-scope-assets.csv`, `out-of-scope.md`, `roe/` (Rules of Engagement), `credentials.pointer.md` (pointer ke vault, BUKAN rahasia) |
| `02-asset-inventory/` | Daftar lengkap semua aset yang diuji | Semua tim | `assets.csv` (asset-id, hostname, IP, URL, service, env, owner), `diagrams/` (topologi jaringan, arsitektur aplikasi) |
| `03-data-collection/` | Menyimpan SEMUA output dari tools VA/pentest | Pentester | `raw/` — output mentah dari tools (IMMUTABLE, jangan diedit!), `normalized/` — hasil parsing/cleanup untuk analisa, `notes/collection-log.md` — log kapan data diambil dan oleh siapa |
| `04-worknotes/` | Catatan kerja harian tim | Semua tim | `daily-notes.md` — catatan non-rahasia untuk koordinasi |
| `05-findings/` | Tempat utama untuk menyimpan temuan keamanan | Pentester, reviewer | `register.csv` — daftar semua temuan (F-001, F-002, ...), `F-XXX/` — per-folder per temuan, berisi `finding.md`, `timeline.md`, dan `evidence/` (screenshot, request, response, file) |
| `06-analysis/` | Menganalisis dan mengkategorikan temuan | Pentester, reviewer | `risk-rating.md` — ringkasan rating risiko, `mapping/` — CWE, OWASP Top 10, MITRE ATT&CK, `root-cause/` — analisis akar masalah |
| `07-reporting/` | Menyimpan semua dokumen laporan | Pentester, PM | `report-draft/`, `report-final/`, `slides/`, `deliverables/` |
| `08-retest/` | Membuktikan bahwa temuan sudah diperbaiki | Pentester, klien, sysadmin | `retest-plan.md` — rencana retest, `retest-results/` — hasil retest per temuan, `closure-evidence/` — bukti bahwa temuan sudah ditutup |
| `99-archive/` | Menyimpan file yang sudah tidak relevan | Semua tim | File lama, output deprecated, dll |
| `templates/` | Template siap pakai untuk membuat dokumen baru | Semua tim | `scope.md`, `finding.md`, `collection-log.md`, `retest-plan.md` |

---

## Aturan Wajib

Aturan ini **HARUS** diikuti oleh semua anggota tim. Tidak ada pengecualian.

### 1. Raw Output Bersifat IMMUTABLE (Tidak Boleh Diedit)

- Semua output dari tools (nmap, Burp, Nessus, dll) disimpan **apa adanya** di `03-data-collection/raw/<tool-name>/`
- Jangan pernah mengedit, menghapus, atau rename file di `raw/`
- Jika perlu versi yang sudah dibersihkan/di-parse, simpan hasilnya di `03-data-collection/normalized/<tool-name>/`
- Catat command/tool yang digunakan untuk memproses data di `03-data-collection/notes/collection-log.md`

**Mengapa?** Output mentah adalah bukti. Kalau diedit, tidak bisa diaudit dan tidak valid di pengadilan.

### 2. Setiap Temuan Harus Punya Bukti

- Temuan disimpan di `05-findings/F-XXX/`
- Setiap folder `F-XXX/` WAJIB berisi:
  - `finding.md` — deskripsi temuan, langkah reproduksi, dampak, rekomendasi
  - `evidence/` — bukti pendukung (screenshot, request, response, file)
  - `timeline.md` — kronologi temuan (kapan ditemukan, siapa yang menemukan)
- Jangan biarkan bukti tercecer di folder lain

### 3. TIDAK ADA Rahasia di Repo

- **JANGAN** menyimpan password, API key, token, private key, cookie session, atau dump database di repo
- Semua kredensial disimpan di vault (KeePass, Bitwarden, 1Password, dll)
- Repo hanya menyimpan **pointer** ke vault di `01-scope/credentials.pointer.md` — tanpa nilai rahasia
- File `.gitignore` sudah dikonfigurasi untuk memblokir file sensitif (`.env`, `*.pem`, `*.key`, `*secret*`, `*password*`, `*token*`, `*.sql`, dll)

### 4. Penamaan File Bukti

Format: `YYYYMMDD-HHMM_<asset-id>_<deskripsi-singkat>.<ext>`

Contoh:
```
20260330-1430_WEB-01_xss-reflected-login.png
20260330-1445_API-01_sqli-login-request.txt
20260330-1500_DB-01_unauthorized-access-pcap.pcap
```

### 5. Penamaan Aset

- Gunakan `asset-id` dari `02-asset-inventory/assets.csv`
- Contoh: `WEB-01`, `API-01`, `DB-01`, `VPN-01`
- Jangan gunakan IP langsung di nama file (IP bisa berubah, asset-id tetap)

### 6. Semua File Besar Disimpan di Storage Terpisah

- Jika output tools terlalu besar (>50MB), simpan di storage terpisah (S3, Google Drive, NAS, dll)
- Di repo, simpan **manifest** (daftar file + lokasi + checksum SHA256)
- File `.pcap` besar sebaiknya tidak di-commit ke git

---

## Penjelasan per Folder

### `01-scope/` — Batasan Pengujian

Sebelum mulai VA/pentest, tentukan dulu:
- **Apa tujuannya?** (cari kerentanan, uji kepatuhan, red team, dll)
- **Apa yang boleh diuji?** (daftar aset in-scope)
- **Apa yang TIDAK boleh diuji?** (daftar aset out-of-scope)
- **Siapa yang bisa dihubungi jika ada masalah?** (kontak darurat)
- **Aturan main?** (jam operasional, metode yang dilarang, dll)

File utama: `scope.md`, `in-scope-assets.csv`, `out-of-scope.md`

### `02-asset-inventory/` — Inventaris Aset

Daftar lengkap semua aset yang diuji. Gunakan `assets.csv` dengan kolom:
- `asset_id` — ID unik (WEB-01, API-01, dll)
- `hostname` — nama host (contoh: web.example.com)
- `ip` — alamat IP
- `url` — URL jika ada
- `service` — service yang berjalan (HTTP, SSH, MySQL, dll)
- `env` — environment (production, staging, dev)
- `owner` — pemilik aset
- `notes` — catatan tambahan

### `03-data-collection/` — Kumpulan Data

**`raw/`** — Tempat menyimpan SEMUA output dari tools:
- `raw/nmap/` — hasil scan nmap
- `raw/burp/` — export Burp Suite
- `raw/nessus/` — export Nessus
- `raw/nuclei/` — hasil scan nuclei
- Dan lainnya (tambahkan subfolder per tool)

**`normalized/`** — Versi yang sudah diolah untuk analisa:
- Contoh: export nmap ke CSV, filter Burp findings, parse nuclei ke JSON

**`notes/`** — Log pengambilan data:
- `collection-log.md` — siapa, kapan, tool apa, target mana

### `04-worknotes/` — Catatan Harian

- `daily-notes.md` — catatan non-rahasia untuk koordinasi tim
- Berguna untuk: melacak progress, diskusi, catatan rapat

### `05-findings/` — Tempat Utama Temuan

Setiap temuan diberi ID: `F-001`, `F-002`, `F-003`, ...

**Register:** `register.csv` berisi daftar semua temuan:
```
id, title, severity, status, asset_id, cwe, owasp, cvss31, date_found, date_retest, retest_status
F-001, XSS Reflected on Login Page, High, Open, WEB-01, CWE-79, A03:2021, 7.1, 2026-03-30, , 
```

**Per-folder (`F-001/`):**
- `finding.md` — deskripsi lengkap temuan
- `timeline.md` — kronologi temuan
- `evidence/screenshots/` — bukti berupa screenshot
- `evidence/requests/` — bukti request (HTTP request, dll)
- `evidence/responses/` — bukti response (HTTP response, dll)
- `evidence/files/` — bukti lainnya (file exploit, output, dll)

### `06-analysis/` — Analisis

- `risk-rating.md` — ringkasan rating risiko semua temuan
- `mapping/cwe.csv` — mapping temuan ke CWE
- `mapping/owasp.csv` — mapping temuan ke OWASP Top 10
- `mapping/mitre-attack.csv` — mapping temuan ke MITRE ATT&CK
- `root-cause/` — analisis akar masalah per temuan

### `07-reporting/` — Laporan

- `report-draft/` — laporan dalam proses
- `report-final/` — laporan akhir yang sudah disetujui
- `slides/` — presentasi untuk klien
- `deliverables/` — dokumen yang dikirim ke klien

### `08-retest/` — Retest & Closure

Setelah klien memperbaiki temuan, kita lakukan retest:
- `retest-plan.md` — rencana retest (temuan mana yang diretest, kapan)
- `retest-results/` — hasil retest per temuan (pass/fail + bukti)
- `closure-evidence/` — bukti bahwa temuan sudah ditutup

### `99-archive/` — Arsip

- Menyimpan file yang sudah tidak relevan
- Jangan dihapus — simpan di sini untuk audit trail

---

## Contoh Alur Kerja (Workflow)

### 1. Persiapan
```
1. Isi 01-scope/scope.md dengan tujuan dan batasan pengujian
2. Isi 01-scope/in-scope-assets.csv dengan daftar aset
3. Salin ke 02-asset-inventory/assets.csv
4. Taruh kredensial di vault, buat pointer di 01-scope/credentials.pointer.md
```

### 2. Pengumpulan Data
```
1. Jalankan tools (nmap, Burp, Nessus, dll)
2. Simpan output mentah di 03-data-collection/raw/<tool-name>/
3. Parse/bersihkan data, simpan di 03-data-collection/normalized/<tool-name>/
4. Catat di 03-data-collection/notes/collection-log.md
```

### 3. Temuan
```
1. Identifikasi temuan dari data yang terkumpul
2. Buat folder 05-findings/F-XXX/
3. Isi finding.md dengan deskripsi lengkap
4. Simpan bukti di evidence/screenshots/, evidence/requests/, dll
5. Isi timeline.md
6. Update 05-findings/register.csv
```

### 4. Analisis & Laporan
```
1. Isi 06-analysis/risk-rating.md
2. Mapping ke CWE, OWASP, MITRE di 06-analysis/mapping/
3. Tulis laporan di 07-reporting/report-draft/
4. Finalisasi ke 07-reporting/report-final/
```

### 5. Retest (jika ada)
```
1. Klien memperbaiki temuan
2. Buat rencana di 08-retest/retest-plan.md
3. Lakukan retest, simpan hasil di 08-retest/retest-results/
4. Simpan bukti closure di 08-retest/closure-evidence/
5. Update 05-findings/register.csv (status → Closed)
```

---

## Template Siap Pakai

Gunakan template dari folder `templates/` untuk membuat dokumen baru:

| Template | File | Fungsi |
|---|---|---|
| Scope | `templates/scope.md` | Membuat dokumen scope pengujian baru |
| Finding | `templates/finding.md` | Membuat deskripsi temuan baru |
| Collection Log | `templates/collection-log.md` | Mencatat pengambilan data |
| Retest Plan | `templates/retest-plan.md` | Merencanakan retest |

**Cara pakai:** Copy template ke folder yang sesuai, rename sesuai kebutuhan, lalu isi.

---

## Definition of Done — Checklist per Temuan

Sebelum sebuah temuan (F-XXX) dianggap **selesai**, pastikan checklist berikut terpenuhi:

- [ ] `finding.md` sudah lengkap (ringkasan, deskripsi, langkah reproduksi, dampak, rekomendasi)
- [ ] Bukti tersedia di `evidence/` (screenshot, request, response, atau file)
- [ ] `timeline.md` sudah diisi
- [ ] Rating severity sudah ditentukan (Critical / High / Medium / Low / Informational)
- [ ] CVSS 3.1 sudah dihitung (jika berlaku)
- [ ] `register.csv` sudah diupdate
- [ ] Impact sudah dijelaskan dalam konteks bisnis
- [ ] Rekomendasi bisa dieksekusi (bukan sekadar "fix this")
- [ ] Retest sudah dilakukan (jika ada kesempatan) dan statusnya tercatat

---

## Tips untuk Junior

1. **Mulai dari template** — jangan buat dokumen dari nol. Copy dari `templates/`, lalu isi.
2. **Gunakan `register.csv`** sebagai dashboard utama — semua temuan ada di sini. Cek dulu sebelum membuat F-XXX baru.
3. **Bukti itu RAHASIA UTAMA** — tanpa bukti, temuan tidak valid. Screenshot dulu, analisa nanti.
4. **Jangan takut bertanya** — gunakan `04-worknotes/daily-notes.md` untuk mencatat pertanyaan dan diskusi.
5. **Raw itu suci** — jangan pernah mengedit file di `raw/`. Kalau salah output, taruh lagi yang baru.
6. **Cek `.gitignore`** — pastikan file sensitif (password, key, dump) tidak ke-commit.
7. **Konsistensi > kemewahan** — lebih baik temuan F-001 sampai F-010 konsisten formatnya, daripada F-001 bagus tapi F-002 asal-asalan.

---

## Aturan Git

- `git add .` — staging semua perubahan
- `git commit -m "descriptive message"` — commit dengan pesan yang jelas
- `git push` — push ke remote

**Contoh commit message yang baik:**
```
feat: add finding F-003 - SQL Injection on search endpoint
fix: update F-001 evidence with captured response
docs: add retest results for F-001 and F-002
chore: archive deprecated scan results from 2025-12
```

---

# English

> [Bahasa Indonesia](#va--pentest-repository)

This repository is used to store, manage, and track all results from **Vulnerability Assessment (VA)** and **Penetration Testing (Pentest)** — from raw data, finding evidence, analysis notes, to final reports and remediation retests.

This repo is designed to be **easy to use even for juniors**, with clear rules and a consistent structure.

---

## What is this repo and what is it for?

In a security project (VA / Pentest), we generate many files:
- Tool outputs (nmap, Burp Suite, Nessus, Nuclei, etc.)
- Screenshots / finding evidence
- Daily work notes
- Reports for clients
- Proof that remediations have been fixed (retest)

If all these files are scattered across desktops, emails, or random folders — we lose track, can't audit, and risk losing evidence.

**This repo solves that** with an organized structure and mandatory rules for all team members.

---

## Folder Structure

```
va-pentest-repo/
├── 01-scope/              ← What is allowed and not allowed to be tested
├── 02-asset-inventory/    ← List of all assets (servers, websites, applications)
├── 03-data-collection/    ← Raw output from tools + processed data
├── 04-worknotes/          ← Daily team notes (non-sensitive)
├── 05-findings/           ← ALL security findings + their evidence
├── 06-analysis/           ← Analysis: risk rating, CWE/OWASP/MITRE mapping
├── 07-reporting/          ← Draft & final reports, slides, deliverables
├── 08-retest/             ← Proof that findings have been remediated
├── 99-archive/            ← Files no longer relevant / deprecated
└── templates/             ← Ready-to-use templates (scope, finding, retest)
```

### Detailed Explanation

| Folder | Purpose | Who uses it? | Contents |
|---|---|---|---|
| `01-scope/` | Defines testing boundaries — what can/cannot be tested, emergency contacts | Pentester, PM, client | `scope.md` (goals & test types), `in-scope-assets.csv`, `out-of-scope.md`, `roe/` (Rules of Engagement), `credentials.pointer.md` (pointer to vault, NOT secrets) |
| `02-asset-inventory/` | Complete list of all assets being tested | All team | `assets.csv` (asset-id, hostname, IP, URL, service, env, owner), `diagrams/` (network topology, application architecture) |
| `03-data-collection/` | Stores ALL tool output from VA/pentest | Pentester | `raw/` — raw tool output (IMMUTABLE, do not edit!), `normalized/` — parsed/cleaned data for analysis, `notes/collection-log.md` — log of when data was collected and by whom |
| `04-worknotes/` | Daily work notes for team coordination | All team | `daily-notes.md` — non-sensitive notes |
| `05-findings/` | Primary location for all security findings | Pentester, reviewer | `register.csv` — list of all findings (F-001, F-002, ...), `F-XXX/` — per-finding folder with `finding.md`, `timeline.md`, and `evidence/` (screenshot, request, response, files) |
| `06-analysis/` | Analyze and categorize findings | Pentester, reviewer | `risk-rating.md` — risk rating summary, `mapping/` — CWE, OWASP Top 10, MITRE ATT&CK, `root-cause/` — root cause analysis |
| `07-reporting/` | Store all report documents | Pentester, PM | `report-draft/`, `report-final/`, `slides/`, `deliverables/` |
| `08-retest/` | Prove that findings have been fixed | Pentester, client, sysadmin | `retest-plan.md` — retest plan, `retest-results/` — results per finding, `closure-evidence/` — proof of closure |
| `99-archive/` | Store files no longer relevant | All team | Old files, deprecated outputs, etc. |
| `templates/` | Ready-to-use templates for creating new documents | All team | `scope.md`, `finding.md`, `collection-log.md`, `retest-plan.md` |

---

## Mandatory Rules

These rules **MUST** be followed by all team members. No exceptions.

### 1. Raw Output is IMMUTABLE (Must Not Be Edited)

- All tool output (nmap, Burp, Nessus, etc.) is saved **as-is** in `03-data-collection/raw/<tool-name>/`
- Never edit, delete, or rename files in `raw/`
- If you need a cleaned/parsed version, save it in `03-data-collection/normalized/<tool-name>/`
- Log the commands/tools used to process data in `03-data-collection/notes/collection-log.md`

**Why?** Raw output is evidence. If edited, it cannot be audited and is not valid in court.

### 2. Every Finding Must Have Evidence

- Findings are stored in `05-findings/F-XXX/`
- Each `F-XXX/` folder MUST contain:
  - `finding.md` — finding description, reproduction steps, impact, recommendation
  - `evidence/` — supporting evidence (screenshot, request, response, files)
  - `timeline.md` — finding timeline (when discovered, by whom)
- Never let evidence scatter across other folders

### 3. NO Secrets in the Repo

- **NEVER** store passwords, API keys, tokens, private keys, session cookies, or database dumps in the repo
- All credentials go in a vault (KeePass, Bitwarden, 1Password, etc.)
- The repo only stores a **pointer** to the vault at `01-scope/credentials.pointer.md` — without the actual secret value
- The `.gitignore` is configured to block sensitive files (`.env`, `*.pem`, `*.key`, `*secret*`, `*password*`, `*token*`, `*.sql`, etc.)

### 4. Evidence File Naming

Format: `YYYYMMDD-HHMM_<asset-id>_<short-description>.<ext>`

Examples:
```
20260330-1430_WEB-01_xss-reflected-login.png
20260330-1445_API-01_sqli-login-request.txt
20260330-1500_DB-01_unauthorized-access-pcap.pcap
```

### 5. Asset Naming

- Use `asset-id` from `02-asset-inventory/assets.csv`
- Examples: `WEB-01`, `API-01`, `DB-01`, `VPN-01`
- Don't use IPs directly in filenames (IPs change, asset-ids don't)

### 6. Large Files Go in External Storage

- If tool output is too large (>50MB), store in external storage (S3, Google Drive, NAS, etc.)
- In the repo, save a **manifest** (file list + location + SHA256 checksum)
- Large `.pcap` files should not be committed to git

---

## Workflow Example

### 1. Preparation
```
1. Fill 01-scope/scope.md with goals and testing boundaries
2. Fill 01-scope/in-scope-assets.csv with asset list
3. Copy to 02-asset-inventory/assets.csv
4. Store credentials in vault, create pointer at 01-scope/credentials.pointer.md
```

### 2. Data Collection
```
1. Run tools (nmap, Burp, Nessus, etc.)
2. Save raw output to 03-data-collection/raw/<tool-name>/
3. Parse/clean data, save to 03-data-collection/normalized/<tool-name>/
4. Log in 03-data-collection/notes/collection-log.md
```

### 3. Findings
```
1. Identify findings from collected data
2. Create folder 05-findings/F-XXX/
3. Fill finding.md with complete description
4. Save evidence to evidence/screenshots/, evidence/requests/, etc.
5. Fill timeline.md
6. Update 05-findings/register.csv
```

### 4. Analysis & Reporting
```
1. Fill 06-analysis/risk-rating.md
2. Map to CWE, OWASP, MITRE in 06-analysis/mapping/
3. Write report in 07-reporting/report-draft/
4. Finalize to 07-reporting/report-final/
```

### 5. Retest (if applicable)
```
1. Client fixes findings
2. Create plan in 08-retest/retest-plan.md
3. Perform retest, save results in 08-retest/retest-results/
4. Save closure evidence in 08-retest/closure-evidence/
5. Update 05-findings/register.csv (status → Closed)
```

---

## Ready-to-Use Templates

Use templates from the `templates/` folder to create new documents:

| Template | File | Purpose |
|---|---|---|
| Scope | `templates/scope.md` | Create a new testing scope document |
| Finding | `templates/finding.md` | Create a new finding description |
| Collection Log | `templates/collection-log.md` | Log data collection activities |
| Retest Plan | `templates/retest-plan.md` | Plan a retest |

**How to use:** Copy the template to the appropriate folder, rename as needed, then fill in.

---

## Definition of Done — Checklist per Finding

Before a finding (F-XXX) is considered **complete**, verify the following:

- [ ] `finding.md` is complete (summary, description, reproduction steps, impact, recommendation)
- [ ] Evidence is available in `evidence/` (screenshot, request, response, or files)
- [ ] `timeline.md` is filled in
- [ ] Severity rating is assigned (Critical / High / Medium / Low / Informational)
- [ ] CVSS 3.1 is calculated (if applicable)
- [ ] `register.csv` is updated
- [ ] Impact is explained in a business context
- [ ] Recommendation is actionable (not just "fix this")
- [ ] Retest has been performed (if possible) and status is recorded

---

## Tips for Juniors

1. **Start from templates** — don't create documents from scratch. Copy from `templates/`, then fill in.
2. **Use `register.csv` as your main dashboard** — all findings are listed here. Check first before creating a new F-XXX.
3. **Evidence is KING** — without evidence, a finding is invalid. Screenshot first, analyze later.
4. **Don't be afraid to ask** — use `04-worknotes/daily-notes.md` to log questions and discussions.
5. **Raw is sacred** — never edit files in `raw/`. If you got bad output, put the new one alongside it.
6. **Check `.gitignore`** — make sure sensitive files (passwords, keys, dumps) don't get committed.
7. **Consistency > polish** — it's better for F-001 through F-010 to have consistent formatting than F-001 looking great but F-002 being sloppy.

---

## Git Rules

- `git add .` — stage all changes
- `git commit -m "descriptive message"` — commit with a clear message
- `git push` — push to remote

**Good commit message examples:**
```
feat: add finding F-003 - SQL Injection on search endpoint
fix: update F-001 evidence with captured response
docs: add retest results for F-001 and F-002
chore: archive deprecated scan results from 2025-12
```
# inf-isa
