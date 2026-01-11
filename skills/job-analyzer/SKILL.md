# Job Analyzer Skill

## Purpose

This skill extracts structured information from job description markdown files as part of a cover letter generation pipeline. It analyzes job postings to identify requirements, culture signals, and key themes that will be used to generate tailored cover letters.

## Input

**Input Directory:** `inputs/jobs/{company}/`

Read any `.md` file from the `inputs/jobs/{company}/` directory (excluding `job_description.md.example` or any other `.example` files if present). If multiple `.md` files exist, prefer `job_description.md` if present, otherwise use any `.md` file found.

The input is a markdown file containing the job posting. It typically includes:
- Company name and role title
- Job requirements and qualifications
- Responsibilities
- Technical skills
- Company culture and values
- Benefits and other relevant details

The company name will be provided in the execution context, and you should read the job description file from the corresponding company directory.

## Output

**Output File:** `outputs/{company}_{date}/analysis/job_analysis.json`

You must write the analysis results to this exact path. The output directory structure should be created if it doesn't exist.

The output must be valid JSON that strictly conforms to the schema defined in `schemas/job-analysis.json` (Draft 7 JSON Schema).

## Processing Instructions

### 1. Extract Requirements

Categorize requirements into the following categories:
- **Technical Skills**: Programming languages, frameworks, tools, technologies
- **Experience Level**: Years of experience, seniority level, career stage
- **Education**: Degree requirements, educational background
- **Certifications**: Professional certifications, licenses

For each category:
- List all specific items found in the job description
- Assign a priority level:
  - `"required"`: Explicitly stated as required or mandatory
  - `"preferred"`: Listed as preferred, nice-to-have, or bonus
  - `"nice-to-have"`: Implied or mentioned casually

If a category has no items, you may omit it from the requirements array.

### 2. Extract Culture Signals

Identify signals about company culture, values, and work style:

- **values**: Explicit company values mentioned (e.g., "innovation", "collaboration", "diversity")
- **work_style**: Work style indicators (e.g., "remote-first", "collaborative", "fast-paced", "autonomous")
- **team_dynamics**: Team and collaboration signals (e.g., "cross-functional teams", "mentorship culture", "agile environment")

If no signals are found in a particular area, use an empty array `[]`.

### 3. Identify Key Themes

Extract the main themes, focus areas, and priorities for the role. For each theme:
- Provide a clear `theme` name (e.g., "Scalability", "Security", "User Experience")
- Write a `description` explaining why this theme is important for the role
- Assign an `emphasis` level:
  - `"high"`: Repeatedly mentioned, central to the role, emphasized strongly
  - `"medium"`: Mentioned multiple times or in important sections
  - `"low"`: Mentioned briefly or tangentially

### 4. Generate Summary

Create a brief summary (2-3 sentences) describing the role and what the company is looking for. This should capture the essence of the position.

## Schema Reference

The output must validate against `schemas/job-analysis.json`. Key requirements:

**Required Fields:**
- `requirements`: Array of requirement objects, each with `category` and `items` (required), and optional `priority`
- `culture_signals`: Object with optional `values`, `work_style`, and `team_dynamics` arrays
- `themes`: Array of theme objects, each with required `theme` and `description`, and optional `emphasis`

**Optional Fields:**
- `summary`: Brief summary string

Read the schema file at `schemas/job-analysis.json` for complete validation rules and structure.

## Output Format Example

```json
{
  "requirements": [
    {
      "category": "Technical Skills",
      "items": ["Python", "Django", "PostgreSQL", "AWS"],
      "priority": "required"
    },
    {
      "category": "Experience Level",
      "items": ["5+ years of software development experience", "Senior-level expertise"],
      "priority": "required"
    },
    {
      "category": "Education",
      "items": ["Bachelor's degree in Computer Science or related field"],
      "priority": "preferred"
    }
  ],
  "culture_signals": {
    "values": ["innovation", "collaboration", "work-life balance"],
    "work_style": ["remote-first", "autonomous", "agile"],
    "team_dynamics": ["cross-functional teams", "mentorship culture"]
  },
  "themes": [
    {
      "theme": "Scalability",
      "description": "The role focuses on building systems that can handle growth and increased load",
      "emphasis": "high"
    },
    {
      "theme": "Security",
      "description": "Security best practices are important for protecting user data",
      "emphasis": "medium"
    }
  ],
  "summary": "Senior Python developer role focused on building scalable web applications. The company values innovation and collaboration in a remote-first, autonomous work environment."
}
```

## Execution Steps

1. Find and read a job description file from `inputs/jobs/{company}/` directory:
   - Look for any `.md` file (excluding `.example` files)
   - If `job_description.md` exists, use it
   - Otherwise, use any other `.md` file found in the directory
   - If no `.md` files are found (excluding examples), stop execution and inform the user
2. Analyze the content following the processing instructions above
3. Structure the output according to the schema
4. Validate that all required fields are present
5. Write the JSON output to `outputs/{company}_{date}/analysis/job_analysis.json`
6. Ensure the output directory exists before writing

## Edge Cases

- **No job description file found**: If no `.md` files exist in `inputs/jobs/{company}/` (excluding example files), stop execution and inform the user
- **Multiple job description files**: If multiple `.md` files exist, prefer `job_description.md` if present, otherwise use the first one found
- **Missing sections**: If certain sections are missing from the job description, use empty arrays or omit optional fields as appropriate
- **Ambiguous requirements**: When priority is unclear, default to "preferred" rather than "required"
- **Implicit requirements**: Only extract explicitly stated requirements; avoid inferring requirements that aren't mentioned
- **Multiple mentions**: If a requirement or theme is mentioned multiple times, consolidate it into a single entry

