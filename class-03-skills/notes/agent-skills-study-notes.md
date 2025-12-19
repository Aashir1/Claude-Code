# Study Notes: Agent Skills in Claude Code

---

## Summary

**Agent Skills** are a way to add new features to Claude Code. Think of them like plugins or extensions that teach Claude how to do specific tasks. The main difference between Skills and regular commands is that Claude decides on its own when to use a Skill - you don't need to type a special command. Skills are stored in folders containing a `SKILL.md` file that tells Claude what the Skill does. You can save Skills in three places: your personal folder (`~/.claude/skills/`) for your own use, a project folder (`.claude/skills/`) to share with your team, or they can come built-in with plugins. When you ask Claude to do something, it reads the Skill descriptions and automatically picks the right Skill to help you.

---

## Important Terms to Know

### **Agent Skills**
Special folders that add new abilities to Claude. Each folder has a `SKILL.md` file with instructions, and can also include helper files like scripts.

### **Model-Invoked vs User-Invoked**
- **Model-invoked**: Claude chooses when to use it automatically (Agent Skills work this way)
- **User-invoked**: You have to type a command to use it (like typing `/command`)

### **SKILL.md**
The main file in every Skill folder. It has two parts:
- Top section with information about the Skill (called YAML metadata)
- Bottom section with instructions written in Markdown

### **YAML Metadata**
The information at the top of SKILL.md between the `---` lines. It includes the Skill's name and description.

### **Progressive Disclosure**
A smart way Claude reads files - it only opens and reads extra files when it actually needs them, instead of reading everything at once.

### **Allowed-Tools**
An option you can add to SKILL.md that limits which tools Claude can use with that Skill. This is useful for making "read-only" Skills that can't change files.

---

## Three Types of Skills

### 1. Personal Skills

**Where they live**: `~/.claude/skills/`

**What they're for**:
- Your own personal tools and preferences
- Testing new Skills you're building
- Tools only you need

**Note**: These work in all your projects but are not shared with others.

### 2. Project Skills

**Where they live**: `.claude/skills/` (inside your project folder)

**What they're for**:
- Team tools everyone on the project uses
- Project-specific helpers
- Shared scripts and utilities

**Note**: These are saved in git, so when someone clones the project, they get the Skills too.

### 3. Plugin Skills

**Where they come from**: Claude Code plugins

**What they're for**:
- Skills that come built-in with plugins
- Work exactly like personal and project Skills

---

## How to Write SKILL.md

### Required Fields (You MUST include these)

#### `name` Field
- Use only lowercase letters, numbers, and dashes
- Maximum 64 characters
- Example: `generating-commit-messages`

#### `description` Field
- Maximum 1024 characters
- Very important! This helps Claude know when to use your Skill
- Should say WHAT the Skill does AND WHEN to use it
- Include keywords people would actually say

### Optional Fields

#### `allowed-tools`
- List of tools Claude can use with this Skill
- Example: `Read, Grep, Glob`
- Good for read-only Skills or when you want to limit what the Skill can do

### Basic Structure

```markdown
---
name: your-skill-name
description: What your Skill does and when to use it
---

# Your Skill Name

## Instructions
Step by step what Claude should do

## Examples
Show examples of how to use the Skill

## Best Practices (optional)
Tips and suggestions
```

---

## Organizing Your Skill Folder

### Good Folder Setup

```
my-skill/
├── SKILL.md           (required - main file)
├── reference.md       (optional - extra docs)
├── examples.md        (optional - more examples)
├── scripts/           (optional - helper scripts)
│   └── helper.py
└── templates/         (optional - template files)
    └── template.txt
```

### Linking to Other Files

You can mention other files in your SKILL.md:

```markdown
For more details, see [reference.md](reference.md).

To run the helper:
```bash
python scripts/helper.py input.txt
```
```

**Remember**: Claude only reads these extra files when it needs them.

---

## How Claude Finds and Uses Skills

### Finding Skills

Claude automatically looks in these places:
1. Your personal Skills: `~/.claude/skills/`
2. Project Skills: `.claude/skills/`
3. Plugin Skills: from installed plugins

### Using Skills Automatically

When you ask Claude something, it:
- Reads your question
- Checks Skill descriptions
- Picks the best matching Skill
- Uses that Skill to help you

**You don't need to ask for a Skill by name** - Claude figures it out!

### Checking What Skills You Have

**Ask Claude**:
```
What Skills are available?
```

**Or check the folders yourself**:
```bash
# Personal Skills
ls ~/.claude/skills/

# Project Skills
ls .claude/skills/

# Read a specific Skill
cat ~/.claude/skills/my-skill/SKILL.md
```

---

## Testing and Fixing Skills

### How to Test

1. Write a question that matches your Skill description
2. See if Claude uses your Skill
3. Check if it works correctly
4. If Claude doesn't use it, make the description clearer

**Example**: If your Skill helps with PDFs:
```
Can you help me extract text from this PDF?
```

### Common Problems and Fixes

#### Problem 1: Claude Doesn't Use My Skill

**Reason**: Description is too vague

**Bad example**:
```yaml
description: Helps with documents
```

**Good example**:
```yaml
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

#### Problem 2: Can't Find the Skill File

**Check if the file exists**:
```bash
# Personal Skills
ls ~/.claude/skills/skill-name/SKILL.md

# Project Skills
ls .claude/skills/skill-name/SKILL.md
```

#### Problem 3: YAML Format Errors

**Common mistakes**:
- Forgot the `---` at the start or end
- Used Tab instead of spaces
- Forgot quotes around text with special characters

**Check the top of your file**:
```bash
cat SKILL.md | head -n 10
```

#### Problem 4: Wrong Skill Gets Used

**Reason**: Multiple Skills have similar descriptions

**Bad (too similar)**:
```yaml
# Skill 1
description: For data analysis

# Skill 2
description: For analyzing data
```

**Good (clearly different)**:
```yaml
# Skill 1
description: Analyze sales data in Excel files and CRM exports. Use for sales reports, pipeline analysis, and revenue tracking.

# Skill 2
description: Analyze log files and system metrics data. Use for performance monitoring, debugging, and system diagnostics.
```

---

## Sharing Skills with Your Team

### Method 1: Using Plugins (Recommended)

**Steps**:
1. Create a plugin with your Skills
2. Publish to a marketplace
3. Team installs the plugin

**Good for**: Wide distribution, version control, managing updates

### Method 2: Using Git

**Steps**:
```bash
# Step 1: Create the Skill in your project
mkdir -p .claude/skills/team-skill

# Step 2: Save to git
git add .claude/skills/
git commit -m "Add team Skill for PDF processing"
git push

# Step 3: Others pull and get the Skills
git pull
claude  # Skills are now ready to use
```

**Good for**: Simple sharing within one team, quick setup

---

## Best Practices (How to Make Good Skills)

### 1. Keep Each Skill Focused on One Thing

**Good examples** (one clear purpose):
- "PDF form filling"
- "Excel data analysis"
- "Git commit messages"

**Bad examples** (too many things):
- "Document processing" → Make separate Skills for PDF, Word, Excel
- "Data tools" → Split by what kind of data

### 2. Write Clear Descriptions

**Good**:
```yaml
description: Analyze Excel spreadsheets, create pivot tables, and generate charts. Use when working with Excel files, spreadsheets, or analyzing tabular data in .xlsx format.
```

**Bad**:
```yaml
description: For files
```

**What to include**:
- What actions the Skill can do
- What file types it works with
- Keywords people would actually say
- When to use it

### 3. Test with Your Team

**Testing checklist**:
- Does the Skill activate when it should?
- Are the instructions clear?
- Did you miss any examples?
- Are there any edge cases?

### 4. List Required Packages

**In the description**:
```yaml
description: Extract text, fill forms, merge PDFs. Use when working with PDF files, forms, or document extraction. Requires pypdf and pdfplumber packages.
```

**In SKILL.md**:
```markdown
## Requirements

Install these packages first:
```bash
pip install pypdf pdfplumber
```
```

### 5. Track Changes

```markdown
## Version History
- v2.0.0 (2025-10-01): Big changes to how it works
- v1.1.0 (2025-09-15): Added new features
- v1.0.0 (2025-09-01): First version
```

---

## Advanced: Limiting Tools

### Why Use allowed-tools?

The `allowed-tools` setting lets you control what Claude can do with a Skill. It's useful for:

1. **Read-only Skills**: Can look at files but not change them
2. **Limited Skills**: Only for specific tasks, can't do everything
3. **Security**: Reduce risk by limiting what's possible

### Example

```yaml
---
name: safe-file-reader
description: Read files without making changes. Use when you need read-only file access.
allowed-tools: Read, Grep, Glob
---

# Safe File Reader

This Skill can only read files, not change them.

## Instructions
1. Use Read to see file contents
2. Use Grep to search in files
3. Use Glob to find files by name pattern
```

**What happens**: Claude can only use Read, Grep, and Glob with this Skill. It can't edit or write files.

---

## Quick Memory Helpers

### SKILL.md Parts: "NYD"
- **N**ame (use dashes, max 64 letters)
- **Y**AML info (the metadata at top)
- **D**escription (max 1024 letters, be specific)

### Three Skill Locations: "PPP"
- **P**ersonal (`~/.claude/skills/`)
- **P**roject (`.claude/skills/`)
- **P**lugin (comes with plugins)

### Good Description Checklist: "WATT"
- **W**hat does it do?
- **A**ction words (analyze, create, extract)
- **T**rigger words people would say
- **T**ypes of files it works with

### Common Misunderstandings

1. **Wrong**: You have to type a command to use a Skill
   - **Right**: Claude picks Skills automatically

2. **Wrong**: All files in a Skill folder load at once
   - **Right**: Claude only reads files when needed

3. **Wrong**: `allowed-tools` gives Claude new tools
   - **Right**: `allowed-tools` limits which tools Claude can use

4. **Wrong**: Skills only work in one project
   - **Right**: Personal Skills work everywhere; project Skills are project-specific

---

## Questions to Test Understanding

### Basic Understanding

1. What's the difference between Skills (model-invoked) and slash commands (user-invoked)?

2. What are the three places where Skills can be stored, and when should you use each one?

3. What two things MUST be in every SKILL.md file's top section?

4. What does "progressive disclosure" mean and why is it helpful?

5. What should you put in a Skill's description to help Claude find it?

---

### Using What You Learned

6. You're making a Skill to create API docs from code comments. Should it be personal, project, or plugin? Why? What would you write in the description?

7. Your teammate says their Skill never works. What steps would you take to figure out why?

8. Make a SKILL.md file for a Skill that checks Python code for security issues but can only read files, not change them.

9. You made three Skills called "excel-analysis", "csv-processor", and "data-visualization". Users say Claude picks the wrong one sometimes. How would you fix the descriptions?

---

### Thinking Deeper

10. What are the pros and cons of sharing Skills through plugins vs through git? When would you use each method?

11. Why did they make Skills work automatically instead of making users type commands? What problems could this cause?

12. How does `allowed-tools` help with security? What are its limits?

13. Is it better to make one big Skill that does many things, or many small Skills that each do one thing? What factors should you consider?

---

### Complex Scenarios

14. You need to create, test, and share a new Skill across a 10-person team. What's your plan for version control, testing, documentation, and rollback if something goes wrong?

15. What if Skills could work together in chains for complex tasks? How would that work? What problems might come up?

16. You're building a Skill library for a company with 100+ developers. How would you organize naming, quality checks, security reviews, retiring old Skills, and helping people find Skills?

---

## Where to Learn More

- **Official Docs**: [Agent Skills Overview](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview)
- **Best Practices**: [Authoring Best Practices](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/best-practices)
- **Blog Post**: [Equipping Agents for the Real World with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- **All Docs**: [llms.txt](https://code.claude.com/docs/llms.txt)
