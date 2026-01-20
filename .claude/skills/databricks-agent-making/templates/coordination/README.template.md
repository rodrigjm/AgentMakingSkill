# {{PROJECT_NAME}} Databricks Agent Team

> Generated: {{GENERATED_DATE}}
> Databricks Capabilities: {{DETECTED_STACK}}

## Overview

This project uses a team of specialized Databricks agents to handle different aspects of the data platform. Each agent is an expert in specific Databricks capabilities and follows defined ownership boundaries.

## Quick Start

### Invoke an Agent

Use the agent name as a command to activate a specific specialist:

```
/orchestrator    - Coordinate multi-agent tasks
/spark-developer - Spark/PySpark development
/delta-lake-engineer - Delta Lake operations
/mlflow-engineer - ML experiment tracking
... etc
```

### Common Workflows

**1. Build a Data Pipeline**
```
User: Build an ETL pipeline from S3 to gold tables
/orchestrator → Coordinates: data-connector-specialist → delta-lake-engineer → etl-pipeline-architect
```

**2. Train an ML Model**
```
User: Train a churn prediction model
/orchestrator → Coordinates: feature-store-engineer → ml-model-developer → mlflow-engineer
```

**3. Set Up Governance**
```
User: Set up Unity Catalog for our data
/unity-catalog-admin → security-engineer → workspace-admin
```

## Agent Roster

### Core Agents (Always Available)

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| **orchestrator** | Multi-agent coordination | Complex tasks spanning multiple agents |
| **project-manager** | Planning & documentation | Requirements, roadmaps, architecture docs |
| **qa-engineer** | Data quality validation | Review pipelines, validate data |
| **troubleshooter** | Debug & fix issues | Pipeline failures, performance problems |
| **testing-agent** | Execute tests | Run tests, check coverage |

### Domain Agents (Based on Detection)

{{DOMAIN_AGENTS_TABLE}}

## Coordination Files

| File | Purpose | Updated By |
|------|---------|------------|
| `CONTRACT.md` | Ownership boundaries | orchestrator |
| `STATUS.md` | Active tasks & locks | All agents |
| `settings.json` | MCP configuration | workspace-admin |

## File Structure

```
.claude/
├── agents/
│   ├── orchestrator.md
│   ├── project-manager.md
│   ├── qa-engineer.md
│   ├── troubleshooter.md
│   ├── testing-agent.md
{{#each DOMAIN_AGENTS}}
│   ├── {{name}}.md
{{/each}}
├── CONTRACT.md
├── STATUS.md
├── settings.json
└── agents-README.md (this file)
```

## Ownership Model

### Data Layers

| Layer | Owner | Description |
|-------|-------|-------------|
| Bronze | data-connector-specialist, streaming-engineer | Raw ingestion |
| Silver | delta-lake-engineer, etl-pipeline-architect | Cleaned data |
| Gold | sql-analyst | Business aggregations |
| Features | feature-store-engineer | ML features |
| Models | mlflow-engineer | Registered models |

### Resource Types

| Resource | Owner |
|----------|-------|
| Notebooks | Domain agents (by content) |
| Jobs/Workflows | workspace-admin |
| Clusters | workspace-admin |
| Unity Catalog | unity-catalog-admin |
| Secrets | security-engineer |
| Pipelines (DLT) | etl-pipeline-architect |

## Working with Agents

### Best Practices

1. **Start with orchestrator** for complex tasks
2. **Check STATUS.md** before modifying shared resources
3. **Follow handoff protocols** when passing work between agents
4. **Update STATUS.md** when starting/completing tasks
5. **Use CONTRACT.md** to resolve ownership questions

### Conflict Resolution

If multiple agents need the same resource:

1. Check CONTRACT.md for ownership
2. Coordinate via orchestrator
3. Use file locks in STATUS.md
4. Sequence the work appropriately

## MCP Configuration

The `settings.json` file configures MCP servers for enhanced agent capabilities:

```json
{
  "mcpServers": {
    "databricks": {
      "command": "npx",
      "args": ["-y", "@databricks/mcp-server"],
      "env": {
        "DATABRICKS_HOST": "${DATABRICKS_HOST}",
        "DATABRICKS_TOKEN": "${DATABRICKS_TOKEN}"
      }
    }
  }
}
```

### Required Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `DATABRICKS_HOST` | Workspace URL | Yes |
| `DATABRICKS_TOKEN` | Personal access token | Yes |
| `DATABRICKS_WAREHOUSE_ID` | SQL warehouse ID | For SQL queries |
| `MLFLOW_TRACKING_URI` | MLflow tracking server | For ML |

## Context Enrichment

Domain agents may include enriched context from:

| Source | Content | Used For |
|--------|---------|----------|
| Context7 | Library documentation | Best practices |
| Serena | Project analysis | Local conventions |
| GitHub | Code examples | Real-world patterns |
| Databricks Docs | Official guides | Reference |
| WebSearch | Troubleshooting | Common issues |

## Regeneration

To update agents after project changes:

```
/create-databricks-agents --regenerate
```

This will:
1. Re-analyze the project
2. Detect new Databricks capabilities
3. Preserve custom modifications (marked with `<!-- CUSTOM -->`)
4. Update CONTRACT.md and STATUS.md

## Support

### Common Issues

**Agent not recognizing capability?**
- Check if the capability is detected in databricks-mappings.yaml
- Run regeneration to re-detect

**Conflicts between agents?**
- Check CONTRACT.md for ownership
- Use orchestrator to coordinate

**Missing context?**
- Ensure MCP servers are configured
- Re-run with enrichment enabled

### Getting Help

1. Check agent descriptions in `/agents/*.md`
2. Review CONTRACT.md for ownership rules
3. Use troubleshooter for debugging
4. Ask orchestrator for complex coordination

---

*Generated by the Databricks Agent-Making Skill*
