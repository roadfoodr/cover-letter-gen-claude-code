# Cover Letter Generator

## Project Goal

Automatically generate tailored job application cover letters by analyzing job descriptions, matching against the applicant's resume and GitHub projects, and emulating the style of their previous cover letters.

## Inputs

| Input | Format | Description |
|-------|--------|-------------|
| Job description | Markdown file | The job posting to apply for |
| Resume | Markdown file | The applicant's resume |
| Sample cover letters | 2-10 Markdown files | Previous cover letters to match for style, tone, and length |
| GitHub username | Config file | Used to fetch public repositories for project matching |
| Feedback (optional) | Markdown file | Refinement instructions for iterating on a draft |

## Outputs

| Output | Format | Description |
|--------|--------|-------------|
| Cover letter | Markdown file | The generated cover letter |
| Reasoning | Markdown file | Explanation of content choices for each paragraph |
| Analysis artifacts | JSON files | Intermediate analysis (job requirements, resume matches, GitHub matches) |

## Implementation Approach

### Claude Code with Custom Commands

The application runs entirely within Claude Code using custom slash commands. No external dependencies or orchestration code required.

**Pipeline:**
1. **job-analyzer** → Extract requirements, culture signals, key themes from job posting
2. **resume-matcher** → Find relevant experience matching job requirements
3. **github-scout** → Identify GitHub projects that reinforce qualifications
4. **letter-composer** → Synthesize everything into the final cover letter

### Key Design Decisions

- **Skill-based architecture**: Each pipeline stage is a `SKILL.md` file that Claude Code reads and follows
- **JSON schema validation**: Structured outputs validated against schema files for consistency
- **Transparency**: All intermediate analysis saved to output folder for review and debugging
- **Refinement loop**: Edit `feedback.md` in the output folder and re-run to iterate on drafts without re-analyzing inputs
