<p align="center">
  <img src="loqate.png" alt="Loqate CLI" width="120">
</p>

<h1 align="center">Loqate CLI</h1>

<p align="center">
  Verify addresses, emails, and phone numbers against Loqate's APIs.<br>
  Get confidence scores, policy-aware recommendations, and full auditability — in one command.<br>
  Real-time verification decisioning for agents that need to know whether contact data is good enough for the job.<br>
  Part of <a href="https://agents.gbg.com/reach">GBG Reach</a>.
</p>

<p align="center">
  <code>lqt verify</code> &nbsp;·&nbsp; <code>lqt parse</code> &nbsp;·&nbsp; <code>lqt mcp</code>
</p>

---

## Install

### macOS

```bash
# Apple Silicon (M1/M2/M3/M4)
curl -sL https://github.com/gbgplc/lqt/releases/latest/download/lqt_darwin_arm64.tar.gz | tar xz
sudo mv lqt /usr/local/bin/

# Intel
curl -sL https://github.com/gbgplc/lqt/releases/latest/download/lqt_darwin_amd64.tar.gz | tar xz
sudo mv lqt /usr/local/bin/
```

### Linux

```bash
# x86_64
curl -sL https://github.com/gbgplc/lqt/releases/latest/download/lqt_linux_amd64.tar.gz | tar xz
sudo mv lqt /usr/local/bin/

# ARM64
curl -sL https://github.com/gbgplc/lqt/releases/latest/download/lqt_linux_arm64.tar.gz | tar xz
sudo mv lqt /usr/local/bin/
```

### Windows

1. Download `lqt_windows_amd64.zip` from the [latest release](https://github.com/gbgplc/lqt/releases/latest)
2. Extract `lqt.exe`
3. Move it to a directory on your `PATH`, or run it directly

### Verify Installation

```bash
lqt version
```

### All Downloads

See [Releases](https://github.com/gbgplc/lqt/releases) for all versions and platforms. Each release includes SHA-256 checksums.

| Platform | Archive |
|----------|---------|
| macOS (Apple Silicon) | `lqt_*_darwin_arm64.tar.gz` |
| macOS (Intel) | `lqt_*_darwin_amd64.tar.gz` |
| Linux (x86_64) | `lqt_*_linux_amd64.tar.gz` |
| Linux (ARM64) | `lqt_*_linux_arm64.tar.gz` |
| Windows (x86_64) | `lqt_*_windows_amd64.zip` |

---

## Quick Start

### API Keys

You need a **Loqate API key** for verification. Optionally, an **Anthropic API key** for the `parse` command.

**macOS / Linux:**
```bash
export LOQATE_API_KEY=your-key-here

# Optional — for lqt parse
export ANTHROPIC_API_KEY=your-key-here
```

**Windows (PowerShell):**
```powershell
$env:LOQATE_API_KEY="your-key-here"

# Optional — for lqt parse
$env:ANTHROPIC_API_KEY="your-key-here"
```

### Your First Verification

**macOS / Linux:**
```bash
# Verify an address
lqt verify --address "125 Summer Street, Boston, MA 02110, US"

# Verify address + email + phone with a policy
lqt verify -a "10 Downing St, London, GB" \
           -e "pm@gov.uk" \
           -p "+442071234567" \
           --policy shipping

# Parse and standardize without calling Loqate (uses Claude)
lqt parse --address "125 summer street boston ma 02110 us"
```

**Windows (PowerShell):**
```powershell
# Verify an address
.\lqt.exe verify --address "125 Summer Street, Boston, MA 02110, US"

# Verify address + email + phone with a policy
.\lqt.exe verify -a "10 Downing St, London, GB" -e "pm@gov.uk" -p "+442071234567" --policy shipping

# Parse and standardize without calling Loqate (uses Claude)
.\lqt.exe parse --address "125 summer street boston ma 02110 us"
```

---

## Commands

### verify

Verify addresses, emails, and/or phone numbers against Loqate's APIs. Returns a confidence score and a policy-driven recommendation (accept, review, or reject).

```bash
lqt verify [flags]
```

**Flags:**

| Flag | Short | Description |
|------|-------|-------------|
| `--address` | `-a` | Full address to verify |
| `--locality` | | City/town |
| `--admin-area` | | State/province |
| `--postcode` | | Postal/ZIP code |
| `--country` | `-c` | ISO 2-letter country code |
| `--detect-country` | | When no country is supplied, guess it from the address and flag the guess in the result. Address-only; off by default. |
| `--suggest` | | When the address does not clear the policy (`review` or `reject`), look up alternative addresses suggested by Loqate and return them under `address.suggestions`. Not called for an accepted address. **Requires a separately licensed Loqate feature enabled on your account.** Address-only; off by default; not available with `--batch`. |
| `--suggest-limit` | | Maximum suggestions to return, 1–10 (default 5). Requires `--suggest`. |
| `--suggest-below` | | Confidence floor for suggestions: an accepted address scoring below it still gets suggestions. Defaults to the active policy's value (standard 0.85). `0` disables the floor. Requires `--suggest`. |
| `--suggestion-id` | | Verify a suggestion the user chose, by its `id` from `address.suggestions`. Resolves the id to its cleansed components and verifies those. Use instead of `--address`. **Consumes a Loqate credit.** Not available with `--batch` or `--dry-run`. |
| `--email` | `-e` | Email address to verify |
| `--phone` | `-p` | Phone number (E.164 format) |
| `--key` | `-k` | Loqate API key (overrides env) |
| `--policy` | | Policy name: `strict`, `shipping`, `standard`, `permissive` |
| `--policy-file` | | Path to custom policy JSON file |
| `--batch` | `-b` | CSV/TSV/pipe-delimited file path (or `-` for stdin) |
| `--delimiter` | `-d` | Batch delimiter: `comma`, `tab`, `pipe` (auto-detected if omitted) |
| `--output` | `-o` | Output format: `json`, `jsonl`, `table` |
| `--summary` | `-s` | Show batch summary statistics |
| `--field` | | Extra Loqate input field `Key=Value` (repeatable) |
| `--option` | | Loqate API option `Key=Value` (repeatable, dot notation for nesting) |
| `--jsonl` | | JSON Lines output (one object per line) |
| `--no-color` | | Disable color output |
| `--verify-url` | | Custom address verification endpoint URL (overrides `LOQATE_VERIFY_URL` env var) |
| `--verify-key` | | Custom address verification API key (overrides `LOQATE_VERIFY_KEY` env var). When set, `--key` is not required for address-only verification. |
| `--verbose` | `-v` | Show reasoning log |

**Examples:**

```bash
# Simple address verification
lqt verify -a "1600 Amphitheatre Parkway, Mountain View, CA 94043, US"

# Multi-field verification with strict policy
lqt verify -a "221B Baker St, London, GB" \
           -e "sherlock@example.com" \
           -p "+442071234567" \
           --policy strict

# Email-only or phone-only (no address required)
lqt verify -e "sherlock@example.com"
lqt verify -p "+442071234567"

# Guess a missing country from the address (opt-in, address-only)
lqt verify -a "10 Downing St, London SW1A 2AA" --detect-country
# → result includes: country_guessed, detected_country, country_confidence

# Suggest alternatives when the address does not clear the policy (opt-in, address-only)
lqt verify -a "10 downin st london" -c GB --suggest
lqt verify -a "10 downin st london" -c GB --suggest --suggest-limit 3
# → result includes: address.suggestions with a list of candidate addresses

# Accepted but not confident: a street-level match accepted at 0.55 still gets alternatives
lqt verify -a "marsh wall, E14 9TN" -c GB --suggest
# Move or disable the confidence floor
lqt verify -a "marsh wall, E14 9TN" -c GB --suggest --suggest-below 0.95
lqt verify -a "marsh wall, E14 9TN" -c GB --suggest --suggest-below 0

# JSON output for piping to other tools
lqt verify -a "10 Downing St, London, GB" -o json | jq '.address.confidence'

# Extended input fields
lqt verify -a "125 Summer St" --field Organization="Acme Corp" --field Building="Suite 200"

# API options (dot notation for nesting)
# GeoCode is sent at the request root as a JSON boolean (where Loqate expects it);
# ServerOptions values are sent as strings (use Loqate's exact casing).
lqt verify -a "125 Summer St, Boston, MA 02110, US" --option GeoCode=true
lqt verify -a "125 Summer St, Boston, MA 02110, US" --option ServerOptions.OutputCasing=Upper
```

Full list of input fields and API options: [Loqate International Batch Cleanse API](https://docs.loqate.com/api-reference/address-verify/international-batch-cleanse)

#### Address suggestions (`--suggest`)

> **Licensing.** Suggestions use a Loqate feature that is **licensed separately from address
> verification** and must be enabled on your account. If it isn't, nothing breaks: the
> verification still returns its normal result and exit code, and the reason appears in
> `suggestions.error` (typically an unknown-key or licence message from Loqate). Talk to your
> Loqate account contact to have it enabled.

Verification tells you an address is wrong. Suggestions tell you what the right one probably
is. Add `--suggest` and `lqt` asks Loqate for real addresses matching what was typed, returning
them with the decision.

The lookup runs when either:

1. the address was **not accepted** (`review` or `reject`), or
2. the address was accepted but scored **below the policy's suggestion floor** (0.85 on
   `standard` — see the [Policies](#policies) table).

The second case is the one that catches near-misses. A policy accepts anything at or above its
minimum confidence, so *accepted* is not the same as *confident*: on `standard` the input
`marsh wall, E14 9TN` matches a street and scores exactly 0.55, which is accepted — yet the
house number is missing and better addresses exist. Use `--suggest-below` to move that line
(`--suggest-below 0` turns the confidence check off and only suggests on review/reject).

```json
{
  "address": {
    "verified_address": "Downing Street, London",
    "confidence": 0.5,
    "recommendation": "review",
    "suggestions": {
      "requested": true,
      "triggered": true,
      "reason": "accepted but confidence 0.55 is below the 0.85 floor",
      "floor": 0.85,
      "source": "loqate",
      "count": 2,
      "items": [
        {
          "id": "GB|RM|A|52509479",
          "type": "Address",
          "text": "10 Downing Street",
          "description": "London, SW1A 2AA",
          "address": "10 Downing Street, London, SW1A 2AA"
        },
        {
          "id": "GB|RM|A|52509480",
          "type": "Address",
          "text": "11 Downing Street",
          "description": "London, SW1A 2AB",
          "address": "11 Downing Street, London, SW1A 2AB"
        }
      ]
    }
  }
}
```

Things worth knowing:

- **Off by default**, so existing output is unchanged unless you ask for suggestions.
- **No lookup for a confidently accepted address** — you pay the extra latency only where it
  helps. The `suggestions` block is still returned with `"triggered": false` and a `reason`, so
  you can tell "no alternatives needed" from "suggestions were never requested".
- **`floor` reports the threshold that was applied**, so you can see why a lookup ran (or
  didn't) without knowing the policy's configuration.
- **Suggestions never change the decision.** If the lookup fails, the reason appears in
  `suggestions.error` and the verification result stands.
- **Address-only.** With `verify --email` / `--phone`, or `verify_contact`, only the address
  block is decorated.
- An entry with `"expandable": true` is a street, postcode, or building holding several
  addresses rather than one deliverable address. Search within it for a specific premise.
- Suggestion lookups do not consume Loqate verification credits, but the feature must be licensed on your account (see the note above).
- Suggestions need a standard Loqate key (`--key` / `LOQATE_API_KEY`); `--verify-key` alone
  covers address verification only.
- **Not available with `--batch`.** A large file would fan out into an unbounded number of
  lookups, so the combination is rejected. Verify the file, then re-run the flagged rows
  individually with `--suggest`.

Available on the MCP `verify_address` / `verify_contact` tools and the REST
`/v1/verify/address` / `/v1/verify/contact` endpoints as `suggest: true`, with optional
`suggest_limit` (1–10, default 5) and `suggest_below` (0–1).

#### Closing the loop: offer, choose, re-verify

**A suggestion is a candidate, not a verdict.** Items carry no confidence score, no match
level, and no AVC — they are what the reference data thinks the address might have been. Don't
write one into your system of record on faith. Close the loop in three steps:

1. **Verify with `--suggest`.** A non-empty `suggestions.items` means alternatives exist.
2. **Offer `items[].address` to whoever can decide** — the customer in a checkout or support
   flow, the agent in an automated one. That field is the ready-to-display line.
3. **Confirm the choice by its `id`** (`--suggestion-id`). *That* result — its confidence,
   match level, and standardized fields — is the one you keep. Full flow below.

```bash
# 1. verify, asking for alternatives
lqt verify -a "marsh wall, E14 9TN" -c GB --suggest -o json > result.json

# 2. show the candidates
jq -r '.address.suggestions.items[] | .address' result.json
#   1 Marsh Wall, London, E14 9TN
#   2 Marsh Wall, London, E14 9TN

# 3. re-verify the chosen one — this is the decision of record
lqt verify -a "1 Marsh Wall, London, E14 9TN" -c GB -o json
```

Over REST it's the same shape — one call with `suggest`, then a plain call with the choice:

```bash
curl -s -X POST https://your-host/v1/verify/address \
  -H "Authorization: Bearer $LOQATE_API_KEY" -H 'Content-Type: application/json' \
  -d '{"address":"marsh wall, E14 9TN","country":"GB","suggest":true}'

curl -s -X POST https://your-host/v1/verify/address \
  -H "Authorization: Bearer $LOQATE_API_KEY" -H 'Content-Type: application/json' \
  -d '{"address":"1 Marsh Wall, London, E14 9TN","country":"GB"}'
```

#### The full flow, end to end

1. **Verify with `--suggest`** — get candidates for an address that didn't come back clean.
2. **Offer `items[].address`** to whoever decides.
3. **Confirm with `--suggestion-id`**, using the chosen item's `id`. That result is the one you keep.

```bash
# 1 + 2
lqt verify -a "marsh wall london" -c GB --suggest -o json > r.json
jq -r '.address.suggestions.items[] | "\(.id)\t\(.address)"' r.json

# 3 — confirm the chosen one by id, not by re-typing the text
lqt verify --suggestion-id "GB|RM|B|55782678" -o json
```

Passing the **id** rather than the text is what makes step 3 accurate: `lqt` fetches that
address's cleansed components (company, sub-building, number, street, city, postcode, country)
and verifies those, so nothing is re-parsed. It matters for suggestions carrying a company name
— `London Lash, 56 Marsh Wall, London` — where re-reading the text can misplace the company as
part of the street.

The result records what it resolved from, so you can audit it without resolving again:

```json
{
  "verified_address": "56 Marsh Wall, London, E14 9TP",
  "confidence": 0.95,
  "recommendation": "accept",
  "resolved_from": {
    "suggestion_id": "GB|RM|B|55782678",
    "address": "London Lash, Unit 7, Hampton Tower, 56 Marsh Wall, LONDON, E14 9TP"
  }
}
```

Over REST, the same two steps:

```bash
curl -s -X POST https://your-host/v1/verify/address \
  -H "Authorization: Bearer $LOQATE_API_KEY" -H 'Content-Type: application/json' \
  -d '{"address":"marsh wall london","country":"GB","suggest":true}'

curl -s -X POST https://your-host/v1/verify/address \
  -H "Authorization: Bearer $LOQATE_API_KEY" -H 'Content-Type: application/json' \
  -d '{"suggestion_id":"GB|RM|B|55782678"}'
```

**What to know:**

- **Step 3 consumes a Loqate credit** to resolve the id (the search in step 1 does not). Resolve
  the one address that was chosen — not the whole list to compare.
- `suggestion_id` replaces `address`; sending both is a `400`.
- Not available with `--batch`, and not with `--dry-run` (resolving needs a live billable call).
- A stale id returns `SUGGESTION_NOT_FOUND` / `404`. Ids change over time — search again rather
  than retrying the same id.

**Just want the address, not a decision?** Resolve it on its own:

```bash
lqt retrieve --id "GB|RM|B|55782678"           # components as JSON
lqt retrieve --id "GB|RM|B|55782678" -o table  # human-readable
```

`GET /v1/address/{id}` over REST, or the `retrieve_address` MCP tool. This returns reference
data, **not** a verification — no confidence, no recommendation — so don't treat it as checked.

Four rules that keep the loop safe:

- **Omit `suggest` on the confirmation call.** If the chosen address still doesn't clear the
  policy you'd get a fresh set of suggestions, and an automated flow could bounce between them.
  One round of suggestions, then a plain verify.
- **Let the re-verify decide** — not the fact that a suggestion existed. If the confirmation
  returns `review` or `reject`, the record still needs a human. You've narrowed it, not fixed it.
- **Don't re-verify an `expandable` item as-is.** It's a street, postcode, or building, so
  verifying it lands at street level at best. Use it to ask for the missing piece ("which
  number on Marsh Wall?") and verify the completed address.
- **Never store `id`.** It's a Loqate-assigned identifier that changes over time. Key off the
  verified address returned in step 3.

Keep the country from the original request on the confirmation call — dropping it can change
the match.

#### Suggestion ranking comes from Loqate

`lqt` returns suggestions in the order Loqate provides them — no re-ranking or filtering is
applied. Loqate matches across the whole address record, **including organisation names**, so a
query like `marsh wall london` ranks businesses with "London" in their name above plain
residential addresses on that street:

```
London Lash, 56 Marsh Wall, London, E14 9TP
London Metropolis Ltd, 77 Marsh Wall, London, E14 9SH
```

That's standard address-autocomplete behaviour, but it means **suggestion quality tracks input
quality**. Where you know the country, pass it as `--country` rather than leaving it in the
address text — that moves the token out of the fuzzy match and into a filter:

```bash
lqt verify -a "marsh wall london" --suggest      # "london" is matchable text
lqt verify -a "marsh wall" -c GB --suggest       # cleaner: country is a filter
```


### parse

Parse and standardize contact data using Claude (Haiku). Extracts address components, validates email syntax, and normalizes phone numbers with awareness of 250+ country-specific postal formats. No Loqate API calls — no credits spent.

```bash
lqt parse [flags]
```

**Flags:**

| Flag | Short | Description |
|------|-------|-------------|
| `--address` | `-a` | Full address to parse |
| `--email` | `-e` | Email address to validate |
| `--phone` | `-p` | Phone number to normalize |
| `--country` | `-c` | ISO 2-letter country code hint |
| `--batch` | `-b` | CSV/TSV/pipe-delimited file path (or `-` for stdin) |
| `--delimiter` | `-d` | Batch delimiter: `comma`, `tab`, `pipe` (auto-detected if omitted) |
| `--output` | `-o` | Output format: `json`, `jsonl`, `table` |
| `--jsonl` | | JSON Lines output |
| `--no-color` | | Disable color output |
| `--anthropic-key` | | Anthropic API key (overrides env) |

**Examples:**

```bash
# Parse a messy address into structured components
lqt parse -a "125 summer street boston ma 02110 us"

# Parse address + validate email + normalize phone
lqt parse -a "10 downing st london" -e "test@mailinator.com" -p "02071234567"

# Batch parse from CSV
lqt parse --batch messy-data.csv --output json
```

### policy

List, inspect, and validate verification policies.

```bash
lqt policy list                  # List all built-in policies
lqt policy show <name>           # Show full JSON for a policy
lqt policy validate <file>       # Validate a custom policy JSON file
```

### mcp

Start an MCP (Model Context Protocol) server. Exposes LQT as tools for AI agents.

```bash
lqt mcp                    # Stdio transport (launched by clients)
lqt mcp --http :8080       # HTTP transport (deployed as a service)
lqt mcp --smoke-test       # Verify the server starts correctly and exit
```

**Flags:**

| Flag | Default | Description |
|------|---------|-------------|
| `--http` | | Listen address (e.g. `:8080`, `127.0.0.1:8080`) |
| `--rate-limit` | `10` | Max requests/sec per IP (`0` to disable) |
| `--rate-burst` | `20` | Max burst size for rate limiter |
| `--smoke-test` | | Self-test the MCP server (checks tools and prompts register) and exit |
| `--disable-custom-endpoint` | | Block per-request custom verify endpoint fields |
| `--rest` | | Also serve the REST API at `/v1` (HTTP mode only) |

---

### REST API

For clients that don't speak MCP, the HTTP server can also expose a plain REST API — opt-in with `--rest`, mounted at `/v1` on the same port. Same result data as the CLI and MCP.

```bash
lqt mcp --http :8080 --rest
```

**Authentication:** your Loqate API key as a bearer token — `Authorization: Bearer <LOQATE_API_KEY>` (or `key` in the request body).

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/v1/verify/address` | Verify an address (supports `detect_country`, `suggest`, `suggestion_id`) |
| `POST` | `/v1/verify/email` | Verify an email |
| `POST` | `/v1/verify/phone` | Verify a phone number |
| `POST` | `/v1/verify/contact` | Verify any combination + overall recommendation (supports `detect_country`, `suggest`) |
| `GET`  | `/v1/address/{id}` | Resolve a suggestion id to a cleansed address (**consumes a credit**) |
| `GET`  | `/v1/policies` | List decisioning policies |
| `GET`  | `/v1/policies/{name}` | Show one policy |
| `GET`  | `/v1/openapi.json` | OpenAPI 3.1 specification |
| `GET`  | `/v1/docs` | Interactive API reference |

A recommendation (`accept` / `review` / `reject`) is always returned as HTTP `200` with the decision in the body. Errors use standard status codes: `400` invalid input, `401` missing/invalid key, `429` rate limited, `502` upstream error.

```bash
curl -s -X POST https://your-host/v1/verify/address \
  -H "Authorization: Bearer $LOQATE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"address":"125 Summer St, Boston, MA 02110, US","policy":"standard"}'

# Ask for alternatives when the address does not clear the policy
curl -s -X POST https://your-host/v1/verify/address \
  -H "Authorization: Bearer $LOQATE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"address":"10 downin st london","country":"GB","suggest":true,"suggest_limit":5}'
```

Full request/response schemas are documented in the OpenAPI spec at `/v1/openapi.json` (browse it at `/v1/docs`).

---


### Explaining a result

Every address verification also returns detail that explains the score without changing it:

| Field | What it tells you |
|---|---|
| `matchscore` | 0-100, how closely the returned address resembles what you sent. Surfaced so you need not parse it out of `avc`. |
| `coverage_level` + `coverage_level_label` | The country's maximum Loqate verification level, as a number and a name: `5` `delivery_point`, `4` `premise`, `3` `street`, `2` `locality`. The label uses the same vocabulary as `match_level`, so when the two are equal the address matched as precisely as that country's data allows. |
| `coverage_boost` | The level shift applied because that country's data tops out below premise. This is the number that explains why a street-level match in a low-coverage country still scores highly. |
| `changes` | What Loqate altered, in words. **Opt-in — see below.** |

**To get `changes`, ask Loqate for its per-field status codes:**

```bash
lqt verify -a "125 Summer St, Boston, MA 02110, US" \
  --option ServerOptions.FieldStatus=true -o json
```

```json
"changes": [
  { "field": "country_name",            "change": "added",       "value": "United States", "code": "5" },
  { "field": "postal_code",             "change": "reformatted", "value": "02110-1634",    "code": "2" },
  { "field": "postal_code_secondary",   "change": "added",       "value": "1634",          "code": "5" },
  { "field": "sub_administrative_area", "change": "added",       "value": "Suffolk",       "code": "5" }
]
```

Nothing was wrong with that address; three things were **added** (ZIP+4, county, country name) and the
postcode was reformatted. Fields Loqate verified without touching are not listed, so a short list means
a clean address.

`change` is one of `added`, `corrected`, `reformatted` or `unrecognised`. `code` is Loqate's original
status code, kept so the categorisation is not lossy — the codes are Loqate's own, documented at
[docs.loqate.com/report-codes/fieldstatus](https://docs.loqate.com/report-codes/fieldstatus).

Absent `changes` means you did not ask for the codes. An empty array means you asked and nothing changed.
The option also adds a `<field>_status` key per component to `fields`, since that passthrough returns
everything Loqate sends.

This detail appears on **every** address verification — single, batch (per row), MCP and REST — because
all four go through the same result builder.

## Policies

Policies control what gets accepted, reviewed, or rejected. Every verification runs through a policy — there are no hardcoded thresholds.

| Policy | Addr Confidence | Match Level | Email Confidence | Phone Required | Suggest Below | Use Case |
|--------|:-:|:-:|:-:|:-:|:-:|------|
| **strict** | 0.90 | premise | 0.85 | yes | 0.90 | KYC, fraud prevention, regulated |
| **shipping** | 0.85 | street | 0.50 | no | 0.85 | Physical delivery, ecommerce |
| **standard** | 0.55 | street | 0.45 | no | 0.85 | General verification (default) |
| **permissive** | 0.30 | locality | 0.30 | no | 0.70 | Lead capture, early funnel |

**Suggest Below** is not an acceptance threshold — it never changes accept/review/reject. It is
the confidence below which `--suggest` looks up alternatives even for an *accepted* address.
See [Address suggestions](#address-suggestions---suggest). Custom policies can set their own
value, and the `recommend_policy` MCP tool proposes one for the use case you describe.

### Custom Policies

Create a JSON file:

```json
{
  "name": "my-custom-policy",
  "description": "Tuned for my use case",
  "address": {
    "min_confidence": 0.65,
    "min_match_level": "street",
    "reject_verification_status": ["U", "R"]
  },
  "email": {
    "min_confidence": 0.50,
    "allow_catch_all": true,
    "reject_disposable": true
  },
  "phone": {
    "min_confidence": 0.40,
    "required": false
  }
}
```

```bash
lqt policy validate my-policy.json    # Validate first
lqt verify -a "..." --policy-file my-policy.json
```

---

## Batch Processing

Process files with address, email, and phone columns. Supports comma, tab, and pipe delimited input.

```bash
# CSV (auto-detected)
lqt verify --batch addresses.csv --policy shipping -o json > results.json

# Tab-delimited
lqt verify --batch addresses.tsv -o json

# Pipe-delimited
lqt verify --batch addresses.txt --delimiter pipe -o json

# From stdin
cat addresses.csv | lqt verify --batch - --policy standard

# With summary statistics
lqt verify --batch addresses.csv --summary
```

**Windows (PowerShell):**
```powershell
.\lqt.exe verify --batch addresses.csv --policy shipping -o json > results.json
Get-Content addresses.csv | .\lqt.exe verify --batch - --policy standard
```

### Delimiters

Auto-detected from the first line. Override explicitly with `--delimiter`:

| Value | Aliases | Description |
|-------|---------|-------------|
| `comma` | `csv` | Comma-separated (default) |
| `tab` | `tsv` | Tab-separated |
| `pipe` | | Pipe-separated |

### Supported Columns

Use a single `address` column or structured fields — or both. All column names are case-insensitive. Every field from the [Loqate International Batch Cleanse API](https://docs.loqate.com/api-reference/address-verify/international-batch-cleanse) is supported.

**Address lines:**

| Field | Accepted column names |
|-------|----------------------|
| Address (line 1) | `address`, `address1`, `street`, `address_line_1` |
| Address lines 2-8 | `address2`-`address8`, `address_line_2`-`address_line_8` |
| Delivery address | `deliveryaddress`, `delivery_address` |
| Delivery lines 1-8 | `deliveryaddress1`-`deliveryaddress8`, `delivery_address_1`-`delivery_address_8` |

**Geography:**

| Field | Accepted column names |
|-------|----------------------|
| City | `city`, `locality`, `town` |
| State/Province | `state`, `admin_area`, `province`, `region`, `administrative_area` |
| County | `county`, `sub_admin_area`, `sub_administrative_area` |
| Postal code | `postcode`, `postal_code`, `zip`, `zipcode` |
| Country | `country`, `country_code` |

**Street / building / premise:**

| Field | Accepted column names |
|-------|----------------------|
| Street name | `thoroughfare`, `street_name` |
| Building | `building`, `building_name` |
| House number | `premise`, `house_number`, `building_number` |
| Apartment/Suite | `sub_building`, `apartment`, `suite`, `unit`, `flat` |

**Organization / postal:**

| Field | Accepted column names |
|-------|----------------------|
| Organization | `organization`, `organisation`, `company`, `company_name` |
| PO Box | `post_box`, `postbox`, `po_box`, `pobox` |

**Contact / person:**

| Field | Accepted column names |
|-------|----------------------|
| First name | `forename`, `first_name` |
| Last name | `surname`, `last_name` |
| Full name | `full_name`, `name` |

**Non-address fields:**

| Field | Accepted column names |
|-------|----------------------|
| Email | `email`, `email_address` |
| Phone | `phone`, `telephone`, `phone_number`, `mobile` |

### Example CSV

```csv
address,email,phone,country
"125 Summer St, Boston, MA 02110",user@example.com,+16175551234,US
"10 Downing St, London",pm@gov.uk,+442071234567,GB
```

---

## MCP Integration

The `lqt mcp` command exposes LQT as tools for AI agents via the [Model Context Protocol](https://modelcontextprotocol.io/). Connecting the MCP server also provides a built-in usage guide prompt that teaches the AI how to use the tools effectively.

### Claude Code / Cursor

Add to your project's `.mcp.json`:

```json
{
  "mcpServers": {
    "loqate": {
      "command": "lqt",
      "args": ["mcp"],
      "env": {
        "LOQATE_API_KEY": "your-key-here",
        "ANTHROPIC_API_KEY": "your-key-here"
      }
    }
  }
}
```

### Claude Desktop

Add to Claude Desktop's MCP settings:

```json
{
  "mcpServers": {
    "lqt": {
      "command": "/usr/local/bin/lqt",
      "args": ["mcp"],
      "env": {
        "LOQATE_API_KEY": "your-key-here",
        "ANTHROPIC_API_KEY": "your-key-here"
      }
    }
  }
}
```

### GBG-hosted endpoint (recommended)

The fastest way to use Loqate over MCP is the GBG-hosted endpoint — no install, no infrastructure. Point any MCP client at:

```
https://reach.prod.fabric.gbgplatforms.com/mcp
```

**Claude Code / Cursor / any MCP client** — `.mcp.json`:

```json
{
  "mcpServers": {
    "loqate": {
      "url": "https://reach.prod.fabric.gbgplatforms.com/mcp"
    }
  }
}
```

Restart your client, then ask it to "verify 125 Summer St, Boston, MA 02110, US". It will discover the tools, call them, and explain the result.

**Authenticate with your Loqate API key — pick one:**

1. **Per-call** — pass `key` in the tool arguments (`{"key": "YOUR-KEY", "address": "..."}`).
2. **Connection-wide** — set `Authorization: Bearer <YOUR-LOQATE-KEY>` on the HTTP connection. Applies to every tool call.
3. **Out of band (Claude only)** — put `<loqate_api_key>YOUR-KEY</loqate_api_key>` in org / project / user instructions; the model injects it as `key` automatically.

If you supply none of the three, the server returns a `NO_API_KEY` error. The Bearer header only supplies the standard Loqate key; `verify_key` for a custom address-verify endpoint is separate.

**Sanity check from your terminal** (no client needed):

```bash
# List tools (no auth required)
curl -s -X POST https://reach.prod.fabric.gbgplatforms.com/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'

# Verify an address (Bearer auth)
curl -s -X POST https://reach.prod.fabric.gbgplatforms.com/mcp \
  -H 'Authorization: Bearer YOUR-LOQATE-KEY' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"verify_address","arguments":{"address":"125 Summer St, Boston, MA 02110, US"}}}'
```

Hosted deployments disable custom verify endpoints for security. If you receive a `CUSTOM_ENDPOINT_DISABLED` error, remove `verify_url` and `verify_key` from your tool arguments.

### GBG-hosted REST endpoint

Prefer plain REST? The same hosted service also exposes the [REST API](#rest-api) — no MCP client required. Authenticate with your Loqate API key as a bearer token.

```bash
# Verify an address
curl -s -X POST https://reach.prod.fabric.gbgplatforms.com/v1/verify/address \
  -H 'Authorization: Bearer YOUR-LOQATE-KEY' \
  -H 'Content-Type: application/json' \
  -d '{"address":"125 Summer St, Boston, MA 02110, US","policy":"standard"}'

# Browse the interactive API reference (no key required)
open https://reach.prod.fabric.gbgplatforms.com/v1/docs
```

The OpenAPI spec (`/v1/openapi.json`) and reference page (`/v1/docs`) are unauthenticated, so you can explore the full API before you have a key.

### Remote HTTP (self-hosted)

If you'd rather run the server yourself, deploy `lqt mcp --http` as a service:

```json
{
  "mcpServers": {
    "loqate": {
      "url": "https://lqt.your-company.com/mcp"
    }
  }
}
```

Same three-tier key resolution applies (body `key` → `Authorization: Bearer` → server env `LOQATE_API_KEY`).

### Available Tools

10 tools in stdio mode, 7 in HTTP mode (tools marked *stdio mode only* are not available over HTTP).

| Tool | Description |
|------|-------------|
| `verify_address` | Verify an address with confidence score and recommendation (supports `detect_country`, `suggest`, `suggestion_id`) |
| `verify_email` | Verify an email with risk level and recommendation |
| `verify_phone` | Verify a phone number with type/carrier and recommendation |
| `verify_contact` | Verify all fields together with overall recommendation (supports `detect_country`, `suggest`, `suggestion_id`) |
| `retrieve_address` | Resolve a suggestion id to a cleansed address (consumes a credit; not a verification) |
| `parse_address` | Parse and standardize an address via Claude (stdio mode only) |
| `list_policies` | List available decisioning policies |
| `show_policy` | Show details for a specific policy |
| `set_policy` | Register a custom policy (stdio mode only) |
| `recommend_policy` | Get a recommended policy for your use case, including its suggestion confidence floor (stdio mode only) |

---

## Exit Codes

Designed for scripting and CI/CD:

| Code | Meaning |
|:---:|---------|
| **0** | ACCEPT — all fields passed verification |
| **1** | REVIEW — manual review recommended |
| **2** | REJECT — verification failed |
| **3** | ERROR — missing key, invalid input, API failure |

**macOS / Linux:**
```bash
lqt verify -a "125 Summer St, Boston, MA 02110, US" --policy shipping -o json
case $? in
  0) echo "Ship it" ;;
  1) echo "Queue for review" ;;
  2) echo "Bad address" ;;
  3) echo "Something broke" ;;
esac
```

**Windows (PowerShell):**
```powershell
.\lqt.exe verify -a "125 Summer St, Boston, MA 02110, US" --policy shipping -o json
switch ($LASTEXITCODE) {
  0 { Write-Host "Ship it" }
  1 { Write-Host "Queue for review" }
  2 { Write-Host "Bad address" }
  3 { Write-Host "Something broke" }
}
```

---

## API Keys

### Loqate API Key (for `verify`)

**macOS / Linux:**
```bash
# Environment variable
export LOQATE_API_KEY=your-key-here

# Key file (add to .gitignore)
echo "your-key-here" > .loqate-key

# Per-command flag
lqt verify -a "..." --key your-key-here
```

**Windows (PowerShell):**
```powershell
# Current session
$env:LOQATE_API_KEY="your-key-here"

# Persistent (survives restarts)
[System.Environment]::SetEnvironmentVariable("LOQATE_API_KEY", "your-key-here", "User")

# Per-command flag
.\lqt.exe verify -a "..." --key your-key-here
```

### Anthropic API Key (for `parse`)

**macOS / Linux:**
```bash
export ANTHROPIC_API_KEY=your-key-here
```

**Windows (PowerShell):**
```powershell
$env:ANTHROPIC_API_KEY="your-key-here"
```

**Resolution order:** flag > environment variable > key file

### Custom Verify Endpoint (optional)

If you need to route address verification through a different endpoint (e.g., an on-premises or partner-hosted Loqate instance):

**macOS / Linux:**
```bash
# Environment variables
export LOQATE_VERIFY_URL=https://custom-verify.example.com/v1/batch
export LOQATE_VERIFY_KEY=your-custom-key

# Or use flags (override env vars)
lqt verify -a "..." --verify-url https://custom-verify.example.com/v1/batch --verify-key your-custom-key
```

**Windows (PowerShell):**
```powershell
$env:LOQATE_VERIFY_URL="https://custom-verify.example.com/v1/batch"
$env:LOQATE_VERIFY_KEY="your-custom-key"
```

**Resolution order:** flag > environment variable > default Loqate endpoint

When `--verify-key` is set, the standard `--key` / `LOQATE_API_KEY` is not required for address-only verification. If you also verify email (`-e`) or phone (`-p`), the standard key is still needed for those.

These flags only affect address verification. Email and phone always use the standard Loqate endpoints.

In MCP mode, clients can pass `verify_url` and `verify_key` per-request in `verify_address` and `verify_contact` tool inputs.

---

## Support

- **Bug reports & feature requests** — [open an issue](https://github.com/gbgplc/lqt/issues/new/choose)
- **Security vulnerabilities** — please email [labs@gbg.com](mailto:labs@gbg.com) instead of opening a public issue
- **General questions** — [start a discussion](https://github.com/gbgplc/lqt/discussions) or open an issue

When reporting a bug, please include:
- `lqt` version (`lqt --version`)
- OS and architecture (e.g., macOS ARM64, Linux x86_64)
- The command you ran (redact any API keys)
- Expected vs actual behavior

---

## License

Proprietary — see [LICENSE](LICENSE). Use requires an active Loqate subscription.

---

<p align="center">
  Built by <a href="https://www.gbg.com">GBG</a>.
</p>
