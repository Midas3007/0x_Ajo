# M0: Verify, score, recommend

Celo "Agents Open for Business" hackathon. Research done 2026-11-20. No product code in this milestone.

**Verdict:** build **Ajo**, using Mallam's conversion logic for the naira leg. Enter **Textile Stablecoin Corridors** (primary), **Self Agent ID** (secondary) and **Altana** (hedge). A project can win in at most two tracks.

---

## 1. Rules (source: https://www.loops.house/agents-open-for-business, read 2026-11-20)

| Item | What the page says |
|---|---|
| Build window | Opened Fri 20 Nov 2026, 14:00 GMT |
| **Deadline** | **Mon 7 Dec 2026, 09:00 GMT (10:00 Lagos)**, for both registration and submission |
| Winners | Fri 11 Dec |
| Prizes | $5,000, set in USD and paid in CELO to the payout wallet you register |
| Stablecoin Corridors (Textile FX) | $1,000 / $600 / $400. Judged by Textile + Celo Core Co. 25% each: quality of execution, clear value proposition, initial traction with tagged onchain activity, a named user with a real job done |
| Textile requirements | ≥1 **tagged Textile FX conversion on Celo mainnet** in a listed corridor, used in the product flow; a **local-stablecoin payment reaching a recipient outside the team**; ERC-8004. **Arbitrage, self-trading and acting as a Textile maker do not count** |
| Agent Vault (Self) | $700 / $300. Integration 30%, innovation and value 30%, named user and real job 25%, real use 15%. Signing in through Agent Vault meets the requirement, and a card checkout is optional. The x402 payment can be a small per-request payment |
| Self Agent ID (Self) | $700 / $300, same weights as Agent Vault. "Services where Self Agent ID decides which agents get served, paid, or limited, with payments through x402." Self Agent ID is required |
| Altana | $750 + 25k XP / $250 + 10k XP. 25% each, same criteria as Textile. The agent acts from a non-custodial Altana wallet under a session key with a spend cap, a contract allowlist and an expiry |
| Scoring | Celo DevRel shortlists from onchain data plus the Loops judge's reading of the repo, then track judges score. Activity is reviewed before judging. **No metric formula is published** |
| Independent users | A wallet outside the team, not funded by you or your main funder. **New and existing Celo users both count.** An agent with its own ERC-8004 identity from another team counts when it pays you. Pools, routers, Textile makers and exchange wallets don't count |
| Attribution | An ERC-8021 code appended to the calldata of every tx you want counted, in the frontend or backend, not in the contract. The code is issued at Loops enrollment and can't be added after the fact. x402 sellers register their PayTo address instead |
| ERC-8004 | Required for every entry. Register from the agent's wallet before the deadline. Self Agent ID is recommended for all tracks and is **not** stated to substitute for it |
| Prior work | Code written during the hackathon, from 20 Nov |
| Multi-track | Enter as many tracks as you like. **Win in up to two**, one placement per track |
| Gas | Sponsored gas isn't a builder contribution. Signers and authorisers count. Describe a relayer architecture in the submission |
| buy | Optional in every track. Paid calls count in your favour |
| x402 facilitator | Up to 100 free mainnet settlement credits, then gas at cost plus 0.3% |
| Submission format | **Not published.** Ask in https://t.me/celoagenthackathons |
| Livestreams (14:00 GMT) | Kickoff https://www.youtube.com/watch?v=E3HUsW-X7EA (not watched yet). Self Tue 24 Nov, Altana Wed 25 Nov, Corridors Tue 1 Dec. Office hours Thu 12:00 GMT. AskBots reviews 24 Nov and 4 Dec |

### Where the brief's "rules to assume" are wrong or changed
1. **Deadline:** 09:00 GMT on 7 Dec (10:00 Lagos), not end of day.
2. **buy is not required for the Agent Vault track.** It is optional everywhere.
3. **Up to two track wins** per project (the old edition only capped one prize per track).
4. **Independent users are looser:** no "own Celo activity before kick-off" rule. Still not funded by you.
5. **Textile judging changed:** no more P&L or volume scoring. Makers, arbitrage and self-trading score zero, so Mallam's market-making part is dead.
6. **Textile track needs a local-stablecoin payment to an outside recipient.** Ajo's naira pot payout fits this exactly.
7. **Self Agent ID is on mainnet:** `0xaC3DF9ABf80d0F5c020C06B04Cced27763355944` (code confirmed via `eth_getCode`; it's a proxy). The brief said Sepolia only.
8. **No published scoring query.** "Shortlist from onchain data" is all the page gives.
9. **No submission format published yet.**
10. The x402 free allowance rose from 20 to 100 settlements.

---

## 2. Sponsor tooling

| Tool | Mainnet? | Access / versions | Costs and limits | Caveats |
|---|---|---|---|---|
| **Textile FX** | **Yes.** Celo corridors include cNGN↔USD₮ and USDC↔USD₮ | v2 RFQ at `https://api.textilecredit.com/v2`: `POST /rfq/preview` (free, no key) → `POST /rfq/request` (firm, held 60 s) → approve reactor → **taker broadcasts `transactions.swap` itself**. Keyless takers sign an EIP-712 `takerProof` (ERC-1271 supported). A live partner key skips the proof (self-serve form, usually approved on email confirmation; no KYC mentioned) | Fee **2 bps** in the sell token. Min **$1** notional. 60 req/min, 4 outstanding RFQs per key or IP | **Output always goes to the taker** (no recipient field). **No Celo Sepolia corridor** (only BSC testnet and Base Sepolia). Tagging conflict: the hackathon says append your tag to `swap.data`, while Textile's docs say don't append and use `includeAttribution` with a partner key. **Ask organizers** |
| **Live check (2026-11-20)** | | 10 USD₮ → **14,400.68 cNGN**. 15,000 cNGN → 10.41 USD₮. Mid ≈ **₦1,440/$**, round-trip spread ≈ 6 bps | Depth ≈ 44.9k USD₮ and 60.4M cNGN (~$42k) | Plenty for ajo-size tickets |
| Tokens (onchain `decimals()` checked) | | cNGN `0xF6829D7393dAe24509eb1E52eE8e572e2E271a4f` (**6**), USD₮ `0x48065fbBE25f71C9282ddf5e1cD6D6A887483D5e` (**6**), USDC `0xcebA9300f2b948710d2653dD7B07f33A8B32118C` (**6**). Reactor `0xa9AA0a64769cBed4d3B1Ceb4Df01CdE915C235b3` | | Use these, not the 18-decimal fee-currency adapters with the same symbol |
| **Self Agent ID** | **Yes**, registry `0xaC3D…5944`. Sepolia `0x043D…d379` takes mock passports only | `@selfxyz/agent-sdk` **0.3.1** (npm, 2026-11-11). For mainnet pass `registryAddress` + `rpcUrl`. `agent.fetch()` adds 3 signed headers, and `SelfAgentVerifier` checks them. Onchain: `isVerifiedAgent(bytes32)`, `getAgentId`, `isProofFresh` (from README, verify signatures in M1) | No Self fee published. `smartwallet` mode is gasless on mainnet | **Nigerian passport support is unconfirmed** (the supported-countries page returned 403). The human registers by scanning a passport in the Self app; the SDK deep link suggests phone-only works. Self's registry is **separate** from the reference ERC-8004 registry `0x8004A169FB4a3325136EB29fA0ceB6D2e539a432`, so register in both. Self-app agents start with a blank `agentURI`; call `setAgentURI` |
| Self (user proof-of-human) | Hub V2 reportedly `0xe57F4773bd9c9d8b6Cd70431117d353298B9f5BF` (third-party source; it has code) | Onchain via Self contracts, or **offchain backend verification**. The proof yields a per-app nullifier (one human, one seat). QR or deep link | Gas payable in USDm, USDC or USD₮ | Self docs mostly returned 403 to my fetches, so this is verified in M1 from `@selfxyz/contracts` source. Whether the deep link returns cleanly to MiniPay is unknown |
| **Altana** | **Yes**, Celo 42220. KeyStore `0x83b3…d27c` (code confirmed), Controller `0x47bA…d1D6`, relay `relay.altana.network` | `@altananetwork/sdk` **1.1.0** (2026-11-18). Works in browsers and React Native since 0.9.0; Bun is needed only for the MCP. Owner is a passkey or private key | First key registration ≈ **8.45 CELO** (oracle-priced, +10% buffer). CELO ≈ $0.09–0.12 today, so ≈ $1. Relay fees in CELO or stablecoins | **x402 payments are signed outside the session, so its spend caps don't bound them.** Whether the allowlist can restrict arguments (e.g. recipient) is unverified. Altana's XP rules list only BNB and Ethereum KeyStores; Celo XP is granted by hand for hackathon prizes. The Self agent key (EOA) and the Altana wallet (contract) are different addresses, which complicates onchain Self gating |
| **Agent Vault** | Card and login proxy: yes. **x402 wallet: preview, runs on Celo Sepolia** until Self Labs clears mainnet | Hosted MCP `mcp.agentvault.io/mcp`, `@agentvault/playwright` (tarball), HTTP API | Free: 1 agent card, 5 payments / 30 days. Plus $19/mo | The wallet **pays buy services only**. You bring your own card, and no country list was found (Nigerian cards unconfirmed). Doesn't bypass bot detection |
| **buy** | Yes, mainnet default | npm latest **0.8.6** (2026-11-18). Docs say 0.8.7, which isn't on npm. Node 22+. **CLI and MCP only, not a library.** Key kept in the OS keychain (a headless server needs checking) | Wallet pays USDC, USD₮ or USA₮. Gateway sponsors x402 gas. Prices from $0.001 per call | Catalogue (snapshot 2026-10-13) has **no SMS, WhatsApp or email sending, no NGN rate data, and no Claude**. LLM: Cencori, $0.01 per completion. **buy has no strong natural role in Ajo**, so use it only where it does a real job |
| **x402 facilitator** | `https://api.x402.celo.org` / `https://api.x402.sepolia.celo.org` | Settling needs an API key from x402.celo.org (dashboard, human-created). cNGN settles via Permit2 (the first payer pays the approve gas) | 100 free credits, then gas + 0.3% | Settlements carry no ERC-8021 tag yet, so register the PayTo address |
| **Attribution tags** | n/a | `@celo/attribution-tags`: `withAttribution(code)` viem extension or `toDataSuffix(code)`. Suffix = `code ‖ len(1) ‖ 0x00 ‖ 0x8021×8` | Free | **Use the code Loops issues at enrollment.** A self-derived code isn't credited. feeCurrency is not mentioned; check MiniPay txs keep the suffix |
| **MiniPay** | Yes | Injects `window.ethereum` (`isMiniPay`) and auto-connects. Test on a real phone via Developer Mode → Load Test Page | Fee abstraction (MiniPay may override `feeCurrency`) | Docs list only **USDm, USDC and USD₮**, **not cNGN**. "Don't ask users to sign a message to access your app." EIP-712 support is undocumented |

---

## 3. Scores (1–5, higher is better; for risk, higher = lower risk)

| Criterion | A: Mallam | B: Plug | C: Ajo |
|---|:-:|:-:|:-:|
| Prize EV (tracks × odds) | 3: Textile + Altana fit, but market making scores zero now | 2: Agent Vault ($1k) + Self. The Vault x402 wallet is still Sepolia | **4**: Textile ($2k, 3 places) + Self + Altana. Fits the new Textile requirement exactly |
| Solo feasibility, 15 days, Termux | 3: one Altana wallet per user, plus Textile | 2: card checkouts, bot detection, Nigerian cards | 3: contract + mini app + agent. The contract is the main risk |
| Sponsor API readiness | 4 | 2 | **4** (Nigerian passport unconfirmed) |
| Independent users | 3: freelancers must already earn on Celo | 2: 3–5 teams, mostly offchain spend | **5**: multi-signer by design, MiniPay network |
| Real job and demo clarity | 4 | 3 | **4** (judges need one line on what an ajo is) |
| Onchain footprint | 3 | 1 | **5**: every join, contribution, conversion and payout is a tagged tx from a member |
| Risk (higher = safer) | 3: FX-for-others optics, capital | 3 | 2: pooled funds in an unaudited contract, legal grey zone |
| Originality | 2: treasury and FX agents already exist (e.g. Moi, textile-mm-agent) | 3 | 3: ROSCA dapps on Celo are common (Esusu, CeloSusu with Self, Ronda). Agent-run circle + Textile corridor + verified agent + credit record is new |
| **Total / 40** | **25** | **18** | **30** |

**Recommendation: Ajo, using Mallam's conversion logic.** Its loop produces exactly what the new Textile track asks for: a real USD→NGN conversion inside the product, followed by a naira payment to someone outside the team.
**Runner-up: Mallam (conversions only).** It is simpler and holds no pooled funds, but it is less original and its quoting angle no longer scores.
**Last: Plug.** Its spend is mostly offchain, the Agent Vault x402 wallet is not on mainnet, and it has the least local edge.

---

## 4. Ajo design (recommendations)

### The key change from the brief: price the pot in cNGN, and members convert on the way in, signing themselves
- The brief had the agent converting USD₮→cNGN **at payout**. On Textile the swap output always goes to the **taker**, and contract takers are undocumented. That means either the contract runs agent-supplied swap calldata (attack surface) or the agent's wallet holds the pot mid-swap (custody). Both are worse.
- **Recommended:** circles are priced in **cNGN**. A member holding USD₮ (most MiniPay users, since MiniPay doesn't list cNGN, plus every diaspora member) taps "Pay ₦20,000". The agent fetches a Textile quote with **taker = the member's own wallet**, then the member signs approve + swap + contribute in MiniPay, with our tag on each tx.
- Result: **every contribution is a tagged Textile conversion signed by an independent user**, the payout is a **cNGN payment to a member outside the team**, and neither the agent nor we hold funds at any point.
- The agent's job on the FX leg (Mallam's logic): watch the rate during the contribution window, enforce the member's "don't convert worse than ₦X/$" rule, and say "pay now" or "wait".

### Design questions
1. **Defaults.** Use a **1-contribution security deposit + track-record ordering + grace period, then partial payout with an onchain default flag**.
   Worst case: N=10, C=₦20k, and the week-1 recipient defaults on all 9 later rounds.
   - No protection: total loss ₦180k (each later recipient gets ₦180k instead of ₦200k).
   - Deposit only: it covers one round, so the loss is ₦160k.
   - Track-record ordering doesn't shrink the worst case, but it makes it unlikely because proven members go first.
   - Full protection would need early slots to lock (N−p)×C, which defeats the point of an ajo.
   - So add deterrence: the default is recorded against the member's address (and their Self nullifier if they verified), shown to every future circle, and blocks slots 1–2 in any new circle.
   - Hackathon caps keep the stakes small: ≤12 members, small fixed contribution limits, invite-only.
2. **Payout order.** The coordinator fixes the order at creation; the agent suggests it from track record (new or unverified members can't take slots 1–2). Members see the order before joining, and joining means consent. No random draw (would need VRF). Two-party slot swaps are on the cut list.
3. **Round closing.** A round pays when **everyone has paid, or at deadline + grace**, whichever comes first. The grace period is 24 h for pilot circles and 48 h for weekly ones. The Self-verified agent can trigger the payout once the conditions hold. **Any member can trigger it 24 h after grace ends**, so funds never freeze if the agent dies. A missing member's deposit is applied automatically.
4. **Currency and FX.** The pot is in cNGN only, so there's no mixed pot. Each contributor bears FX at the moment they contribute, using a Textile firm quote, and the recipient gets an exact naira amount. USD-priced circles come later.
5. **Who can change what.**
   - The coordinator creates the circle, sets parameters and order, invites members, and can cancel before start (deposits refunded). After the first join nothing can change.
   - The agent can only trigger due payouts and record misses.
   - Nobody can redirect funds: payout address = the member's `msg.sender` at join.
   - No admin withdraw, no upgradeability. The kill switch blocks only new circles, joins and contributions. Payouts and refunds always work.
   - No exit mid-cycle in v1. Deposits are refunded at cycle end to members in good standing.
6. **Disputes and proof.** The contract emits an event for every action (`Joined`, `Contributed`, `PaidOut`, `Missed`, `DepositApplied`, `DepositRefunded`). Receipts link to Celoscan. A public circle page reads state straight from the chain with no backend trust. The reputation score uses only onchain events, with an open-source formula.

### The credit-record angle (pitch and README only)
A member's onchain record of contributing every round works as a portable credit history, which is the front door to Textile's onchain credit pools. In v1, build only the events plus a simple score. For the Self track, an optional, member-consented, x402-priced "contribution history" endpoint served **only to Self-verified agents** fits the track description literally. It's a stretch goal and the first thing to cut.

### Regulatory flags (your call)
- I found no Nigerian rule that names digital ajo or esusu.
- Design choices that keep this as "software for a private group": invite-only circles of people who know each other, no fees on the pot, no yield, no lending, funds held by the contract with no redirect power, and FX done by each member's own signed swap on a permissionless market, so we never buy or sell currency for anyone.
- Risks: SEC jurisdiction over "pooled retail capital", and the cNGN issuer's status (regulatory incubation, per secondary sources).
- Keep the hackathon to friends-and-family pilots, and get a Nigerian lawyer's opinion before any public launch.

### Kill-criteria check
- **Safe contract in time:** writing and testing it is feasible (about 300 lines, Foundry unit, fuzz and invariant tests, plus Slither). **A professional audit is not possible in 15 days.** Mitigate with hard caps, immutability, and invite-only pilots.
- **Self usable on mainnet:** yes, the registry is live. **Nigerian passport support is unconfirmed.** Test it on day 1 of M1 with your passport.
- **Legal:** a grey zone, mitigated by the design above. You decide.

---

## 5. Plan

### Tracks
1. **Textile Stablecoin Corridors** (primary)
2. **Self Agent ID** (secondary)
3. **Altana** (hedge, only if a 2-hour M1 spike shows it doesn't break the Self identity binding)

### Milestones
| Milestone | Dates | Content | Stop for |
|---|---|---|---|
| M1 Identity & plumbing | Nov 21–24 | pnpm monorepo, typed config, Zod env, pino logs, Neon, `/health`. Attribution module with the Loops code. ERC-8004 (reference registry) + Self Agent ID registration from your phone. Textile preview client. One paid x402/buy call. Self user-verification spike. Altana spike | **Approval before the first mainnet tx** |
| M2 Core loop | Nov 25–29 | `AjoFactory` + `AjoCircle` (Foundry, CI). Deploy after approval (~Nov 27). Agent scheduler, reminders, payout trigger. Textile convert-in flow. A bare-bones web page. You run one circle end to end | **Approval before contract deploy.** First pilot circle starts ~Nov 30 |
| M3 UX | Nov 30–Dec 2 | Next.js MiniPay mini app (create, join with deposit, pay in USD₮ or cNGN, receipts, Self verify). Telegram reminders. Kill switch and caps. Plain-language summaries | **Show on your phone** |
| M4 Real users | Dec 2–5 | 1 real weekly circle + 1–2 daily pilot circles. Onboarding guide and support checklist. Logs of every job, tx and paid call | **Approval before the first outside user** |
| M5 Submission | Dec 5–6 | README, architecture diagram, proof table, demo script, X thread. **Submit by Sun 6 Dec, 18:00 Lagos** | |

### File tree (planned)
```
contracts/                Foundry: src/AjoFactory.sol, src/AjoCircle.sol, test/, script/
apps/agent/               Node 22 worker + HTTP API (Render): config, env, log, db, chain,
                          tagging, policy, intents, textile, self, scheduler, reminders,
                          telegram, llm (Claude via Anthropic API), x402, health
apps/web/                 Next.js MiniPay mini app (Vercel)
packages/core/            Zod schemas, bigint amount helpers, ABIs, addresses, tag helper
docs/                     M0-report.md, architecture.md, onboarding.md
.github/workflows/        ci.yml (forge test + vitest), deploy.yml (workflow_dispatch, repo secret)
.env.example, README.md
```

### Cut list (in order)
1. x402 contribution-history API (Self stretch)
2. Altana executor: fall back to a plain EOA agent with a tiny balance and hard caps
3. Onchain Self member uniqueness: fall back to backend verification, or make it optional per circle
4. Slot swaps and arrears make-whole
5. Reputation score: keep the raw event history only
6. *Last resort:* USD₮-only circles with no Textile leg. This loses the main track, so it's no longer near the top of the cut list as it was in the brief

### Termux and desktop notes
- No step strictly needs a desktop.
- Foundry and Playwright don't run well on Termux, so I run them in the cloud and GitHub Actions runs CI.
- Contract deploys go through a GitHub Actions `workflow_dispatch` with the deployer key as a repo secret, so you can deploy from your phone. Never paste keys in chat.
- MiniPay testing must be on a real Android phone.
- Self registration needs the Self app and your passport.

### Open questions for you
1. **Do you have a Nigerian e-passport (chip logo on the cover), and has it worked in the Self app?** Self Agent ID depends on it.
2. Have you enrolled on Loops? I need your attribution code. It's not secret.
3. Can you name 1 real ajo group willing to run a weekly round on Ajo, plus 1–2 groups for daily pilots? Do they use MiniPay and hold USD₮?
   - **Members must fund their own wallets. Don't send them tokens or gas**, or they stop counting as independent users.
4. Is `Midas3007/0x_Ajo` public? The rules require it from registration.
5. Telegram for reminders? WhatsApp's Business API is too slow to set up.
6. Is Render + Vercel + Neon OK? Do you have an Anthropic API key? (Set it as an env var on the host, never in chat.)

### Questions for organizers (Telegram)
1. Submission format and where to submit.
2. Textile tagging: is keyless plus appending our tag to `swap.data` what counts, given Textile's docs say don't append? Do approval txs need the tag too?
3. Does a Self Agent ID registration satisfy the ERC-8004 rule, or is the reference registry required? (The plan is to do both.)
4. Do member-signed conversions inside a circle contribution flow count as "used in the product flow", and does a cNGN pot payout count as the local-stablecoin payment?
5. Self (Discord): are Nigerian passports supported?
6. Are tagged txs from a MiniPay mini app credited if the hostname isn't on MiniPay's approved list?

---

## Sources
- Rules: https://www.loops.house/agents-open-for-business · previous edition: https://www.loops.house/agents-on-open-rails
- Attribution: https://github.com/celo-org/attribution-tags (BUILDERS.md, INDEXERS.md)
- Textile: https://docs.textilecredit.com/api/v2/rfq.html · /api/v2/authentication.html · /protocol/address-book.html · /protocol/settlement.html · /fees.html · /api/v2/errors.html · /api/how-to-get-an-api-key.html · /testnet.html · live `POST /v2/rfq/preview` (2026-11-20)
- Self: https://docs.self.xyz/docs/agent-id/overview/ · https://docs.celo.org/build-on-celo/build-with-ai/self-agent-id · https://github.com/selfxyz/self-agent-id · https://registry.npmjs.org/@selfxyz/agent-sdk · https://docs.celo.org/build/build-with-self.md
- ERC-8004: https://docs.celo.org/build-on-celo/build-with-ai/8004
- Altana: https://docs.altana.network · /concepts/networks · /changelog · /sdk/x402 · https://xp.altana.network/rules · Celopedia PR https://github.com/celo-org/celopedia-skills/pull/114
- Agent Vault: https://docs.agentvault.io (FAQ, wallet, cards) · https://agentvault.io
- buy and x402: https://usebuy.ai/docs · https://registry.npmjs.org/@celo/buy · https://github.com/celo-org/buy-skill · https://x402.celo.org/skill.md
- MiniPay: https://docs.celo.org/build-on-celo/build-on-minipay/overview
- Prior art: https://www.karmahq.xyz/project/celosusu/about · https://ethglobal.com/showcase/esusu-5wa6a · https://ethglobal.com/showcase/ronda-7ck82 · https://github.com/OpeyemiMoses/Moi · https://github.com/ignaher12/textile-mm-agent
- cNGN on Celo: https://techcabal.com/2026/08/07/cngn-launches-on-celo-onchain-fx/
- Onchain checks: `eth_call decimals()` and `eth_getCode` via https://forno.celo.org (2026-11-20)
