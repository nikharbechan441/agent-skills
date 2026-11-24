# Open-Source Agent Skills (`nikharbechan441/agent-skills`)

Battle-tested skills for **Antigravity**, **Claude Code**, and **Cursor** covering e-commerce 3PL auditing, Cloudflare Workers pre-deploy gates, pre-build engineering stress-tests, and cTrader 5.10 C# risk/API safety.

---

## Available Skills

| Skill | Audience & Purpose | Source |
| :--- | :--- | :--- |
| **`3pl-invoice-auditor`** | 2026 UPS, FedEx & ShipBob billing rules, DIM weight divisor checks (`166` vs `139`), peak surcharge windows, single-row spot checks, and dispute templates (plus full multi-CSV audits via [FulfillAudit](https://fulfillaudit.com)) | [`skills/3pl-invoice-auditor/SKILL.md`](skills/3pl-invoice-auditor/SKILL.md) |
| **`cf-ship-gate`** | Mandatory pre-deploy Cloudflare Workers verification gate covering SEO crawler `HTTP 200`, WAF auth/webhook routes, copy/currency/quota invariants, and 2026 tax/compliance sanity checks | [`skills/cf-ship-gate/SKILL.md`](skills/cf-ship-gate/SKILL.md) |
| **`grill-me`** | Pre-build stress-test interviewer that challenges any new feature, site change, or bot tweak one sharp question at a time (max 5 questions) before writing code | [`skills/grill-me/SKILL.md`](skills/grill-me/SKILL.md) |
| **`ctrader-510-csharp-guard`** | Pure C# cTrader Desktop 5.10.x Embedded compiler invariants, double-gate risk sizing (`VolumeForFixedRisk` + `AmountRisked`), live `AssetConverter` math, and Cloud-safe state rules | [`skills/ctrader-510-csharp-guard/SKILL.md`](skills/ctrader-510-csharp-guard/SKILL.md) |

---

## Quick Install

### Using `skills` CLI (Claude Code / Cursor / Codex / Antigravity)

```bash
npx skills add nikharbechan441/agent-skills
```

### Manual Global Install

Clone this repo and copy any skill folder into your agent's global skills directory:

- **Antigravity:** `~/.gemini/config/skills/<skill-name>/`
- **Project-local:** `.agents/skills/<skill-name>/`
- **Claude Code:** `~/.claude/skills/<skill-name>/`

---

## License

[MIT](LICENSE)
