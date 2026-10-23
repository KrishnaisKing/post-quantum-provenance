---
name: post-quantum-provenance-papers
description: Reference index of the research papers in this folder, all related to post-quantum cryptography (PQC) applied to watermarking and AI/model provenance. Use this skill whenever working inside the "post quantum provenance" folder — for literature review, summarizing or comparing papers, drafting related-work sections, extracting citations/BibTeX, checking what has already been collected, or identifying which paper covers a given technique (diffusion-model watermarking, medical-image watermarking, federated learning + PQC signatures, AI supply chain / MBOM provenance). Also trigger when the user mentions PQC watermarking, model provenance, Dilithium, FALCON, SPHINCS+, ML-DSA, SLH-DSA, or asks "what papers do I have on X".
---

# Post-Quantum Provenance — Paper Collection

This folder holds source papers for the user's ongoing research at the intersection of
**post-quantum cryptography (PQC)** and **watermarking / provenance** (mainly for AI
models, media, and medical images). Use this file to orient quickly instead of re-opening
every PDF.

## Papers

1. **`fphy-13-1750515.pdf`**
   Hu, Zhang, Al-Dossari, El-Gawaad, Rakhimzhanova, Khan (2026).
   *"Robust watermarking for diffusion models using error-correcting codes and
   post-quantum key encapsulation."* Frontiers in Physics 13:1750515.
   DOI: 10.3389/fphy.2025.1750515
   Watermarks diffusion-model outputs; combines error-correcting codes with a PQ key
   encapsulation mechanism for robustness/security of the embedded watermark.

2. **`pqc watermark 4.pdf`**
   Li, Chen, Liu (UNC Chapel Hill / U. Pittsburgh, 2024).
   *"Enhancing Quantum Security over Federated Learning via Post-Quantum Cryptography."*
   arXiv:2409.04637.
   Uses NIST PQC signature schemes (Dilithium, FALCON, SPHINCS+) to authenticate model
   updates transmitted in federated learning, protecting against quantum-capable forgery.

3. **`pqc watermark medical imagery.pdf`**
   Hacini Ikram, Moad Med Sayah, Kafi Redouane, Zermi Narima, Khaldi Amine, Boukhamla,
   Sahu (2026). *"Quantum-inspired post-quantum secure DCT watermarking for tamper
   detection and localization in medical images."* Array 30 (2026) 100870 (Elsevier).
   Fragile DCT-domain watermarking for medical images with PQC-secured keys; detects and
   localizes tampering for telemedicine/EHR integrity.

4. **`systems-14-00593.pdf`**
   Robert Campbell (2026). *"AI Supply Chain Security: MBOM-PQC Provenance, PQC
   Attestation, and a Maturity Model for Quantum-Resistant Assurance."* Systems 2026,
   14, 593 (MDPI). DOI: 10.3390/systems14050593.
   Proposes a Model Bill of Materials (MBOM) plus PQC attestation (ML-DSA, SLH-DSA,
   hybrid signatures) for verifiable AI model/dataset lineage, and a maturity model for
   PQC migration. Closest paper in this set to the "provenance" framing of the folder
   name — likely a key related-work / positioning reference.

5. **`pqc watermark us patent.pdf`**
   Scanned/image-based PDF (no extractable text layer — `pdftotext` returns empty).
   Filename suggests a US patent on PQC watermarking. To use its content, OCR is needed
   first (no OCR tool was available in this environment when this index was built).

6. **`pqc watermark paper 1.pdf`** — ⚠️ **filename mismatch**
   Despite the filename, this PDF's actual content is Harrison Dahme (2026, preprint),
   *"Poisoned Acoustics: Targeted Data Poisoning Attacks on Acoustic Vehicle
   Classification at Sub-1% Corruption Rates,"* arXiv:2602.22258 — a data-poisoning
   attack paper on acoustic vehicle classification, unrelated to PQC or watermarking.
   Flag this to the user before citing it as a PQC/watermarking source; it may be
   misfiled or the wrong file was saved under this name.

7. **`A_Study_of_Post-Quantum_Digital_Signatures_in_the_Automotive_Industry.pdf`**
   Joseph Stalin, Ritvik Kumar Rai (Elektrobit India, 2025). *"A Study of Post-Quantum
   Digital Signatures in the Automotive Industry."* IEEE ICVTTS 2025.
   DOI: 10.1109/ICVTTS67119.2025.11296596.
   Benchmarks CRYSTALS-Dilithium (ML-DSA, FIPS 204) on automotive-grade hardware (SIMCom
   SIM8918EA infotainment SoC, Infineon AURIX TC-222 Classic AUTOSAR ECU) to assess
   feasibility of replacing RSA/ECDSA/EdDSA for secure FOTA, secure boot, V2X, and
   diagnostics. Pure PQC performance/feasibility study — no watermarking content.

8. **`ML-DSA.pdf`**
   Zheng Liu, An Wang, Congming Wei, Yaoling Ding, Jingqi Zhang, Annyu Liu, Liehuang Zhu
   (Beijing Institute of Technology / Communication University of China). *"Release the
   Power of Rejected Signatures: An Efficient Side-Channel Attack on the ML-DSA
   Cryptosystem."*
   Belief-propagation side-channel key-recovery attack on ML-DSA that, unlike prior work,
   also exploits leakage from *rejected* signatures (not just the final valid one) via a
   joint factor-graph model. Demonstrated on Cortex-M4, reduces required traces for full
   key recovery by ≥50%, and recovers the key in <30 traces from rejected-signature
   leakage alone. Offensive/attack paper on ML-DSA itself — relevant context for any paper
   here that assumes ML-DSA is safe to deploy without side-channel countermeasures.

9. **`Quantum_Resistant_cryptography_based_deepfakedetection_models_using_Hybrid_Watermarking.pdf`**
   Shaik Abdul YunusBasha, Dr. K. UlagaPriya (VISTAS, Chennai, India, 2026). *"Quantum
   Resistant cryptography based deepfake detection models using Hybrid Watermarking."*
   IEEE ICDSBS 2026. DOI: 10.1109/ICDSBS69077.2026.11634826.
   Combines hybrid DWT-DCT watermark embedding, PQC (liboqs, Microsoft SEAL) to protect
   watermark/key material, blockchain-assisted verification, and a deep-learning detector
   for deepfake video/audio artifacts. Closest sibling to `fphy-13-1750515.pdf` and
   `pqc watermark medical imagery.pdf` — candidate related-work cluster (PQC-secured
   watermarking pattern applied to a third media domain).

## Working with the PDFs in this environment

- The `Read` tool's page-image rendering doesn't work here (`pdftoppm` is not installed),
  so it will error on these PDFs.
- `pdftotext` (poppler, via mingw64) **is** available and works — use it via Bash to pull
  text, e.g.: `pdftotext -f 1 -l 2 "<file>.pdf" -` for the first two pages, or without
  `-f`/`-l` for the whole document.
- `pqc watermark us patent.pdf` has no text layer at all — it's a scanned document, so
  `pdftotext` returns nothing; it needs OCR before its content can be reviewed.

## Suggested workflows

- **Literature review / related work section**: extract each paper's problem statement,
  method, and key results via `pdftotext`, then synthesize by sub-topic (media
  watermarking vs. medical imaging vs. FL vs. supply-chain provenance).
- **Citation list / BibTeX**: the metadata above (authors, title, venue, year, DOI/arXiv
  ID) is enough to generate BibTeX entries directly — verify DOIs before final use.
- **Comparison table**: compare papers along axes like target domain, PQC primitive used
  (KEM vs. signature scheme), watermark type (robust vs. fragile), and threat model.
- Before treating `pqc watermark paper 1.pdf` as a source, confirm with the user whether
  it was meant to be a different file — its content doesn't match its filename.
- **Cross-collection note**: `ML-DSA.pdf` (attack paper) undercuts the turnkey-security
  assumption relied on by `systems-14-00593.pdf`'s PQC attestation and
  `A_Study_of_Post-Quantum_Digital_Signatures_in_the_Automotive_Industry.pdf`'s deployment
  benchmarks — read together when assessing real-world ML-DSA deployment readiness.
  `Quantum_Resistant_cryptography_based_deepfakedetection_models_using_Hybrid_Watermarking.pdf`
  joins `fphy-13-1750515.pdf` and `pqc watermark medical imagery.pdf` as a third
  PQC-secured-watermarking domain (deepfake media vs. generative-model output vs. medical
  images) — natural related-work cluster for a watermarking comparison table.
