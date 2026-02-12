Fetch new GitHub PR review comments, track seen state, and activate gh-comment-analyst for fix planning.

Arguments:
- `<pr-number>` (optional) - PR number. Auto-detects from current branch if omitted.
- `--all` (optional) - Ignore seen state and process all comments as new.

Steps:
1. Determine PR number:
   - If argument provided, use it
   - Otherwise: `gh pr view --json number -q .number`
   - If no PR found, output "No open PR for current branch." and stop
2. Get repo owner/name: `gh repo view --json nameWithOwner -q .nameWithOwner`
3. Fetch all review comments:
   - PR review comments (inline): `gh api repos/{owner}/{repo}/pulls/{pr}/comments --paginate`
   - PR reviews (top-level): `gh api repos/{owner}/{repo}/pulls/{pr}/reviews --paginate`
   - Issue comments (conversation): `gh api repos/{owner}/{repo}/issues/{pr}/comments --paginate`
4. Load seen state from `/tmp/gh-review-pr-<number>/seen-comments.json`
   - If file doesn't exist or `--all` flag, treat all comments as new
   - File format:
     ```json
     {
       "pr_number": 123,
       "last_fetched": "2026-02-11T10:00:00Z",
       "seen_ids": {
         "review_comments": [111, 222],
         "reviews": [333],
         "issue_comments": [444, 555]
       }
     }
     ```
5. Diff fetched comments against seen IDs to identify new comments only
6. If no new comments, output "No new review comments on PR #<number>." and stop
7. Save updated seen state to `/tmp/gh-review-pr-<number>/seen-comments.json`
   - Merge new IDs into existing seen IDs (don't replace, append)
   - Update `last_fetched` timestamp
8. Activate gh-comment-analyst agent with the new comments for analysis and fix planning

Important:
- All state files go in `/tmp/gh-review-pr-<number>/` (auto-cleans on reboot, no project clutter)
- Create the temp directory if it doesn't exist
- Always preserve existing seen IDs when updating (append, never overwrite)
- Filter out bot comments (github-actions, dependabot, etc.)
- Filter out your own comments (only process comments from others)
- Include comment metadata: author, file path, line number, created date, in_reply_to
- Pass the full diff context for inline comments so the analyst understands what code is being discussed

Example usage:
- `/read-gh-comments` - Auto-detect PR, show only new comments
- `/read-gh-comments 152` - Fetch comments for PR #152
- `/read-gh-comments --all` - Re-process all comments (ignore seen state)
