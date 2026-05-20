# Lintmus

**Token due diligence for the bankr.bot ecosystem.**

[lintmus.com](https://lintmus.com) · [API Docs](#direct-api) · [Install as a skill](#install-as-a-bankrbot-skill)

Lintmus runs a multi-source safety analysis on any EVM or Solana token and returns a 0–100 score with a full breakdown — before you trade, before you ape, before you launch.

---

## What it checks

| Check | Source |
|-------|--------|
| Honeypot detection | GoPlus Security + honeypot.is (cross-validated) |
| Buy / sell tax (scored separately) | GoPlus Security + honeypot.is |
| Mint function, hidden owner, pausable transfers | GoPlus Security |
| LP lock status | GoPlus Security |
| Liquidity, volume, age | DexScreener |
| Wash trading detection (vol/liq ratio) | DexScreener |
| Active pair count across DEXes | DexScreener |
| Contract verification | Etherscan API V2 |
| Deployer address & history | Alchemy |
| Solana deep scan (holder concentration, mint/freeze authority) | Rugcheck.xyz |

---

## Example report

```
Lintmus Report — 0x532f...142e4 (base)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Safety Score:      84 / 100  ✓ Low Risk
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Honeypot           No ✓  (GoPlus + honeypot.is)
Buy / Sell Tax     0% / 0% ✓
Mintable           No ✓
Hidden Owner       No ✓
Transfer Pausable  No ✓
LP Locked          72% ✓
Liquidity          $284,310 ✓
Volume (24h)       $41,200
Wash Trading       No ✓  (vol/liq 0.14×)
Active Pairs       8 ✓
Age                312h (13d) ✓
Contract           Verified ✓
Deployer           0x46f6...2b45
Deployer Rugs      0 ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Verdict: Low risk. No major red flags detected.
Price: 0.03 USDC
```

---

## Install as a bankr.bot skill

In any agent that supports the SKILL.md format (Claude Code, OpenClaw, Cursor):

```
install the lintmus skill from https://github.com/lintmus/lintmus
```

Then ask your agent:

```
analyze token 0x532f27101965dd16442e59d40670faf5ebb142e4 on base
```

```
is this safe to buy? 0xADDRESS on ethereum
```

```
run lintmus before I ape into this
```

---

## Direct API

```bash
curl -X POST https://x402.bankr.bot/0xD3aDb4D4B787eE631A1B2618464e21B229873075/analyze \
  -H "Content-Type: application/json" \
  -d '{"address": "0x532f27101965dd16442e59d40670faf5ebb142e4", "chain": "base"}'
```

Payment of **0.03 USDC** is handled automatically via the [x402 protocol](https://x402.org). Reports are cached for 60 minutes.

---

## Supported chains

`base` · `eth` · `polygon` · `solana`

---

## How scoring works

Lintmus starts at 100 and applies penalties and bonuses:

| Signal | Impact |
|--------|--------|
| Honeypot confirmed (both sources) | −90 |
| Honeypot confirmed (one source) | −80 |
| Sell tax > 30% | −40 |
| Sell tax 15–30% | −25 |
| Rugcheck danger (Solana) | −30 |
| Hidden owner | −20 |
| Can reclaim ownership | −15 |
| Top 10 holders > 80% | −15 |
| Known rug deployer | −15 per rug |
| Rugcheck warn (Solana) | −15 |
| Wash trading (vol/liq > 10×) | −15 |
| Transfer pausable | −10 |
| Mintable supply | −10 |
| Creator holds > 20% | −10 |
| Buy tax > 20% | −10 |
| Dangerously low liquidity (< $1K) | −10 |
| Token age < 1 hour | −10 |
| Wash trading (vol/liq 5–10×) | −8 |
| Buy tax > 10% | −5 |
| Liquidity > $100K | +10 |
| LP locked > 80% | +5 |
| 5+ active trading pairs | +5 |
| Contract verified | +5 |
| Token age > 1 month | +5 |
| 3+ active trading pairs | +3 |
| Token age > 1 week | +3 |

Scores are capped at 0–100. Missing data scores neutral — never inflated.

---

## Data sources

- [GoPlus Security](https://gopluslabs.io) — honeypot, tax, ownership flags
- [honeypot.is](https://honeypot.is) — independent honeypot cross-validation
- [Rugcheck.xyz](https://rugcheck.xyz) — Solana deep scan
- [DexScreener](https://dexscreener.com) — liquidity, volume, pair count, age
- [Etherscan API V2](https://etherscan.io) — contract verification
- [Alchemy](https://alchemy.com) — deployer address lookup

---

## The rug database

Every token analyzed by Lintmus is logged. Outcomes (rug, alive, abandoned) are recorded as they happen and published openly so anyone can verify the scoring methodology and track historical accuracy.

This dataset grows with every report and forms the foundation for future ML-based scoring improvements.

---

## Token

**$LINTMUS** on Base — trading fees from the token fund compute costs and ongoing development.

---

## License

MIT
