<p align="center">
  <a href="https://dastanzar.github.io/"><img src="https://dastanzar.github.io/og.png" alt="Abhinav Chauhan — I make inference cheaper. And I show the receipts." /></a>
</p>

<p align="center">
  <strong>MLSys / inference systems.</strong> Quantization, KV memory, scheduling, kernels, speculative decoding.
  <br/>
  Every number ships with a committed receipt — not a screenshot.
</p>

<p align="center">
  <a href="https://dastanzar.github.io/">Portfolio</a> ·
  <a href="https://dastanzar.github.io/#receipts">Receipt console</a> ·
  <a href="https://dastanzar.github.io/app/sdecode/session.html">sdecode session app</a>
</p>

---

## Twenty systems, five layers

Twenty public repos, grouped by the part of the inference loop they touch. Every result below is read from a committed receipt in that repo — including the ones that went the wrong way.

### Decode loop

| # | System | Receipt-backed result |
|---:|---|---|
| 01 | [**sdecode**](https://github.com/DastanZar/sdecode) · [receipt](https://github.com/DastanZar/sdecode/blob/main/receipts/real_run.json) | **2.52× tokens per target call**, token-identical to naive. Fixed K=8 on GPT-2; the adaptive-K negative result stays in the receipt. |
| 02 | [**treetrace**](https://github.com/DastanZar/treetrace) · [receipt](https://github.com/DastanZar/treetrace/blob/main/receipts/receipt.json) | **5.28×** packed node pass, but end-to-end only **1.06×**: nearby drafts accept 3 of 4 tokens; independent drafts regress. |
| 03 | [**pushdown**](https://github.com/DastanZar/pushdown) · [receipt](https://github.com/DastanZar/pushdown/blob/main/receipts/receipt.json) | **25/103 legal tokens** at the JSON value state; a 1,024-deep synthetic stack parses in 51 ms — not a production DFA. |
| 04 | [**streamdetok**](https://github.com/DastanZar/streamdetok) · [receipt](https://github.com/DastanZar/streamdetok/blob/main/receipts/receipt.json) | **60.1× faster** with **0 replacements**: 128-byte chunks over a 2,880-byte payload, versus 18 naive splits. |

### Serving control

| # | System | Receipt-backed result |
|---:|---|---|
| 05 | [**cplan**](https://github.com/DastanZar/cplan) · [receipt](https://github.com/DastanZar/cplan/blob/main/receipts/receipt.json) | **94% lower TTFT** at 1.5 req/s: 34.7 ms continuous vs 565.5 ms static, p99 in the same receipt. |
| 06 | [**chunkpipe**](https://github.com/DastanZar/chunkpipe) · [receipt](https://github.com/DastanZar/chunkpipe/blob/main/receipts/receipt.json) | **71.7% lower p95 TTFT** at 1 burst/s: 21.7 s → 6.1 s once 128→8192-token chunks replace one giant job (46.7% at 5/s). |
| 07 | [**gatekeeper**](https://github.com/DastanZar/gatekeeper) · [receipt](https://github.com/DastanZar/gatekeeper/blob/main/receipts/receipt.json) | **71.8% lower p95 response**: token bucket and GCRA hold p95 at 4.07 s versus 14.46 s fixed window; 90 vs 88 admitted. |
| 08 | [**radixlite**](https://github.com/DastanZar/radixlite) · [receipt](https://github.com/DastanZar/radixlite/blob/main/receipts/receipt.json) | **92.3% prefill saved** at a 128-slot budget via cache-aware routing; round-robin saves 73.4%. |

### Memory & attention

| # | System | Receipt-backed result |
|---:|---|---|
| 09 | [**pagemem**](https://github.com/DastanZar/pagemem) · [receipt](https://github.com/DastanZar/pagemem/blob/main/receipts/receipt.json) | **204 blocks vs 504** for a prefix-shared KV pool — **59.5% fewer blocks**, prefix hit rate 1.0. |
| 10 | [**kvstamp**](https://github.com/DastanZar/kvstamp) · [receipt](https://github.com/DastanZar/kvstamp/blob/main/receipts/receipt.json) | **8.0× KV compression** at 1.14 relative error; token-importance eviction swept to the error knee. |
| 11 | [**expertcache**](https://github.com/DastanZar/expertcache) · [receipt](https://github.com/DastanZar/expertcache/blob/main/receipts/receipt.json) | **14.2% fewer misses**, 10.7% lower median CPU on the hot scan; phase-shift regresses, and both cases stay in the receipt. |
| 12 | [**pagedattn**](https://github.com/DastanZar/pagedattn) · [receipt](https://github.com/DastanZar/pagedattn/blob/main/receipts/receipt.json) | **9/9 parity cases**, max abs error 3.0e-7: streaming paged attention vs dense gather. Parity, not a speed win; no CUDA claim. |

### Numerics & compression

| # | System | Receipt-backed result |
|---:|---|---|
| 13 | [**softfp8**](https://github.com/DastanZar/softfp8) · [receipt](https://github.com/DastanZar/softfp8/blob/main/receipts/receipt.json) | **2.0× fewer weight bytes** with **4.87%** relative output error; software FP8 GEMV for non-FP8 GPUs. |
| 14 | [**latq**](https://github.com/DastanZar/latq) · [receipt](https://github.com/DastanZar/latq/blob/main/receipts/receipt.json) | **15.8× compression** at 0.607 relative error; D8/D4/Z2 lattice ladder down to 1 bit per weight. |
| 15 | [**bithash**](https://github.com/DastanZar/bithash) · [receipt](https://github.com/DastanZar/bithash/blob/main/receipts/receipt.json) | **3.98× compression** at 4.02 effective bits per weight; uniform16 beats odd16 on dense rows. |
| 16 | [**bitforge**](https://github.com/DastanZar/bitforge) · [receipt](https://github.com/DastanZar/bitforge/blob/main/receipts/receipt.json) | **100% decidable argmax**, 0.00239 max probability deviation; near ties are reported separately. |

### Kernels & topology

| # | System | Receipt-backed result |
|---:|---|---|
| 17 | [**fusednorm**](https://github.com/DastanZar/fusednorm) · [receipt](https://github.com/DastanZar/fusednorm/blob/main/receipts/receipt.json) | **33% less norm-site traffic**; **1 MiB saved** per LLaMA-3 8B decode token. RMSNorm, residual, and SwiGLU epilogue in one pass. |
| 18 | [**gembench**](https://github.com/DastanZar/gembench) · [receipt](https://github.com/DastanZar/gembench/blob/main/receipts/receipt.json) | **1.8× GEMV throughput** on CPU: 119.24 ms naive rows → 64.72 ms `torch.mv`. |
| 19 | [**roofline-planner**](https://github.com/DastanZar/roofline-planner) · [receipt](https://github.com/DastanZar/roofline-planner/blob/main/receipts/receipt.json) | **34 deployment plans** across 5 models × 9 GPUs: fit check, roofline regime, decode throughput. |
| 20 | [**ring**](https://github.com/DastanZar/ring) · [receipt](https://github.com/DastanZar/ring/blob/main/receipts/receipt.json) | **2.01 GB/s busbw** at 4 ranks, falling to 0.99 GB/s at 8: loopback TCP, formula-normalized — not NCCL. |

---

## Method

- **A real measurement first.** CPU timings, Triton interpreter parity, real HF weights. GPU figures are labelled models, never borrowed numbers.
- **A harness you can run.** CLI, test, or live app — then a receipt generated by executing it. The featured sdecode session app is live on the site.
- **Tests defend the claim.** Assertions live next to the number; CI fails when a receipt drifts.

The portfolio page is generated by [`src/build.py`](https://github.com/DastanZar/DastanZar.github.io/blob/main/src/build.py) from the committed receipts. Change a number in a repo, rebuild, and the page changes with it.

## Start here

- **[dastanzar.github.io](https://dastanzar.github.io/)** — the three featured case files and the full shipping index.
- **[Receipt console](https://dastanzar.github.io/#receipts)** — the actual JSON behind every headline number.
- **[sdecode session app](https://dastanzar.github.io/app/sdecode/session.html)** — playback of the measured real run, token-for-token.

Open to MLSys / inference engineering roles. Bring me the p99.

<sub>© 2026 Abhinav Chauhan · Built from receipts, not screenshots.</sub>
