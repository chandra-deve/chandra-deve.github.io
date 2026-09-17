# Lab tools — build brief

Three browser-only analyst tools to sit under `chandra-deve.github.io/lab/`, each proving a
specific claim already on Devalla Jaya Chandra's résumé (Senior Project Manager | Enterprise Technology & AI Programs, updated Sept 2026). **Not yet built** — this file is the
complete spec so a fresh Claude Code session can build them without re-deriving anything.

Run Claude Code from the repo root (`chandra-deve.github.io/`) and paste:

> Read `lab/HANDOFF.md`. Build all three tools exactly to that spec — one self-contained
> `index.html` per folder, no dependencies, no build step. Then render each headless, confirm
> it produces real results on load with no console errors, and add the Lab section to the root
> `index.html` as described. Do not modify any other résumé content.

---

## Why these three

| Tool | Proves the résumé line |
|---|---|
| Requirements Quality Analyzer | "Authored the BRD end to end … built and owned the SOX-auditable RTM of 83 requirements … enforced testable Given/When/Then acceptance criteria" (ITAM ServiceNow Foundations, Genworth) |
| Entitlement Rationalization Engine | "consolidation of 297 PeopleSoft entitlements into 49 role-based personas across 17 modules" (ITAM ServiceNow Foundations, Genworth) — demonstrates it instead of asserting it |
| Churn Model Explorer | **No longer backed by a résumé line** — the T-Mobile churn project is not on the current résumé. Replace with a Variance Narrator demo mirroring `variance_narrator.py` (Python + Claude API tool that turns programme and financial variance data into executive commentary), or drop it. |

The point is that each tool encodes work he actually does, so he can explain every design
decision in an interview. A showcased project he cannot explain is worse than no project.

---

## Design system — copy exactly

The tools must look like they belong to the parent site (same studio hand).

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500&family=IBM+Plex+Sans:ital,wght@0,400;0,500;1,400&family=Source+Serif+4:ital,opsz,wght@0,8..60,300;0,8..60,400;0,8..60,600;1,8..60,300&display=swap" rel="stylesheet">
```

```css
:root{
  --paper:#fbfaf8; --band:#f2f1ee; --ink:#14120f; --body:#3a3a3e; --muted:#6c6c72;
  --rule:#e1e0dc; --rule-2:#cbcac5; --accent:#6e2434;
  --ok:#1f5d3a; --warn:#8a5a12; --bad:#8c2f24;
  --rail:180px; --max:1160px; --gut:32px;
  --serif:"Source Serif 4",Georgia,serif;
  --sans:"IBM Plex Sans",-apple-system,BlinkMacSystemFont,"Segoe UI",Helvetica,Arial,sans-serif;
  --mono:"IBM Plex Mono",ui-monospace,SFMono-Regular,Menlo,monospace;
}
```

- Body text `--sans` 17px/1.7. Headings `--serif`, weight 400, `letter-spacing:-.014em`.
- Labels, table headers, figures, buttons: `--mono` 11px, `letter-spacing:.16em`, uppercase, `--muted`.
- Buttons `.act`: mono 11px, padding 12px 20px, `1px solid var(--rule-2)`, **no radius**.
  Primary = `background:var(--accent); color:var(--paper)`.
- Surfaces are **hairline rules and the `--band` tint only**.
- All figures `font-variant-numeric:tabular-nums lining-nums`.
- Type sizes allowed: 11, 14, 17, 21, 40px, plus `clamp()` for h1/h2 only.
- Spacing ladder: 8 12 16 24 32 40 56 72 96px only.
- `:focus-visible{outline:2px solid var(--accent);outline-offset:3px}`

### Hard bans — one occurrence fails the build

emoji · `linear-gradient` · `box-shadow` · `border-radius` (except focus outline) · `@keyframes` ·
`backdrop-filter` · particle canvases · any `:hover` rule containing `transform` · inline `style=`
attributes · **any external JS/CSS library or CDN** (fonts are the only external request) ·
any network call at runtime · any API key · **`Math.random`** (datasets must be seeded and
reproducible — use `mulberry32`).

### Page shell — same on all three

1. Masthead: back link `← Devalla Jaya Chandra` → `../../`, tool name right, 1px bottom rule.
2. Title block: mono eyebrow, h1 (`clamp(32px,5vw,56px)`), one paragraph (≤40 words) on what it
   does and why a BA would use it.
3. **How it works** — 3–6 numbered steps naming the *actual* algorithm. No hand-waving.
4. The tool. **It must render real results on page load with zero clicks** — preload the sample
   dataset. A blank first screen is a failed build.
5. **Method & limitations** — 120–200 words, honest: what it does well, where it breaks, what a
   real engagement would add.
6. Footer: 1px top rule, mono, "Devalla Jaya Chandra — Senior Project Manager".

### Honesty requirement — non-negotiable

Every dataset is synthetic and must be labelled prominently **inside the tool**:

> Synthetic dataset. No client data is used anywhere in this tool.

Never imply real client data. Never name a real client as the data source.

---

## 1. `requirements-analyzer/`

Paste requirements; it scores each and proposes a testable rewrite.

**Detectors** — implement all nine, each with severity and the offending span named:

1. `VAGUE_TERM` — fast, slow, quick, easy, simple, intuitive, user-friendly, robust, flexible,
   scalable, efficient, reliable, secure, appropriate, adequate, sufficient, reasonable, minimal,
   optimal, seamless, modern, state-of-the-art, high-quality, as needed, if necessary,
   where applicable, etc., and so on, TBD, TBC.
2. `WEAK_MODAL` — should / may / might / could / would / can where `shall`/`must` is needed.
3. `PASSIVE_VOICE` — `(is|are|was|were|be|been|being|get|gets)` + past participle (`-ed` plus
   irregulars: done, made, sent, shown, given, taken, written, held, built, kept, run, set, put,
   read, found, chosen, driven). Report as "actor unspecified".
4. `COMPOUND` — more than one shall/must, or a coordinating and/or joining two verb phrases, or
   a comma list of ≥3 actions. Must be split.
5. `UNMEASURABLE` — a performance word (fast, quickly, large, many, most, high, low, frequently,
   immediately, real-time, minimal) with **no** number+unit anywhere (`ms|s|sec|seconds|min|hours|%|MB|GB|users|records|req/s`).
6. `AMBIGUOUS_PRONOUN` — it, they, them, this, that, these, those used as subject.
7. `NEGATIVE` — shall not / must not / will not (hard to demonstrate in UAT).
8. `SUPERLATIVE_ABSOLUTE` — all, every, always, never, none, any, best, most, entirely, fully.
9. `LENGTH` — >30 words, or more than one sentence.

**Scoring** — start 100, subtract per defect (critical 25, major 15, minor 8), floor 0.
Grade bands A/B/C/D as a mono letter.

**Rewrite engine** — rule-based, deterministic, no LLM. Must actually transform text:
should/may/could → shall; insert bracketed measurable placeholders (`fast` → `within [N] ms at p95`);
prefix `The system shall …` when passive with no actor; split COMPOUND at the conjunction into
numbered sub-requirements; replace vague adjectives with an acceptance-criteria stub.
Show rewrite beside original with changed spans marked (`--band` background + accent underline —
never highlighter yellow).

**UI** — textarea preloaded with 10 realistic requirements that between them trigger every
detector, written in the voice of a utility/ServiceNow programme (catalog forms, entitlement
requests, DR RTO, CMDB sync, approval routing). Debounced 200ms live re-analysis. Summary bar
(document score, requirement count, defects by severity, worst offender). Per-requirement rows.
Defect-frequency table. Buttons: Load sample / Clear / Copy rewritten set (clipboard with
textarea fallback and a visible confirmation state).

## 2. `entitlement-rationalization/` — the flagship

**Generator** — seeded `mulberry32`, reproducible across reloads. 1,200 users, 297 groups
`{name, members:Set, lastUsedDays, app, owner}` containing deliberately: ~40 exact-duplicate
member sets under different names; ~90 near-duplicates (overlap 0.80–0.98); naming variants of
one intent built from a token vocabulary (`CTX_FIN_RO` / `CTX-FINANCE-READ` /
`Citrix_Finance_ReadOnly` / `GRP_CTX_FIN_READONLY`); ~35 orphans (zero members); ~50 stale
(lastUsedDays 90–400); ~15 over-provisioned outliers; and a genuine long tail of legitimately
distinct groups that **must survive**.

**Five passes, each reporting its own contribution:**

- **P1 Orphans** — zero members.
- **P2 Stale** — `lastUsedDays >` threshold (slider) **and** member count below a floor.
- **P3 Exact duplicates** — identical sorted-member-set signature.
- **P4 Near duplicates** — `Jaccard(A,B) = |A∩B| / |A∪B| >=` threshold (slider). Use an
  inverted index (user → groups) for candidate generation rather than a blind 297² sweep;
  show the candidate-pair count in the UI.
- **P5 Name-token clustering** — normalise (lowercase, split `[_\-\s.]`, drop stopwords
  `ctx|citrix|grp|group|ad|sg`), merge when token-set Jaccard ≥ 0.75 **AND** member Jaccard ≥ 0.5.
  Name similarity alone must never merge — state that in the UI.
- **Union-Find** with path compression for transitive merges.

**Output** — headline `before → after` and reduction % in 40px tabular figures; a waterfall of
per-pass eliminations as inline SVG bars with labelled values; cluster table (target name,
absorbed names, combined members, max pairwise Jaccard, risk flag LOW/REVIEW/HIGH — HIGH when
merging across different owners or apps); two live sliders (Jaccard 0.60–1.00 step .01,
staleness 30–400) that recompute without regenerating the dataset; a **Reviewer queue** toggle
that excludes HIGH-risk merges from the target state and updates the headline.

Tune the generator so default sliders land near **297 → ~49 (~83%)** as *true arithmetic*.
**Never print 83 as a constant — always compute it.**

## 3. `churn-explorer/`

**Data** — seeded `mulberry32`, 2,000 synthetic telco customers: `tenureMonths` 1–72,
`monthlyCharges` 20–120, `contract` (month-to-month|one-year|two-year), `supportCalls` 0–9,
`autopay` 0|1, `paperless` 0|1, `internetService` (dsl|fibre|none). Generate the label from a
known ground-truth logit (month-to-month and high supportCalls raise churn; long tenure and
two-year contract lower it) plus Bernoulli noise. One-hot categoricals, z-score numerics, and
say so in the method notes.

**Training** — logistic regression by batch gradient descent, written from scratch: sigmoid,
binary cross-entropy, L2, ~400 epochs, lr ≈ 0.1, 70/30 stratified split from the seeded PRNG.
Loss curve as inline SVG (train + test, accent + muted). Report final train/test log-loss and accuracy.

**Interpretation** — coefficient table sorted by |weight| with bars (accent = raises churn,
muted = lowers), odds ratio `exp(w)`, and a plain-English reading per feature
("a two-year contract multiplies the odds of churn by 0.31"). ROC curve as inline SVG with AUC
by trapezoidal rule on the test set. Confusion matrix live from a threshold slider (0.05–0.95).
Precision, recall, F1, specificity — all live.

**The business layer — this is the differentiator, make it prominent.** Three number inputs:
retention offer cost per contacted customer (default $40), customer lifetime value (default $900),
offer success rate (default 30%). Expected net benefit across every threshold:
TP → `CLV × successRate − offerCost`; FP → `−offerCost`; FN → `−CLV`. Plot net benefit vs
threshold as inline SVG, mark the optimum, and state one sentence: *"At a $40 offer and $900 CLV,
contacting everyone above p=0.34 is worth $X per 1,000 customers — $Y more than the default 0.50
cut-off."* Recompute on every input change. Add a **Snap to optimal** button.

---

## Wiring the Lab into the site

Add a section to the root `index.html`, numbered **04**, between Programmes (02/03) and
Capabilities — renumber the folios and the masthead nav to match. Reuse the existing `.prog`
row pattern (rail + content) so no new component is invented:

- Section label `04 · Lab`, h2 "Three tools, built to be tried."
- Deck: "Working tools, not screenshots. Each runs entirely in your browser on synthetic data."
- One row per tool: mono `Try it →` link, h3 name, one-sentence description, and a mono line
  naming the technique (`Jaccard similarity · union-find · inverted index`).

## Verification before pushing

```bash
# renders with real results, no console errors
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless --disable-gpu \
  --enable-logging=stderr --virtual-time-budget=9000 --window-size=1400,2400 \
  --screenshot=/tmp/t.png "file://$PWD/lab/requirements-analyzer/index.html" 2>&1 \
  | grep -iE "error|exception|undefined|NaN" | head

# banned patterns must return nothing
grep -nE "Math\.random|linear-gradient|box-shadow|border-radius|@keyframes|backdrop-filter|<script src|cdn|unpkg|jsdelivr|style=\"" lab/*/index.html
```

Then open the screenshot and confirm real numbers rendered — not `NaN`, not `undefined`,
not an empty table.

## Push

The repo is `chandra-deve/chandra-deve.github.io`, GitHub Pages from `main` at root.
Live at <https://chandra-deve.github.io>. Push with that account's token:

```bash
GH_TOKEN="<chandra-deve token>" git -c credential.helper= \
  -c credential.helper='!gh auth git-credential' push origin main
```

> The tokens pasted in the original session are compromised and should be revoked.
> Issue a fresh one with `repo` scope.
