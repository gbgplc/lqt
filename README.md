<p align="center">
  <img src="loqate.png" alt="Loqate CLI" width="120">
</p>

<h1 align="center">Loqate CLI</h1>

<p align="center">
  Verify addresses, emails, and phone numbers against Loqate's APIs.<br>
  Get confidence scores, policy-aware recommendations, and full auditability — in one command.<br>
  Real-time verification decisioning for agents that need to know whether contact data is good enough for the job.
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
| `--email` | `-e` | Email address to verify |
| `--phone` | `-p` | Phone number (E.164 format) |
| `--key` | `-k` | Loqate API key (overrides env) |
| `--policy` | | Policy name: `strict`, `shipping`, `standard`, `permissive` |
| `--policy-file` | | Path to custom policy JSON file |
| `--batch` | `-b` | CSV/TSV/pipe-delimited file path (or `-` for stdin) |
| `--delimiter` | `-d` | Batch delimiter: `comma`, `tab`, `pipe` (auto-detected if omitted) |
| `--output` | `-o` | Output format: `json`, `table` |
| `--summary` | `-s` | Show batch summary statistics |
| `--field` | | Extra Loqate input field `Key=Value` (repeatable) |
| `--option` | | Loqate API option `Key=Value` (repeatable, dot notation for nesting) |
| `--jsonl` | | JSON Lines output (one object per line) |
| `--no-color` | | Disable color output |
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

# JSON output for piping to other tools
lqt verify -a "10 Downing St, London, GB" -o json | jq '.address.confidence'

# Extended input fields
lqt verify -a "125 Summer St" --field Organization="Acme Corp" --field Building="Suite 200"

# API options (dot notation for nesting)
lqt verify -a "125 Summer St, Boston, MA 02110, US" --option GeoCode=true
```

Full list of input fields and API options: [Loqate International Batch Cleanse API](https://docs.loqate.com/api-reference/address-verify/international-batch-cleanse)

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
| `--output` | `-o` | Output format: `json`, `table` |
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
```

**HTTP mode flags:**

| Flag | Default | Description |
|------|---------|-------------|
| `--http` | | Listen address (e.g. `:8080`, `127.0.0.1:8080`) |
| `--rate-limit` | `10` | Max requests/sec per IP (`0` to disable) |
| `--rate-burst` | `20` | Max burst size for rate limiter |

---

## Policies

Policies control what gets accepted, reviewed, or rejected. Every verification runs through a policy — there are no hardcoded thresholds.

| Policy | Addr Confidence | Match Level | Email Confidence | Phone Required | Use Case |
|--------|:-:|:-:|:-:|:-:|------|
| **strict** | 0.90 | premise | 0.85 | yes | KYC, fraud prevention, regulated |
| **shipping** | 0.70 | street | 0.50 | no | Physical delivery, ecommerce |
| **standard** | 0.55 | street | 0.45 | no | General verification (default) |
| **permissive** | 0.30 | locality | 0.30 | no | Lead capture, early funnel |

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

The `lqt mcp` command exposes LQT as tools for AI agents via the [Model Context Protocol](https://modelcontextprotocol.io/).

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

### Remote HTTP

Deploy `lqt mcp --http` as a service:

```json
{
  "mcpServers": {
    "loqate": {
      "url": "https://lqt.your-company.com/mcp"
    }
  }
}
```

In HTTP mode, clients can pass API keys per-request via the `key` field in tool arguments.

### Available Tools

| Tool | Description |
|------|-------------|
| `verify_address` | Verify an address with confidence score and recommendation |
| `verify_email` | Verify an email with risk level and recommendation |
| `verify_phone` | Verify a phone number with type/carrier and recommendation |
| `verify_contact` | Verify all fields together with overall recommendation |
| `parse_address` | Parse and standardize an address via Claude |
| `list_policies` | List available decisioning policies |
| `show_policy` | Show details for a specific policy |
| `set_policy` | Register a custom policy (stdio mode only) |
| `recommend_policy` | Get a recommended policy for your use case (stdio mode only) |

---

## Confidence Scoring

Every address verification produces a confidence score between 0 and 1, computed from the AVC code returned by Loqate.

```
confidence = base(verification_status, post_match_level)
           × (1 - 0.5 × (1 - matchscore / 100))
           → capped at 0.99
```

| Scenario | Confidence |
|----------|:---:|
| Clean US address (V4, ms=100) | **0.95** |
| Messy UK address (P4, ms=100) | **0.70** |
| Fake/unverifiable address | **0.03** |
| German address (V4, ms=85) | **0.88** |
| Missing house number (P3, ms=100) | **0.55** |

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

---

<p align="center">
  Built by the <a href="https://www.loqate.com">Loqate</a> team at <a href="https://www.gbg.com">GBG</a>.
</p>
