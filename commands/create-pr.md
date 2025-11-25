Create PR from current branch to main.

Arguments:
- `--base` (optional) - Target branch (default: main)

Steps:
1. Get current branch: `git branch --show-current`
2. Extract issue number from branch prefix (e.g., `152-feature-name` → #152)
3. Get commits since base: `git log <base>..HEAD --format="%s"`
4. Determine type from branch/commits: feature, fix, refactor, docs, chore, test
5. Generate title: `<type>/<description>`
6. Generate body:
   - One sentence summary of the feature/fix
   - Checked list of completed work from commits: `- [x] <item>`
   - `Closes #<issue-number>`
7. Create PR: `gh pr create --title "<title>" --body "<body>" --base <base>`
8. Get PR number from output, update title: `gh pr edit <num> --title "<type>/<description> (#<num>)"`
9. Output: `Created PR: <url>`

Important:
- No Claude signature
- Use `- [x]` for list items
- Title format: `<type>/<message> (#<pr-number>)`

Example usage:
- `/create-pr`
- `/create-pr --base develop`
