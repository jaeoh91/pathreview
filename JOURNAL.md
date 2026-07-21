# PathReview Module 3 Journal

Working branch: `test/71-prompt-injection-red-team`  
Fork: https://github.com/jaeoh91/pathreview

---

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/71

**Issue title:** Implement a red-teaming test suite for the prompt injection defense

**Tier:** [ ] Tier 1  [ ] Tier 2  [x] Tier 3

**Problem summary:**
PathReview’s safety layer already has a `PromptDefense` class (`safety/prompt_defense.py`) that detects common injection patterns (role switching, separators, template delimiters, ignore/override instructions, code-execution-looking calls) and a basic `sanitize()` helper. Unit tests under `tests/unit/test_prompt_defense.py` exercise those regexes one at a time, but there is no curated red-team test suite, no `tests/security/` suite that loads attack fixtures, and no CI job that re-runs injection checks whenever `safety/` changes. 
A successful fix adds a fixture set under `tests/fixtures/injection_attempts/`, a security test module that asserts every curated attack is blocked, and a GitHub Actions path filter that PRs touching `safety/` must pass.

**Branch name:** `test/71-prompt-injection-red-team`

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:**  [x] Issue added to cohort ledger

### Selection notes — “Is this right for me?”

- **Why this issue (personal fit):** I have a background & interest in AI safety and have taken a course on red-teaming AI applications, so a security-focused Tier 3 issue is a deliberate stretch that matches skills I want to practice.

- **Why this addition matters for PathReview:** The product pipeline is “ingest untrusted user content (resumes, GitHub profiles, repo READMEs) → agent/RAG → LLM-written career feedback.” Prompt injection is therefore a core product risk: a crafted resume or README can try to override system instructions, bias the review, or otherwise manipulate model behavior. PathReview already ships `PromptDefense` in the safety layer, but a defense without a living attack corpus drifts—today’s unit tests poke individual regexes, while real attacks combine separators, role switches, and “ignore previous instructions” phrasing. A curated red-team suite makes those known attacks fixtures that CI re-runs whenever `safety/` changes, so a well-meaning refactor can’t silently weaken the gate. For an app that advises people based on personal documents, robust safety tests such as those implemented in this issue are critical.

- **Scope I understand:**
  1. Curate mock prompt-injection fixtures under `tests/fixtures/injection_attempts/`
  2. Write `tests/security/test_prompt_injection.py` that loads them and asserts `PromptDefense` blocks them (`is_injection_attempt` and/or sanitize+detect)
  3. Extend `.github/workflows/ci.yml` so PRs that touch `safety/` run that suite

