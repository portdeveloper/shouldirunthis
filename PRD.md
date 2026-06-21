# shouldirunthis — PRD

**One-liner:** A calculator for vibecoders that answers one question honestly — *should you buy a local AI rig, or just keep paying for Claude/GPT?* (Spoiler: almost always keep the subscription, and the tool shows you exactly why.)

**Status:** v1 spec — locked · **Owner:** port@monad.foundation · **Last updated:** 2026-06-21

---

## 1. Why this exists

Viral posts keep doing napkin math on running models like GLM 5.2 locally — "$20K of hardware, ~20 tok/s, 5.5 years to break even." The takes are seductive but compare against the **wrong baseline**.

Two truths the discourse keeps missing:

1. **The real competition isn't API pricing — it's a subscription.** A $200/mo Codex plan delivers up to ~$14,000/mo of API-equivalent usage; Claude Max 20x ($200) ≈ $8,000/mo; even a $20 ChatGPT Plus plan ≈ $700/mo ([SemiAnalysis](https://x.com/SemiAnalysis_)). Your rig doesn't have to beat the API meter — it has to beat a flat $20–200/mo.
2. **The catch-22.** The only local models good enough to actually drive an agentic coding loop need $8–20K rigs that lose 3–6× to a $100 subscription. The rigs that are *affordable* run models that can't really vibecode. There is no cheap local setup that replaces Claude Max / Codex for real coding.

> "GLM 5.2 is great, but my $200/mo Codex sub gets me more usage than $200 of GLM API. A great supplemental model — not a replacement." — @mweinbach

`shouldirunthis` makes this concrete and personal: tell it what you pay today and whether you hit the limit, and it tells you — in dollars — whether owning hardware is worth it. The honest answer is usually "no," and the *one* regime where it flips ("you've outgrown subscriptions") becomes visible.

## 2. Decisions (locked)

| Area | Decision |
|---|---|
| **Audience** | Vibecoders — people who code with AI. Everything is coding-specific. |
| **Input metric** | **Current plan + how often you hit the limit.** It's the one thing users actually know, and it maps straight onto subscription caps. Power users get an "Advanced: I know my $/mo API spend" override. |
| **Comparison** | Against subscriptions, not API. Four columns: **GPT $20, Claude $20, GPT $100, Claude $100**, cap-aware. Verdict auto-compares against the *cheapest sub that actually covers your usage*. |
| **Verdict** | **5 tiers**, blending *cost* and *coding ability*: 🟢 buy the rig · 🔵 toss-up · 🟡 only if you must · 🟠 overpriced · 🔴 just subscribe. |
| **Model quality** | A coding-capability tier per model: ◆◆◆ agentic-ready · ◆◆ light agentic · ◆ autocomplete-only. The verdict respects it (cheap-but-can't-code never reads as "worth it"). |
| **Data** | Curated estimates from public benchmarks — sourced, dated, and **every cell editable**. Web-only, no CLI in v1. |
| **Landing** | Big **personalized verdict** up top, full **colored, editable table** below. Default scenario pre-selected so it's never blank. |
| **Share** | OG "share this verdict" card (Vercel OG) — the growth loop. |
| **CTA** | After the verdict, **recommend + link the cheapest covering plan** ("Get Claude Max 5x →"). Affiliate-monetizable later. |
| **Stack** | Next.js (App Router) + TypeScript + Tailwind + shadcn/ui on Vercel. All compute client-side (pure functions); data in typed JSON; state in the URL. |

## 3. The core experience

Reference layout (ASCII — the agreed v1 UI):

```
 shouldirunthis     Models   Rigs   Subs   Docs   GitHub↗                [ Login ]
 ═══════════════════════════════════════════════════════════════════════════════════

  you vibecode. should you buy a rig            ┌─ what you'd actually pay ──────────┐
  instead of paying for it?                     │  Your usage ...  ≈ $1,800/mo value  │
                                                │  Cheapest sub that covers you:      │
  Today I pay  ▛Claude Max · $100/mo▟ ▾         │     Claude Max 5x — $100/mo  ✓      │
  and I  ▛hit the cap most weeks▟ ▾             │  A rig that can match it (GLM 5.2): │
                                                │     ~$628/mo all-in  →  6× the sub   │
  → you burn ≈ $1,800/mo of API value.          │  ─────────────────────────────────  │
                                                │  Verdict: 🔴 keep the sub           │
  [ Get Claude Max 5x → ]                        └─────────────────────────────────────┘

  ┌ 💡 your plan has a cap — a rig only wins once you blow past it ─────────────────┐
  │  $20 →$400–700/mo · $100 →$2,000–3,500/mo · $200 →$8,000–14,000/mo [SemiAnalysis]│
  │  You're on $100 and maxing it → still inside the cap. Only past ~$8–14k/mo of   │
  │  usage does no single sub cover you — that's the only door a rig walks through. │
  └────────────────────────────────────────────────────────────────────────────────┘

  Models   cells = local cost ÷ that sub  (under 1.0× = local cheaper · "maxed" = your usage tops the plan)
  ─────────────────────────────────────────────────────────────────────────────────────────
   MODEL                       SPEED   RIG       LOCAL   GPT    CLDE   GPT    CLDE   VERDICT
   can it vibecode? · rig      (out)  (approx)   /MO     $20    $20    $100   $100
  ─────────────────────────────────────────────────────────────────────────────────────────
   Qwen3-4B                    110t/s  $600      ~$17    maxed  maxed  0.2×   0.2×   🟡 can't really agent
     ◆ autocomplete-only · Mac Mini
   gpt-oss-20b                  88t/s  $1,600    ~$49    maxed  maxed  0.5×   0.5×   🔵 toss-up if it's enough
     ◆◆ light agentic · 1×4090
   Qwen3-32B                    35t/s  $3,000    ~$105   maxed  maxed  1.0×   1.0×   🔵 toss-up — privacy?
     ◆◆ light agentic · 2×3090
   DeepSeek-V3                  30t/s  $8,000    ~$267   maxed  maxed  2.7×   2.7×   🟠 overpriced vs the sub
     ◆◆◆ agentic-ready · 4×3090
   GLM 5.2                      20t/s  $20,000   ~$628   maxed  maxed  6.3×   6.3×   🔴 just keep Claude Max
     ◆◆◆ agentic-ready · 8×3090
  ─────────────────────────────────────────────────────────────────────────────────────────
   verdict:  🟢 buy the rig   🔵 toss-up   🟡 only if you must   🟠 overpriced   🔴 just subscribe
   coding:   ◆◆◆ agentic-ready   ◆◆ light agentic   ◆ autocomplete-only
```

**The two interactions that sell it:**
- **Change your plan + throttle answer.** "Free / $20, never throttled" → every rig looks absurd and even upgrading your *plan* is unnecessary. "$200, maxed, second account" → the `$100` columns go "maxed" too, the receipt flips to "no single sub covers you," and the ◆◆◆ rigs finally turn 🟢. You watch the exact threshold where subscriptions stop being enough.
- **Edit any cell.** Don't believe our tok/s or rig price? Change it; everything recomputes live. Sourced defaults, your reality.

Rows are subtly tinted by verdict color. The model subline keeps it honest about what you're actually buying and whether it can even do the job.

## 4. The input metric (the heart of the UX)

Vibecoders don't know their token counts — but they know **what they pay and whether they hit the wall.** That's a direct readout of where they sit against the caps that decide everything.

**Basic (default):** two dropdowns.
- *Today I pay:* none / ChatGPT Plus $20 / Claude Pro $20 / ChatGPT $100 / Claude Max 5x $100 / ChatGPT $200 / Claude Max 20x $200.
- *Do you hit the limit:* never · sometimes (end of week) · most weeks · constantly, I run a second account.

**Advanced (power users):** a single field — "I spend ≈ $___/mo on API" — overrides the derived band.

**Derivation → usage band ($/mo of frontier API-value):**
```
usage$ = planCap × throttleFactor
  throttleFactor:  never 0.4 · sometimes 0.8 · most weeks 1.0 · constantly+2nd 1.7
  (plan = none → usage$ ≈ free-tier floor, ~$30/mo)
```
This `usage$` drives the cap-coverage of each sub column, the cheapest-covering-sub pick, and (via a frontier $/Mtok constant) the token volume used for local power/throughput.

## 5. The math engine (`computeVerdict`)

A single pure function, transparent, with a "show the math" expander.

```
// 1. usage
usage$        = from §4 (plan+throttle, or advanced override)
tokensMo      = usage$ / FRONTIER_PRICE_PER_MTOK        // e.g. ~$5/Mtok
outTokMo      = tokensMo / (1 + ioRatio)                // ioRatio default 12

// 2. local all-in monthly cost (per model+rig row)
genHoursMo    = outTokMo / tokPerSec / 3600
powerMo       = powerWatts/1000 * genHoursMo * electricity   // $/kWh, default 0.15
localMo       = powerMo + capex / amortMonths                // amort default 36

// 3. cheapest subscription that actually covers the usage
covering      = SUBS.filter(s => s.cap >= usage$)
cheapestSub   = covering.length ? min(covering, by price) : null   // null = outgrown all subs

// 4. compare + verdict
ratio         = cheapestSub ? localMo / cheapestSub.price : 0
verdict       = tier(ratio, codingTier, cheapestSub == null)
```

**Verdict tiers** (`tier(...)`):
- **🟢 buy the rig** — `cheapestSub == null` (usage beyond every sub) AND model is ◆◆+ ; *or* `ratio < 0.8` AND ◆◆+.
- **🔵 toss-up** — `0.8 ≤ ratio ≤ 1.3` AND ◆◆+ (decide on privacy / control / offline).
- **🟡 only if you must** — `ratio < 0.8` but model is ◆ (cheap, can't really code); *or* you need privacy/offline despite a worse deal.
- **🟠 overpriced** — `1.3 < ratio ≤ 3`.
- **🔴 just subscribe** — `ratio > 3`.

Capability gate: a ◆ model can never read greener than 🟡 *as a coding replacement* — cost-competitive ≠ can-do-the-job.

**Honesty levers** (all editable, shown in Advanced / "show the math"): `tokPerSec`, `capex`, `powerWatts`, `electricity`, `amortMonths`, `ioRatio`, `FRONTIER_PRICE_PER_MTOK`. Numbers ship sourced and dated; users override anything.

## 6. Data model

Curated, typed (zod), dated, easy to PR. Every value carries `source` + `asOf`; the UI shows "data as of <date>".

```
/data/models.json    // { id, name, codingTier: '◆'|'◆◆'|'◆◆◆', ioRatioDefault, notes, source }
/data/rigs.json      // { id, label, capex, powerWatts, tokPerSec: {modelId: number}, source, asOf }
/data/subs.json      // { id, vendor:'gpt'|'claude', label, price, capMonthly, source, asOf }
/data/constants.json // { FRONTIER_PRICE_PER_MTOK, electricityDefault, amortMonthsDefault }
```

Seed `subs.json` from the SemiAnalysis figures:
`gpt-plus $20→$700` · `claude-pro $20→$400` · `gpt-100 $100→$3,500` · `claude-max-5x $100→$2,000` · (`$200` tier kept for the "outgrown subs" case).

## 7. Scope

**v1 (ship this)**
- Personalized verdict hero (plan + throttle input → big answer + receipt)
- Colored, editable comparison table with the four sub columns + cap mechanic
- 5-tier verdict blending cost and coding ability
- Advanced mode: `$/mo` override + all assumptions editable; "show the math" expander
- "Get <cheapest covering plan> →" CTA
- Shareable verdict via URL state + OG card (Vercel OG)
- Fully client-side, deployed on Vercel

**Fast-follow**
- More models / rigs; a browsable catalog
- Affiliate links on the CTA; basic analytics on shares
- Cursor / Copilot plans (need cap data first)
- "Notify me when local gets viable" capture

**Non-goals (v1)**
- No benchmarking CLI, no backend, no live price scraping
- Not a benchmark authority — sourced estimates, clearly labeled, editable
- No accounts

## 8. Risks & mitigations

| Risk | Mitigation |
|---|---|
| Sub caps / prices change fast | All data dated + editable; JSON easy to PR; "as of" labels; caps cited to source |
| tok/s & rig prices disputed | Cite sources, every cell editable, ship rough-but-honest defaults |
| Verdict read as gospel | "Show the math," assumptions inline, clearly-labeled estimates |
| Reads as "never self-host" propaganda | The 🟢 path (outgrown subs / privacy) is first-class, not buried |
| Usage band feels hand-wavy | Anchor on plan+throttle (knowable) and expose the `$/mo` override + every constant |

## 9. Success metrics

- **Verdict cards shared** (primary — the growth loop)
- **% sessions that reach a verdict** and **change their inputs** (engagement / "made it mine")
- **CTA click-through** to the recommended plan
- Return visits

## 10. Build plan

1. Scaffold Next.js + Tailwind + shadcn; add Card, Select, Input, Tooltip, Badge, Table.
2. Write `computeVerdict()` + unit tests pinned to the worked examples in §3/§5.
3. Seed `/data/*.json` (models, rigs, subs, constants) with sourced numbers.
4. Build the hero: plan+throttle inputs → usage band → verdict + receipt + CTA.
5. Build the table: four sub columns, cap "maxed" logic, verdict pills, row tint, editable cells, live recompute.
6. Advanced mode + "show the math" expander + URL state.
7. OG verdict card (Vercel OG) + share button.
8. Deploy to Vercel.
