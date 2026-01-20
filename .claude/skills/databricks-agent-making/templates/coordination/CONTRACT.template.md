# Databricks Agent Coordination Contract

> **Project**: {{PROJECT_NAME}}
> **Generated**: {{GENERATED_DATE}}
> **Databricks Capabilities**: {{DETECTED_STACK}}
> **Workspace**: {{WORKSPACE_URL}}
> **Catalog**: {{CATALOG_NAME}}

This document defines ownership boundaries, responsibilities, and coordination rules for the Databricks agent team.

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
| `notebooks/**/*` | READS | READS | READS | OWNS | READS |
| `tests/**/*` | READS | READS | READS | OWNS | OWNS |
| `pipelines/**/*` | READS | READS | READS | OWNS | READS |

### Domain Agents

| Path Pattern | {{#each DOMAIN_AGENTS}}{{name}} | {{/each}}
|--------------|{{#each DOMAIN_AGENTS}}------|{{/each}}
{{#each FILE_OWNERSHIP}}
| `{{pattern}}` | {{#each agents}}{{access}} | {{/each}}
{{/each}}

---

## Databricks Resource Ownership

### Unity Catalog Objects

| Object Type | Path | Owner Agent | Access Policy |
|-------------|------|-------------|---------------|
| Catalog | `{{CATALOG_NAME}}` | unity-catalog-admin | Managed |
| Bronze Schema | `{{CATALOG_NAME}}.bronze` | delta-lake-engineer | Data Engineering |
| Silver Schema | `{{CATALOG_NAME}}.silver` | delta-lake-engineer | Data Engineering |
| Gold Schema | `{{CATALOG_NAME}}.gold` | sql-analyst | Analytics |
| Features Schema | `{{CATALOG_NAME}}.features` | feature-store-engineer | ML |
| Models Schema | `{{CATALOG_NAME}}.models` | mlflow-engineer | ML |

### Delta Tables

| Table | Owner | Schema | Update Pattern |
|-------|-------|--------|----------------|
{{#each DELTA_TABLES}}
| `{{name}}` | {{owner}} | {{schema}} | {{update_pattern}} |
{{/each}}

### Compute Resources

| Resource | Type | Owner | Purpose |
|----------|------|-------|---------|
{{#each COMPUTE_RESOURCES}}
| {{name}} | {{type}} | {{owner}} | {{purpose}} |
{{/each}}

---

## API Contracts

### Data Engineering ↔ ML

```python
# Feature table interface
class FeatureTableContract:
    """Features must follow this structure."""
    primary_key: List[str]  # Unique identifier columns
    timestamp_key: str      # For point-in-time lookups
    features: Dict[str, DataType]  # Feature columns and types

# Example
customer_features = FeatureTableContract(
    primary_key=["customer_id"],
    timestamp_key="feature_timestamp",
    features={
        "total_purchases": "INT",
        "avg_order_value": "DOUBLE",
        "days_since_last_order": "INT"
    }
)
```

### Bronze ↔ Silver

```python
# Bronze table contract
class BronzeTableContract:
    """Bronze tables must include metadata."""
    _ingested_at: Timestamp    # Ingestion timestamp
    _source_file: String       # Source file path
    _raw_data: String          # Original payload (if applicable)

# Silver table contract
class SilverTableContract:
    """Silver tables must include audit columns."""
    _bronze_id: String         # Reference to source record
    _processed_at: Timestamp   # Processing timestamp
    _is_valid: Boolean         # Data quality flag
```

### ML Model Contract

```python
# Model serving interface
class ModelServingContract:
    """Models must provide consistent interface."""
    input_schema: Schema       # MLflow signature input
    output_schema: Schema      # MLflow signature output
    feature_dependencies: List[str]  # Required feature tables
```

---

## Coordination Rules

### 1. Delta Table Protocol

Before modifying any Delta table:
1. Check `STATUS.md` for active writes
2. If another agent is writing, wait or coordinate
3. Use MERGE for concurrent-safe updates
4. Enable Change Data Feed for tracking

### 2. Notebook Execution Rules

| Scenario | Rule |
|----------|------|
| Same notebook | **NEVER** concurrent edits |
| Same cluster | Allowed with separate sessions |
| Shared data | Coordinate via orchestrator |
| Production jobs | Only workspace-admin modifies |

### 3. Pipeline Coordination

```
DLT Pipeline Ownership:
├── Bronze pipelines → data-connector-specialist
├── Silver pipelines → etl-pipeline-architect
├── Gold pipelines → sql-analyst
└── ML pipelines → mlflow-engineer
```

### 4. Handoff Protocol

When passing work between agents:

```markdown
## Handoff: [From Agent] → [To Agent]

### Completed
- [What was done]
- [Tables created/modified]
- [Schema changes]

### For You
- [What needs to be done]
- [Expected deliverables]

### Data Contract
- Source table: [catalog.schema.table]
- Expected schema: [columns and types]
- Update frequency: [batch/streaming/on-demand]

### Context
- [Relevant information]
- [Known constraints]
```

### 5. Conflict Resolution

Priority order for conflicts:
1. **orchestrator** - Final decision authority
2. **Data integrity** - Preserve data quality always
3. **Unity Catalog** - unity-catalog-admin decisions win
4. **ML artifacts** - mlflow-engineer owns model lifecycle
5. **Compute** - workspace-admin manages resources

---

## Communication Patterns

### Status Updates

Agents MUST update `STATUS.md` when:
- Starting a task
- Completing a task
- Modifying shared resources
- Encountering a blocker

### Requesting Help

```markdown
@orchestrator

## Help Request

**Agent**: [your name]
**Issue**: [description]
**Databricks Resource**: [table/cluster/job affected]
**Blocked On**: [what you need]
**Tried**: [what you attempted]
```

### Reporting Completion

```markdown
## Task Complete

**Agent**: [your name]
**Task**: [description]

**Resources Changed**:
- Tables: [list]
- Jobs: [list]
- Models: [list]

**Data Quality**: [checks passed/warnings]
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
| Tables/files changed | Paths only | Yes |
| Verification | Single command | Yes |

All agents MUST NOT return:
- Code snippets (reference `file:line` instead)
- Raw logs, query results, or traces (summarize findings)
- Reasoning or exploration steps
- Full notebook contents or job outputs

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
- Notebooks: Read specific cells, not entire notebooks
- Job logs: Read tail first, then targeted sections
- Query plans: Summarize, never paste full EXPLAIN

### Token Budget Priorities

| Priority | Content Type |
|----------|--------------|
| 1 (Highest) | Current task goal and approved plan |
| 2 | Table schemas and data contracts |
| 3 | Minimal code/queries required for changes |
| 4 (Lowest) | Summaries of prior work |

### Anti-Patterns

Agents MUST avoid:
- Re-planning during execution phase
- Re-explaining data architecture during debugging
- Keeping exploratory dead ends in context
- Pasting full query plans or job logs
- Re-reading notebooks already processed in session

---

## Quality Gates

### Before Any Deployment

| Check | Responsible Agent | Required |
|-------|-------------------|----------|
| Schema validation | domain agent | Yes |
| Data quality checks | qa-engineer | Yes |
| DLT expectations pass | etl-pipeline-architect | If DLT |
| Feature validation | feature-store-engineer | If ML |
| Security review | security-engineer | For PII |
| Cost review | workspace-admin | For large jobs |

### Review Requirements

- **Critical tables** (gold layer, features): 2 reviews required
- **Standard changes**: 1 review required
- **Documentation only**: Self-review allowed

---

## Verification Quality Gates

### Per-Task Verification Gate
| Gate | Agent | Required |
|------|-------|----------|
| Unit Tests Pass | testing-agent | CRITICAL |
| Schema Validation Pass | testing-agent | CRITICAL |
| Data Quality Checks Pass | testing-agent | CRITICAL |
| DLT Expectations Pass | testing-agent | CRITICAL |
| Row Count Verification | testing-agent | HIGH |
| Data Freshness Check | testing-agent | HIGH |

### Integration Verification Gate
| Gate | Agent | Required |
|------|-------|----------|
| All Tasks Verified | orchestrator | Yes |
| Pipeline Chain Tests Pass | testing-agent | CRITICAL |
| Cross-Table Tests Pass | testing-agent | CRITICAL |
| End-to-End Data Quality | testing-agent | CRITICAL |

### Loop Limits
| Limit | Value | Escalation |
|-------|-------|------------|
| Max verification attempts | 3 | Orchestrator → User |
| Max integration attempts | 3 | Orchestrator → User |

---

## Emergency Procedures

### Data Quality Incident

1. **qa-engineer** identifies issue
2. **troubleshooter** investigates root cause
3. Affected pipelines paused
4. Fix deployed with expedited review
5. Post-mortem documented by **project-manager**

### Pipeline Failure

1. **testing-agent** detects failure
2. **troubleshooter** diagnoses
3. **workspace-admin** manages compute if needed
4. Fix applied by owning domain agent
5. Validation by **qa-engineer**

### Agent Conflicts

1. Pause conflicting work
2. Report to **orchestrator**
3. Wait for resolution
4. Resume with clear ownership

---

## Cost Management

### Compute Usage Guidelines

| Agent | Cluster Type | Max Runtime | Auto-Terminate |
|-------|--------------|-------------|----------------|
| spark-developer | All-purpose | 4 hours | 60 min |
| ml-model-developer | GPU | 8 hours | 30 min |
| sql-analyst | SQL Warehouse | N/A | Auto-suspend |
| testing-agent | Job cluster | 1 hour | On completion |

### Query Optimization Requirements

- All SQL queries must use partition pruning
- Large table scans require orchestrator approval
- Production queries should use SQL warehouses

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | {{GENERATED_DATE}} | Initial generation |

---

*This contract is auto-generated and should be updated when the agent team or Databricks resources change.*
