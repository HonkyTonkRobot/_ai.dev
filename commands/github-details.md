Fetch GitHub project configuration and add it to CLAUDE.md.

Run this once per repository to enable `/create-issue` functionality.

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
   - Get priority field: `gh project field-list <number> --owner <repo-owner> --format json` and find field where `name` contains "Priority" (optional)
7. Get default assignee from `gh api user --jq .login`
8. Read CLAUDE.md and check if "## GitHub Project Configuration" section already exists.
9. If exists, ask user: "GitHub project configuration already exists. Overwrite? (yes/no)"
   - If no, stop
   - If yes, remove old section and continue
10. Generate markdown configuration block with format:
   ```
   ## GitHub Project Configuration

   Project integration for issue and branch management commands.

   - **Project Owner**: <repo-owner>
   - **Project Number**: <number>
   - **Project Node ID**: <node_id>
   - **Default Assignee**: <username>

   ### Status Field
   - **Field ID**: <field_id>
   - **Options**:
     - <option_name>: `<option_id>`
     - <option_name>: `<option_id>`
     ...

   ### Priority Field
   - **Field ID**: <field_id>
   - **Options**:
     - <option_name>: `<option_id>`
     - <option_name>: `<option_id>`
     ...
   ```
11. Append this configuration block to CLAUDE.md
12. Output success message:
    ```
    Found project: <project_name> (#<number>)

    Added to CLAUDE.md:
    - Project Node ID: <node_id>
    - Default Assignee: <username>
    - Status field with <count> options
    - Priority field with <count> options (or "- No priority field" if not found)

    GitHub project configuration complete. You can now use /create-issue.
    ```

Error messages:
- "GitHub CLI not authenticated. Run: `gh auth login`"
- "No GitHub projects found for <repo-owner>. Create one at: https://github.com/orgs/<repo-owner>/projects"
