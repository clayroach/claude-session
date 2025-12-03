# Search Archived Claude Sessions

Search my archived Claude Code session gists for: **$ARGUMENTS**

## Instructions

1. **List my gists** - Run this command to get recent session gists:

   ```bash
   gh gist list --limit 50
   ```

   This outputs tab-separated: `ID  DESCRIPTION  FILES  VISIBILITY  UPDATED`

2. **Filter to Claude sessions** - Look for descriptions containing "Claude" (case-insensitive). The description format is: `Claude Code session: {project-name} ({date})`

3. **Search descriptions first** - Filter results matching the search query from descriptions before fetching full content.

4. **For promising matches, fetch content**:

   ```bash
   gh gist view <gist-id> --raw
   ```

5. **Search the content** for the query terms and extract relevant context.

6. **Present results** as a numbered list with:
   - Date (from UPDATED column or description)
   - Project name (from description)
   - Commit SHA (if present in content - look for `> **Commit:**` block)
   - Brief preview of the matching content

## After Finding Results

When the user selects a session (e.g., "load #2"), offer these three options:

### Option 1: Load Gist + Git Diff

- Fetch the full gist markdown content with `gh gist view <id> --raw`
- Parse the commit info block to get the branch name (look for `> **Branch:** <name>`)
- Run `git log --oneline <branch> -10` and `git diff main...<branch>` to show code context
- Present the session content plus the code changes

### Option 2: Load Full Local Session (if available)

- Parse the Session ID from the gist's metadata table (`| **Session ID** | \`uuid\` |`)
- Check if the session file exists locally:

  ```bash
  ls ~/.claude/projects/*/<session-id>.jsonl 2>/dev/null
  ```

- If found, read and present the full session content
- If not found, inform the user the local session is no longer available

### Option 3: Resume via /resume

- Parse the Session ID from the gist
- Check if the session exists locally (same as option 2)
- If it exists, tell the user:
  "The session exists locally. Run `/resume` and look for the session from [date] with [N] messages in project [project-name]."
- Note: `/resume` is interactive-only; we cannot auto-jump to a specific session

## Token Efficiency Tips

- Only fetch full gist content for matches (not all gists)
- Parse metadata from the first ~20 lines (commit block + metadata table)
- Stop searching after finding enough good matches
