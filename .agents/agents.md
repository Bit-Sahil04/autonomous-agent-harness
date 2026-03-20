# Agent Execution Harness

This system operates as a deterministic, file-backed agent loop.

All state is externalized. No reliance on chat history is allowed.

---

# SESSION CLASSIFICATION

IF `/harness/project_spec.xml` does NOT exist:
  MODE = INITIALIZATION

ELSE:
  MODE = EXECUTION

---

# GLOBAL CONTEXT (ALWAYS LOAD)

- @harness/project_spec.xml (if exists)
- @harness/feature-list.json (if exists)
- @harness/claude-progress.txt (if exists)
- @harness/spec_rubric.md :contentReference[oaicite:0]{index=0}

---

# INITIALIZATION MODE

LOAD:
- @prompts/initializer_prompt.md
- @harness/spec_template.xml :contentReference[oaicite:1]{index=1}
- @harness/spec_rubric.md

---

## INITIALIZATION PROTOCOL

### STEP 1 — SPEC GENERATION

- Parse user intent
- Instantiate spec using spec_template.xml
- Fill ALL CRITICAL sections to FULL/DEFINED depth
- Apply fidelity tags to all features

---

### STEP 2 — SPEC VALIDATION (MANDATORY)

Use spec_rubric.md as a strict validator:

- Enforce all CRITICAL sections reach max score
- Reject any anti-patterns:
  - missing fidelity
  - vague success criteria
  - incomplete API/schema
- Run full checklist validation

If invalid:
- iterate until compliant

---

### STEP 3 — FEATURE DECOMPOSITION

Use rubric semantics to construct:

`/harness/feature-list.json`

Rules:

- Only include `fidelity="concrete"` and `p0/p1` features
- Each feature becomes an atomic unit of work
- Each unit must include:
  - id
  - description
  - dependencies
  - acceptance_criteria (derived from spec)
  - done_definition

Rubric-derived constraints:
- no feature without acceptance criteria
- no ambiguous completion condition

---

### STEP 4 — INITIAL STATE WRITE

Create:

- `/harness/project_spec.xml`
- `/harness/feature-list.json`
- `/harness/claude-progress.txt`

claude-progress.txt must contain:
- current phase: initialization complete
- total features
- next feature id
- unresolved open questions

---

### STEP 5 — REPO INITIALIZATION

IF repo is empty:
- initialize structure based on spec
- respect technology_stack strictly

IF repo exists:
- DO NOT override:
  - framework
  - language
  - existing architecture

Only fill missing components.

---

### STEP 6 — CHECKPOINT

- commit all files
- exit

---

# EXECUTION MODE

LOAD:
- @prompts/coding_prompt.md
- @harness/spec_rubric.md

---

## EXECUTION STARTUP PROTOCOL (MANDATORY)

1. Read project_spec.xml
2. Read feature-list.json
3. Read claude-progress.txt
4. Inspect repository state

Reconstruct:

- completed features
- pending features
- current system capabilities

---

## FEATURE SELECTION

Select exactly ONE feature:

- first incomplete feature in feature-list.json
- dependencies must be satisfied

---

## IMPLEMENTATION RULES

- implement only selected feature
- adhere strictly to spec:
  - behaviour
  - constraints
  - edge cases

- do not reinterpret spec
- do not expand scope

---

## VALIDATION (PRE-COMMIT)

Use spec_rubric.md:

- ensure feature meets acceptance criteria
- verify no anti-pattern violations
- ensure system remains runnable

---

## STATE UPDATE

Update:

### feature-list.json
- mark feature complete
- attach completion metadata

### claude-progress.txt
- append:
  - feature completed
  - changes made
  - decisions taken
- update next feature id

---

## CHECKPOINT

- commit changes
- exit

---

# CROSS-SESSION RULES

- never trust prior chat context
- always reconstruct state from files
- never skip validation
- never batch features
- never mark complete unless all features are done

---

# SPEC USAGE RULES

## spec_template.xml

Used for:
- initial spec creation
- spec refinement in future sessions

Constraint:

IF repo already exists:
- DO NOT redefine:
  - technology_stack.framework
  - runtime
  - database choice

Only extend:
- features
- API
- schema (additive only)

---

## spec_rubric.md

Used for:

1. spec validation
2. feature decomposition
3. runtime verification

Hard constraints enforced:

- every concrete feature must have:
  - behaviour
  - constraints
  - edge cases
  - acceptance criteria

- every success criterion must be measurable

- reject vague language

---

# FAILURE MODES (STRICT)

IF any of the following occurs:

- missing acceptance criteria
- incomplete schema/API
- ambiguous feature definition

THEN:

- do not proceed
- log in claude-progress.txt
- block execution

---

# TERMINATION CONDITION

Project is complete ONLY IF:

- all features in feature-list.json are complete
- all acceptance criteria satisfied
- all success criteria measurable and met