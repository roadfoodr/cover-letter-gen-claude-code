# Generate Cover Letter

## Arguments
- `$COMPANY`: Company name (required) - maps to `inputs/jobs/{company}/`

## Execution Steps

### Step 1: Setup and Validation

1. Read `config.yaml` to extract the GitHub username (required field)
2. Get the current date in `YYYY-MM-DD` format
3. Create output directory structure: `outputs/{company}_{date}/analysis/`
4. Verify that `inputs/jobs/{company}/job_description.md` exists
   - If it doesn't exist, stop execution and inform the user
5. Verify that `inputs/resume.md` exists
   - If it doesn't exist, stop execution and inform the user
6. Check that at least one sample letter exists in `inputs/sample_letters/`
   - If none exist, warn the user but continue (style matching will be limited)

### Step 2: Job Analysis

1. Read and follow the instructions in `skills/job-analyzer/SKILL.md`
2. Read the job description from `inputs/jobs/{company}/job_description.md`
3. Perform the analysis according to the skill instructions
4. Write the output to `outputs/{company}_{date}/analysis/job_analysis.json`
5. Validate the output JSON against `schemas/job-analysis.json` (Draft 7 JSON Schema)
   - If validation fails, fix the output and re-validate before proceeding

### Step 3: Resume Matching

1. Read and follow the instructions in `skills/resume-matcher/SKILL.md`
2. Read `inputs/resume.md`
3. Read the job analysis from `outputs/{company}_{date}/analysis/job_analysis.json` (created in Step 2)
4. Perform the matching analysis according to the skill instructions
5. Write the output to `outputs/{company}_{date}/analysis/resume_matches.json`
6. Validate the output JSON against `schemas/resume-matches.json` (Draft 7 JSON Schema)
   - If validation fails, fix the output and re-validate before proceeding

### Step 4: GitHub Scout

1. Read and follow the instructions in `skills/github-scout/SKILL.md`
2. Read the GitHub username from `config.yaml` (from Step 1)
3. Check if `cache/github/repos.json` exists:
   - If it exists and is recent (less than 24 hours old), use cached data
   - Otherwise, fetch repositories from GitHub API using the username
   - Save/update the cache to `cache/github/repos.json`
4. Read the job analysis from `outputs/{company}_{date}/analysis/job_analysis.json` (from Step 2)
5. Perform the GitHub matching analysis according to the skill instructions
6. Write the output to `outputs/{company}_{date}/analysis/github_matches.json`
7. Validate the output JSON against `schemas/github-matches.json` (Draft 7 JSON Schema)
   - If validation fails, fix the output and re-validate before proceeding

### Step 5: Letter Composition

1. Read and follow the instructions in `skills/letter-composer/SKILL.md`
2. Read all three analysis JSONs:
   - `outputs/{company}_{date}/analysis/job_analysis.json`
   - `outputs/{company}_{date}/analysis/resume_matches.json`
   - `outputs/{company}_{date}/analysis/github_matches.json`
3. Read all sample letters from `inputs/sample_letters/` directory
4. Check if `outputs/{company}_{date}/feedback.md` exists:
   - If it exists, read it and incorporate feedback into the composition
   - If it doesn't exist, proceed without feedback
5. Perform the letter composition according to the skill instructions
   - **CRITICAL**: The skill emphasizes PRIMARY EMPHASIS on matching tone, length, and style from sample letters
   - Ensure style matching is achieved, not just attempted
6. Write the cover letter to `outputs/{company}_{date}/cover_letter.md`
7. Write the reasoning document to `outputs/{company}_{date}/reasoning.md`
8. Optionally create metadata JSON conforming to `schemas/composed-letter.json` if desired

### Step 6: Summary and Completion

1. Print a summary of the execution:
   - Company name and date
   - Paths to all generated files:
     - `outputs/{company}_{date}/cover_letter.md`
     - `outputs/{company}_{date}/reasoning.md`
     - `outputs/{company}_{date}/analysis/job_analysis.json`
     - `outputs/{company}_{date}/analysis/resume_matches.json`
     - `outputs/{company}_{date}/analysis/github_matches.json`
2. Count and display the word count of the generated cover letter
3. Confirm that all outputs were successfully created and validated

## Error Handling

- If any step fails, stop execution and clearly report the error
- If validation fails at any step, attempt to fix the output before stopping
- If required input files are missing, provide clear guidance on what needs to be created
- If GitHub API fails, use cached data if available, otherwise warn and continue without GitHub matches

## Notes

- All JSON outputs must strictly conform to their respective schemas
- The pipeline is designed to be reproducible: same inputs should produce similar outputs
- All intermediate analysis artifacts are saved for transparency and debugging
- The output directory structure follows the pattern: `outputs/{company}_{date}/`

