# Agent Coordination Contract

> **Project**: {{PROJECT_NAME}}
> **Generated**: {{GENERATED_DATE}}
> **Stack**: {{DETECTED_STACK}}

This document defines ownership boundaries, responsibilities, and coordination rules for the agent team.

---

## Agent Roster

| Agent | Model | Responsibility | Status |
|-------|-------|----------------|--------|
{{#each CORE_AGENTS}}
| {{name}} | {{model}} | {{description}} | Active |
{{/each}}
{{#each DOMAIN_AGENTS}}
| {{name}} | {{model}} | {{description}} | Active |
{{/each}}

---

## File Ownership Matrix

### Ownership Legend
- **OWNS**: Full read/write access, primary responsibility
- **READS**: Can read for context, no modifications
- **---**: Cannot access (out of scope)

### Core Agents

| Path Pattern | orchestrator | project-manager | qa-engineer | troubleshooter | testing-agent |
|--------------|--------------|-----------------|-------------|----------------|---------------|
| `.claude/STATUS.md` | OWNS | READS | READS | READS | READS |
| `.claude/CONTRACT.md` | OWNS | READS | READS | READS | READS |
| `docs/**/*` | READS | OWNS | READS | READS | READS |
| `*.md` (root) | READS | OWNS | READS | READS | READS |
| `src/**/*` | READS | READS | READS | OWNS | READS |
| `tests/**/*` | READS | READS | READS | OWNS | OWNS |

### Domain Agents

| Path Pattern | {{#each DOMAIN_AGENTS}}{{name}} | {{/each}}
|--------------|{{#each DOMAIN_AGENTS}}------|{{/each}}
{{#each FILE_OWNERSHIP}}
| `{{pattern}}` | {{#each agents}}{{access}} | {{/each}}
{{/each}}

---

## API Contracts

### Frontend ↔ Backend

```typescript
// API Response Format
interface ApiResponse<T> {
  data: T;
  error?: {
    code: string;
    message: string;
  };
  meta?: {
    page?: number;
    limit?: number;
    total?: number;
  };
}

// Error Codes
type ErrorCode =
  | 'VALIDATION_ERROR'
  | 'UNAUTHORIZED'
  | 'FORBIDDEN'
  | 'NOT_FOUND'
  | 'INTERNAL_ERROR';
```

### Backend ↔ Database

```typescript
// Query Interface
interface QueryOptions {
  page?: number;
  limit?: number;
  orderBy?: string;
  order?: 'asc' | 'desc';
  filters?: Record<string, any>;
}
```

### Shared Types Location

All shared types MUST be defined in:
- TypeScript: `src/types/` or `types/`
- Python: `src/schemas/` or `app/schemas/`

---

## Coordination Rules

### 1. File Locking Protocol

Before modifying any file:
1. Check `STATUS.md` for existing locks
2. If locked by another agent, wait or coordinate via orchestrator
3. Acquire lock before editing
4. Release lock immediately after completion

### 2. Concurrent Work Rules

| Scenario | Rule |
|----------|------|
| Same file | **NEVER** concurrent edits |
| Same directory | Allowed if different files |
| Related features | Coordinate via orchestrator |
| Independent features | Parallel work allowed |

### 3. Handoff Protocol

When passing work between agents:

```markdown
## Handoff: [From Agent] → [To Agent]

### Completed
- [What was done]
- [Files modified]

### For You
- [What needs to be done]
- [Expected deliverables]

### Context
- [Relevant information]
- [Known constraints]
```

### 4. Conflict Resolution

Priority order for conflicts:
1. **orchestrator** - Final decision authority
2. **Security concerns** - Always take precedence
3. **Backend contracts** - Frontend adapts to backend
4. **Database schema** - Applications adapt to schema

---

## Communication Patterns

### Status Updates

Agents MUST update `STATUS.md` when:
- Starting a task
- Completing a task
- Encountering a blocker
- Needing input from another agent

### Requesting Help

```markdown
@orchestrator

## Help Request

**Agent**: [your name]
**Issue**: [description]
**Blocked On**: [what you need]
**Tried**: [what you attempted]
```

### Reporting Completion

```markdown
## Task Complete

**Agent**: [your name]
**Task**: [description]
**Files Changed**:
- [file1]
- [file2]

**Tests**: [passed/added/none]
**Ready For**: [next agent or review]
```

---

## Inter-Agent Communication Standards

Optimize token usage in all agent communications.

### Output Requirements

All agents MUST return:
| Element | Format | Required |
|---------|--------|----------|
| Summary | ≤10 bullets | Yes |
| Files changed | Paths only | Yes |
| Verification | Single command | Yes |

All agents MUST NOT return:
- Code snippets (reference `file:line` instead)
- Raw logs or traces (summarize findings)
- Reasoning or exploration steps
- Full file contents

### Authority Chain
1. **Main agent (user session)** - Final decision-maker
2. **Orchestrator** - Coordinates, proposes plans
3. **Domain agents** - Execute, report results

Subagents propose; orchestrator/main agent approves.

### File Reading Protocol

Before reading any file:
```
1. Ask: "Do I need the whole file or just a section?"
2. Use Grep with head_limit=10 to locate relevant sections
3. Read only those sections with offset/limit
4. Never re-read files already in session
```

#### Large Files (>200 lines)
- `STATUS.md`, `CONTRACT.md`, specs: Read only relevant sections
- Source files: Read symbol definitions, not entire files
- Test files: Read only failing tests
- Logs: Read tail first, then targeted sections

### Token Budget Priorities

| Priority | Content Type |
|----------|--------------|
| 1 (Highest) | Current task goal and approved plan |
| 2 | Integration contracts and shared types |
| 3 | Minimal code required for changes |
| 4 (Lowest) | Summaries of prior work |

### Anti-Patterns

Agents MUST avoid:
- Re-planning during execution phase
- Re-explaining architecture during debugging
- Keeping exploratory dead ends in context
- Pasting large logs when summarizing suffices
- Re-reading files already processed in session

---

## Quality Gates

### Before Any Merge

| Check | Responsible Agent | Required |
|-------|-------------------|----------|
| Linting | domain agent | Yes |
| Type checking | domain agent | Yes |
| Unit tests | testing-agent | Yes |
| Code review | qa-engineer | Yes |
| Integration test | testing-agent | If applicable |

### Review Requirements

- **Critical paths** (auth, payments): 2 reviews required
- **Standard changes**: 1 review required
- **Documentation only**: Self-review allowed

---

## Verification Quality Gates

### Per-Task Verification Gate
| Gate | Agent | Required |
|------|-------|----------|
| Unit Tests Pass | testing-agent | CRITICAL |
| Type Check Pass | testing-agent | CRITICAL |
| Lint Pass | testing-agent | HIGH |
| Coverage >= 80% | testing-agent | HIGH |

### Integration Verification Gate
| Gate | Agent | Required |
|------|-------|----------|
| All Tasks Verified | orchestrator | Yes |
| Integration Tests Pass | testing-agent | CRITICAL |
| Contract Compliance | testing-agent | CRITICAL |

### Loop Limits
| Limit | Value | Escalation |
|-------|-------|------------|
| Max verification attempts | 3 | Orchestrator → User |
| Max integration attempts | 3 | Orchestrator → User |

---

## Emergency Procedures

### Production Issues

1. **troubleshooter** takes immediate lead
2. All non-critical work pauses
3. Fix deployed with minimal review
4. Post-mortem documented by **project-manager**

### Agent Conflicts

1. Pause conflicting work
2. Report to **orchestrator**
3. Wait for resolution
4. Resume with clear ownership

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | {{GENERATED_DATE}} | Initial generation |

---

*This contract is auto-generated and should be updated when the agent team or project structure changes.*
