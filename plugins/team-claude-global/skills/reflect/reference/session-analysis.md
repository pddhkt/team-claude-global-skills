# Session Analysis Patterns

How to identify problems in the current conversation.

---

## Analysis Approach

### Step 1: Scan for Error Patterns

Look for these indicators in the conversation:

**Tool Errors:**
- "Error:", "Failed:", "Invalid:"
- Exit codes != 0
- Stack traces
- "does not exist", "not found"

**API Mismatches:**
- "unknown property", "unexpected field"
- "ArgumentValidationError"
- "does not match validator"
- Schema validation errors
- Type errors in function calls
- Deprecated method warnings

**Permission Issues:**
- "permission denied"
- "access not allowed"
- Tool blocked messages

### Step 2: Identify Repeated Patterns

**Workflow Friction Indicators:**
- Same file read multiple times
- Repeated glob/grep for same patterns
- Multiple attempts at same edit
- Backtracking phrases: "actually", "let me try", "on second thought"

**Context Gaps:**
- Information that exists in skills but was manually discovered
- Conventions rediscovered through trial/error
- Questions about patterns documented elsewhere

### Step 3: Categorize by Severity

| Severity | Criteria | Action |
|----------|----------|--------|
| Critical | Blocks progress completely | Fix immediately |
| High | Causes repeated failures | Fix soon |
| Medium | Slows workflow noticeably | Improve when convenient |
| Low | Minor friction | Optional improvement |

---

## Problem Extraction

### From Tool Calls

```
Pattern: Tool call -> Error response

Extract:
- Tool name
- Parameters used
- Error message
- Expected vs actual behavior
```

### From Conversation Flow

```
Pattern: User request -> Multiple attempts -> Success/Failure

Extract:
- Original intent
- Attempted approaches
- What finally worked (or didn't)
- Effort wasted (number of attempts)
```

---

## Mapping Problems to Components

### API Errors -> Skill Updates

| Error Type | Component to Update |
|------------|---------------------|
| Wrong function signatures | Skill reference files |
| Missing patterns | Add to patterns.md |
| Outdated syntax | Update examples in SKILL.md |
| Unknown fields | Update schema documentation |

### Workflow Issues -> Skill Structure

| Issue Type | Component to Update |
|------------|---------------------|
| Missing context | Add reference file |
| Repeated exploration | Add navigation hints |
| Unclear triggers | Update description in frontmatter |
| Wrong tool used | Update allowed-tools |

### Tool Failures -> Command/Hook Updates

| Failure Type | Component to Update |
|--------------|---------------------|
| Environment issues | Add validation hooks |
| Permission errors | Adjust allowed-tools |
| Script failures | Fix scripts or add documentation |

---

## Example Analysis

### Input (from conversation)

```
Claude: Let me create the task...

curl -s -X POST "http://localhost:3211/api/tasks" \
  -d '{"projectId": "proj_123", "title": "Test", "type": "feature", "priority": 1}'

Error: ArgumentValidationError: Value does not match validator.
Path: .priority
Value: 1.0
Validator: v.union(v.literal("low"), v.literal("medium"), v.literal("high"))

Claude: Let me fix that...

curl -s -X POST "http://localhost:3211/api/tasks" \
  -d '{"projectId": "proj_123", "title": "Test", "type": "feature", "priority": "high"}'

Success!
```

### Extracted Problem

```markdown
## Problem: API Schema Mismatch

**Severity:** High
**Category:** API Mismatch

**Details:**
- Field: `priority`
- Expected: Number (1, 2, 3, 4)
- Actual: String literal ("low", "medium", "high", "critical")

**Affected Component:**
- Skill: `init-phase` command
- File: `.claude/commands/init-phase.md`

**Suggested Fix:**
Update the command documentation to use correct priority values:
- Change `"priority": 1` to `"priority": "high"`
- Document valid values: "low", "medium", "high", "critical"
```

---

## Analysis Report Template

```markdown
## Session Analysis Report

Generated: [timestamp]
Session scope: [recent errors | workflow friction | full session]

### Summary

| Category | Count | Severity |
|----------|-------|----------|
| API Mismatches | X | Critical/High |
| Workflow Friction | X | Medium |
| Context Gaps | X | Medium/Low |
| Tool Failures | X | High |

### Problems Found

#### 1. [Problem Title] (Severity)

**Category:** [API Mismatch | Workflow Friction | Context Gap | Tool Failure]

**Location in conversation:**
- [Quote or description of where error occurred]

**Root cause:**
- [Why this happened]

**Affected component:**
- Type: [skill | command | agent]
- Path: [file path]

**Suggested fix:**
- [Specific change to make]

---

### Improvement Priority

| # | Component | Change | Priority | Effort |
|---|-----------|--------|----------|--------|
| 1 | [name] | [description] | Critical | Low |
| 2 | [name] | [description] | High | Medium |
```
