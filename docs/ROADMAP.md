# Cover Letter Generator - Implementation Roadmap

## Status: Phase 1 Complete ✅ | Phase 2 Complete ✅ | Phase 3 Complete ✅ | Phase 4 Not Started

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

### Phase 2: Skill Files (Complete ✅)

#### Completed Skills

1. **`skills/job-analyzer/SKILL.md`** ✅
   - **Date Completed:** 2026-01-11
   - Extracts requirements, culture signals, key themes from job postings
   - Input: `inputs/jobs/{company}/job_description.md`
   - Output: `outputs/{company}_{date}/analysis/job_analysis.json`
   - Validates against `schemas/job-analysis.json`
   - Includes comprehensive processing instructions, schema reference, output format example, and edge case handling

2. **`skills/resume-matcher/SKILL.md`** ✅
   - **Date Completed:** 2026-01-11
   - Finds relevant experience matching job requirements
   - Input: `inputs/resume.md` + job_analysis.json
   - Output: `outputs/{company}_{date}/analysis/resume_matches.json`
   - Validates against `schemas/resume-matches.json`
   - Includes comprehensive processing instructions for matching resume sections, calculating relevance scores (0-10), creating requirement mappings, identifying strengths and gaps, and extracting years of experience

3. **`skills/github-scout/SKILL.md`** ✅
   - **Date Completed:** 2026-01-11
   - Identifies GitHub projects that reinforce qualifications
   - Input: job_analysis.json + GitHub API data (from config.yaml)
   - Output: `outputs/{company}_{date}/analysis/github_matches.json`
   - Validates against `schemas/github-matches.json`
   - Caches API responses to `cache/github/repos.json`
   - Includes comprehensive processing instructions for fetching repositories, matching to job requirements, calculating relevance scores (0-10), creating skill mappings, and handling edge cases

4. **`skills/letter-composer/SKILL.md`** ✅
   - **Date Completed:** 2026-01-11
   - Synthesizes everything into final cover letter with PRIMARY EMPHASIS on matching tone, length, and style from sample letters
   - Input: All three analysis JSONs + sample letters from `inputs/sample_letters/` + optional feedback.md
   - Output: `outputs/{company}_{date}/cover_letter.md` and `reasoning.md` (optional metadata JSON)
   - Must validate against `schemas/composed-letter.json` (for optional metadata)
   - Includes comprehensive processing instructions with CRITICAL style matching requirements:
     - Tone analysis (word choice, formality, enthusiasm level, sentence structure)
     - Length analysis (word count, paragraph count, target length category)
     - Style analysis (opening/closing patterns, paragraph structure, voice, formatting, qualification presentation style)
   - Emphasizes that style matching is not optional - it is the core requirement for generating authentic cover letters
   - Includes detailed reasoning document requirements explaining how style matching was achieved

---

### Completed: Phase 2 - Skill Files

**Date Completed:** 2026-01-11

All four skill files have been created with comprehensive instructions. Each skill includes:
- Clear input/output specifications
- Reference to JSON schema file for validation
- Explicit "write output to `{path}`" instructions
- Examples of expected output format
- Comprehensive edge case handling

---

### Completed: Phase 3 - Custom Command

**Date Completed:** 2026-01-11

#### Main Command Created
- **`.claude/commands/generate-cover-letter.md`** ✅
  - Takes `$COMPANY` argument
  - Executes all four skills in sequence:
    1. Setup and validation (reads config, creates output directories, verifies inputs)
    2. Job analysis (reads job-analyzer skill, processes job description)
    3. Resume matching (reads resume-matcher skill, matches resume to job)
    4. GitHub scout (reads github-scout skill, fetches and matches repositories)
    5. Letter composition (reads letter-composer skill, generates final cover letter)
    6. Summary (displays generated files and word count)
  - Validates all JSON outputs against schemas at each step
  - Creates output directory structure automatically
  - Handles errors gracefully with clear reporting
  - Supports optional feedback.md for refinement iterations

---

### Phase 4: Refinement Command (Not Started)

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

