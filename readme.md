# Hash Lab

A hands-on, 100% client-side security sandbox and privacy-focused credential manager designed to demystify password entropy, demonstrate local hash cracking techniques, and showcase real-world cryptographic defenses.

Everything runs entirely in your browser using modern Web APIs and WebAssembly. No accounts, no backend servers, no tracking, and no external data leaks.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
  - [1. Password Strength Checker](#1-password-strength-checker)
  - [2. Hash Lab (Cracking Sandbox)](#2-hash-lab-cracking-sandbox)
  - [3. Password & Passphrase Generator](#3-password--passphrase-generator)
  - [4. Encrypted Local Vault](#4-encrypted-local-vault)
- [Security & Privacy Architecture](#security--privacy-architecture)
- [Supported Algorithms & Technologies](#supported-algorithms--technologies)
- [Getting Started](#getting-started)
- [Educational Purpose & Scope](#educational-purpose--scope)

---

## Overview

Most users know passwords should be "strong", but few understand *why* certain patterns fail or *how* hashing algorithms protect (or fail to protect) stolen databases. 

**Hash Lab** provides an interactive laboratory to:
- Visualize how entropy dictates resistance against brute-force attacks.
- Experience the dramatic speed difference between fast legacy hashes (MD5, SHA-256) and slow, memory-hard Key Derivation Functions (bcrypt, scrypt, Argon2).
- Generate truly unpredictable passwords and memorable passphrases.
- Store sensitive credentials securely in an encrypted local vault protected by state-of-the-art cryptography (Argon2id + AES-256-GCM).

---

## Key Features

### 1. Password Strength Checker

Analyze passwords in real time without submitting them anywhere:
- **Entropy Calculation**: Computes Shannon entropy ($\log_2(\text{charset}^{\text{length}})$) based on the active character space.
- **Passphrase Detection**: Automatically identifies multi-word passphrases (e.g. `correct-horse-battery-staple-42`), evaluating entropy based on dictionary combinatorial depth rather than naive character sets.
- **Vulnerability & Heuristic Detection**:
  - Flags passwords present in a built-in dictionary of common compromised passwords.
  - Detects keyboard walks and alphabet sequences (e.g., `qwerty`, `abc`, `123`).
  - Warns on consecutive character repetitions (e.g., `aaa`, `111`).
  - Evaluates length resilience (< 8 characters flagged as dangerous; 14+ characters recognized as brute-force resistant).
- **Realistic Crack Time Estimates**: Benchmarks the password against four distinct attacker profiles:
  1. *Online login (rate-limited)*: ~1 guess/sec
  2. *Online login (unrestricted)*: ~100 guesses/sec
  3. *Offline, slow hash (bcrypt)*: ~10,000 guesses/sec
  4. *Offline, fast hash (MD5/SHA-1 on modern GPU cluster)*: ~10,000,000,000 guesses/sec
- **Have I Been Pwned (HIBP) Breach Check**: Optional check using Cloudflare-backed k-anonymity. The browser computes a SHA-1 digest locally and sends only the first 5 hex characters to the HIBP API; the matching suffix is verified client-side so your password never leaves your device.

---

### 2. Hash Lab (Cracking Sandbox)

Test and recover passwords against known hashes (useful for CTF challenges, recovering forgotten personal passwords, or learning how credential stuffing works):
- **Supported Hash Types**:
  - **Fast / Checksums**: MD5, CRC32, SHA-1, SHA-256, SHA-384, SHA-512.
  - **Slow KDFs / Password Hashes**:
    - **bcrypt**: Matches modular crypt format hashes (`$2a$`, `$2b$`) including embedded salt and cost parameters.
    - **Argon2**: Verifies encoded strings (`$argon2i$`, `$argon2d$`, `$argon2id$`) with custom memory and iteration parameters via WebAssembly.
    - **scrypt**: Interactive parameter tuning ($N$ cost, $r$ block size, $p$ parallelization, $dkLen$, and text or `0x` hex salts).
- **Wordlist Flexibility**:
  - Built-in curated list of common baseline passwords (~600 entries).
  - Custom wordlist input for user-supplied candidate files.
- **Combinatorial Mutation Engine**:
  - Capitalization rules (`password` $\to$ `Password`).
  - Numeric suffixes (`0` through `999`).
  - Year suffixes (`1970` through `2030`).
  - Leetspeak substitutions (`a` $\to$ `4`, `e` $\to$ `3`, `i` $\to$ `1`, `o` $\to$ `0`, `s` $\to$ `5`).
- **Interactive Execution & Telemetry**:
  - Live search space counter calculating candidate permutations before running.
  - Real-time stats display tracking total attempts, guess rate (guesses/sec), and elapsed time.
  - Virtual streaming console logging batch progress and crack hits.

---

### 3. Password & Passphrase Generator

Generate cryptographically secure passwords locally using `window.crypto.getRandomValues`:
- **Three Generation Styles**:
  1. **Random Characters**: Highest entropy density. Configurable length (8–64 characters) with granular toggles for lowercase, uppercase, digits, and special characters, plus an option to exclude ambiguous characters (`l`, `1`, `I`, `0`, `O`).
  2. **Word-based Passphrases**: Combines 4–8 curated words from a built-in nature dictionary, applies random casing, and appends a numeric tail for human-memorable yet cryptographically resilient access.
  3. **Strengthen Existing Sequence**: Takes a memorable base word and scatters randomized digits and symbols throughout the string (rather than predictable appending), demonstrating how to reinforce existing patterns with quantifiable added entropy.
- **Strength & Entropy Meter**: Displays instantaneous bit entropy and projected offline GPU cracking resistance.
- **Safe Clipboard Copy**: Native clipboard copy with automatic fallback support for non-secure contexts.

---

### 4. Encrypted Local Vault

A zero-knowledge, client-side credential vault stored securely in the browser's `localStorage`:
- **Military-Grade Cryptography**:
  - **Key Derivation**: Derives a 256-bit encryption key from your master password using **Argon2id** (3 iterations, 19 MiB memory cost, parallelism 1, and a 16-byte cryptographically random salt).
  - **Authenticated Encryption**: Encrypts the entire credential store using **AES-GCM** (Galois/Counter Mode) with unique 12-byte initialization vectors (IVs) generated per save operation.
  - **Zero-Knowledge Architecture**: The master password and unencrypted keys are never stored on disk. If the master password is forgotten, the vault cannot be recovered.
- **Session & Memory Protection**:
  - **Inactivity Auto-Lock**: Automatically locks and clears memory keys after 5 minutes of inactivity (tracks mouse movement, keystrokes, touch, and scroll events).
  - **Clipboard Auto-Clear**: Automatically wipes copied passwords and usernames from the system clipboard after 20 seconds.
- **Credential Management**:
  - Store site/app name, URL, username/email, category, password, and custom notes (recovery codes, security questions).
  - Category tagging (`Personal`, `Work`, `Finance`, `Social`, `Other`) with color-coded UI pills.
  - One-click integrated password generator in the entry form.
  - Reveal/hide password toggle.
  - Live search filter across sites, usernames, categories, and URLs.
- **Portability & Backups**:
  - **Export Encrypted Vault**: Download an encrypted `.json` backup file (`hash-lab-vault-YYYY-MM-DD.json`).
  - **Import Vault**: Restore or migrate your encrypted vault file across browsers or devices by unlocking with your master password.

---

## Security & Privacy Architecture

| Principle | Implementation |
| :--- | :--- |
| **No Telemetry or Remote Servers** | The application does not send analytics, logs, or user data to any server. |
| **Local Cryptography** | Relies on the browser's native Web Crypto API (`crypto.subtle`) and compiled WebAssembly for heavy KDF computation. |
| **k-Anonymity Breach Checking** | Password breach verification never exposes the password or full hash. Only a 5-character SHA-1 prefix is transmitted; matching occurs locally. |
| **Non-Extractable Keys** | AES-GCM vault encryption keys are loaded as non-extractable CryptoKey objects in memory and discarded upon lock. |
| **Clipboard Sanitation** | Automatic timer sanitizes copied credentials to minimize clipboard snooping. |

---

## Supported Algorithms & Technologies

- **Front-End**: Pure HTML5, modern CSS3 (Custom Properties, CSS Grid, Flexbox), Vanilla JavaScript (ES6+).
- **Typography**: IBM Plex Sans & IBM Plex Mono via Google Fonts.
- **Cryptographic Libraries**:
  - [Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API): Hardware-accelerated SHA-1/256/384/512 and AES-GCM.
  - [bcryptjs](https://github.com/dcodeIO/bcrypt.js) (`v2.4.3`): Pure JavaScript implementation of bcrypt.
  - [scrypt-async](https://github.com/dchest/scrypt-async-js) (`v2.0.1`): Asynchronous scrypt implementation.
  - [argon2-browser](https://github.com/antelle/argon2-browser) (`v1.18.0`): WebAssembly/bundled implementation of Argon2 (i, d, id).
  - Built-in lightweight implementations of **MD5** and **CRC32**.

---

## Getting Started

No installation, build step, Node.js, or web server is required.

1. Clone or download this repository:
   ```bash
   git clone https://github.com/your-username/secure-pass.git
   cd "secure-pass"
   ```
2. Open [`hash-lab.html`](file:///d:/Projects/Secure%20Pass/hash-lab.html) directly in any modern web browser (Google Chrome, Mozilla Firefox, Microsoft Edge, Brave, Safari).
3. *(Optional)* If you prefer running through a local development server:
   ```bash
   npx serve .
   # or
   python -m http.server 8000
   ```
   Then navigate to `http://localhost:8000/hash-lab.html`.

> [!NOTE]
> Some browser security models restrict clipboard operations (`navigator.clipboard`) when opening files directly from the filesystem (`file://`). Running via a local server or `localhost` enables full modern clipboard support.

---

## Educational Purpose & Scope

This tool is created strictly for educational purposes, defensive security training, and credential management. It is designed to demonstrate:
- Why passphrases and length provide exponential advantages over complex short passwords.
- Why fast cryptographic hashes (MD5, SHA families) are unsafe for password storage.
- How attackers utilize dictionary and mutation rules against password databases.
- How memory-hard KDFs (bcrypt, scrypt, Argon2) effectively protect user passwords by multiplying cracking costs.

*Disclaimer: This project does not attempt live network authentication, attack WiFi networks, or interact with unauthorized external systems.*
