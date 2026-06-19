# Sentinel — Password Breach Auditor

A zero-leak password auditing tool. Checks whether a password has appeared in known data breaches and scores its resistance to offline cracking — **without the password ever leaving the browser intact.**

🔗 **Live demo:** https://sumukh10.github.io/sentinel-breach-auditor/
🏠 **Portfolio:** https://sumukh10.github.io/

## What it does

- **Breach check via k-anonymity** — hashes the password locally with SHA-1, sends only the first 5 hash characters to the [HaveIBeenPwned](https://haveibeenpwned.com/API/v3#PwnedPasswords) range API, and matches the remainder client-side. The server never sees enough to identify the password.
- **Entropy scoring** — estimates strength in bits from character pool and length.
- **Offline crack-time estimate** — models time to break against a fast offline attacker (~10¹¹ guesses/sec).
- **Policy validation** — length, character mix, and common-sequence checks.

## Security concepts demonstrated

| Concept | Where it shows up |
|---|---|
| k-anonymity | Only a 5-char hash prefix is transmitted |
| Cryptographic hashing | SHA-1 via the Web Crypto API |
| Threat modeling | Crack-time assumes an offline fast-hash attacker |
| Privacy by design | No password, full hash, or result is stored or sent in full |

## Run locally

```bash
git clone https://github.com/YOUR_USERNAME/sentinel-breach-auditor.git
cd sentinel-breach-auditor
# any static server works:
python3 -m http.server 8000
# open http://localhost:8000
```

> The Web Crypto API requires a secure context, so use `localhost` or HTTPS (not a raw `file://` path) for the SHA-1 hashing to work.

## Deploy (free)

Push to GitHub, then either:
- **GitHub Pages** — Settings → Pages → deploy from `main` / root.
- **Netlify / Vercel** — drag the folder in, or connect the repo. Zero config; it's a single static file.

## How the k-anonymity flow works

1. `SHA-1("hunter2")` → `F3BBBD66A63D4BF1747940578EC3D0103530E21D`
2. Send prefix `F3BBB` to `api.pwnedpasswords.com/range/F3BBB`
3. API returns ~500 suffixes that share the prefix, with breach counts
4. The suffix `D66A63D...` is matched locally — the match never touches the wire

## Tech

Single-file vanilla JS. No dependencies, no build step, no backend. Web Crypto for hashing, Fetch for the API.

## License

MIT
