<img width="1024" height="1536" alt="SteganoShield" src="https://github.com/user-attachments/assets/5e7bfbf4-b478-4c38-acbe-b8262f5dbc9b" />

# Morphs SteganoShield: Technical Architectural Description

### 1. Overview & System Mission
**Morphs SteganoShield** is an air-gapped, zero-telemetry steganographic security suite designed to weave encrypted data into lossless digital images. Operating entirely in local system RAM with zero cloud dependencies, the engine embeds UTF-8 text or arbitrary binary files directly into the spatial pixel bitplanes of digital carriers without introducing perceptible visual artifacts.

---

### 2. Core Cryptographic Architecture & STG1 Protocol
The engine operates on a deterministic, low-overhead binary envelope format embedded across the carrier’s least significant bitplanes:

* **37-Byte STG1 Envelope Standard:**
  * **Bytes 0–3 (`0x53, 0x54, 0x47, 0x31`):** Magic signature (`STG1`). Serves as an integrity anchor and messenger compression detector.
  * **Byte 4:** Armoring configuration flags (AES Encryption, Arbitrary File Mode, PRNG Coordinate Shuffling, Anti-Steganalysis Dithering).
  * **Bytes 5–20:** 16-byte cryptographic salt generated via secure hardware random number generators.
  * **Bytes 21–32:** 12-byte initialization vector (IV).
  * **Bytes 33–36:** 32-bit unsigned big-endian integer defining payload byte length.
* **Origin Header Allocation:** The 37-byte envelope is mapped across exactly 99 sequentially addressed origin pixels ($\lceil(37 \times 8) / 3\rceil$).
* **Cryptographic Ciphers:**
  * **Web / Hybrid:** AES-256-GCM authenticated cipher with 128-bit authentication tags, derived via PBKDF2-SHA256.
  * **Windows Desktop:** Hardware AES-256 in CBC mode with PKCS7 padding, keyed via 10,000-iteration PBKDF2-HMAC.
* **Bitplane Isolation:** Bit insertion is restricted to the Red, Green, and Blue color channels (RGB). The Alpha channel is locked at 255 to prevent unintended alpha blending or transparency recalculation corruption.

---

### 3. Anti-Steganalysis & Stealth Armoring Systems
To defeat automated steganalysis (e.g., Chi-square attacks, RS analysis, Sample Pair analysis), SteganoShield incorporates multi-stage bitplane obfuscation:

* **PRNG Coordinate Scrambler:** Uses a high-speed 32-bit PRNG (Mulberry32 / FNV-1a hash state) keyed to the user’s secret passphrase to generate a non-sequential spatial permutation. Payload bits are scattered pseudo-randomly across all pixels beyond the 99-pixel origin header, preventing concentrated statistical density clusters.
* **Anti-Steganalysis Noise Dithering:** Automatically injects pseudo-random bit pools into trailing carrier pixels beyond the payload boundary (up to 8,000 subsequent pixels) to equalize LSB variance and mask artificial entropy transitions.
* **Lossless Transport Guard:** Scans incoming carrier headers for lossy transport artifacts. If carrier bitplanes evaluate to pure zeroes (indicative of WhatsApp, Discord, or JPEG recompression), the system automatically triggers an alert instructing the user to transfer the image via uncompressed document mode.

---

### 4. Payload Modes & Data Encapsulation
* **Text Message Mode:** Direct encoding of raw UTF-8 plain text with live capacity-utilization tracking and character limit monitoring based on image resolution.
* **Arbitrary Binary File Injection:** Encapsulates arbitrary file formats (ZIP, PDF, DOCX, executables, custom encrypted volumes) into the carrier. Embeds a binary file descriptor prefix containing:
  * 1 byte: Filename length ($L_n$)
  * $L_n$ bytes: UTF-8 encoded filename
  * 1 byte: MIME type length ($L_m$)
  * $L_m$ bytes: MIME type string
  * Trailing stream: Raw binary file payload
* **Proportional Dimension Clamping:** Protects memory budgets by normalizing incoming carrier images exceeding hardware bounds (clamped to 1024px maximum dimension on mobile/web and 1600px on desktop) using high-quality bicubic downsampling while preserving 32-bit ARGB fidelity.

---

### 5. Steganalysis Radar & Forensic Visualizer
SteganoShield provides real-time forensic inspection tools to audit image integrity and detect tampering:

* **Pixel Difference Heatmap Visualizer:** Computes per-pixel absolute deltas between the unaltered carrier and the encoded Stegano-PNG. Altered bitplanes are amplified $20\times$ in high-contrast cyan/amber (`#38BDF8`), while untouched pixels remain deep obsidian (`#05131E`).
* **LSB Randomness Variance Radar:** Computes bit distribution across all color planes, displaying live variance metrics ($\sigma$), carrier-to-payload saturation percentages, and an automated Visual Anomaly Score (Undetectable, Nominal, or Elevated).

---

### 6. Deployment Environments & Platform Specifications

| Feature / Metric | Web / Mobile Hybrid Edition (`steganoshield.html`) | Windows Desktop Edition (`SteganoShield_updated.ps1`) |
| :--- | :--- | :--- |
| **Runtime Target** | Modern Web Browsers, PWA, Android WebViews | Windows PowerShell 5.1+ / .NET 4.5+ (PS2EXE Ready) |
| **GUI Framework** | CSS3 Flexbox/Grid + HTML5 Canvas API | System.Windows.Forms + Native GDI+ Graphics |
| **Cryptographic Core** | WebCrypto API (`window.crypto.subtle`) | `System.Security.Cryptography.Aes` + C# Native PRNG |
| **Workspace Layout** | Tabbed Workspaces (Encrypt, Decrypt, Radar) | Dual-Column Chassis with Side-by-Side Dual Viewports |
| **Export Engine** | `UniversalDownloader` (Web Share / Blob / Anchor) | Direct Stream Disk Commit via `SaveFileDialog` |
| **Theme System** | 10 High-Contrast Themes (WCAG AAA Compliant) | 10 High-Contrast Themes with Dynamic GDI+ Repainting |
| **Telemetry / Logging** | Volatile `sessionStorage` with TXT/Clipboard export | Synchronized `RichTextBox` Audit Log with Export |
