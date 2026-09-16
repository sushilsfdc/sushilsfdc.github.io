Salesforce Technical Architect — 15+ years on the platform. This page is a map of what I've built in the open.

[GitHub @sushilsfdc](https://github.com/sushilsfdc) · [GitHub @sushilgit](https://github.com/sushilgit) · [Medium](https://medium.com/@sfdcsushil) · [LinkedIn](https://linkedin.com/in/sfdcsushil) · [Trailblazer](https://www.salesforce.com/trailblazer/sfdcsushil)

---

## AI Agents & Generative AI on Salesforce

### [AuditAgentHackathon](https://github.com/sushilsfdc/AuditAgentHackathon) — "Agent for Setup"
An Agentforce agent that lets admins interrogate their org's security posture in natural language: *"Does Benjamin have access to Opportunity?"*, *"Who has the ManageUsers permission?"*, *"What did this user change in Setup yesterday?"*

**How it's built**
- **ReAct planner** (`Agent_for_Setup`) extending the standard Admin Copilot topics with three custom topics: **User Access Management**, **Setup Audit Trail**, and **License Management**.
- **5 custom GenAI functions** with typed input/output JSON schemas, each backed by an **invocable Apex action**:
  | Function | Apex | Queries |
  |---|---|---|
  | `Setup_Audit_Trail_Summary` | `SummarizeAuditTrail` | `SetupAuditTrail` by user + date |
  | `Object_Field_Permission_Analysis` | `OrgPermissions` | `ObjectPermissions` / `FieldPermissions` with parent profile/perm-set resolution |
  | `Get_Users_With_Permission` | `UserPermissionRetrieval` | `PermissionSetAssignment` → `User` (dynamic SOQL, escaped) |
  | `User_License_Analysis` | `OrgLicenseInformation` | `UserLicense` totals vs. used |
  | `Retrieve_Permission_API_Name_From_Documentation` | — | Prompt-only: maps a plain-English permission name to its API name |
- **5 prompt templates** that turn raw JSON from Apex into a summarized, human-readable answer, with planner instructions that force disambiguation (multiple matching users/objects) and forbid raw JSON in agent output.
- Standard SFDX project layout — deployable straight from `manifest/package.xml`.

*Stack: Agentforce (GenAiPlanner / GenAiPlugin / GenAiFunction / GenAiPromptTemplate), Apex invocable methods, SOQL.*

---

### [dreamforce2025](https://github.com/sushilsfdc/dreamforce2025) — Generative AI Answers from Chatter Data
Reference implementation from my **Dreamforce 2025 session**: an end-to-end RAG pipeline that grounds AI answers in a community's own Chatter Q&A history.

**Pipeline**
1. **Ingest** — `ChatterDataInsert` pulls `FeedItem`s that have a *Best Comment* plus their `FeedComment`s, strips HTML, normalizes timestamps to UTC, and streams them in chunked batches to the **Data Cloud Ingestion API** (with a `/actions/test` validation mode before the real run). Schema for both objects is included as YAML.
2. **Auth** — `DataCloudAuthenticationService` implements the two-step Data Cloud token exchange (org OAuth token → `urn:salesforce:grant-type:external:cdp` → Data Cloud bearer token) via Named/External Credentials.
3. **Transform** — Data Cloud transformation denormalizes feed items + comments into a single *chatter thread* DLO → DMO → **vector search index** chunked on title, body, and comments.
4. **Generate** — prompt template `Get_AI_Generated_Answer_For_FeedPost` retrieves from that index and produces a grounded answer.
5. **Serve** — `communityAskQuestion` LWC ("Ask a Question") for Experience Cloud, backed by `CommunityAskQuestionController`, with a Flow fallback (`Answer_From_Prompt`) to work around prompt invocation limits in community context.

Includes `PromptUtils`, a reusable Apex wrapper around `ConnectApi.EinsteinLLM.generateMessagesForPromptTemplate`.

*Stack: Data Cloud (Ingestion API, DLO/DMO, Search Index), Prompt Builder, Apex, LWC, Flow, Named Credentials.*

---

### [EinsteinAgents](https://github.com/sushilsfdc/EinsteinAgents) — Einstein Agent & Prompt Experiments
Earlier-generation experiments that fed into the projects above.

- **Admin Agent v1** — an Einstein Copilot planner with a topic + action that summarizes a user's Setup Audit Trail for a given day (`SummarizeAuditTrail` invocable + prompt template). Written up in [Building an Agent for Salesforce Admins](https://medium.com/@sfdcsushil/building-agent-for-salesforce-admins-c1bc8b4f4d89).
- **Prompt-guarded case creation** — `caseCreation` LWC → `CaseCreationController` runs the subject/description through a `Case_PII_Detection` prompt template *before* insert and rejects the submission if PII is detected. Demonstrates invoking prompt templates from Apex, covered in [this article](https://medium.com/@sfdcsushil/941d56b7829e).
- Ships with a Jest test for the LWC.

*Stack: Einstein Copilot, Prompt Builder, Apex, LWC, Jest.*

---

## CI/CD & Code Quality

### [SFCodeAnalyzerWithStandardRules](https://github.com/sushilsfdc/SFCodeAnalyzerWithStandardRules) — PR gate with built-in rules
GitHub Actions workflows that run **Salesforce Code Analyzer** on every pull request and block the merge on violations.

- Two workflow variants: scan **newly added files only**, or scan **added + modified files**.
- Diffs the PR (`jitterbit/get-changed-files`), copies only touched files into a scan directory, runs `pmd` + `eslint-lwc` engines with normalized severity via `forcedotcom/run-code-analyzer`.
- Fails the PR when `exit-code > 0`, **any Sev-1 violation**, or **more than 10 total violations**; the HTML results file is uploaded as a build artifact.
- [Sample blocked PR](https://github.com/sushilsfdc/SFCodeAnalyzerWithStandardRules/pull/1) with results summary.

### [SFCodeAnalyzerWithCustomRules](https://github.com/sushilsfdc/SFCodeAnalyzerWithCustomRules) — PR gate with your own rules
Same pipeline, but driven by a **custom PMD ruleset** (`pmd-rules-list.xml`, e.g. `ApexCRUDViolation`) and a **custom ESLint config**, for teams that want to enforce their own standards rather than the defaults.

- Documents the extra `npm install --legacy-peer-deps` step that custom rules require and standard rules don't.
- Includes intentionally bad `BadApexClass` / `badLWC` fixtures to prove the gate fires.
- [Sample blocked PR](https://github.com/sushilsfdc/SFCodeAnalyzerWithCustomRules/pull/2) with the violation list.

*Stack: GitHub Actions, Salesforce CLI, sfdx-scanner, PMD, ESLint.*

---

## Admin & Metadata Tooling (Java)

Standalone desktop tools for Salesforce admins and architects doing security reviews, org comparisons, and migrations. Each takes metadata retrieved via Workbench / Migration Tool (a `package.xml` is provided), parses the XML with the Java DOM parser, and writes an Excel report with **Apache POI**. Run via a `.bat` file — no install.

### [ProfileComparison](https://github.com/sushilgit/ProfileComparison) ⭐ 5
Diffs two Profile XMLs and produces a **10-tab workbook**: Summary, Object Permissions, FLS Comparison, Record Type Permissions, Layout Assignment, Tab Permissions, App Permissions, Apex Class Comparison, VF Page Comparison, External Data Source.
Covers assigned apps, object CRUD, page-layout assignments, record-type access, field-level security, tab visibility, Apex/VF access, external data sources, system/user permissions, and custom permissions. A sample output workbook is in the repo. ~1,000 lines of Java.

### [LayoutComparisonTool](https://github.com/sushilgit/LayoutComparisonTool) ⭐ 1
Diffs two page layouts and outputs a workbook with **side-by-side field comparison** and an **"extra fields in each layout"** tab. Handy for reconciling layouts across record types or orgs during a merge.

### [ExportPermissionSet](https://github.com/sushilgit/ExportPermissionSet)
Flattens a Permission Set into a reviewable Excel sheet for audits and access reviews.

### [ProfileExporter](https://github.com/sushilgit/ProfileExporter)
Same as above for Profiles — export to Excel for review outside Setup.

---

## Writing

Longer write-ups on the ideas behind these repos live on [Medium](https://medium.com/@sfdcsushil):
- Salesforce Audit Agent with **Google ADK + Salesforce MCP** (McpToolset over the `sobject-all` endpoint — natural-language audit with zero hardcoded SOQL)
- Data Cloud RAG patterns — Chatter grounding and a release-notes analysis agent
- [Building an Agent for Salesforce Admins](https://medium.com/@sfdcsushil/building-agent-for-salesforce-admins-c1bc8b4f4d89)
- [Connecting to external APIs with External Credentials + custom headers](https://medium.com/@sfdcsushil/connecting-to-external-api-from-salesforce-using-external-credentials-with-custom-header-option-4a7820c9d02a)
