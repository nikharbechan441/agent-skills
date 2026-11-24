---
name: cf-ship-gate
description: Mandatory pre-deploy verification gate for Cloudflare Workers web apps. Enforces search crawler HTTP 200 checks, WAF auth and webhook checks, copy/currency/quota invariants, and 2026 tax and privacy compliance before marking any deploy complete.
---

# Cloudflare Workers Pre-Deploy Ship Gate (`cf-ship-gate`)

Run ALL 4 verification gates before declaring any Cloudflare Workers web app deployment complete.

---

## Gate 1 — SEO & Crawler Accessibility (`HTTP 200`)
Confirm that search engine crawlers and browsers are never blocked by Cloudflare Bot Fight Mode or Super Bot Fight Mode (`403` / JS challenge):
- `GET /` -> `HTTP 200` (verify real HTML, no challenge page)
- `GET /robots.txt` -> `HTTP 200`
- `GET /sitemap.xml` -> `HTTP 200` (valid XML, active canonical routes)
- `GET /privacy` -> `HTTP 200`
- `GET /terms` -> `HTTP 200`

---

## Gate 2 — Auth, Checkout & Webhook WAF Gate (`Zero 403s`)
Verify every interactive API route passes Cloudflare WAF cleanly:
- `POST /api/auth/*` reaches the Worker/origin handler cleanly without a Cloudflare WAF `403` block.
- Payment checkout creation & webhook verification routes pass WAF (returning expected `200`/`400`/`401`, never WAF `403`).
- Offline/EFT fallback downloads or receipt endpoints return valid downloadable payloads with no broken routes or `5xx` errors.

---

## Gate 3 — Copy, Math, Currency & Quota Consistency
- **Zero Rule-Count Contradictions:** Landing copy, pricing cards, badges, and schema metadata must match the exact number of rules implemented in code.
- **Zero Currency / Unit Leaks:** Localized or Metric modes must never display mismatched currency symbols (e.g., Metric view showing `$0.00`). Verify client display matches server pricing tables.
- **Zero Broken Query Params:** Check region/locale/filter parameters (e.g., `?region=UK` vs `?region=GB`) resolve cleanly without missing pricing or `404` errors.
- **Sample/Demo Data Never Burns Quotas:** Loading or running built-in sample/demo modes must **never** decrement or exhaust free trial user counters.
- **Realistic Demo Math:** Sample savings or percentages must reflect realistic ranges cleanly and avoid inflated placeholder math.

---

## Gate 4 — Privacy & Tax Compliance Gate
- **Statutory Disclosures:** Privacy Policy, Terms, and required supplier/operator contact disclosures must be live and reachable.
- **VAT Registration Integrity:** **NEVER** print "VAT", VAT line-item splits, or "Tax Invoice" labels unless backed by a valid registered VAT number on file.
- **2026 US Tax Thresholds:** **NEVER** cite outdated `$600` US `1099-NEC` thresholds—use the **2026 `$2,000` OBBBA threshold**.

---

## Quick Verification Script (Run After Deploy)
```powershell
$base = "https://<your-domain>"
foreach ($path in @("/", "/robots.txt", "/sitemap.xml", "/privacy", "/terms")) {
    $res = Invoke-WebRequest -Uri "$base$path" -UseBasicParsing -SkipHttpErrorCheck
    "{0} -> HTTP {1}" -f $path, $res.StatusCode
}
```
If ANY gate fails, mark the release **BLOCKED** and fix + re-test before announcing launch.
