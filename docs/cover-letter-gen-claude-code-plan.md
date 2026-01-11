# Cover Letter Generator - Claude Code Implementation Plan

## Overview

A cover letter generator that runs entirely within Claude Code. The goal is maximum reproducibility: change inputs, run one command, get outputs.

**Core approach:**
- Four skills (SKILL.md files) handle analysis and composition
- A custom slash command orchestrates the pipeline
- JSON schemas ensure structured, validated outputs
- All intermediate artifacts saved for transparency and debugging

## Directory Structure

```
cover-letter-gen/
├── .claude/
│   └── commands/
│       └── generate-cover-letter.md    # Main command
│
├── skills/
│   ├── job-analyzer/
│   │   └── SKILL.md
│   ├── resume-matcher/
│   │   └── SKILL.md
│   ├── github-scout/
│   │   └── SKILL.md
│   └── letter-composer/
│       └── SKILL.md
│
├── schemas/                            # JSON schemas for validation
│   ├── job-analysis.json
│   ├── resume-matches.json
│   ├── github-matches.json
│   └── composed-letter.json
│
├── inputs/
│   ├── resume.md
│   ├── sample_letters/
│   │   ├── company_a.md
│   │   └── company_b.md
│   └── jobs/
│       └── {company_name}/
│           └── job_description.md
│
├── outputs/
│   └── {company_name}_{YYYY-MM-DD}/
│       ├── cover_letter_{HH-MM}.md
│       ├── reasoning_{HH-MM}.md
│       ├── feedback.md                 # Created for refinement iterations
│       └── analysis/
│           ├── job_analysis.json
│           ├── resume_matches.json
│           └── github_matches.json
│
├── cache/
│   └── github/
│       └── repos.json                  # Cached repo metadata
│
└── config.yaml                         # GitHub username, preferences
```

## Implementation Phases

### Phase 1: Project Setup

**Tasks:**
1. Create directory structure
2. Create `config.yaml` template
3. Create JSON schema files (converted from original Pydantic models)
4. Create placeholder inputs for testing

**Deliverables:**
- Empty directory tree
- `schemas/*.json` files
- `config.yaml` with GitHub username field
- Sample `inputs/resume.md` and `inputs/sample_letters/example.md`

### Phase 2: Skill Files

**Tasks:**
1. Create `job-analyzer/SKILL.md` - extracts requirements, culture signals, key themes
2. Create `resume-matcher/SKILL.md` - finds relevant experience matching job requirements  
3. Create `github-scout/SKILL.md` - identifies GitHub projects that reinforce qualifications
4. Create `letter-composer/SKILL.md` - synthesizes everything into final cover letter

**Each skill should include:**
- Clear input/output specifications
- Reference to JSON schema file for output validation
- Explicit "write output to `{path}`" instructions
- Examples of expected output format

**Deliverables:**
- Four complete SKILL.md files

### Phase 3: Custom Command

**Tasks:**
1. Create `.claude/commands/generate-cover-letter.md`

**Command Structure:**

```markdown
# Generate Cover Letter

## Arguments
- $COMPANY: Company name (required) - maps to `inputs/jobs/{company}/`

## Execution Steps

### Step 1: Setup
- Read `config.yaml` for GitHub username
- Create output directory: `outputs/{company}_{date}/`
- Verify `inputs/jobs/{company}/job_description.md` exists

### Step 2: Job Analysis
- Read `skills/job-analyzer/SKILL.md`
- Input: `inputs/jobs/{company}/job_description.md`
- Output: `outputs/{company}_{date}/analysis/job_analysis.json`
- Validate against `schemas/job-analysis.json`

### Step 3: Resume Matching
- Read `skills/resume-matcher/SKILL.md`
- Input: `inputs/resume.md` + job_analysis.json from Step 2
- Output: `outputs/{company}_{date}/analysis/resume_matches.json`
- Validate against `schemas/resume-matches.json`

### Step 4: GitHub Scout
- Read `skills/github-scout/SKILL.md`
- Fetch repos from GitHub API using username in config
- Input: job_analysis.json + repo data
- Output: `outputs/{company}_{date}/analysis/github_matches.json`
- Validate against `schemas/github-matches.json`
- Cache results to `cache/github/repos.json`

### Step 5: Letter Composition
- Read `skills/letter-composer/SKILL.md`
- Input: All three analysis JSONs + all files in `inputs/sample_letters/`
- Output: `outputs/{company}_{date}/cover_letter_{HH-MM}.md`
- Output: `outputs/{company}_{date}/reasoning_{HH-MM}.md`

### Step 6: Summary
- Print paths to generated files
- Print word count of cover letter
```

**Deliverables:**
- `.claude/commands/generate-cover-letter.md`

### Phase 4: Refinement Command

**Tasks:**
1. Create `.claude/commands/refine-cover-letter.md`

**Command Arguments:**
- `$COMPANY`: Company name (required)
- `$DATE`: Output date folder (required) - identifies which run to refine

**Workflow:**
1. User edits `outputs/{company}_{date}/feedback.md` with refinement instructions
2. User runs `/refine-cover-letter {company} {date}`
3. Command re-runs only letter-composer using existing analysis + feedback
4. Creates new timestamped files `cover_letter_{HH-MM}.md` and `reasoning_{HH-MM}.md` (preserves previous versions)

**Deliverables:**
- `.claude/commands/refine-cover-letter.md`

### Phase 5: Testing & Documentation

**Tasks:**
1. Test with a real job description
2. Write README with usage instructions
3. Document how to add new sample letters

**Deliverables:**
- `README.md`
- Example successful run in `outputs/`

## Usage

```bash
# Generate a new cover letter
claude "/generate-cover-letter anthropic"

# Iterate on a draft (after editing feedback.md in the output folder)
claude "/refine-cover-letter anthropic 2026-01-11"
```
