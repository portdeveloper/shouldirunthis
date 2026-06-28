# shouldirunthis data calibration

Status: calibrated (research agent) + independently validated (Sonnet agent) + adjudicated + Codex-reviewed. APPLIED to prototype.html / index.html but NOT yet deployed to the public link. Date: June 2026.

## Final applied MODELS array

```js
const MODELS = [
  {id:'qwen3.6-flash',    name:'Qwen3.6-Flash',        tier:1, rig:'1× RTX 4090',     tps:160, capex:2600,  watts:450,  api:0.21, params:'35B / 3B active',    coding:0.65},
  {id:'qwen3.6-27b',      name:'Qwen3.6-27B',          tier:2, rig:'1× RTX 5090',     tps:60,  capex:4500,  watts:575,  api:0.52, params:'27B dense',          coding:0.72},
  {id:'gemma-4-31b',      name:'Gemma 4 31B',          tier:1, rig:'1× RTX 5090',     tps:63,  capex:4500,  watts:575,  api:0.15, params:'31B dense',          coding:0.60},
  {id:'deepseek-v4-flash',name:'DeepSeek-V4-Flash',    tier:2, rig:'used M3 Ultra 256GB',   tps:30,  capex:6000,  watts:230,  api:0.12, params:'284B / 13B active',  coding:0.69},
  {id:'minimax-m2.5',     name:'MiniMax-M2.5',         tier:2, rig:'used M3 Ultra 256GB',   tps:38,  capex:6000,  watts:230,  api:0.21, params:'230B / 10B active',  coding:0.71},
  {id:'minimax-m3',       name:'MiniMax-M3',           tier:3, rig:'used M3 Ultra 512GB',   tps:26,  capex:9500,  watts:250,  api:0.37, params:'428B / 23B active',  coding:0.68},
  {id:'glm-5.2',          name:'GLM-5.2',              tier:3, rig:'used M3 Ultra 512GB',   tps:17,  capex:9500,  watts:250,  api:1.42, params:'744B / 40B active',  coding:0.80},
  {id:'kimi-k2.7',        name:'Kimi K2.7-Code',       tier:3, rig:'2× used M3 Ultra 512GB',tps:15,  capex:19000, watts:500,  api:0.80, params:'1T / 32B active',    coding:0.74},
  {id:'deepseek-v4-pro',  name:'DeepSeek-V4-Pro',      tier:3, rig:'8× RTX 3090',     tps:8,   capex:13000, watts:2400, api:0.47, params:'1.6T / 49B active',  coding:0.70},
  {id:'nemotron-3-550b',  name:'Nemotron-3-Ultra 550B',tier:1, rig:'8× RTX 3090',     tps:10,  capex:13000, watts:2400, api:0.63, params:'550B / 55B active',  coding:0.71},
];
const CONST = { FRONTIER:5, ELEC:0.18, AMORT:36, IORATIO:12 };
```
Sub caps unchanged (confirmed vs SemiAnalysis, ~June 2026): ChatGPT Plus $20=700, Claude Pro $20=400, ChatGPT 5x $100=3500, Claude Max 5x $100=2000, ChatGPT 20x $200=14000, Claude Max 20x $200=8000. Throttle {0.4, 0.7, 0.9, 1.6} confirmed (optional: heaviest 1.6 to 2.0).

## Laptop-runnable additions (2026-06-25)

Added a "runs on a laptop" block at the top of MODELS, sourced from the LiveBench open-weight set (sort=Agentic Coding, May 12 2026 snapshot) and grounded for fit/throughput against whatcani.run measured runs. Goal: the device picker previously showed **zero** models for a 16GB MacBook Air (smallest entry needed 18GB); now it shows four. All rigs are M4 Macs, so capex (and thus monthly cost) is much lower than the GPU builds.

```js
{id:'qwen3.6-8b',      name:'Qwen3.6-8B',          tier:1, mem:7,  rig:'MacBook Air · M4 · 16GB',     tps:30, capex:1100, watts:30, api:0.06, params:'8B dense',           coding:0.52},
{id:'gemma-4-12b',     name:'Gemma 4 12B',         tier:1, mem:9,  rig:'MacBook Air · M4 · 16GB',     tps:22, capex:1100, watts:30, api:0.08, params:'12B dense',          coding:0.48},
{id:'gpt-oss-20b',     name:'gpt-oss-20B',         tier:2, mem:13, rig:'MacBook Air · M4 · 16GB',     tps:45, capex:1100, watts:32, api:0.07, params:'21B / 3.6B active',  coding:0.56},
{id:'mistral-small-4', name:'Mistral Small 4',     tier:1, mem:16, rig:'MacBook Pro · M4 · 32GB',     tps:13, capex:1600, watts:38, api:0.12, params:'24B dense',          coding:0.57},
{id:'glm-4.7-flash',   name:'GLM-4.7-Flash',       tier:2, mem:19, rig:'MacBook Pro · M4 · 32GB',     tps:46, capex:1600, watts:40, api:0.11, params:'30B / 3B active',    coding:0.66},
{id:'nemotron-3-nano', name:'Nemotron-3-Nano 30B', tier:2, mem:19, rig:'MacBook Pro · M4 Pro · 48GB', tps:18, capex:2500, watts:45, api:0.14, params:'30B dense',          coding:0.63},
{id:'gpt-oss-120b',    name:'gpt-oss-120B',        tier:3, mem:60, rig:'MacBook Pro · M4 Max · 64GB', tps:40, capex:3900, watts:60, api:0.20, params:'117B / 5.1B active', coding:0.67},
```

Grounding / confidence:
- `mem`: gpt-oss-20B and the small Qwen sizes anchored to whatcani.run measured peak memory (gpt-oss-20B Q8 ~9.9GB, so MXFP4 fits 16GB comfortably; Qwen 9B 4bit ~8GB). Dense 24-30B at Q4 ~15-19GB. gpt-oss-120B MXFP4 ~60GB (OpenAI card: "runs on a single 80GB GPU"; fits a 64GB Mac, tight). MED-HIGH.
- `coding` (LiveBench Coding Avg /100): MED confidence estimates pending exact per-model LiveBench rows; ordering is right (GLM-Flash > gpt-oss-120B ≈ Nemotron > gpt-oss-20B > Mistral > Qwen-8B > Gemma-12B), absolute values approximate. Editable consideration: these are not yet exposed as editable cells; revise when exact LiveBench rows are read.
- `tier` (agentic-loop capability, separate from raw coding): gpt-oss + GLM-Flash + Nemotron carry native tool-use / strong agentic behaviour (tier 2-3); Gemma/Qwen-8B/Mistral-Small are autocomplete-to-light (tier 1). LOW-MED.
- `tps`: on the *named laptop* at Q4, not on a GPU. MoE (gpt-oss, GLM-Flash) fast even on base-M4 bandwidth; dense 24-30B slow on base M4 (hence M4 Pro 48GB for Nemotron). whatcani.run M4 Max numbers are higher (more bandwidth) and were de-rated for the cheaper rigs. LOW-MED; power is negligible at laptop watts so this barely moves verdicts.
- `capex`: mid-2026 street: MBA M4 16GB ~$1100, MBP M4 32GB ~$1600, MBP M4 Pro 48GB ~$2500, MBP M4 Max 64GB ~$3900. Dominates monthly cost (capex/36); laptop rows land ~$31-109/mo.

### Bottom rung (2026-06-25, follow-up)

Extended the cheapest end so the "can the cheapest possible box do anything?" question has an answer. Added one 4B-class anchor plus a new sub-16GB device tier (they only make sense together; a 4B model with no <16GB device in the picker is just a weaker duplicate of the 8B):

- Model: `{id:'qwen3.6-4b', name:'Qwen3.6-4B', tier:1, mem:5, rig:'Mac mini · M2 · 8GB (used)', tps:30, capex:450, watts:22, api:0.04, params:'4B dense', coding:0.40}`. mem from whatcani.run (Qwen3.5-4B 4bit ~4.9GB). tier 1 / autocomplete-only is the honest verdict at 4B. capex = used M2 mini 8GB (~$450 mid-2026).
- Device: `Mac mini · M2 · 8GB (used)` (mem 8) inserted at DEVICES index 1 (after "any device"). NOTE: this shifts every later device index by +1, so pre-existing shared `?d=` URLs point one device off; acceptable this early post-launch. DEVICES / I18N.en.devices / I18N.tr.devices must stay the same length and order (labels come from I18N, mem from DEVICES, indexed together).
- On the 8GB mini only the 4B (yes) and 8B (yes, optimistic at small context) fit; everything 12B+ reads "no". Deliberately did NOT add 14B/27B/32B mid-variants: the 8B and 27B rows already bracket them and they change no verdict.

### Mid-tier gap-fillers (2026-06-28, follow-up)

Reconciled the GPU/Mac-Studio block against a fuller LiveBench open-weight leaderboard. Confirmed the leaderboard's 3rd column is Coding Avg, matching every existing `coding` value exactly (GLM-5.2 79.65=0.80, Qwen3.6-27B 71.78=0.72, etc.). Of 9 leaderboard models not yet present, added only the two that fill a real size/cost gap between DeepSeek-V4-Flash (284B) and GLM-5.2 (744B); skipped the pure version-duplicates (GLM 5 / 5.1, Kimi K2 / K2.5 / K2.6) that share hardware with a row already shown and would only differ by score.

- `{id:'glm-4.7', name:'GLM-4.7', tier:2, mem:200, rig:'used M3 Ultra 256GB', tps:21, capex:6000, watts:230, api:0.55, params:'357B / 32B active', coding:0.73}`. Full GLM-4.7 (not the laptop Flash). coding from leaderboard (73.13). Hardware by analogy to the 256GB-Mac MoEs (deepseek-v4-flash / minimax-m2.5): 357B/32B Q4 GGUF ~200GB fits a 256GB Mac with headroom; tps scaled from glm-5.2 (40B active to 17) for 32B active. tier 2: Agentic-Coding 41.67 is borderline, kept at light-agentic for consistency with the family's native tool-use and its tier-2 Flash sibling (not 1). api a mid-estimate between Flash (0.11) and 5.2 (1.42); editable. MED-LOW on tps/api.
- `{id:'deepseek-v3.2', name:'DeepSeek-V3.2', tier:1, mem:404, rig:'used M3 Ultra 512GB', tps:18, capex:9500, watts:250, api:0.28, params:'671B / 37B active', coding:0.65}`. Released V3.2 "Thinking" (overall 62.20), not the experimental variant. coding from leaderboard (64.62). 671B Q4_K_M ~404GB fits 512GB Mac (same rig as glm-5.2); tps scaled from glm-5.2's 40B-active 17. tier 1: Agentic-Coding 40.00 (= Gemma 31B). api ~0.28 (DeepSeek cheap; between v4-flash 0.12 and v4-pro 0.47). MED on mem/tps.

## Key changes vs the old mock estimates

- ELEC 0.15 to 0.18 (EIA US residential, Mar 2026). FRONTIER/AMORT/IORATIO confirmed.
- API prices re-derived from OpenRouter / cheapest reputable provider, blended 12:1. Most dropped (open APIs are cheap); glm-5.2 1.63 to 1.42; deepseek-v4-flash to 0.12.
- tps from real local-inference benchmarks: small MoE much faster (qwen-flash 160); huge models on CPU/RAM offload much slower (deepseek-v4-pro 8, nemotron 10).
- capex re-priced mid-2026 street: 4090 build 2600, 5090 build 4500, 8x3090 13000, Mac 256GB 6000, Mac 512GB 9500, Kimi cluster 19000.
- watts: Apple rigs far lower (230 to 250); 8x3090 2400.

## Fit-at-Q4 corrections (load-bearing rig changes)

1. deepseek-v4-flash (284B): did not fit 128GB; moved to M3 Ultra 256GB.
2. minimax-m3 (428B): Q4 ~208-265GB, does not safely fit 256GB with KV cache; moved to M3 Ultra 512GB. (Accepted from validation.)
3. kimi-k2.7 (1T): Q4 ~580GB > 512GB; moved to 2x M3 Ultra 512GB cluster ($19k). On one machine it only runs Q2/Q3.
4. deepseek-v4-pro (1.6T): Q4 ~430-800GB, will not fit 192GB VRAM on 8x3090; CPU/RAM offload only at ~8 tps. Borderline impractical.
5. nemotron-3-550b (~275GB): exceeds 192GB VRAM; offload at ~10 tps.

## Validation (independent Sonnet agent) outcome

The validator raised 6 points; the calibrator adjudicated each against primary sources.

ACCEPTED (2):
- minimax-m3 relocated 256GB to 512GB (Q4 GGUF sizes confirm 256GB too tight). capex/watts/tps recomputed.
- deepseek-v4-flash api 0.15 to 0.12 (OpenRouter blends ~0.10, first-party ~0.15; midpoint).

REJECTED / HELD (4):
- glm-5.2 "does not fit single 512GB" REJECTED: Q4_K_M ~476GB fits 512GB (source confirms). Kept on single M3 Ultra 512GB.
- FRONTIER 5 to 6.5-7 HELD at 5: constant is frontier CODING API; coding workhorses (Sonnet 4.6 ~3.92, GPT-5.3-Codex ~2.69) sit below flagships. 5 is a defensible midpoint. Highest-leverage swing; flag for reviewer.
- RTX 5090 tps 60/63 to 90-120 HELD: measured llama.cpp benchmarks (marvin-42, leaderboard) beat the validator's roofline hypothesis.
- Apple discontinuation: validator confirmed the calibrator's flag; 256/512GB rows are buy-used-only mid-2026.

## Codex review outcome / remaining caveats

1. FRONTIER_PRICE_PER_MTOK = 5. Highest-leverage constant. Anchoring to flagships (Opus/GPT-5.5) would push it to ~6.5-7 and shift many verdicts. Codex held 5 as a coding-model blend, changed UI copy from "frontier API prices" to "coding-model API prices," and exposed it as an editable assumption.
2. Apple >96GB Mac Studio discontinuation claim (DRAM shortage, 2026). VERIFIED against current Apple Mac Studio buy/spec pages: new-buy M3 Ultra is presented at 96GB and memory is not configurable. UI now marks 256/512GB M3 Ultra rows as used-market estimates and adds a visible caveat.
3. Offload tps for deepseek-v4-pro (8) and nemotron-3-550b (10): LOW confidence, setup-dependent, order-of-magnitude only.
4. minimax-m2.5 tps 38 and 256GB-Mac MoE throughput: LOW-MED, no clean Q4-MLX primary benchmark; scaled from active-param bandwidth.
5. minimax-m3 api 0.37 is a promo; standard list blends ~0.74. Consider 0.74 for headroom. UI now exposes per-model API prices as editable cells.
6. GPU street prices (MED): volatile (4090 $1.1k-2.75k, 5090 $2k-4.3k across sources); build totals are mid-range, not precise. UI keeps capex editable.
7. IORATIO 12: correct for a cost-weighted (cache-aware) conversion; raw token-volume would be ~30-40. UI now exposes IORATIO as an editable assumption.
