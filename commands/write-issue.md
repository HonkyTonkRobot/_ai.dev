Guide drafting of GitHub issues with consistent structure and quality.

Apply Hemingway writing principles:
- Short sentences. Clear language.
- Active voice over passive.
- No adverbs. Strong verbs.
- One idea per sentence.
- Cut unnecessary words.

Steps:
1. Ask user for issue type. Present options:
   - Feature
   - Bug
   - Refactor
   - Chore
2. Ask user for a brief description of the issue (1-2 sentences).
3. Gather context through clarifying questions:
   - What is the current state?
   - What is the desired state?
   - Why does this matter?
   - Are there any constraints or technical decisions already made?
4. Based on the conversation, generate a draft following this template:

   ```markdown
   # <Type>: <Title>

   ## Summary

   <One to three sentences describing the goal.>

   ## Context

   <Background information. Why this matters. Current state vs desired state.>

   ## Technical Decisions

   <Key decisions already made. Architecture choices. Constraints.>

   ### <Decision Category>
   - Decision point 1
   - Decision point 2

   ## Tasks

   ### <Category>
   - [ ] Task 1
   - [ ] Task 2

   ### <Category>
   - [ ] Task 3
   - [ ] Task 4

   ## Success Criteria

   - [ ] Criterion 1
   - [ ] Criterion 2
   - [ ] Criterion 3

   ## Files to Modify

   - `path/to/file1.ts`
   - `path/to/file2.tsx`

   ## Out of Scope

   - Item 1
   - Item 2
   ```

5. Generate a slug from the title:
   - Convert to lowercase
   - Replace spaces with hyphens
   - Remove special characters
   - Keep 3-5 key words maximum
   - Example: "Feature: Hybrid Assessment Type" → "hybrid-assessment-type"
6. Save draft to `.docs/<slug>.md`
7. Output: "Draft saved to `.docs/<slug>.md`. Run `/create-issue <slug>` to publish."

Notes:
- Create `.docs/` directory if it doesn't exist
- If file already exists, ask user: "Draft '<slug>.md' already exists. Overwrite? (yes/no)"
- Ensure all writing follows Hemingway principles
- Tasks should be actionable and specific
- Success criteria should be measurable
