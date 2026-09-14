# Hash Lab

Hash Lab is a hands-on, browser-based sandbox designed for understanding password strength, hashing algorithms, and local cracking techniques. It operates entirely client-side, ensuring your passwords and hashes never leave your browser.

## Features

- **Password Strength Checker**: Analyze your password's entropy, detect common weak patterns (e.g., sequential characters, repeated patterns), and view estimated crack times across various attack scenarios. Includes an optional integration with the Have I Been Pwned (HIBP) k-anonymity API to check if your password has been exposed in known breaches.
- **Hash Lab (Cracking Sandbox)**: Attempt to recover passwords from hashes using built-in or custom wordlists along with mutation rules (like leetspeak, capitalization, and appending numbers). This hands-on lab highlights the stark differences between fast hashes (MD5, SHA-1/256/384/512, CRC32) and slow Key Derivation Functions (bcrypt, scrypt, argon2).
- **Password Generator**: Generate secure, random passwords or word-based passphrases locally using the browser's cryptographically secure random number generator (`crypto.getRandomValues`).

## Usage

Simply open `hash-lab.html` in any modern web browser. No server setup or installation is required.

## Educational Purpose

This project is built for learning to demonstrate:
- Entropy math and why password length often matters more than complexity.
- How dictionary and mutation attacks work in practice.
- The critical importance of using slow hashing algorithms (like bcrypt, scrypt, and argon2) for storing real passwords compared to fast hashing algorithms (like MD5 or SHA).

*Disclaimer: This tool is strictly for educational purposes. It does not attempt logins, attack WiFi networks, or interact with any live systems.*
