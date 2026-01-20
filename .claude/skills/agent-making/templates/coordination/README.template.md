# Agent Team Documentation

> **Project**: {{PROJECT_NAME}}
> **Generated**: {{GENERATED_DATE}}
> **Stack**: {{DETECTED_STACK}}

This document explains how to work with the generated agent team.

---

## Quick Start

### Invoke the Orchestrator
```
/orchestrator
```
The orchestrator coordinates all other agents and should be your primary interface for complex tasks.

### Invoke Specific Agents
```
/frontend-{{FRONTEND_FRAMEWORK}}  - UI development
/backend-{{BACKEND_FRAMEWORK}}    - API development
/database-{{DATABASE_TYPE}}       - Schema and queries
/qa-engineer                      - Code review
/troubleshooter                   - Debugging
/testing-agent                    - Run tests
/project-manager                  - Documentation
```

---

## Agent Team Overview

### Core Agents (Always Available)

| Agent | Purpose | Best For |
|-------|---------|----------|
| **orchestrator** | Coordination | Multi-step tasks, complex features |
| **project-manager** | Planning & docs | Documentation, specs, roadmaps |
| **qa-engineer** | Quality | Code review, quality checks |
| **troubleshooter** | Debugging | Errors, performance issues |
| **testing-agent** | Testing | Running tests, coverage |

### Domain Agents (Project-Specific)

{{#each DOMAIN_AGENTS}}
| **{{name}}** | {{description}} | {{use_case}} |
{{/each}}

---

## Common Workflows

### Building a New Feature

1. **Start with orchestrator**
   ```
   /orchestrator
   "Build a user profile page with edit functionality"
   ```

2. The orchestrator will:
   - Break down the task
   - Delegate to appropriate agents
   - Coordinate the work
   - Report progress

### Fixing a Bug

1. **Start with troubleshooter**
   ```
   /troubleshooter
   "The login form shows a 500 error when submitting"
   ```

2. The troubleshooter will:
   - Investigate the issue
   - Identify root cause
   - Implement a fix
   - Verify the fix

### Code Review

1. **Use qa-engineer**
   ```
   /qa-engineer
   "Review the authentication module I just created"
   ```

2. The QA engineer will:
   - Check for security issues
   - Review code quality
   - Suggest improvements
   - Report findings

### Writing Documentation

1. **Use project-manager**
   ```
   /project-manager
   "Document the new API endpoints"
   ```

2. The project manager will:
   - Analyze the code
   - Create documentation
   - Add examples
   - Update README if needed

---

## Coordination Files

### CONTRACT.md
Defines ownership boundaries and coordination rules.
- **Who can edit what**
- **API contracts between agents**
- **Conflict resolution rules**

### STATUS.md
Tracks current state of work.
- **Active agents and tasks**
- **File locks**
- **Blockers**
- **Recent activity**

---

## Model Assignments

| Agent | Model | Rationale |
|-------|-------|-----------|
| orchestrator | opus | Complex coordination requires highest capability |
| project-manager | sonnet | Balanced for planning and documentation |
| qa-engineer | haiku | Quick checks, fast feedback |
| troubleshooter | sonnet | Debugging needs thorough analysis |
| testing-agent | haiku | Test execution is straightforward |
| frontend-* | sonnet | Technical implementation |
| backend-* | sonnet | Technical implementation |
| database-* | sonnet | Schema design needs care |
| devops-* | haiku | Infrastructure tasks are procedural |

---

## Tips for Best Results

### 1. Be Specific
```
❌ "Fix the bug"
✅ "Fix the 500 error that occurs when submitting the login form with an invalid email"
```

### 2. Provide Context
```
❌ "Add a button"
✅ "Add a logout button to the header component that clears the session and redirects to /login"
```

### 3. Use the Right Agent
```
❌ Using frontend agent to fix a database query
✅ Using database agent for queries, frontend agent for UI
```

### 4. Let Orchestrator Coordinate
For tasks spanning multiple domains, start with `/orchestrator` rather than individual agents.

### 5. Check STATUS.md
Before starting new work, check if any agents are currently working or if files are locked.

---

## Regenerating Agents

If your stack changes (new dependencies, frameworks), regenerate:

```
/create-agents --regenerate
```

This will:
1. Re-analyze the project
2. Detect new frameworks
3. Update agents accordingly
4. Preserve customizations marked with `<!-- CUSTOM -->`

---

## Customizing Agents

To add custom instructions to an agent that survive regeneration:

```markdown
<!-- CUSTOM -->
## Additional Rules for This Project
- Always use Tailwind CSS for styling
- Follow the project's naming conventions
<!-- /CUSTOM -->
```

---

## Troubleshooting

### Agent Not Found
```
Error: Agent 'frontend-react' not found
```
Run `/create-agents` to generate the agent team.

### File Lock Conflict
```
Error: File is locked by another agent
```
Check STATUS.md and wait for the lock to be released, or coordinate via orchestrator.

### Agent Error
```
Error: Agent encountered an issue
```
Check the error details and use `/troubleshooter` to investigate.

---

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

---

*Generated by the agent-making skill. For issues, run `/create-agents --regenerate` or modify agents manually.*
