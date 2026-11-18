# Token Radar — build notes (for the parent agent)

Entry for make.fun bounty `token-radar-01` (Build the best token launch radar,
$1,325 USDC, deadline 2026-12-14 10:00 UTC). Built 2026-11-18 by subagent
session 5104278a (child of parent session 51a85148, top-level agent b247a651).

## Model (radar-2/comps, lib/model.ts)

- Board: 198 rows — the snapshot's whole 40-protocol watchlist + every other
  pre-token protocol passing the watchlist's own exclusion rule without its
  $50M floor (no CEX/bridge/liquid-staking/versioned/wrapped/exchange-branded).
  416 protocols record a token and are set aside in the "Have a token" view.
- Implied value = TVL x category comp x growth x maturity x breadth.
  Category comp = median FDV/TVL of that category's live-token protocols in
  this snapshot (n>=3, else whole-market median 0.2138x, clamped 0.02-1.00).
  No protocol name is special-cased.
- Farm plans: 6 steps each, category-aware, with costs and times, derived from
  category/chains/TVL/age. All steps 89-213 chars (gate: 20-600).

## Test iterations

1. First run: BUG — board only 40 rows, live_count 758. Cause: `parseSnapshot`
   dropped the `mcap_usd` field, so `isPreToken` was false for every protocol.
   Fixed by adding the field (plus a strict `listed_at` check).
2. Second run: 198 rows, watchlist 40/40 covered, ranks 1..198, implied values
   strictly non-increasing, 198 distinct values, steps in range.
   Top 5: steakhouse-financial $538.8M, sentora-curator $498.6M,
   gauntlet $320.9M, veda $304.9M, usdd $155.4M.
3. Screenshots: first attempt produced 3 identical PNGs — hash-only navigation
   does not reload the page, so deep links never ran. Fixed shot.mjs to open a
   fresh page per shot. Re-took: 1-board 170KB, 2-detail 156KB, 3-filtered
   168KB, all 1440x900, visually checked (board / detail with formula + farm
   plan / watchlist-only filter "40 of 198 rows"). Method + live views also
   rendered with zero console/page errors.
4. Determinism: two consecutive runs produce byte-identical board.json,
   data.js and dossier.json (sha256 compared).
5. Local gate replica (`.shot/gate-check.mjs`, the 7 gate rows): ALL PASS.
   Dossier 682-698KB (cap 1.5MiB), dossier text 24,322 bytes (cap 65,536 —
   whole dashboard source shown to the judge). `buildDossier` output verified
   byte-identical to the written dossier.json. No http(s) anywhere in
   dashboard/ files. Run time ~1s (limit 120s).
6. Kit check (`check --kit . --repo <build> --worktree`): 4 passed
   (competition_open, snapshot, license_osi MIT, entrypoint main.ts/node22),
   1 failed = transcripts_present (no bundle yet — expected, parent's step),
   5 checked at submission (repo public/owner/created-after-open/commit/
   duplicate). Kit itself verified: all 20 files sha256 OK.

## Transcript bundle (parent's step)

- `bundle --dry-run` FINDS both sessions: this subagent's
  `sessions/muse-app/5104278a-...jsonl` (56 turns) and the parent's
  `sessions/muse-app/51a85148-...jsonl` (14 turns kept).
- Still missing for a real bundle: the prompt (Garong's verbatim opening
  words — pass with --prompt-file + --prompt-origin + --prompt-via), MCP
  servers (--mcp none: this build used no MCP tools), agent facts
  (--harness-version, --provider meta, --model muse-spark-1.3,
  --effort default), and the usage rows (--muse-usage, run by the TOP-LEVEL
  agent b247a651 with its own agent_id plus 51a85148 and 5104278a).
- System prompt: in-session. Instruction files: collected (AGENTS.md).

## What is still needed before submit (parent)

1. Pairing must finish (Garong approves the connect code) — nothing here
   depends on it yet, but submit needs the account + API key.
2. Create the GitHub repo (public, created after 2026-11-14T10:00Z) under
   Garong's GitHub login, push this folder's committed files, set About
   (website https://make.fun/bounty/token-radar-01, topic `makefun`) —
   or use the kit's source-bundle path instead (then run `init` FIRST in an
   empty repo; do NOT commit anything before init).
3. README badges assume owner `geishakampret`, repo `token-radar-01` —
   fix the owner/repo in the badge URLs if the real repo differs.
4. `.makefun/owner.json` is NOT included: only needed if the repo owner is
   not Garong's GitHub login.
5. Commit the work (repo is git-init'd, files uncommitted on purpose), run
   `bundle` from the top-level agent, then `check --transcripts <bundle>`,
   then `submit` (do NOT submit from here: pairing + repo are pending).
6. Re-run `node main.ts` after any repo-side change that touches
   dashboard/ or screenshots/ so dossier.json stays byte-identical.

## Reproduce

```
mkdir -p out && MAKEFUN_OUTPUT_DIR=$PWD/out node main.ts --input ../token-radar-01-kit/input/snapshot.json
node ../.shot/gate-check.mjs $PWD/out ../token-radar-01-kit/input/snapshot.json
```

Screenshots (if the dashboard changes):
```
node ../.shot/shot.mjs $PWD/out/dashboard $PWD/screenshots   # then re-run main.ts
```

Playwright Chromium lives in ~/.cache/ms-playwright (installed 2026-11-18
for this task); the meta-chromium in /opt refuses to start outside Hatch.
