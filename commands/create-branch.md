Create a branch linked to a GitHub issue with AI-generated concise name.

Arguments:
- `<issue-number>` (required) - GitHub issue number
- `--name` (optional) - Custom branch name slug (default: AI-generated)
- `--base` (optional) - Base branch (default: main)

Steps:
1. Check `gh auth status`. If not authenticated, output: "GitHub CLI not authenticated. Run: `gh auth login`" and stop.
2. Fetch issue title via `gh issue view <issue-number> --json title --jq .title`
3. If issue doesn't exist (command fails), output: "Issue #<issue-number> not found. Check the issue number and try again." and stop.
4. Generate branch name:
   - If `--name` provided: use `<issue-number>-<name>`
   - Else: AI generates concise slug from issue title using these rules:
     - Convert to lowercase
     - Replace spaces with hyphens
     - Remove special characters (keep alphanumeric and hyphens)
     - Keep 3-5 key words maximum
     - Preserve key meaning
     - Remove articles (a, an, the), prepositions (of, for, to, in, on), filler words
     - Example: "Feature: Hybrid Assessment Type" → "hybrid-assessment-type"
     - Example: "Teacher Context-Based Assessment Viewing" → "teacher-assessment-context"
     - Example: "Fix Login Error on Safari Browsers" → "fix-safari-login"
     - Example: "Replace teacher submissions search input with categorized dropdown" → "teacher-search-dropdown"
   - Format: `<issue-number>-<slug>`
5. Create and checkout branch: `gh issue develop <issue-number> --name <branch-name> --base <base> --checkout`
6. Output success message:
   ```
   Branch: <branch-name>
   Base: <base>
   Linked to: Issue #<issue-number>

   Checked out branch '<branch-name>'.
   ```

Error messages:
- "GitHub CLI not authenticated. Run: `gh auth login`"
- "Issue #<issue-number> not found. Check the issue number and try again."

Example usage:
- `/create-branch 152`
- `/create-branch 152 --name custom-branch-name`
- `/create-branch 152 --base develop`

Branch name generation examples:
| Issue Title | Generated Branch |
|-------------|------------------|
| Feature: Hybrid Assessment Type | `151-hybrid-assessment-type` |
| Teacher Context-Based Assessment Viewing | `152-teacher-assessment-context` |
| Fix Login Error on Safari Browsers | `153-fix-safari-login` |
| Replace teacher submissions search input with categorized dropdown | `154-teacher-search-dropdown` |
