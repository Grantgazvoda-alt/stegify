# Stegify — Grant's Fork Notes

**Upstream:** DimitarPetrov/stegify (https://github.com/DimitarPetrov/stegify)
**Forked:** 2026-11-02
**Language:** Go
**Stars (upstream):** ~1,300

## What it is

LSB (least significant bit) steganography tool. Embeds arbitrary data into the least significant bits of pixel color channels in PNG and JPEG images. Clean encode/decode CLI, no visible artifacts at typical payload sizes.

## Core modules

- `stegify.go` — CLI entrypoint, flag parsing, encode/decode dispatch
- `steg/steg_encode.go` — LSB embedding logic, carrier image loading, payload chunking
- `steg/steg_decode.go` — LSB extraction, payload reconstruction
- `bits/bit_utils.go` — low-level bit manipulation helpers
- `steg/*_test.go`, `stegify_test.go`, `bits/*_test.go` — unit tests covering round-trip encode/decode

## CLI usage

```bash
# Encode: hide payload.txt inside carrier.png -> output.png
go run . -encode -carrier carrier.png -payload payload.txt -out output.png

# Decode: recover hidden data from stego image
go run . -decode -stego output.png -out recovered.txt
```

## Capacity math

One bit per color channel per pixel. For an RGB image of W×H: 3×W×H bits ≈ 0.375 bytes per pixel. A 1920×1080 image holds roughly 760 KB. JPEG recompression can destroy the payload — PNG is the safe carrier format.

## Detection surface

- Chi-square analysis on color channel distributions
- Histogram anomalies in LSBs
- File entropy spikes vs. clean carrier
- Structural comparison against original if you have it

## Why this fork matters for the stack

Pairs with the php-ctf interceptor: you can hide exploit captures or flag material inside innocuous-looking images for offline transfer without touching the network. Also useful as a detection-research target — the BountyOps Sigma repo can grow rules for stego-in-JPEG anomalies.

## Status

Fork only. No local build or runtime test on the ROG yet. Next step: `go build` on the ROG, run the example lake.jpeg encode/decode round-trip, confirm bit-exact recovery.
