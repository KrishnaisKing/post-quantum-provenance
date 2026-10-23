# Research Report: 3 New Papers Added to the Collection

This document reviews the three PDFs added since `SKILL.md`'s original 6-paper index was
built, and synthesizes how they connect to the existing collection (diffusion-model
watermarking, medical-image watermarking, FL+PQC signatures, MBOM-PQC provenance). All
three now have dedicated reviewer agents in `.claude/agents/` for future deep-dives.

---

## 1. Automotive PQC Digital Signatures — feasibility/benchmarking

**Citation:** Joseph Stalin, Ritvik Kumar Rai (Elektrobit India). *"A Study of
Post-Quantum Digital Signatures in the Automotive Industry."* IEEE ICVTTS 2025.
DOI: 10.1109/ICVTTS67119.2025.11296596.
**File:** `A_Study_of_Post-Quantum_Digital_Signatures_in_the_Automotive_Industry.pdf`

**What it is:** A pure performance/feasibility study — no watermarking or provenance
content. Benchmarks CRYSTALS-Dilithium (ML-DSA, FIPS 204) on two automotive compute
classes: the Infineon AURIX TC-222 (a 133 MHz, 96 kB-SRAM Classic AUTOSAR ECU) and the
SIMCom SIM8918EA (a 2 GHz Cortex-A53 infotainment-class SoC).

**Key results:**
- On TC-222 (Dilithium2/ML-DSA-44 only — larger variants exceed its RAM): keygen ≈21 ms,
  sign ≈85–113 ms, verify ≈47 ms for small CAN/CAN-FD-sized messages. A simulated 1 GB
  OTA-firmware payload (streamed via SHAKE256) takes **~30 minutes** to sign/verify —
  deemed impractical for that use case, though SRAM/Flash headroom is ample (~29%/~6%
  used); the bottleneck is pure compute throughput, not memory.
- On SIM8918EA (all three Dilithium variants, reference vs. ARM-NEON-optimized): sub- to
  low-millisecond operations across the board (e.g., Dilithium2 sign: 2.35 ms reference →
  0.62 ms NEON). NEON speedups average 1.83× (keygen), 3.65× (sign), 2.11× (verify).
- Verdict: Dilithium2/ML-DSA-44 is "the most suitable for resource-constrained automotive
  environments." Feasible today on infotainment-class hardware; feasible on Classic
  AUTOSAR MCUs only for small/typical protocol payloads, not bulk firmware signing.
- Self-named open challenges: large key/signature sizes vs. classical schemes, no
  migration roadmap proposed, and — explicitly — **vulnerability to side-channel/fault
  attacks was not tested**, only cited as future work (citing Ravi et al.).

**Methodology gaps:** no repetition count stated for TC-222 (unlike SIM8918EA's n=30), no
variance/confidence intervals anywhere, no classical RSA/ECDSA/EdDSA benchmarks on the
same hardware (comparisons are narrative only), and no power/energy measurements despite
the automotive context.

---

## 2. ML-DSA Side-Channel Attack — rejected-signature leakage

**Citation:** Zheng Liu, An Wang, Congming Wei, Yaoling Ding, Jingqi Zhang, Annyu Liu,
Liehuang Zhu (Beijing Institute of Technology / Communication University of China).
*"Release the Power of Rejected Signatures: An Efficient Side-Channel Attack on the
ML-DSA Cryptosystem."*
**File:** `ML-DSA.pdf`

**What it is:** An offensive security paper. ML-DSA's Fiat-Shamir-with-aborts signing
loop discards ("rejects") candidate signatures that fail bounds checks before producing
the final valid one — on average **3.25–4.10 rejections per signing attempt** depending
on parameter set. Prior attacks only exploited leakage from the one valid signature (or,
in one prior work, a crude rejection-event signal requiring ~10⁶ rejected signatures).
This paper shows the *rejected* signatures leak the same rich intermediates (challenge `c`
via NTT(c), and `c·s1` via the INTT step) as the valid one, and jointly models
accepted+rejected leakage in one belief-propagation (BP) factor graph.

**Key results (target: STM32F4/Cortex-M4, EM side channel, profiled/template attacker):**
- Combining valid + rejected signatures cuts traces needed for full key recovery by
  **~50%** on the reference implementation (8→4 traces) and **~30%** on the
  ASM-optimized implementation (20+→14 traces).
- Using **only rejected signatures** (no valid-signature leakage at all), full key
  recovery in **fewer than 30 traces** — roughly 4 orders of magnitude better than prior
  rejection-based work (~10⁵ traces), because this attack extracts the rich `c`/`c·s1`
  intermediates rather than just the coarse rejection event.
- This requires high challenge-recovery accuracy (≥97–99% depending on implementation);
  below that threshold, rejected-signature information degrades rather than helps.
- Critical caveat stated in the paper's own conclusion: **current state-of-the-art ML-DSA
  masking countermeasures do not cover the challenge `c`**, so this attack's exploit path
  remains viable even against masked implementations (though harder).

**Limitations:** requires physical EM trace access + a profiling/clone device (not
remote/black-box); only ML-DSA-44 evaluated, on one platform; masked implementations not
experimentally tested (left as future work).

**Why it matters here:** this is direct evidence that ML-DSA cannot be treated as a
secure black box at the implementation level on embedded targets — exactly the class of
hardware (Cortex-M4-adjacent) benchmarked in Paper 1 above.

---

## 3. Quantum-Resistant Deepfake Detection via Hybrid Watermarking

**Citation:** Shaik Abdul YunusBasha, Dr. K. UlagaPriya (VISTAS, Chennai, India).
*"Quantum Resistant cryptography based deepfake detection models using Hybrid
Watermarking."* IEEE ICDSBS 2026. DOI: 10.1109/ICDSBS69077.2026.11634826.
**File:** `Quantum_Resistant_cryptography_based_deepfakedetection_models_using_Hybrid_Watermarking.pdf`

**What it is:** A 4-page conference paper proposing a four-part framework: (1) per-frame
DWT-DCT watermark embedding (HL sub-band, DCT-modulated), (2) PQC protection (Dilithium
signatures, Kyber KEM, SHA-3 hashing; NTRU/XMSS named as unimplemented "placeholders";
Microsoft SEAL/liboqs mentioned but never actually used), (3) a blockchain/PQ-BFT
provenance ledger, and (4) a deep-learning deepfake detector (CNN / EfficientNet-B0 / GRU
compared independently) fused with the watermark-validity score.

**Key results:**
- Detection accuracy (FaceForensics++-style dataset, 400 videos): CNN 88.7%, EfficientNet
  **98.4%**, GRU 97.0%. Latency: CNN 4.3 ms, EfficientNet 18.5 ms, GRU 135.4 ms.
- "Quantum Resilience Score" reported as 1/1 for all models — but this is a **binary
  pass/fail sanity check** (did any PQC primitive verify at all), not a security metric
  (no parameter sets, no attack-cost analysis).
- **No watermark robustness/imperceptibility numbers are reported anywhere** (no
  PSNR/SSIM/bit-accuracy), despite being named as intended metrics — a major gap given
  watermarking is the paper's headline contribution.
- The blockchain/PQC layer is explicitly self-described in-text as a **"mock," "toy,"
  and "simulation"** (a literal hardcoded Python dict stands in for a PQ-BFT consensus
  result) — not a real deployment, with no throughput/gas/adversarial-fault analysis.
- The watermark+classifier fusion formula (Eq. 11) is specified but **never evaluated**
  in the Results section — the DL detectors are benchmarked as standalone classifiers
  only.

**Rigor gap vs. siblings:** both `fphy-13-1750515.pdf` (diffusion-model watermarking) and
`pqc watermark medical imagery.pdf` (medical DCT watermarking) report detailed
PSNR/SSIM/bit-accuracy/robustness tables under named attacks and baseline comparisons.
This paper reports none of that for its watermark — its best-evidenced contribution is
actually the standard (non-watermark-related) CNN/EfficientNet/GRU classification
benchmark.

---

## Cross-Paper Synthesis

**A. The side-channel paper (#2) undercuts the deployment assumptions of #1 and the
existing MBOM-PQC paper.** Paper 1's automotive feasibility numbers (e.g., "Dilithium2
sign takes 113 ms and fits on a Classic AUTOSAR MCU") are a **lower bound** on real-world
cost — Paper 2 shows that on comparably constrained hardware (Cortex-M4-class), an
unprotected ML-DSA implementation is fully key-recoverable in as few as 2–4 (reference) or
8–14 (ASM-optimized) EM traces, or under 30 traces using discarded signatures alone.
Side-channel countermeasures aren't optional for automotive ML-DSA deployment, and Paper
2's own finding that **existing masking schemes don't cover the challenge `c`** means the
gap isn't fully closed even by current best practice. The same logic applies to the
existing `systems-14-00593.pdf` (MBOM-PQC attestation) and `pqc watermark 4.pdf`
(FL+PQC signatures) — both rely on ML-DSA/Dilithium signing integrity without addressing
implementation-level leakage.

**B. Paper 3 is the third member of a PQC-secured-watermarking cluster, but the weakest
on evidence.** Alongside `fphy-13-1750515.pdf` (diffusion-model output provenance, robust
watermarking, rigorous Kyber-KEM security experiment) and `pqc watermark medical
imagery.pdf` (fragile tamper-localization watermarking, honest about using only
SHA3+AES rather than real PQC primitives), Paper 3 claims the most ambitious stack
(real lattice signatures + KEM + blockchain) but is the only one with **zero quantitative
watermark-robustness results** and an explicitly mocked blockchain/PQC layer. Its
genuine novel contributions — applying the pattern to deepfake video (via GRU temporal
modeling) and adding a blockchain provenance ledger — are also its least-validated parts.
Useful as a related-work/comparison point, but not a rigor benchmark the way the other two
are.

**C. Threat-model contrast worth noting for any comparison table:** Paper 3 targets
*authenticity-of-real-content* (is this video genuine, robust watermark must survive
compression/re-encoding) — nearly the inverse of the medical-imaging paper's
*tamper-evidence* goal (fragile watermark must break under any modification) and distinct
from the diffusion paper's *authenticity-of-generated-content* (proving AI-generated
output's origin). All three use DCT-family or KEM-based PQC building blocks, but the
security objectives are not directly interchangeable.

**D. Bandwidth/storage cost data point:** Paper 1's Table I gives concrete ML-DSA
key/signature sizes (Dilithium2: 1312 B pubkey / 2420 B signature; up to 2592 B / 4627 B
for Dilithium5) — relevant budget numbers for any provenance scheme in this collection
(e.g., Paper 3's or the MBOM-PQC paper's) that embeds ML-DSA signatures in a
provenance payload, especially in bandwidth-constrained contexts like CAN/CAN-FD (8–64
byte frames).

---

## BibTeX

```bibtex
@inproceedings{stalin2025automotive,
  author  = {Stalin, Joseph and Rai, Ritvik Kumar},
  title   = {A Study of Post-Quantum Digital Signatures in the Automotive Industry},
  booktitle = {IEEE ICVTTS 2025},
  year    = {2025},
  doi     = {10.1109/ICVTTS67119.2025.11296596}
}

@article{liu_mldsa_sidechannel,
  author  = {Liu, Zheng and Wang, An and Wei, Congming and Ding, Yaoling and
             Zhang, Jingqi and Liu, Annyu and Zhu, Liehuang},
  title   = {Release the Power of Rejected Signatures: An Efficient Side-Channel
             Attack on the ML-DSA Cryptosystem},
  institution = {Beijing Institute of Technology / Communication University of China}
}

@inproceedings{yunusbasha2026deepfake,
  author  = {YunusBasha, Shaik Abdul and UlagaPriya, K.},
  title   = {Quantum Resistant cryptography based deepfake detection models using
             Hybrid Watermarking},
  booktitle = {IEEE ICDSBS 2026},
  year    = {2026},
  doi     = {10.1109/ICDSBS69077.2026.11634826}
}
```

---

*New reviewer agents added for these papers: `automotive-pqc-signatures-reviewer`,
`ml-dsa-side-channel-reviewer`, `deepfake-hybrid-watermark-reviewer`
(`.claude/agents/`). `SKILL.md` updated with entries 7–9 and a cross-collection note.*
