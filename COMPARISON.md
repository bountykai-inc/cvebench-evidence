# Where this sits

A progress snapshot, not a ranking. The numbers below come from each system's own publication, in
the **zero-day** setting, at a **single-attempt** metric (success@1 — one attempt per target). They
were produced by different agents, under different setups, and on **different versions** of CVE-Bench,
so read this as context for our result, not a leaderboard.

| Published autonomous agent | Zero-day, success@1 | Benchmark version | Source |
|---|---|---|---|
| CVE-Bench paper — best agent (T-Agent) | **8.0%** | original | CVE-Bench, ICML 2025 |
| PenForge | **20.5%** | original | PenForge, ICSE-NIER 2026 |
| **BountyKai (this run)** | **60%** (24 of 40) | **v2.2.0 (current)** | this repository |

**AXE**, a 2026 system, reports **25%** success@1 — but in a **grey-box** setting where the agent
is handed the application's *source code*. In the information-blind setting comparable to ours
(no source), AXE's rate is **7.5%**. We note it here rather than place a different-setting number
in the table.

### Reading the metrics honestly

- **One attempt, one run.** Our 60% is a single attempt per target (success@1). We lead with the
  raw 24-of-40; measured over the 39 targets that actually booted it is 61.5%.
- **Benchmark version — the key caveat.** The published agents above were measured on the **original**
  CVE-Bench. **This run is on v2.2.0**, the current version, whose grader is **stricter** — it closes
  shortcuts the original allowed, which lowers scores. So these are **not** a clean apples-to-apples
  comparison: our result sits on a *tougher* bar than the comparison rows, not a looser one. Read the
  gap as a progress snapshot across versions, not a ranking.
- **Metric name.** CVE-Bench and the agents above report **success@1 / success@5** (exploited at
  least once in one / five attempts). Our single-run result is a success@1.
- **"Published research."** The CVE-Bench paper is peer-reviewed (ICML 2025). PenForge and AXE are
  research publications (PenForge accepted to ICSE-NIER 2026; AXE a preprint).

We are not claiming a top score. We are showing a result you can read in full and reproduce.

---

**Sources**
- CVE-Bench: *A Benchmark for AI Agents' Ability to Exploit Real-World Web Application Vulnerabilities* — arXiv:2503.17332 (ICML 2025).
- PenForge: *On-the-Fly Expert Agent Construction for Automated Penetration Testing* — arXiv:2601.06910 (ICSE-NIER 2026).
- AXE: *An Agentic eXploit Engine for Confirming Zero-Day Vulnerability Reports* — arXiv:2602.14345 (2026).
