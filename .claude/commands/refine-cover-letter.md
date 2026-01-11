# Refine Cover Letter

## Arguments
- `$COMPANY`: Company name (required) - identifies which company's cover letter to refine
- `$DATE`: Output date folder in `YYYY-MM-DD` format (required) - identifies which run to refine

## Purpose

This command allows iterative refinement of a cover letter without re-analyzing the job description, resume, or GitHub repositories. It re-runs only the letter-composer skill using existing analysis artifacts and any feedback provided.

## Execution Steps

### Step 1: Validation and Setup

1. Construct the output directory path: `outputs/{company}_{date}/`
2. Get the current time in `HH-MM` format (24-hour format, e.g., `14-30` for 2:30 PM) - this will be used for the new refined output filenames
3. Verify that the output directory exists
   - If it doesn't exist, stop execution and inform the user that no previous run was found for this company and date
4. Verify that all required analysis files exist:
   - `outputs/{company}_{date}/analysis/job_analysis.json`
   - `outputs/{company}_{date}/analysis/resume_matches.json`
   - `outputs/{company}_{date}/analysis/github_matches.json`
   - If any are missing, stop execution and inform the user that the analysis files are incomplete
5. Check for feedback file:
   - Look for `outputs/{company}_{date}/feedback.md`
   - If it exists, read it and note that feedback will be incorporated
   - If it doesn't exist, warn the user that no feedback.md was found, but proceed anyway (they may want to regenerate with updated sample letters or config)
6. Verify that sample letters exist in `inputs/sample_letters/`
   - If none exist, warn but continue (style matching will be limited)

### Step 2: Letter Composition (Refinement)

1. Read and follow the instructions in `skills/letter-composer/SKILL.md`
2. Read all three existing analysis JSONs:
   - `outputs/{company}_{date}/analysis/job_analysis.json`
   - `outputs/{company}_{date}/analysis/resume_matches.json`
   - `outputs/{company}_{date}/analysis/github_matches.json`
3. Read all sample letters from `inputs/sample_letters/` directory
4. Read `config.yaml` for any preferences (tone, length, emphasis areas)
5. If `outputs/{company}_{date}/feedback.md` exists:
   - Read the feedback file
   - **CRITICAL**: Feedback takes priority over style matching when there is a conflict
   - Incorporate feedback fully, using sample letters for style guidance only when feedback doesn't specify style preferences
   - If feedback requests specific tone, length, or structure changes, prioritize those over matching sample letters
7. Optionally read the most recent previous cover letter from `outputs/{company}_{date}/` to understand what's being refined:
   - Look for files matching `cover_letter_*.md` pattern and use the most recent one (by timestamp if multiple exist)
   - This can help maintain continuity if only minor changes are requested
   - However, if feedback requests significant changes, prioritize the feedback over the previous version
8. Perform the letter composition according to the skill instructions:
   - **CRITICAL**: If feedback.md exists, feedback takes priority over style matching
   - Use sample letters for style guidance when feedback doesn't specify style preferences
   - If feedback conflicts with style matching, prioritize feedback and note the conflict in the reasoning document
   - If no feedback exists, maintain PRIMARY EMPHASIS on matching tone, length, and style from sample letters
9. **Create new timestamped files** (preserves previous versions):
   - Write the refined cover letter to `outputs/{company}_{date}/cover_letter_{time}.md` (where `{time}` is the timestamp from Step 2, e.g., `14-30`)
   - Write the updated reasoning document to `outputs/{company}_{date}/reasoning_{time}.md` (using the same timestamp as the cover letter)
   - The reasoning should explain how feedback was incorporated and prioritized, and how style matching was applied (when not overridden by feedback)
   - Note: Previous versions are preserved since new timestamps are used

### Step 3: Summary and Comparison

1. Print a summary of the refinement:
   - Company name and date
   - Paths to the refined files:
     - `outputs/{company}_{date}/cover_letter_{time}.md` (new version with timestamp)
     - `outputs/{company}_{date}/reasoning_{time}.md` (new version with timestamp)
2. Count and display the word count of the refined cover letter
3. If a previous cover letter existed, optionally compare word counts:
   - Previous word count (if available)
   - New word count
   - Difference
4. Confirm that the refinement was successful
5. Remind the user that they can:
   - Edit `outputs/{company}_{date}/feedback.md` and run this command again for further iterations
   - Run `/generate-cover-letter {company}` to start fresh with new analysis

## Error Handling

- If the output directory doesn't exist, provide clear guidance on what company/date combination was expected
- If analysis files are missing, suggest running `/generate-cover-letter {company}` first
- If feedback.md doesn't exist, warn but continue (user may want to regenerate with updated inputs)
- If letter composition fails, preserve the previous cover letter and reasoning files

## Notes

- This command does NOT re-run job analysis, resume matching, or GitHub scouting
- All analysis artifacts remain unchanged from the original run
- Only the cover letter and reasoning documents are regenerated (with new timestamps to preserve previous versions)
- The refinement loop allows quick iteration without re-analyzing inputs
- Feedback takes priority over style matching - users can override style preferences through feedback if needed
- Each refinement creates new timestamped files, preserving all previous versions

## Usage Example

```bash
# User edits outputs/anthropic_2026-01-11/feedback.md with refinement instructions
# Then runs:
claude "/refine-cover-letter anthropic 2026-01-11"
```

