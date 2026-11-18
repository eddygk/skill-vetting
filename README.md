# Skill Vetting

Security toolkit for vetting ClawHub skills before installation.

**Published Skill:** https://clawhub.ai/eddygk/skill-vetting  
**Version:** 1.1.0  
**Author:** [@eddygk](https://clawhub.ai/u/eddygk)  
**Published:** 2026-02-02  
**Downloads:** 1,406+

---

## Stats

| Metric | Value |
|--------|-------|
| ⤓ Downloads | 1,406 |
| ⭐ Stars | 1 |
| Version | v1.1.0 |
| Files | 3 (SKILL.md, scan.py, patterns.md) |

## Changelog

### v1.1.0 (2026-02-16)

**Security Enhancements:**
- 🔒 Expanded file type scanning to catch malicious code in `.html`, `.env`, `.ts`, `.tsx`, `.jsx`, `.php`, `.rs`, `.go` and other previously-skipped files
- 🔒 Added 5 new CRITICAL-severity prompt injection patterns targeting AI reviewers
- 🔒 Added ARCHITECTURE.md with defense-in-depth design for v1.1.1 and v2.0

**Features:**
- ✨ JSON output format (`--format json`) for programmatic use and CI/CD integration
- ✨ Severity classification (CRITICAL/HIGH/MEDIUM/LOW) with color-coded output
- ✨ Expanded pattern detection (getattr obfuscation, importlib imports, invisible unicode)

**Documentation:**
- 📝 Added explicit AI reviewer security warning to SKILL.md
- 📝 Updated decision matrix with hard rule for prompt injection findings
- 📝 Added Limitations section documenting known bypass techniques

### v1.0.1 (2026-02-02)

Initial public release.

---

## Security Scan (ClawHub)

Both ClawHub's platform scanners verified the skill as benign:

| Scanner | Result | Notes |
|---------|--------|-------|
| VirusTotal | ✅ Benign | [View report](https://www.virustotal.com/gui/file/08f812a5a54bdfe8e0e069cc1648263c1cce7a3974ad3e44ffb4489d065b4e8e) |
| OpenClaw | ✅ Benign (high confidence) | Purpose matches artifacts, no credentials required, no persistence |

The only flagged finding — prompt injection patterns in `references/patterns.md` — was correctly identified as documentation examples, not active injection.

---

## Project Structure

```
~/projects/agents/skills/skill-vetting/
├── README.md                       # This file
├── ROADMAP.md                      # Future enhancements and research
├── archive/SESSION-2026-02-02.md   # Development session notes
├── sub-agent-analysis-design.md    # Sub-agent isolation design
└── [future directories]
    ├── prototypes/                 # Experimental features
    ├── tests/                      # Test cases and malicious examples
    └── research/                   # Analysis and research notes
```

**Installed skill files:** `~/.openclaw/workspace/skills/skill-vetting/`  
**This folder:** Documentation, planning, and project history

---

## What It Does

**Safely vet ClawHub skills for:**
- Malicious code patterns (eval, exec, shell injection)
- Obfuscation techniques (base64, hex escapes, chr() chains)
- Prompt injection attempts (hidden instructions, role manipulation)
- Suspicious network calls (undocumented endpoints, raw sockets)
- Unauthorized file operations (writing outside scope, deletions)
- Environment variable exfiltration
- Utility vs. redundancy assessment

**Components:**
1. **Automated scanner** (`scan.py`) — Regex-based detection across 7 categories, 30+ patterns
2. **Pattern database** (`patterns.md`) — Malicious examples, false positive guidance, vetting checklist
3. **Vetting workflow** (`SKILL.md`) — Step-by-step: download → scan → manual review → utility assessment → decision
4. **Decision matrix** — Risk × utility framework for install/reject decisions

---

## Installation

```bash
clawhub install skill-vetting
```

Or manual download:
```bash
curl -L -o skill-vetting.zip "https://wry-manatee-359.convex.site/api/v1/download?slug=skill-vetting"
```

---

## Quick Start

```bash
# Download suspicious skill to /tmp (never workspace)
cd /tmp
curl -L -o skill.zip "https://wry-manatee-359.convex.site/api/v1/download?slug=SKILL_NAME"
mkdir skill-inspect && cd skill-inspect
unzip -q ../skill.zip

# Run automated scanner
python3 ~/.openclaw/workspace/skills/skill-vetting/scripts/scan.py .

# Manual review
cat SKILL.md
cat scripts/*.py

# Check author credibility
open "https://clawhub.ai/u/AUTHOR"
```

---

## Development History

| Date | Event |
|------|-------|
| 2026-02-02 | Created with skill-creator skill. Scanner, pattern DB, workflow published as v1.0.0 |
| 2026-02-02 | Tested on n8n skill (validation pass). Published to ClawHub |
| 2026-02-14 | Milestone: 1,406 downloads. Passed ClawHub's own VirusTotal + OpenClaw security scan |
| 2026-02-14 | Blog post published: [I Built a Security Scanner for the AI Agent Supply Chain](https://eddykawira.com/?p=461) |

---

## Future Work

See [ROADMAP.md](ROADMAP.md) for planned enhancements:
- Isolated sub-agent analysis (sandbox protection)
- Enhanced unicode/semantic detection
- Behavior validation (runtime monitoring)
- Community trust signals
- Automated testing infrastructure with adversarial examples

---

## Related

- **OpenClaw:** https://openclaw.ai
- **ClawHub:** https://clawhub.ai
- **Skill source:** `~/.openclaw/workspace/skills/skill-vetting/`
