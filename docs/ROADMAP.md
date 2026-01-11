# Cover Letter Generator - Implementation Roadmap

## Status: Phase 1 Complete ✅ | Phase 2 In Progress 🚧

### Completed: Phase 1 - Project Setup

**Date Completed:** 2026-01-11

All foundational infrastructure for the cover letter generator has been established.

#### Directory Structure Created
- `.claude/commands/` - Ready for slash command definitions (Phase 3)
- `skills/` - Four skill directories created:
  - `job-analyzer/`
  - `resume-matcher/`
  - `github-scout/`
  - `letter-composer/`
- `schemas/` - JSON schema validation files
- `inputs/` - Input file directories:
  - `jobs/` - For job description files
  - `sample_letters/` - For example cover letters
- `outputs/` - For generated cover letters and analysis artifacts
- `cache/github/` - For GitHub API response caching

#### JSON Schema Files Created
Four comprehensive JSON Schema files (Draft 7) for structured output validation:

1. **`schemas/job-analysis.json`**
   - Validates extraction of job requirements, culture signals, and key themes
   - Structure: requirements array, culture_signals object, themes array
   - Includes priority levels and emphasis indicators

2. **`schemas/resume-matches.json`**
   - Validates matching of resume sections to job requirements
   - Includes relevance scoring (0-10), requirement mappings, and gap analysis
   - Tracks strengths and areas where resume may not fully address requirements

3. **`schemas/github-matches.json`**
   - Validates matching of GitHub repositories to job skills
   - Includes repository metadata, relevance scoring, and skill demonstrations
   - Tracks how projects reinforce qualifications

4. **`schemas/composed-letter.json`**
   - Optional metadata structure for composed letters
   - Includes paragraph-level metadata and style notes
   - Note: Actual cover letter output is markdown, this is for analysis

#### Configuration File
- **`config.yaml`** - Template configuration file with:
  - Required: GitHub username field
  - Optional preferences: tone, length, emphasis areas

#### Placeholder Input Files
- **`inputs/resume.md`** - Template resume structure with clear instructions
- **`inputs/sample_letters/example.md`** - Sample cover letter format template

#### Git Tracking
- `.gitkeep` files added to preserve empty directories (`inputs/jobs/`, `cache/github/`)

---

## Next Steps

### Phase 2: Skill Files (In Progress)

#### Completed Skills

1. **`skills/job-analyzer/SKILL.md`** ✅
   - **Date Completed:** 2026-01-11
   - Extracts requirements, culture signals, key themes from job postings
   - Input: `inputs/jobs/{company}/job_description.md`
   - Output: `outputs/{company}_{date}/analysis/job_analysis.json`
   - Validates against `schemas/job-analysis.json`
   - Includes comprehensive processing instructions, schema reference, output format example, and edge case handling

#### Remaining Skills

2. **`skills/resume-matcher/SKILL.md`**
   - Finds relevant experience matching job requirements
   - Input: `inputs/resume.md` + job_analysis.json
   - Output: `outputs/{company}_{date}/analysis/resume_matches.json`
   - Must validate against `schemas/resume-matches.json`

3. **`skills/github-scout/SKILL.md`**
   - Identifies GitHub projects that reinforce qualifications
   - Input: job_analysis.json + GitHub API data
   - Output: `outputs/{company}_{date}/analysis/github_matches.json`
   - Must validate against `schemas/github-matches.json`
   - Should cache API responses to `cache/github/repos.json`

4. **`skills/letter-composer/SKILL.md`**
   - Synthesizes everything into final cover letter
   - Input: All three analysis JSONs + sample letters
   - Output: `outputs/{company}_{date}/cover_letter.md` and `reasoning.md`

Each skill should include:
- Clear input/output specifications
- Reference to JSON schema file for validation
- Explicit "write output to `{path}`" instructions
- Examples of expected output format

### Phase 3: Custom Command (Not Started)
Create `.claude/commands/generate-cover-letter.md` that orchestrates the pipeline:

- Takes `$COMPANY` argument
- Executes all four skills in sequence
- Validates outputs against schemas
- Creates output directory structure
- Provides summary of generated files

### Phase 4: Refinement Command (Not Started)
Create `.claude/commands/refine-cover-letter.md` for iterative improvements:

- Takes `$COMPANY` and `$DATE` arguments
- Re-runs only letter-composer using existing analysis + feedback
- Allows iteration without re-analyzing inputs

### Phase 5: Testing & Documentation (Not Started)
- Test with real job description
- Write `README.md` with usage instructions
- Document how to add new sample letters
- Create example successful run in `outputs/`

---

## Architecture Overview

The cover letter generator uses a **skill-based architecture** where:

- **Skills** (`SKILL.md` files) are modular capabilities that Claude Code reads and follows autonomously
- **Commands** (`.claude/commands/*.md`) are user-triggered prompts that orchestrate workflows
- **Schemas** ensure structured, validated outputs at each pipeline stage
- **Transparency** is maintained by saving all intermediate analysis artifacts

### Pipeline Flow

```
Job Description → job-analyzer → Job Analysis JSON
                                      ↓
Resume + Job Analysis → resume-matcher → Resume Matches JSON
                                      ↓
GitHub Data + Job Analysis → github-scout → GitHub Matches JSON
                                      ↓
All Analysis + Sample Letters → letter-composer → Cover Letter + Reasoning
```

---

## Usage (Once Complete)

```bash
# Generate a new cover letter
claude "/generate-cover-letter anthropic"

# Iterate on a draft (after editing feedback.md in the output folder)
claude "/refine-cover-letter anthropic 2026-01-11"
```

---

## Notes

- All JSON schemas use Draft 7 format with comprehensive validation
- The system is designed for maximum reproducibility: change inputs, run one command, get outputs
- All intermediate artifacts are saved for review and debugging
- The refinement loop allows iteration without re-analyzing inputs

