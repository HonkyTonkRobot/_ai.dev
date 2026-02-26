Fetch GitHub project configuration and team members into `.claude/GITHUB.md`.

Run this once per repository to enable `/create-issue` and team-aware assignments.

Steps:
1. Check `gh auth status`. If not authenticated, output: "GitHub CLI not authenticated. Run: `gh auth login`" and stop.
2. Detect project owner from the repository:
   - Run `gh repo view --json owner --jq '.owner.login'` to get the repo owner
   - Use this owner for all subsequent `gh project` commands
3. Run `gh project list --owner <repo-owner> --format json` to list projects.
4. If no projects found, output: "No GitHub projects found for <repo-owner>. Create one at: https://github.com/orgs/<repo-owner>/projects" and stop.
5. Auto-select project if one matches the repo name. Otherwise, if multiple, prompt user to select one.
6. For the selected project, fetch details:
   - Get project node ID from `gh project view <number> --owner <repo-owner> --format json` (extract `.id`)
   - Get status field: `gh project field-list <number> --owner <repo-owner> --format json` and find field where `name` contains "Status"
   - Get priority field: find field where `name` contains "Priority" (optional)
   - Get size field: find field where `name` contains "Size" (optional)
7. Fetch team members:
   - Run `gh api graphql -f query='{ organization(login: "<repo-owner>") { membersWithRole(first: 50) { nodes { login name } } } }'`
   - If org query fails (personal repo), use current user only: `gh api user --jq '.login'`
8. Determine default assignee:
   - Current authenticated user: `gh api user --jq '.login'`
9. Read `.claude/GITHUB.md` if it exists and check if content already exists.
10. If exists, ask user: "`.claude/GITHUB.md` already exists. Overwrite? (yes/no)"
    - If no, stop
    - If yes, continue
11. Generate GITHUB.md with this format:
    ```
    ## Project Configuration

    - **Project Owner**: <repo-owner>
    - **Project Number**: <number>
    - **Project Node ID**: <node_id>

    ### Status Field
    - **Field ID**: <field_id>
    - **Options**:
      - <option_name>: `<option_id>`
      ...

    ### Priority Field
    - **Field ID**: <field_id>
    - **Options**:
      - <option_name>: `<option_id>`
      ...

    ### Size Field
    - **Field ID**: <field_id>
    - **Options**:
      - <option_name>: `<option_id>`
      ...

    ## Team

    Default assignee: <current-user-login>

    | Name | GitHub |
    |------|--------|
    | <display_name or login> | <login> |
    ...
    ```
12. Write to `.claude/GITHUB.md`.
13. Output success message:
    ```
    Found project: <project_name> (#<number>)

    Written to .claude/GITHUB.md:
    - Project Node ID: <node_id>
    - Status field with <count> options
    - Priority field with <count> options (or "- No priority field")
    - Size field with <count> options (or "- No size field")
    - <count> team members

    Default assignee: <username>

    GitHub configuration complete. You can now use /create-issue.
    ```

Error messages:
- "GitHub CLI not authenticated. Run: `gh auth login`"
- "No GitHub projects found for <repo-owner>. Create one at: https://github.com/orgs/<repo-owner>/projects"
