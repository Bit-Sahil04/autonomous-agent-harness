# App Specification Template — Completeness Rubric & Agent Guide  
**v1.0** · Audience: AI Agents (also usable by human authors)

---

## 1. Section Completeness Rubric

Each section carries a weight and minimum depth requirement. Agents must evaluate each section before finalising a spec. A spec is considered complete when all **CRITICAL** sections score at their maximum weight.

| Section | Weight | Min Depth | Max Score | Pass Condition |
|---|---|---|---|---|
| `project_name` + `overview` | 🔴 CRITICAL | FULL | 5 | Both present; overview is 2–4 sentences covering what/who/why |
| `goals` | 🔴 CRITICAL | FULL | 5 | 3–7 goals; each is falsifiable (contains a measurable condition) |
| `non_goals` | 🔴 CRITICAL | DEFINED | 4 | At least 2 explicit non-goals; prevents scope assumptions |
| `technology_stack` | 🔴 CRITICAL | DEFINED | 5 | All runtimes, key libs with versions; rationale for non-obvious choices |
| environment vars | 🔴 CRITICAL | DEFINED | 4 | Every env var named, required flag set, default documented |
| features (concrete) | 🔴 CRITICAL | FULL | 5 | Behaviour, constraints, edge cases, rationale present for every p0/p1 feature |
| features (guided) | 🟠 HIGH | DEFINED | 3 | Core behaviour + ≥1 constraint per guided feature |
| features (mock/deferred) | 🟡 LOW | ABSTRACT | 1 | Fidelity tag present; one-line description; no implementation detail |
| `database_schema` | 🔴 CRITICAL | FULL | 5 | Every table used by concrete features; all columns typed + nullable; JSON keys listed |
| `api_endpoints` | 🔴 CRITICAL | FULL | 5 | Method, path, auth, request + response shape, all error codes; streaming protocol noted |
| `ui_layout` | 🟠 HIGH | DEFINED | 3 | Named regions; explicit responsive behaviour; pixel or proportion sizes |
| `design_system` | 🔵 MEDIUM | DEFINED | 2 | Hex values for all colour roles; font families; spacing scale |
| `key_interactions` | 🟠 HIGH | FULL | 4 | Happy path + error paths + postconditions for every multi-component flow |
| `implementation_steps` | 🟠 HIGH | DEFINED | 3 | Ordered; each step has `done_when` condition; no circular deps |
| `success_criteria` | 🔴 CRITICAL | FULL | 5 | Every criterion is measurable, observable, scoped; no vague adjectives |
| `acceptance_criteria` | 🟠 HIGH | FULL | 4 | Given/when/then for all p0/p1 concrete features; AC-IDs present |
| `open_questions` | 🔵 MEDIUM | ENUMERATED | 2 | Blocking questions flagged; `agent_default` stated for each |
| `out_of_scope` | 🟡 LOW | ENUMERATED | 1 | All deferred/mock features listed; reason given |

**Scoring:** 5 = fully meets depth requirement · 3 = partially meets · 1 = present but shallow · 0 = missing

---

## 2. Feature Fidelity Levels

Every feature in the spec must carry a `fidelity` attribute. Fidelity determines the depth required and what an agent is expected to implement.

| Fidelity | Meaning | Minimum Content Required |
|---|---|---|
| `concrete` | Implement as written — fully specced | Behaviour + constraints + edge cases + acceptance criteria |
| `guided` | Core behaviour defined — agent fills implementation gaps | Core behaviour + ≥1 constraint |
| `mock` | Static UI only — no real logic | Visual description only |
| `deferred` | Do NOT implement in this version | One-line description |

> **Rule:** A spec with any `fidelity="concrete"` p0/p1 feature missing its behaviour, constraints, or edge cases is considered incomplete and must not be submitted.

---

## 3. Anti-Patterns — Reject If Present

The following patterns indicate an incomplete or ambiguous spec. Agents must scan for these before finalising and either resolve or log them as open questions.

- Feature listed as a bullet point with no `fidelity` attribute
- `"TBD"`, `"TODO"`, or `"as needed"` in any CRITICAL section
- Success criterion using vague adjectives (`"intuitive"`, `"clean"`, `"beautiful"`, `"fast"`)
- Behaviour described only by UI metaphor (`"a nice modal"`, `"a card"`)
- Open question silently resolved without logging in `<open_questions>`
- Concrete feature with no acceptance criteria
- API endpoint with no error codes documented
- Database column with no type or nullability
- JSON column with no keys documented
- Implementation step with no `done_when` condition

---

## 4. Good vs. Bad Examples

| Context | Quality | Example |
|---|---|---|
| Feature goal | ❌ Bad | Build a settings page with good UX |
| Feature goal | ✅ Good | Settings persist across sessions; changes apply without page reload |
| Success criterion | ❌ Bad | Streaming feels fast and responsive |
| Success criterion | ✅ Good | First token renders within 800ms of send on local network (p95) |
| Edge case | ❌ Bad | Handle errors gracefully |
| Edge case | ✅ Good | If SSE drops mid-stream: show inline retry banner; partial message retained |
| Acceptance criteria | ❌ Bad | Chat works correctly |
| Acceptance criteria | ✅ Good | Given 2 prior messages; when user sends; then stop-button appears; and click halts stream |

---

## 5. Agent Self-Evaluation Checklist

Run this checklist before submitting any spec. A spec is ready only when all items are checked. Log unresolved items as `<open_questions>` with `blocking="true"`.

### 5.1 Structure
- [ ] All CRITICAL sections are present and filled to their required depth
- [ ] Every feature has a `fidelity` attribute (`concrete` / `guided` / `mock` / `deferred`)
- [ ] No section contains placeholder text: `TBD`, `TODO`, or `N/A` in a CRITICAL field
- [ ] All `fidelity="deferred"` features are also listed in `<out_of_scope>`

### 5.2 Features
- [ ] Every p0 and p1 feature with `fidelity="concrete"` has: behaviour, constraints, edge cases
- [ ] Every p0 and p1 feature with `fidelity="concrete"` has at least one acceptance criterion
- [ ] Guided features have at least one explicit constraint
- [ ] Mock features have a visual description and a note confirming no real logic

### 5.3 Data & API
- [ ] Every table used by a concrete feature is in the schema
- [ ] Every column has a type and nullability declaration
- [ ] Every JSON-typed column lists its expected keys
- [ ] Every API endpoint has method, path, auth, request shape, response shape
- [ ] Every endpoint lists all error codes the client must handle
- [ ] Streaming endpoints specify their protocol (SSE or WebSocket)

### 5.4 Verification
- [ ] Every success criterion is measurable (contains a number, threshold, or binary condition)
- [ ] No success criterion uses vague adjectives: `intuitive`, `clean`, `fast`, `beautiful`, `responsive`
- [ ] Every acceptance criterion follows `given`/`when`/`then` structure
- [ ] Every acceptance criterion has a unique AC-ID
- [ ] All blocking open questions are flagged and have an `agent_default`

---

## 6. Analysis of Reference Spec (claude.ai Clone)

The following analysis of the original spec informed the design of this template, illustrating what the rubric would flag in a real-world document.

### Well-Explored — Would Pass Rubric

- **Technology stack** — specific libraries, versions, port placeholder
- **Database schema** — all tables with columns, types, JSON fields, relationships
- **API endpoints** — full REST surface with verbs, paths, parameters
- **UI layout** — three-column structure with named regions
- **Implementation steps** — numbered, ordered, tasked per step
- **Design system** — hex colour tokens, font sizes, animation durations
- **Key interaction flows** — message flow and artifact flow as numbered sequences

### Left Abstract — Would Fail Rubric

- **Projects** — 8-bullet wish list; no template schema, no analytics definition, no sharing model
- **Collaboration** — entirely mock; no spec for share link behaviour or token lifecycle
- **Advanced features** — branching, voice input, response suggestions with zero implementation detail
- **Onboarding** — 5 bullets; no screen count, no copy, no trigger logic
- **Custom instructions** — `"preview how instructions affect responses"` is undefined behaviour
- **Search** — no ranking logic, no index strategy, no result format

### Missing Entirely — Would Be Flagged by Checklist

- **Rationale** — zero "why" provided for any architectural decision
- **Non-goals** — nothing explicitly excluded; scope implied only by omission
- **Open questions** — no questions logged; agent must guess at ambiguities
- **Measurable acceptance criteria** — `<success_criteria>` block uses only vague adjectives
- **Fidelity declarations** — mock/deferred features not differentiated from concrete ones
