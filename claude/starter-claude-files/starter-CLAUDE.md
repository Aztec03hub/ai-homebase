# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
All entries added to this file must use a standardized schema and be added to their appropriate section, ALWAYS after the standardized schemas. Standardized schemas should always appear at the top of the file immediately after this instruction.

---

# STANDARDIZED SCHEMAS

## Critical Rule Schema

````yaml
## 🚨 CRITICAL RULE - [RULE_NAME] ([DATE])

**MANDATORY REQUIREMENT**: Brief statement of the rule

**Context**: Why this rule exists

**Correct Pattern**:
```code
// Example of correct implementation
```

**What Breaks Without This:**
- Specific failure modes
- Error messages you'll see

**Verification:** How to check it's working

---

````

---

## Tool Usage Pattern Schema

````yaml
## 🔧 TOOL USAGE PATTERN - [TOOL_NAME] - [PATTERN_NAME] ([DATE])

**Mandatory Pattern**:
```code
// Exact syntax that must be used
```

**Key Requirements**:
- Specific requirements for success
- Common failure modes to avoid

**Why This Pattern**:
- Technical reasoning
- What breaks without it

**Example Usage**:
```code
// Working example
```

**Common Mistakes**:
- ❌ Wrong pattern
- ✅ Correct pattern

---

````

---

## Memory Entry Schema

```yaml
## 🧠 MEMORY ENTRY - [CATEGORY] - [MEMORY_ENTRY_NAME] - [PRIORITY (CRITICAL/HIGH/MEDIUM/LOW)] ([DATE])

**Context**: Brief description of the situation/problem (concise summary)
**Issue/Challenge**: Specific problem encountered (concise summary)
**Solution**: What was implemented/decided (concise summary)
**Details**:
- Specific implementation details
- Code patterns used
- Commands/tools involved

---

```

---

## Testing Information Schema

```yaml
## 🧪 TESTING INFORMATION - [TEST_TYPE] - [COMPONENT/FEATURE] ([DATE])

**Test Status**: ✅ PASSING ([X]/[Y]) / 🔄 PARTIAL / ❌ FAILING / [ ] NOT_IMPLEMENTED

**Coverage**: [XX]% (if applicable)

**Key Test Cases**:
- Test case 1: Description (status)
- Test case 2: Description (status)

**Implementation Details**:
- Framework/tools used
- Specific patterns established

**Lessons Learned**:
- What worked well
- What to avoid next time

**Files**: Test file locations

**Priority Levels**: 🚨 CRITICAL, ⚡ HIGH, 📋 MEDIUM, 🎨 LOW

**Categories**: TOOL_USAGE, TESTING, CONFIG, DEBUGGING, API, DATABASE, WORKFLOW, etc.

---

```

---

## Task Plan Schema

```yaml
## 📋 Task Plan: [TITLE]

**Goal**: Clear objective description

**Priority**: 🔥 CRITICAL / ⚡ HIGH / 📋 MEDIUM / 🎨 LOW

**Status**: ✅ [x] COMPLETE / 🔄 [~] IN_PROGRESS / [ ] NOT_STARTED

**Implementation Phases**:
#### Phase 1: [Name] [Status]
- [ ] Step 1.1: Description
- [x] Step 1.2: Completed item

**Success Criteria**:
- [ ] Measurable outcome 1
- [ ] Measurable outcome 2

**Files Created/Modified**: List when complete

---

```

---

# CRITICAL RULES SECTION

## 🚨 CRITICAL RULE - Workspace Tool Usage Requirements (2025-06-24)

**MANDATORY REQUIREMENT**: Always start with get_workspace_context, read CLAUDE.md in full, then follow TDD patterns

**Context**: Ensures proper workspace understanding and follows established development patterns

**Correct Pattern**:

```bash
# Required startup sequence:
1. get_workspace_context() - understand current workspace
2. read_file_code(CLAUDE.md) in 1500 line chunks - get latest context
3. Use TDD (Test-Driven Development): write tests first, implement basic functionality, add complexity
4. Always check diagnostics after apply_diff
5. Run linter/prettier after fixing syntax errors
6. Always ask for permission first before updating/adding an entry to CLAUDE.md. All entries added to CLAUDE.md must use a standardized schema and be added to their appropriate section, ALWAYS after the standardized schemas.
```

**What Breaks Without This:**

- Missing critical workspace context
- Duplicate work or conflicting patterns
- Poor code quality without TDD
- Syntax errors going undetected

**Verification**: Workspace context obtained, CLAUDE.md read completely, tests written before implementation

---

## 🚨 CRITICAL RULE - CLAUDE.md Schema Usage (2025-06-18)

**MANDATORY REQUIREMENT**: All new content added to CLAUDE.md must use the standardized schemas and be placed in an appropriate section.
**Context**: Maintains organization and prevents file from becoming bloated again
**Correct Pattern**:

```yaml
# Choose appropriate schema from top of file:
# Memory Entry, Tool Usage Pattern, Task Plan, Critical Rule, or Testing Information
# Follow exact format including priority levels and categories
# Use concise summaries, avoid verbose explanations
# Remove or consolidate duplicate information
# Add to appropriate section, for example `# CRITICAL RULES SECTION`, `# TOOL USAGE PATTERN SECTION`, `# MEMORY ENTRY SECTION`, `# TESTING INFORMATION SECTION`, or `# TASK PLAN SECTION`.
```

**What Breaks Without This:**

- File becomes disorganized and hard to navigate
- Information becomes redundant and bloated
- Future Claude instances waste time parsing verbose content
- Token costs increase unnecessarily

**Verification**: New entries follow schema format exactly and are appropriately categorized

---

## 🚨 CRITICAL RULE - Source Code Modification Workflow (2025-06-13)

**MANDATORY REQUIREMENT**: All source code modifications must follow complete quality assurance workflow

**Context**: Ensures quality, consistency, and reliability for all code changes

**Correct Pattern**:

```bash
# After apply_diff
npm run lint                    # Check linting issues
npx prettier --write .          # Format code consistently
# Then use get_diagnostics_code tool
# Fix any issues found
# For tests, run: npm test (or specific test command)
# Fix any test failures
```

**What Breaks Without This:**

- Syntax errors and type issues go undetected
- Inconsistent code formatting across project
- Broken tests that appear to work
- Professional standards violations

**Verification**: All tools pass (lint, prettier, diagnostics, tests) before proceeding

---

## 🚨 CRITICAL RULE - apply_diff File Modification Pattern (2025-06-24)

**MANDATORY REQUIREMENT**: Never rewrite entire files when using apply_diff; always use precise, batch calls after re-reading file content

**Context**: Avoids file corruption and maintains precision in code modifications

**Correct Pattern**:

```bash
# 1. Re-read entire file in 1500 line chunks when issues exist
read_file_code({ path: 'file.ts', maxCharacters: 200000, startLine: -1, endLine: 1500 })
read_file_code({ path: 'file.ts', maxCharacters: 200000, startLine: 1500, endLine: 3000 })
# Continue until entire file is read

# 2. Use precise, batch apply_diff calls to fix issues
apply_diff({
  description: 'Fix specific issues',
  diffs: [
    { search: 'exact text 1', replace: 'fixed text 1', startLine: X, endLine: Y },
    { search: 'exact text 2', replace: 'fixed text 2', startLine: A, endLine: B }
  ],
  filePath: 'file.ts'
})
```

**What Breaks Without This:**

- File corruption from incomplete rewrites
- Loss of existing functionality
- Major formatting issues requiring complete file reconstruction
- Context loss from not understanding complete file state

**Verification**: File maintains all existing functionality, no syntax errors, proper formatting

---

## 🚨 CRITICAL RULE - Step-by-Step Approval Workflow (2025-06-24)

**MANDATORY REQUIREMENT**: Get explicit approval for each step; prior approval does not imply subsequent approval

**Context**: Ensures user maintains control over implementation process and can provide feedback

**Correct Pattern**:

```bash
# For each step:
1. Present comprehensive task plan with confidence level (out of 10)
2. Wait for explicit approval
3. Implement only the approved step
4. Verify step works properly
5. Update task plan status
6. Wait for approval before next step
```

**What Breaks Without This:**

- Implementing unwanted changes
- Missing critical user feedback
- Proceeding with incorrect assumptions
- User loses control over development process

**Verification**: Each step gets explicit user approval before any code modification tools are used

---

# TOOL USAGE PATTERN SECTION

## 🔧 TOOL USAGE PATTERN - apply_diff - File Creation Pattern (2025-09-12)

**Mandatory Pattern**:

```code
// For creating new files, ALWAYS use apply_diff with empty search:
{
  "search": "",
  "endLine": -1,
  "replace": "full file content here",
  "startLine": 1
}
```

**Key Requirements**:

- NEVER use internal file creation tools (create_file, etc.)
- Always use vscode-mcp-server:apply_diff for ALL file operations
- Empty search string indicates new file creation
- endLine: -1 means "to end of file"

**Why This Pattern**:

- User explicitly requires all file operations through vscode MCP tools
- Ensures consistency across all file operations
- Maintains proper workspace integration

**Example Usage**:

```typescript
// Creating a new TypeScript file
{
  "search": "",
  "endLine": -1,
  "replace": "import { json } from '@sveltejs/kit';\n\nexport async function GET() {\n  return json({ status: 'ok' });\n}",
  "startLine": 1
}
```

**Common Mistakes**:

- ❌ Using create_file or other internal tools
- ✅ Always using apply_diff for file creation

---

## 🔧 TOOL USAGE PATTERN - apply_diff - Exact Text Matching Pattern (2025-06-16)

**Mandatory Pattern**:

```typescript
// 1. ALWAYS read file first
read_file_code({ path: 'file.ts', startLine: X, endLine: Y });

// 2. Use exact text matching
apply_diff({
  description: 'Clear description of change',
  diffs: [
    {
      search: 'exact text from file (including tabs/spaces)',
      replace: 'exact replacement text',
      startLine: X,
      endLine: Y
    }
  ],
  filePath: 'path/to/file'
});
```

**Key Requirements**:

- Read file content first to get exact text
- Search text must match EXACTLY (whitespace, tabs, indentation)
- Never pass empty diffs array
- Always include startLine/endLine for targeting

**Why This Pattern**:

- Fuzzy matching handles content drift but needs exact base text
- Empty diffs cause "Cannot convert undefined or null to object" error
- Reading first ensures accurate text matching

**Example Usage**:

```typescript
// Read first, then apply exact changes
const fileContent = await read_file_code({ path: 'src/component.ts' });
apply_diff({
  description: 'Update import statement',
  diffs: [
    {
      search: "import { old } from './utils';",
      replace: "import { old, new } from './utils';",
      startLine: 5,
      endLine: 5
    }
  ],
  filePath: 'src/component.ts'
});
```

**Common Mistakes**:

- ❌ Empty or undefined diffs array
- ❌ Approximate text matching without reading file
- ❌ Missing whitespace or incorrect indentation
- ✅ Always read file first, use exact text

---

## 🔧 TOOL USAGE PATTERN - search_files - Absolute Path Pattern (2025-06-16)

**Mandatory Pattern**:

```typescript
search_files({
  path: 'C:/Users/plafayette/workspace/node_projects/NavTrack',
  pattern: 'ComponentName'
});
```

**Key Requirements**:

- Always use full absolute paths with forward slashes
- Never use relative paths (./src, src/lib, etc.)
- Must be within allowed directories
- Case-sensitive paths must match filesystem exactly

**Why This Pattern**:

- Tool validates paths against allowed directories for security
- Relative paths cause "Access denied" errors every time
- Essential for navigating large codebases efficiently

**Example Usage**:

```typescript
// ✅ CORRECT - Full absolute path
search_files({
  path: 'C:/Users/plafayette/workspace/node_projects/NavTrack',
  pattern: '*.svelte'
});
```

**Common Mistakes**:

- ❌ `search_files({ path: 'src', pattern: 'Component' })` // Access denied
- ❌ `search_files({ path: './src/lib', pattern: 'utils' })` // Access denied
- ✅ Use workspace root with forward slashes always

---

---

# MEMORY ENTRY SECTION

---

# TESTING INFORMATION SECTION

---

# TASK PLAN SECTION

---

[EOF]