Fetch GitHub project configuration and add it to CLAUDE.md.

Run this once per repository to enable `/create-issue` functionality.

Steps:
1. Check `gh auth status`. If not authenticated, output: "GitHub CLI not authenticated. Run: `gh auth login`" and stop.
2. Run `gh project list --owner @me --format json` to list user's projects.
3. If no projects found, output: "No GitHub projects found. Create one at: https://github.com/settings/projects" and stop.
4. If only one project, use it. If multiple, prompt user to select one.
5. For the selected project, fetch details:
   - Get project node ID from `gh project view <number> --owner @me --format json` (extract `.id`)
   - Get status field: `gh project field-list <number> --owner @me --format json` and find field where `name` contains "Status"
   - Get priority field: `gh project field-list <number> --owner @me --format json` and find field where `name` contains "Priority" (optional)
6. Get default assignee from `gh api user --jq .login`
7. Read CLAUDE.md and check if "## GitHub Project Configuration" section already exists.
8. If exists, ask user: "GitHub project configuration already exists. Overwrite? (yes/no)"
   - If no, stop
   - If yes, remove old section and continue
9. Generate markdown configuration block with format:
   ```
   ## GitHub Project Configuration

   Project integration for issue and branch management commands.

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
10. Append this configuration block to CLAUDE.md
11. Output success message:
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
- "No GitHub projects found. Create one at: https://github.com/settings/projects"
