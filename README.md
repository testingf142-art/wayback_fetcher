# ⚡ Wayback URL & Parameter Fetcher

A fast, lightweight, and **100% keyless** Bash tool designed for security researchers, bug bounty hunters, and pentesters to extract historical endpoints and parameterized URLs directly from the **Wayback Machine CDX API**.

---

## 🚀 Features

- **No API Keys Required:** Uses the public Wayback Machine CDX API for free and unlimited historical reconnaissance.
- **Subdomain Discovery (`-s`):** Queries wildcard domains (`*.domain.com/*`) to surface subdomains and forgotten endpoints.
- **Smart Noise & Asset Filtering (`-p`):**
  - Excludes plain paths with no parameters.
  - Strips noisy static files:
    - **Images:** `.png`, `.jpg`, `.jpeg`, `.gif`, `.svg`, `.webp`, `.ico`, `.bmp`, `.tiff`
    - **Docs & Data:** `.pdf`, `.txt`, `.xml`, `.doc`, `.xls`, `.ppt`, `.csv`, `.rtf`
    - **Web Assets:** `.css`, `.woff`, `.woff2`, `.ttf`, `.eot`, `.otf`
    - **Media & Archives:** `.mp3`, `.mp4`, `.zip`, `.tar`, `.gz`, `.7z`, `.rar`, `.exe`, `.dmg`
  - Keeps parameters even if query values reference extensions (e.g. `/api/view?file=doc.png` is preserved).
- **Anti-Timeout Engine (`-l`):** Set custom snapshot limits to avoid 504 timeouts on massive domains like `rumble.com` or `yahoo.com`.
- **Bulk Scan:** Supports scanning single targets or a list of domains from a file.

---

## 📥 Installation & Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/testingf142-art/wayback_fetcher.git
   cd wayback_fetcher
   ```

2. **Grant execution permissions:**
   ```bash
   chmod +x wayback_fetcher.sh
   ```

3. *(Optional)* Move to your PATH for global access:
   ```bash
   sudo mv wayback_fetcher.sh /usr/local/bin/wayback-fetcher
   ```

---

## 🛠 Usage & Flags

```text
Usage: ./wayback_fetcher.sh [options] <domain | domains_file>

Options:
  -s, --subs         Include all subdomains (*.domain.com/*)
  -p, --params-only  Exclude static files & plain paths; keep ONLY endpoints with query parameters (?)
  -l, --limit NUM    Limit results fetched from Wayback (prevents timeouts on huge domains)
  -o, --output FILE  Save clean results to an output file
  -h, --help         Show this help message
```

## 💡 Practical Examples

### 1. Extract only parameterized URLs for a target (XSS, SSRF, SQLi hunting)
```bash
./wayback_fetcher.sh -p example.com
```

### 2. Full domain + subdomains recon without server timeouts
```bash
./wayback_fetcher.sh -s -p -l 10000 target.com -o target_params.txt
```

### 3. Bulk scan a list of in-scope domains
```bash
./wayback_fetcher.sh -s -p -o all_params.txt domains.txt
```


## 🔗 Chaining with Other Tools

Combine this tool with the standard bug bounty pipeline:

```bash
# Extract parameters -> check live status with httpx
./wayback_fetcher.sh -s -p target.com | httpx -silent -status-code -mc 200,301,302

# Extract parameters -> Scan for vulnerabilities with Nuclei
./wayback_fetcher.sh -s -p target.com | nuclei -t dasti/cves/ -severity medium,high,critical
```

---

## 📋 Requirements

The script uses native Unix utilities:
- `bash`
- `curl`
- `grep`
- `sed`
- `sort`

---

## ⚖️ License
This project is licensed under the [MIT License](LICENSE).
