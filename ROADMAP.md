# Skill Vetting - Future Enhancements

**Current Status:** v1.0.0 published to ClawHub  
**Published:** 2026-02-02  
**ClawHub URL:** https://clawhub.com/eddygk/skill-vetting

---

## Current Capabilities

✅ Automated pattern scanner (40+ malicious patterns)  
✅ Malicious code pattern database  
✅ Step-by-step vetting workflow  
✅ Decision matrix for risk assessment  
✅ Example walkthrough (n8n skill)

## Limitations

⚠️ **Prompt Injection Detection:**
- Catches obvious patterns (HTML comments, role manipulation)
- Misses sophisticated semantic attacks
- Can't detect invisible unicode in actual rendered form
- No defense against natural-language instructions disguised as documentation

⚠️ **Manual Review Required:**
- Scanner is layer 1, human judgment is layer 2
- Semantic analysis still requires human reading
- No automated behavior-vs-documentation validation

---

## Planned Enhancements

### 1. Isolated Sub-Agent Analysis (HIGH PRIORITY)

**Concept:** Spawn disposable analysis sub-agents to protect the main agent from prompt injection.

**Architecture:**
```
User → Main Agent (Claude / Gemini / Antigravity / Groot)
           ↓
       Spawns Sub-Agent (isolated session)
           ↓
       Sub-Agent reads potentially malicious skill
           ↓
       Reports findings back
           ↓
       Main Agent validates report + makes decision
```

**Benefits:**
- If skill contains prompt injection → sub-agent compromised, not main agent
- Sub-agent can safely execute scripts in `/tmp` sandbox
- Deleted after analysis (no persistent infection)
- Can use cheaper/weaker model for cost optimization

**Challenges:**

1. **Trust Problem:** How do we know a compromised sub-agent isn't lying?
   
   **Solutions:**
   - Multi-agent consensus (spawn 3, require 2/3 agreement)
   - Structured output validation (verify JSON schema + spot-check claims)
   - Independent verification (main agent runs scanner separately)
   - Blind analysis (sub-agent doesn't know it's vetting a skill)

2. **Implementation Steps:**
   - [ ] Create sub-agent spawn workflow
   - [ ] Define JSON output schema for analysis reports
   - [ ] Build validation logic for sub-agent responses
   - [ ] Test with known-malicious examples
   - [ ] Attempt to trick sub-agent and verify detection
   - [ ] Add multi-agent consensus (3+ agents)
   - [ ] Document workflow in SKILL.md

**Prototype Command:**
```bash
sessions_spawn \
  --task "Analyze the skill at /tmp/skill-inspect. Run security scanner, read all code, check for prompt injection. Output findings as JSON with: scanner_exit_code, files_analyzed, suspicious_patterns, behavior_matches_docs (bool), recommendation (install/reject/investigate)." \
  --cleanup delete \
  --label "skill-analysis-$(date +%s)"
```

**Validation Checklist:**
- [ ] Verify JSON structure
- [ ] Independently run scanner on same files
- [ ] Compare file lists (ensure sub-agent analyzed everything)
- [ ] Spot-check suspicious_patterns claims
- [ ] Cross-reference with multi-agent consensus

---

### 2. Enhanced Scanner Capabilities

**Unicode Steganography Detection:**
- Detect actual invisible characters (not just escapes)
- Check for: U+200B, U+FEFF, U+2060, etc.
- Flag suspicious unicode ranges

**Semantic Analysis:**
- Keyword flagging: "execute", "override", "ignore", "must", "always"
- Instruction-like language detection
- Mismatch detection (claims vs. actual behavior)

**LLM-Based Analysis:**
- Optional: Use sub-agent to perform semantic code review
- Check if documentation matches implementation
- Detect instruction-like language in comments/docs

---

### 3. Behavior Validation

**Problem:** Skills can claim to do X but actually do Y.

**Solution:**
- Compare SKILL.md description with actual code behavior
- Check imports against stated purpose
- Verify network calls match documented APIs
- Flag file operations outside documented scope

**Implementation Ideas:**
- Static analysis of imports → map to purpose categories
- Network call extraction → validate against documentation
- File path analysis → ensure scope compliance

---

### 4. Community Trust Signals

**ClawHub Integration:**
- Display author reputation metrics on skill page
- Show GitHub account age + activity
- Aggregate community reports
- "Verified" badge for trusted publishers

**Peer Review:**
- Allow community members to submit analysis reports
- Track who's vetted which skills
- Build reputation scores for reviewers

---

### 5. Automated Testing

**Sandboxed Execution:**
- Run skill scripts in isolated container
- Monitor system calls, network activity, file operations
- Compare actual behavior to documented behavior
- Detect undocumented side effects

**Static Analysis Tools:**
- Integrate with existing security scanners (bandit, semgrep)
- AST-based Python analysis
- Dependency vulnerability scanning

---

## Defense in Depth Strategy

| Layer | Method | Catches |
|-------|--------|---------|
| 1 | Automated scanner | Obvious patterns, known bad code |
| 2 | Manual review | Semantic attacks, context-specific issues |
| 3 | Isolated sub-agent | Prompt injection (with validation) |
| 4 | Multi-agent consensus | Sophisticated attacks, lying agents |
| 5 | Community reporting | Real-world issues after installation |

---

## Research Questions

1. **Can we reliably detect when a sub-agent has been compromised?**
   - What validation techniques work?
   - How many agents needed for consensus?
   - What's the false positive rate?

2. **What's the attack surface of LLM-based semantic analysis?**
   - Can malicious skills trick the analyzer?
   - Are there adversarial examples?
   - What's the computational cost?

3. **How do we balance security vs. usability?**
   - Too strict → good skills rejected
   - Too loose → malicious skills slip through
   - Where's the sweet spot?

---

## Timeline

**Phase 1 (Current):** Basic scanner + manual workflow ✅  
**Phase 2 (Next):** Isolated sub-agent analysis prototype  
**Phase 3:** Enhanced scanner (unicode, semantic analysis)  
**Phase 4:** Behavior validation + automated testing  
**Phase 5:** Community trust signals + peer review  

---

## Success Metrics

- Number of malicious skills detected
- False positive rate (good skills flagged)
- False negative rate (bad skills missed)
- Community adoption (downloads, stars, reports)
- Security incidents prevented

---

## Notes from Development

- Built with skill-creator skill (2026-02-02)
- Tested on n8n skill (clean result)
- Published to ClawHub as first security skill
- Scanner tested on itself (correctly flagged pattern examples)

---

## Contributing Ideas

If you have ideas for improving skill vetting:
- Open an issue on ClawHub
- Submit analysis reports for skills you've vetted
- Share adversarial examples (responsible disclosure)
- Propose new detection patterns
