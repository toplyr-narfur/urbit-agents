# Migration Guide

## Repository Consolidation

This guide helps you migrate from the previous repository structure to the new unified `agents` repository.

## What Changed?

### Repository Structure

**Previous Structure** (before consolidation):
```
~/.claude/
├── agents/                    # wshobson/agents (agents only)
│   ├── backend-architect.md
│   ├── frontend-developer.md
│   └── ... (83 agent files in root)
└── commands/                  # wshobson/commands (separate repo)
    ├── feature-development.md
    ├── api-scaffold.md
    └── ... (workflow and tool files)
```

**New Unified Structure** (current):
```
~/.claude/
└── agents/                    # wshobson/agents (everything unified)
    ├── agents/                # All agent definitions
    │   ├── backend-architect.md
    │   ├── frontend-developer.md
    │   └── ... (83 files)
    ├── workflows/             # Multi-agent orchestrators
    │   ├── feature-development.md
    │   ├── security-hardening.md
    │   └── ... (15 files)
    ├── tools/                 # Development utilities
    │   ├── api-scaffold.md
    │   ├── security-scan.md
    │   └── ... (42 files)
    └── README.md
```

### Key Changes Summary

| What Changed | Before | After |
|--------------|--------|-------|
| **Agents location** | Root of `agents/` repo | `agents/agents/` subdirectory |
| **Workflows location** | Separate `commands/` repo | `agents/workflows/` subdirectory |
| **Tools location** | Separate `commands/` repo | `agents/tools/` subdirectory |
| **Repository count** | 2 repositories | 1 unified repository |

## Migration Steps

### Step 1: Update Your Agents Repository

```bash
cd ~/.claude/agents
git pull origin main
```

This will:
- Move all agent files into the `agents/` subdirectory
- Add the `workflows/` directory with 15 workflow orchestrators
- Add the `tools/` directory with 42 development tools

### Step 2: Remove Old Commands Repository (If Installed)

If you previously had the `commands` repository installed:

```bash
# Check if it exists
ls ~/.claude/commands

# If it exists, remove it
rm -rf ~/.claude/commands
```

The `commands` repository is now deprecated. All functionality has been moved to the unified `agents` repository.

### Step 3: Verify Installation

Check that your directory structure is correct:

```bash
ls -la ~/.claude/agents
```

You should see:
```
agents/
workflows/
tools/
README.md
LICENSE
```

### Step 4: Update Your Workflow

No changes needed for agent invocations, but workflows and tools now use prefixed commands.

## Command Syntax Changes

### Workflows (formerly in commands repo)

**Old Syntax**:
```bash
/feature-development implement user authentication
/commands:feature-development implement user authentication
```

**New Syntax**:
```bash
/workflows:feature-development implement user authentication
```

### Tools (formerly in commands repo)

**Old Syntax**:
```bash
/api-scaffold create user endpoints
/commands:api-scaffold create user endpoints
```

**New Syntax**:
```bash
/tools:api-scaffold create user endpoints
```

### Agents (no change)

Agent invocations work exactly as before:

```bash
"Use backend-architect to design the API"
"Have security-auditor review this code"
"Get performance-engineer to optimize this query"
```

## What Stays the Same?

✅ All 83 agents work identically
✅ Agent capabilities unchanged
✅ Direct agent invocation syntax unchanged
✅ All workflow and tool functionality preserved
✅ Multi-agent orchestration patterns unchanged

## What's Different?

⚠️ Agent files moved to `agents/` subdirectory
⚠️ Workflow command prefix changed to `/workflows:`
⚠️ Tool command prefix changed to `/tools:`
⚠️ Commands repository deprecated

## Common Migration Issues

### Issue: "Command not found" errors

**Symptom**: `/feature-development` no longer works

**Solution**: Use the new prefix syntax:
```bash
/workflows:feature-development
```

### Issue: Agents not loading

**Symptom**: Claude Code doesn't recognize agents

**Solution**:
1. Verify structure: `ls ~/.claude/agents/agents/`
2. Restart Claude Code
3. Check for errors in Claude Code logs

### Issue: Both repos installed

**Symptom**: Duplicate commands showing up

**Solution**: Remove the old commands repository:
```bash
rm -rf ~/.claude/commands
```

## Testing Your Migration

After migrating, test these commands to verify everything works:

### Test Workflows
```bash
/workflows:feature-development test feature
/workflows:security-hardening scan project
```

### Test Tools
```bash
/tools:api-scaffold create test endpoint
/tools:code-explain describe this function
```

### Test Agents
```bash
"Use backend-architect to review the architecture"
"Have frontend-developer suggest improvements"
```

## Rollback Instructions

If you need to rollback to the previous structure:

```bash
cd ~/.claude/agents
git checkout <previous-commit-hash>
```

Or reinstall the old commands repository:

```bash
cd ~/.claude
git clone https://github.com/wshobson/commands.git
```

Note: The `commands` repository is deprecated and won't receive updates.

## Need Help?

- **Documentation**: [README.md](README.md)
- **Issues**: https://github.com/wshobson/agents/issues
- **Claude Code Docs**: https://docs.anthropic.com/en/docs/claude-code

## Timeline

- **Before**: Two separate repositories (`agents` + `commands`)
- **Now**: One unified repository (`agents` with subdirectories)
- **Future**: Plugin marketplace integration (coming soon)
