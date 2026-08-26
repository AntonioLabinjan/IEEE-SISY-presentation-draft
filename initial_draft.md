Here's the 15-slide plan for your 15-minute talk, mapped directly to your paper's sections.

## Slide 1 — Title Slide
**Visual Layout:** Centered title block, authors + affiliation below, university logo bottom corner, subtle biometric-themed background (fingerprint/waveform motif, low opacity).
**Content:**
- Real-Time Training-Free Multimodal Identity Verification using Biometric Temporal Score Fusion
- Antonio Labinjan, Nikola Tanković
- Faculty of Informatics, Juraj Dobrila University of Pula
**Speaker Notes:** Greet audience, one-sentence hook: "Single biometric systems fail in the real world — we show how to fuse face and voice without retraining anything."
**Formulas/Tables:** None.

## Slide 2 — The Problem: Unimodal Biometrics Fail
**Visual Layout:** Two-column comparison — left icon "Face" with bullet issues, right icon "Voice" with bullet issues; red warning icons.
**Content:**
- Face recognition: sensitive to illumination, pose, blur
- Voice verification: degrades under noise, reverberation
- Result: elevated false rejection/acceptance rates
**Speaker Notes:** Ground the motivation in everyday deployment conditions — cameras and mics are never ideal in the wild.
**Formulas/Tables:** None.

## Slide 3 — The Gap in Existing Multimodal Fusion
**Visual Layout:** Simple flow diagram: "Paired dataset → Joint training → Fused model" with a red X over it; contrast with a green checkmark path you propose later.
**Content:**
- Deep multimodal fusion needs synchronized, paired datasets
- Joint training = computational overhead + overfitting risk
- Prevents independent model upgrades
**Speaker Notes:** Emphasize this is the core limitation you're solving — training-free, no paired data required.
**Formulas/Tables:** None.

## Slide 4 — Our Contributions
**Visual Layout:** Vertical checklist with 5 icons (one per contribution), clean numbered list.
**Content:**
- Real-time multimodal framework (face + voice)
- Unified Qdrant retrieval backend for heterogeneous embeddings
- Training-free fusion, no joint optimization
- Temporal decision layer (agreement / missing / conflict)
- Offline evaluation: security trade-offs quantified
**Speaker Notes:** Preview the talk structure — architecture, then each pipeline, then fusion logic, then results.
**Formulas/Tables:** None.

## Slide 5 — System Architecture Overview
**Visual Layout:** Full-slide reproduction of Fig. 1 (architecture diagram), minimal text, arrows labeled with data flow stages.
**Content:**
- Async sensor streams → unimodal embeddings → Qdrant lookup → decision fusion
**Speaker Notes:** Walk through the pipeline left to right: this is the mental map for the rest of the talk.
**Formulas/Tables:** Fig. 1 (architecture diagram).

## Slide 6 — Face Recognition Pipeline
**Visual Layout:** Left: pipeline steps as stacked boxes (Image → CLIP ViT-B/32 → 512-D embedding → Qdrant). Right: highlighted number "512-D".
**Content:**
- CLIP (openai/clip-vit-base-patch32) vision backbone
- 224×224 normalized face frames
- 512-dimensional embeddings, robust to pose/illumination/blur
- Migrated from FAISS → Qdrant for unified backend
**Speaker Notes:** Note this builds on your prior zero-shot work (Labinjan et al.); the key change here is the backend, not the encoder.
**Formulas/Tables:** None (optional: cite embedding dimension as callout number).

## Slide 7 — Voice Verification Pipeline
**Visual Layout:** Mirrored layout to Slide 6 for visual parallelism. Boxes: Audio chunk → log-Mel spectrogram → 5 conv blocks (32→256 channels) → 128-D embedding.
**Content:**
- Custom VoiceNetEmbedding CNN, trained on VoxCeleb
- 5 conv blocks: Conv2D + BatchNorm + ReLU + MaxPool
- Softmax head used for training only, then truncated
- Output: 128-dimensional open-set speaker embedding
**Speaker Notes:** Stress the "truncation trick" — train closed-set, discard classifier head, reuse backbone as zero-shot extractor.
**Formulas/Tables:** None.

## Slide 8 — Why Unify on Qdrant?
**Visual Layout:** Split comparison box: "Two FAISS indexes" (cluttered icon set: 2 DBs, 2 metadata stores, sync arrows) vs. "One Qdrant backend" (single clean box).
**Content:**
- Face (512-D) and voice (128-D) embeddings stay in separate spaces
- Qdrant unifies identity metadata, filtering, retrieval — not the vectors themselves
- Avoids duplicated indexes and sync overhead
**Speaker Notes:** Clarify the common misconception up front: this is not a shared latent space, just shared infrastructure.
**Formulas/Tables:** None.

## Slide 9 — Score Alignment & Normalization
**Visual Layout:** Two equation boxes side by side, each with a small icon (camera / mic) above it.
**Content:**
- Asynchronous streams → grouped by ground-truth identity
- Peak similarity score taken per modality per evaluation window
**Speaker Notes:** Explain why strict frame-level alignment is skipped in favor of peak-confidence aggregation.
**Formulas/Tables:** Eq. (1) $S_V = \max(\{s_{v1},...,s_{vn}\})$, Eq. (2) $S_A = \max(\{s_{a1},...,s_{am}\})$

## Slide 10 — Temporal Fusion Logic (Decision States)
**Visual Layout:** State diagram / flowchart with 4 colored boxes: CONFIRMED (green), VISION_ONLY / AUDIO_ONLY (yellow), CONFLICT (red). Harmonic mean formula highlighted under CONFIRMED box.
**Content:**
- CONFIRMED: both modalities agree → harmonic mean score
- VISION_ONLY / AUDIO_ONLY: fallback when one stream missing
- CONFLICT: mismatched identities → suppressed, rejected
**Speaker Notes:** This is the core novelty slide — spend extra time here. Emphasize CONFLICT as the safety mechanism.
**Formulas/Tables:** Eq. (3) $S_{fused} = \dfrac{2 \cdot S_V \cdot S_A}{S_V + S_A + \epsilon}$

## Slide 11 — Dataset & Evaluation Protocol
**Visual Layout:** Folder-tree diagram: known_data/ → identity/ → train/(audio, vision) + val/(audio, vision).
**Content:**
- Identity-centric directory structure
- train/ = enrollment gallery, val/ = probe stream
- Outcomes: correct identification / misidentification / rejection
**Speaker Notes:** Quick, don't dwell — just enough to justify how results were measured.
**Formulas/Tables:** None (optionally show Eq. 4–6 metric definitions briefly).

## Slide 12 — Evaluation Metrics
**Visual Layout:** Three equal boxes, each with one formula and a short label (Accuracy / Misidentification Rate / Rejection Rate).
**Content:**
- Accuracy = correct / total
- Misidentification Rate = unsafe wrong-accept / total
- Rejection Rate = refused decisions / total
**Speaker Notes:** Define these precisely — the results slide depends on the audience understanding "misidentification" as the dangerous failure mode.
**Formulas/Tables:** Eq. (4) Accuracy, Eq. (5) Misidentification Rate, Eq. (6) Rejection Rate.

## Slide 13 — Results: Unimodal vs. Fused
**Visual Layout:** Reproduce Table I as a clean 3-row table; big callout numbers beside it: "9.00% → 0.00%" misidentification, in red-to-green arrow.
**Content:**
- Face-only: 92% acc / 0% misid / 8% reject
- Voice-only: 83% acc / 9% misid / 7% reject
- Fused: 75% acc / 0% misid / 25% reject
**Speaker Notes:** Walk through trade-off explicitly: accuracy drops, but misidentification hits zero — frame as intentional, security-first design.
**Formulas/Tables:** Table I (Unimodal vs. Fused Results).

## Slide 14 — Why It Works: Complementary Error Patterns
**Visual Layout:** Side-by-side confusion matrices (Fig. 3) with annotation arrows: "sparse errors" (face) vs. "scattered errors" (voice); optionally small PCA plots (Fig. 2) as inset.
**Content:**
- Face errors: mostly rejections, few cross-identity mistakes
- Voice errors: scattered across multiple identities
- Non-overlapping failure modes → fusion catches contradictions
**Speaker Notes:** This is the "why" behind the numbers on the previous slide — visual and acoustic mistakes rarely align, so CONFLICT catches them.
**Formulas/Tables:** Fig. 2 (PCA plots) and/or Fig. 3 (confusion matrices).

## Slide 15 — Conclusion & Future Work
**Visual Layout:** Two-column summary: "What we did" (left, checkmarks) / "What's next" (right, arrow icons).
**Content:**
- Training-free, no paired datasets, no fusion network
- Misidentification reduced 9.00% → 0.00%
- Future: edge deployment, latency/resource profiling, live cross-modal sync
- Thank you + contact/questions
**Speaker Notes:** Close by reiterating the core trade-off framing (security over raw accuracy) and invite questions.
**Formulas/Tables:** None.

---

**Timing tip:** ~1 min/slide fits 15 min with buffer; slides 10 and 13 (the fusion logic and results) deserve ~1.5–2 min each — trim slide 11 (dataset structure) to 30 seconds if you're running long.

Want me to turn this into an actual .pptx file next?
