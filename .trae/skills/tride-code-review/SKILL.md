---
name: tride-code-review
description: "Use as independent verifier only when explicitly asked to review a specified code change. Report evidence-backed findings and hand business fixes to developer. Not for security-only audits, implementation requests, or claiming builds/tests pass."
---

## Tride integration

Local adaptation of the installed TRAE-code-review, not an independently authored MIT skill. Respect all project and role rules. This review covers source changes; document/config review and actual builds/tests remain separate checks. Report the exact reviewed version. Do not modify business code or claim user acceptance.


### Basic Rules

- Always use **new-version line numbers** (i.e., line numbers from the new/right side of the diff) for issue locations.
- For multi-line issues, provide `[line_start, line_end]`; for single-line issues, both can be the same.

---

### Execution Steps

**Step 1: Determine Review Scope**
- If the user specifies a review scope (e.g., specific files, a merge request, or comparison with a specific branch), follow the user's requirements.
- If the user does not specify a scope, use the `AskUserQuestion` tool to ask the user for clarification before proceeding.
- Common scope scenarios:
  - "Review current changes" → Use `git diff` or workspace diff to get uncommitted changes.
  - "Review changes compared to branch X" → Use `git diff X` to get the diff.
  - "Review MR/PR #N" → Fetch the merge request diff using appropriate tools.
  - "Review file X" → Focus on the specified file's changes.

**Step 2: Context Gathering**
- You **MUST** call tools (such as `SearchCodebase`, `Read`) to fetch all relevant source and design evidence before commenting.

**Step 3: Infer Author's Intent**
- Analyze the provided code diff as a whole.
- Based on the patterns of changes (e.g., adding error handling, changing algorithms, refactoring variable names, modifying configuration), deduce the most probable intent of the author.
- Formulate this intent as a concise summary. For example:
  - "Intent: Refactoring the `calculate_total` function for better readability."
  - "Intent: Adding null checks to prevent potential `NullPointerException` in the `process_user` method."
  - "Intent: Fixing an off-by-one error in the pagination logic."
  - "Intent: Upgrading a dependency version and adapting the code to its new API."
- This inferred intent will serve as a critical context for the next steps.

**Step 4: Visual Summary with Mermaid Diagrams**
- **Always provide** at least one mermaid diagram to summarize the key changes.
- Determine the **number of diagrams** based on the scope of changes:
  - **1 diagram**: For simple or single-aspect changes (e.g., a bug fix, minor feature, changes in one module).
  - **2 diagrams**: For complex changes that span multiple aspects (e.g., both business logic and technical implementation changes, cross-module interactions, new features with multiple components).
- Generate mermaid diagrams to summarize the key changes:
  - **Business flow diagram**: Use `flowchart` or `sequenceDiagram` to illustrate the business logic changes (e.g., user workflow, data processing pipeline).
  - **Technical flow diagram**: Use `flowchart` or `sequenceDiagram` to show the technical changes (e.g., call sequences, data flow, request handling).
- **Diagram guidelines**:
  - **PREFER flowcharts and sequence diagrams** that show actual **logic chains, call sequences, or data flow**.
  - **AVOID static box diagrams** that only categorize or group changes without showing relationships or flow.
  - Use color blocks (`style` or `classDef`) to group related logic and highlight changed parts.
  - Keep diagrams concise — focus on **key changes only**, not the entire system.
  - Add brief annotations to explain the purpose of each section.
  - **Color Contrast Requirements** (IMPORTANT):
    - Always specify **both fill color AND text color** to ensure readability in both light and dark themes.
    - Use dark text (`color:#000` or `color:#1a1a1a`) on light backgrounds.
    - Use light text (`color:#fff` or `color:#f5f5f5`) on dark backgrounds.
    - Recommended high-contrast combinations:
      - ✅ `fill:#c8e6c9,color:#1a5e20` (light green bg + dark green text)
      - ✅ `fill:#bbdefb,color:#0d47a1` (light blue bg + dark blue text)
      - ✅ `fill:#fff3e0,color:#e65100` (light orange bg + dark orange text)
      - ✅ `fill:#f3e5f5,color:#7b1fa2` (light purple bg + dark purple text)
      - ❌ `fill:#c8e6c9` (missing text color — unreadable in dark theme!)
- **Bad example** (avoid this pattern — static boxes without flow):
  ```mermaid
  flowchart LR
      subgraph Category1["Debug Code"]
          A[console.log]
          B[TODO comment]
      end
      subgraph Category2["New Features"]
          C[/new route]
          D[Demo user fallback]
      end
  ```
- **Good example** (show actual logic flow):
  ```markdown
  📊 **Change Overview**

  **Business Flow Changes:**
  ```mermaid
  flowchart LR
      A[User Login] --> B{Has Token?}
      B -->|Yes| C[Validate Token]
      B -->|No| D[Redirect to Auth]
      C --> E[Load User Data]
      E --> F[Render Dashboard]
      style C fill:#c8e6c9,color:#1a5e20
      style E fill:#c8e6c9,color:#1a5e20
  ```

  **Technical Flow Changes:**
  ```mermaid
  sequenceDiagram
      participant Client
      participant API
      participant Cache
      participant DB
      Client->>API: POST /login
      API->>Cache: Check session
      Cache-->>API: Cache miss
      API->>DB: Query user
      DB-->>API: User data
      API->>Cache: Store session
      API-->>Client: 200 OK + token
  ```
  ```

**Step 5: Scan for Issues**
- Using the inferred intent from Step 3 as context, carefully analyze the code diff again.

**Step 5.5: Cross-Validation with Sub-Agents**
- Before presenting issues to the user, perform a **second-pass validation** to verify the existence and severity of each identified issue.
- **Validation Strategy**:
  1. **Optional Parallel Verification**: Only when the user has explicitly adopted subagent review and the tools are available, dispatch **2 sub-agents** to validate **ALL X issues**. Otherwise use the self-review fallback below and state that no independent subagent validation occurred.
  2. **Verification Focus**: Each validator should check every issue for:
     - **Existence**: Does the issue actually exist in the code? Is the line reference correct?
     - **Severity Assessment**: Is this a real problem (critical/major) or a minor/stylistic concern?
     - **False Positive Detection**: Could this be a false positive due to missing context?
  3. **Confidence Scoring**: Each issue receives a confidence score based on validator consensus:
     - ✅ **High Confidence** (2/2 validators agree the issue exists): Include in final report
     - ⚠️ **Medium Confidence** (1/2 validator confirms): Include with caveat
     - ❌ **Low Confidence** (0/2 confirm, both say false positive): Exclude from final report

- **Sub-Agent Dispatch Rules**:
  - Each sub-agent receives the **complete list of all X issues** to validate
  - Each sub-agent independently reads the relevant code context for every issue
  - Sub-agents work in parallel, unaware of each other's findings
  - Each sub-agent must return validation results for **ALL issues**:
    ```json
    [
      { "issue_id": 1, "exists": true, "severity": "major", "reasoning": "..." },
      { "issue_id": 2, "exists": false, "severity": "false_positive", "reasoning": "..." },
      ...
    ]
    ```

- **Validation Consolidation**:
  - Collect validation results from both sub-agents
  - For each issue, count how many validators confirmed it exists
  - Apply consensus-based confidence scoring:
    - 2/2 confirm → High Confidence → Include
    - 1/2 confirms → Medium Confidence → Include with note
    - 0/2 confirm → Low Confidence → Exclude
  - Document excluded issues with reasoning (for transparency, but do not present to user)

- **Fallback (Sub-Agent Not Available)**:
  - If sub-agents are unavailable or not part of the adopted method, perform a self-review:
    1. Re-read each issue's code context with fresh perspective
    2. Challenge each finding: "Is this definitely a problem?"
    3. Remove any issue that cannot be confidently confirmed

**Step 6: Output Review Results**
- If **no issues** are found, output a brief summary stating that the code looks good (e.g., "✅ No issues found. The code changes look good.") and skip to the end.
- If issues are found, present the review results in a **table** with the following columns:
  | No. | Issue Title | Suggestion | Code Link |
  |-----|-------------|------------|-----------|
  | 1   | Title       | Suggestion | [file:line](file:///path/to/file#L123-L145) |
- The code link should use the standard markdown link format pointing to the file URI with line numbers.

**Step 7: Handoff Findings**
- Return findings, evidence, affected version, and reproduction/test suggestions.
- Independent verifier does not fix business code. Coordination prepares the handoff to developer; developer uses receiving-code-review before authorized fixes.
- Ask the user only for unresolved scope, acceptance, architecture, budget, or actual authorization decisions. Already authorized routine fixes need no repeated approval.
- Retest the submitted version. After two failed developer-fix/independent-retest rounds for the same issue, pause that issue under project rules.

---

### User Interaction Strategy

Use an available question tool, or plain text, for genuine scope ambiguity. Do not offer to fix all issues in the verifier session. Existing authorized handoffs remain valid.

---

### Key Constraints

- **Language Consistency**: All outputs (review comments, questions, suggestions) **MUST** use the user's preferred language. Follow these rules in order:
  1. If the user explicitly specifies a language preference, use that language.
  2. Otherwise, use the same language as the user's latest message.
  3. If the user communicates in Chinese, respond in Chinese; if in English, respond in English.
- **Evidence-based**: You **MUST** fetch relevant repository context using tools before producing review comments, and reference evidence in your comments.
- **Line Range Limit**: `line_start`/`line_end` must be the minimal continuous span that contains the concrete evidence of the issue. Do NOT use whole-file or overly broad ranges (Max range per comment: `line_end - line_start <= 100`).
- **Handoff**: Findings are routed to the developer; this role never enters an iterative business-code repair loop.

---

### Tips

1. **Prohibit low-value comments**: Do not emit purely descriptive, praise-only, or no-action comments; avoid change-narration (e.g., "this improves X"); do not comment based on missing context or uncertainty; avoid "might", "possibly", or "you may want to check".

2. **Source-review scope**: This skill does not review prose/config files (e.g., `.md`, `.json`, `.txt`, `.svg`, `cargo.lock`). List relevant excluded files so the independent verifier can inspect them separately; exclusion is not approval.

3. **UI code handling**: For UI style code (e.g., CSS), assume the user has confirmed the visual design meets expectations. Numeric values such as font size and spacing are assumed correct by default. For UI source code, assume UI/interaction/animation changes are intentional unless they violate correctness or documented invariants. Do not emit review comments for low-risk UI style or presentation changes.

4. **Comment clarity**: For comment clarity issues, be cautious unless there is a major problem.

5. **Consider context**: When giving normative comments, consider best practices and also the user's intent, project preferences, and code functionality; for example, using vague naming to mitigate security risks, or special practices due to specific dependencies.

6. **Deduplicate**: Be truthful and restrained. Deduplicate and merge similar issues; for cross-file repetitions, give one representative comment and list affected locations. If a concern depends on surrounding logic, initialization, lifecycle, or call sites, fetch and read the complete file before commenting.

7. **Respect intentional deletions**: When large functionality is removed, assume it was done intentionally by the user.

8. **Compiled-code evidence**: Do not infer build success from a source review. Run the appropriate build if available and authorized, or mark it not verified. Report type/interface/reference defects only with concrete evidence.

9. **Project rules**: Read applicable AGENTS.md and project rules, including product requirements, role boundaries, evidence preservation, and authorization. Review-specific instructions do not override them.

10. **Identifier spelling**: Do not flag misspelled identifiers unless they are newly introduced and inconsistent with their definitions.
