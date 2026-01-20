# Agent Status Board

> **Project**: {{PROJECT_NAME}}
> **Last Updated**: {{LAST_UPDATED}}

---

## Current State

| Metric | Value |
|--------|-------|
| Active Agents | 0 |
| Tasks In Progress | 0 |
| File Locks | 0 |
| Blockers | 0 |

---

## Agent Status

| Agent | Status | Current Task | Since |
|-------|--------|--------------|-------|
{{#each AGENTS}}
| {{name}} | 🟢 Available | - | - |
{{/each}}

### Status Legend
- 🟢 Available - Ready for new tasks
- 🔵 Working - Currently on a task
- 🟡 Waiting - Blocked or waiting for input
- 🔴 Error - Encountered an issue
- ⚪ Offline - Not active

---

## File Locks

| File | Locked By | Since | Purpose |
|------|-----------|-------|---------|
| - | - | - | - |

### Lock Rules
1. Maximum lock duration: 30 minutes
2. Release locks immediately after completion
3. Stale locks (>30min) can be claimed by orchestrator

---

## Task Queue

### In Progress
| ID | Task | Agent | Started | Priority |
|----|------|-------|---------|----------|
| - | - | - | - | - |

### Pending
| ID | Task | Assigned To | Priority | Dependencies |
|----|------|-------------|----------|--------------|
| - | - | - | - | - |

### Recently Completed
| ID | Task | Agent | Completed | Duration |
|----|------|-------|-----------|----------|
| - | - | - | - | - |

---

## Verification Status

### Per-Task Verification
| Task ID | Agent | Status | Attempts | Last Updated |
|---------|-------|--------|----------|--------------|
| - | - | - | - | - |

#### Task Verification States
- `PENDING_VERIFICATION` - Task done, awaiting verification
- `VERIFYING` - testing-agent running tests
- `FIX_IN_PROGRESS` - troubleshooter fixing issues
- `VERIFIED` - All critical tests pass
- `BLOCKED` - Max attempts exceeded

### Integration Verification
| Phase | Tasks | Status | Attempts | Last Updated |
|-------|-------|--------|----------|--------------|
| - | - | - | - | - |

#### Integration States
- `PENDING` - Waiting for all tasks to verify
- `INTEGRATION_VERIFYING` - Running integration tests
- `INTEGRATION_FIX_IN_PROGRESS` - Fixing integration issues
- `INTEGRATION_VERIFIED` - All integration tests pass
- `INTEGRATION_BLOCKED` - Max attempts exceeded

---

## Blockers

| ID | Description | Blocking | Waiting On | Since |
|----|-------------|----------|------------|-------|
| - | - | - | - | - |

---

## Recent Activity

```
[No activity recorded yet]
```

### Activity Format
```
[TIMESTAMP] [AGENT] [ACTION] [DETAILS]
```

Examples:
```
2024-01-15 10:30:00 frontend-react STARTED Creating login component
2024-01-15 10:45:00 frontend-react COMPLETED Login component created
2024-01-15 10:45:00 frontend-react LOCK_RELEASED src/components/Login.tsx
2024-01-15 10:46:00 qa-engineer STARTED Reviewing login component
```

---

## Session History

### Current Session
- **Started**: {{GENERATED_DATE}}
- **Agents Activated**: {{AGENT_COUNT}}
- **Tasks Completed**: 0

### Previous Sessions
| Date | Duration | Tasks | Agents |
|------|----------|-------|--------|
| - | - | - | - |

---

## Quick Commands

### For Orchestrator
```markdown
## Update Agent Status
Agent: [name]
Status: [available/working/waiting/error]
Task: [description or -]

## Add Task
Task: [description]
Priority: [high/medium/low]
Assign To: [agent or unassigned]
Dependencies: [task IDs or none]

## Lock File
File: [path]
Agent: [name]
Purpose: [reason]

## Release Lock
File: [path]

## Report Blocker
Description: [what's blocking]
Blocking: [task ID]
Waiting On: [what's needed]
```

---

## Health Check

| System | Status | Last Check |
|--------|--------|------------|
| Git | ✅ OK | - |
| Build | ✅ OK | - |
| Tests | ✅ OK | - |
| Lint | ✅ OK | - |
| Verification Loop | ⚪ Idle | - |
| Integration Tests | ⚪ Idle | - |

---

*This status board should be updated by agents as they work. The orchestrator maintains overall accuracy.*
