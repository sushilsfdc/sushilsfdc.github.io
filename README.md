Salesforce Technical Architect — 15+ years on the platform. This page is a map of what I've built in the open.

[GitHub @sushilsfdc](https://github.com/sushilsfdc) · [GitHub @sushilgit](https://github.com/sushilgit) · [Medium](https://medium.com/@sfdcsushil) · [LinkedIn](https://linkedin.com/in/sfdcsushil) · [Trailblazer](https://www.salesforce.com/trailblazer/sfdcsushil)

---

## AI Agents & Generative AI on Salesforce

### [AuditAgentHackathon](https://github.com/sushilsfdc/AuditAgentHackathon) — "Agent for Setup"
An Agentforce agent that lets admins interrogate their org's security posture in natural language — questions like *"Does Benjamin have access to Opportunity?"* or *"Who has the ManageUsers permission?"*. The agent plans across a set of security-focused topics (user access, audit history, license usage), pulls the underlying data through Apex, and summarizes it back in plain English rather than dumping raw records — with guardrails that make it ask for clarification when a query is ambiguous instead of guessing.

*Stack: Agentforce, Apex, SOQL.*

### [dreamforce2025](https://github.com/sushilsfdc/dreamforce2025) — Generative AI Answers from Chatter Data
Reference implementation from my **Dreamforce 2025 session**: an end-to-end RAG pipeline that grounds AI answers in a community's own Chatter Q&A history. Historical questions and answers are ingested into Data Cloud, transformed into a searchable vector index, and used to ground a generative prompt that answers new questions — surfaced to end users through an Experience Cloud component, with a Flow-based fallback for reliability.

*Stack: Data Cloud, Prompt Builder, Apex, LWC, Flow.*

### [EinsteinAgents](https://github.com/sushilsfdc/EinsteinAgents) — Einstein Agent & Prompt Experiments
Earlier-generation experiments that fed into the two projects above: an Einstein Copilot agent that summarizes a user's Setup Audit Trail on request (written up in [Building an Agent for Salesforce Admins](https://medium.com/@sfdcsushil/building-agent-for-salesforce-admins-c1bc8b4f4d89)), and a prompt-guarded case creation flow that screens submissions for PII before they're saved ([write-up](https://medium.com/@sfdcsushil/941d56b7829e)).

*Stack: Einstein Copilot, Prompt Builder, Apex, LWC.*

---

## CI/CD & Code Quality

### [SFCodeAnalyzerWithStandardRules](https://github.com/sushilsfdc/SFCodeAnalyzerWithStandardRules) — PR gate with built-in rules
GitHub Actions workflows that run Salesforce Code Analyzer against every pull request and block the merge on violations, scanning either newly added files or all changed files. Fails the build past a violation threshold and publishes the results as a build artifact. [Sample blocked PR](https://github.com/sushilsfdc/SFCodeAnalyzerWithStandardRules/pull/1).

### [SFCodeAnalyzerWithCustomRules](https://github.com/sushilsfdc/SFCodeAnalyzerWithCustomRules) — PR gate with your own rules
Same pipeline, driven by custom PMD and ESLint rulesets instead of the defaults, for teams enforcing their own standards rather than out-of-the-box ones. Ships with intentionally bad fixtures to prove the gate actually fires. [Sample blocked PR](https://github.com/sushilsfdc/SFCodeAnalyzerWithCustomRules/pull/2).

*Stack: GitHub Actions, Salesforce CLI, sfdx-scanner, PMD, ESLint.*

---

## Admin & Metadata Tooling (Java)

Standalone desktop tools for Salesforce admins and architects doing security reviews, org comparisons, and migrations. Each reads metadata exported from an org, compares it, and writes a formatted Excel report — run via a `.bat` file, no install required.

### [ProfileComparison](https://github.com/sushilgit/ProfileComparison) ⭐ 5
Diffs two Profiles and produces a multi-tab workbook covering object permissions, field-level security, layout assignments, record types, Apex/VF access, and more — everything needed for a thorough access-review or org-merge comparison.

### [LayoutComparisonTool](https://github.com/sushilgit/LayoutComparisonTool) ⭐ 1
Diffs two page layouts side by side and highlights the fields unique to each, for reconciling layouts across record types or orgs.

### [ExportPermissionSet](https://github.com/sushilgit/ExportPermissionSet)
Flattens a Permission Set into a reviewable Excel sheet for access audits.

### [ProfileExporter](https://github.com/sushilgit/ProfileExporter)
Same idea, for Profiles — exports to Excel for review outside Setup.

---

## Writing

Longer write-ups on the ideas behind these repos live on [Medium](https://medium.com/@sfdcsushil):
- Salesforce Audit Agent with **Google ADK + Salesforce MCP** — natural-language auditing with zero hardcoded SOQL
- Data Cloud RAG patterns — Chatter grounding and a release-notes analysis agent
- [Building an Agent for Salesforce Admins](https://medium.com/@sfdcsushil/building-agent-for-salesforce-admins-c1bc8b4f4d89)
- [Connecting to external APIs with External Credentials + custom headers](https://medium.com/@sfdcsushil/connecting-to-external-api-from-salesforce-using-external-credentials-with-custom-header-option-4a7820c9d02a)
