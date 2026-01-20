# Databricks Agent Status

> **Project**: {{PROJECT_NAME}}
> **Last Updated**: {{GENERATED_DATE}}
> **Workspace**: {{WORKSPACE_URL}}

---

## Active Agents

| Agent | Status | Current Task | Started |
|-------|--------|--------------|---------|
{{#each AGENTS}}
| {{name}} | Available | - | - |
{{/each}}

---

## Resource Locks

### Delta Tables

| Table | Locked By | Lock Type | Since | Expires |
|-------|-----------|-----------|-------|---------|
| - | - | - | - | - |

**Lock Types:**
- `WRITE` - Exclusive write access
- `MERGE` - Concurrent merge allowed
- `OPTIMIZE` - Optimization in progress
- `VACUUM` - Cleanup in progress

### Notebooks

| Notebook | Locked By | Since |
|----------|-----------|-------|
| - | - | - |

### Jobs

| Job | Status | Last Run | Owner |
|-----|--------|----------|-------|
| - | - | - | - |

---

## Active Pipelines

| Pipeline | Status | Progress | Started | Owner |
|----------|--------|----------|---------|-------|
| - | - | - | - | - |

**Pipeline Status:**
- `RUNNING` - Pipeline is executing
- `COMPLETED` - Pipeline finished successfully
- `FAILED` - Pipeline failed (see errors)
- `PENDING` - Waiting to start

---

## Task Queue

### High Priority

| Task | Assigned To | Status | Dependencies |
|------|-------------|--------|--------------|
| - | - | - | - |

### Normal Priority

| Task | Assigned To | Status | Dependencies |
|------|-------------|--------|--------------|
| - | - | - | - |

### Backlog

| Task | Assigned To | Status | Notes |
|------|-------------|--------|-------|
| - | - | - | - |

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

## Recent Completions

| Task | Agent | Completed | Duration | Output |
|------|-------|-----------|----------|--------|
| - | - | - | - | - |

---

## Blockers

| Blocker | Affects | Reported By | Since | Resolution |
|---------|---------|-------------|-------|------------|
| - | - | - | - | - |

---

## Data Quality Status

### Table Health

| Table | Last Check | Rows | Quality | Issues |
|-------|------------|------|---------|--------|
{{#each TABLES}}
| `{{name}}` | - | - | - | - |
{{/each}}

### DLT Pipeline Health

| Pipeline | Last Update | Status | Expectations |
|----------|-------------|--------|--------------|
| - | - | - | - |

---

## Compute Status

### Active Clusters

| Cluster | Type | State | Workers | Owner |
|---------|------|-------|---------|-------|
| - | - | - | - | - |

### SQL Warehouses

| Warehouse | State | Size | Queries/Hour |
|-----------|-------|------|--------------|
| - | - | - | - |

### Running Jobs

| Job | Run ID | State | Progress | Started |
|-----|--------|-------|----------|---------|
| - | - | - | - | - |

---

## ML Model Status

### Active Experiments

| Experiment | Active Runs | Last Run | Owner |
|------------|-------------|----------|-------|
| - | - | - | - |

### Model Registry

| Model | Champion Version | Challenger | Status |
|-------|------------------|------------|--------|
| - | - | - | - |

### Serving Endpoints

| Endpoint | Model | State | Queries/Min |
|----------|-------|-------|-------------|
| - | - | - | - |

---

## Handoff Queue

| From | To | Task | Resources | Status |
|------|-----|------|-----------|--------|
| - | - | - | - | - |

---

## Notes

[Add any relevant notes or context here]

---

## Health Check

| System | Status | Last Check |
|--------|--------|------------|
| Workspace | ✅ OK | - |
| Unity Catalog | ✅ OK | - |
| Pipelines | ✅ OK | - |
| Jobs | ✅ OK | - |
| Verification Loop | ⚪ Idle | - |
| Integration Tests | ⚪ Idle | - |

---

## Quick Commands

```bash
# Check cluster status
databricks clusters list

# Check job runs
databricks runs list --active-only

# Check pipeline status
databricks pipelines list-updates --pipeline-id <id>

# Get model versions
databricks mlflow model-versions list --model-name <name>
```

---

*Last automated update: {{GENERATED_DATE}}*
*Manual updates should include timestamp and agent name*
