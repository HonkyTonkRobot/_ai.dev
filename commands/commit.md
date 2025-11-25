Make logical semantic commits for uncommitted changes.

Steps:
1. Get changes: `git status --porcelain`
2. If no changes, output "No changes to commit." and stop
3. Read diffs to understand what changed: `git diff`, `git diff --cached`
4. Group changes by logical purpose (one commit is often enough)
5. For each group:
   - Stage files: `git add <files>`
   - Commit: `git commit -m "<type>(<scope>): <message>"`
6. Output list of commits created:
   ```
   Created <n> commit(s):
   - <hash> <type>(<scope>): <message>
   ```

Commit format:
- `<type>(<scope>): <message>` - scope is optional
- Types: feat, fix, refactor, docs, test, chore, style
- Lowercase, imperative mood, no period, ~72 chars max

Important:
- No Claude signature or co-author
- Group logically, don't over-split

Example usage:
- `/commit`
