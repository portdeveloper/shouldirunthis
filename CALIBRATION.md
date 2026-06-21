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
