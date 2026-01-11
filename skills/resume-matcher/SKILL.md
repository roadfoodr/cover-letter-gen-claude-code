# Resume Matcher Skill

## Purpose

This skill matches resume content against job requirements to identify relevant experience, skills, and qualifications. It analyzes how well the resume addresses the job requirements extracted by the job-analyzer skill, providing structured matching data that will be used to generate tailored cover letters.

## Input

**Primary Input File:** `inputs/resume.md`

The resume is a markdown file containing the applicant's professional background. It typically includes:
- Contact Information
- Professional Summary
- Experience (work history with roles, companies, dates, and achievements)
- Education (degrees, institutions, graduation dates)
- Skills (technical and soft skills)
- Projects (optional - personal or professional projects)
- Certifications (optional - professional certifications and licenses)

**Secondary Input File:** `outputs/{company}_{date}/analysis/job_analysis.json`

This file contains the structured job analysis from the job-analyzer skill, including:
- Job requirements (categorized by Technical Skills, Experience Level, Education, Certifications)
- Culture signals (values, work style, team dynamics)
- Key themes (main focus areas for the role)
- Summary of the role

The company name and date will be provided in the execution context, and you should read both the resume file and the corresponding job analysis file.

## Output

**Output File:** `outputs/{company}_{date}/analysis/resume_matches.json`

You must write the matching results to this exact path. The output directory structure should be created if it doesn't exist.

The output must be valid JSON that strictly conforms to the schema defined in `schemas/resume-matches.json` (Draft 7 JSON Schema).

## Processing Instructions

### 1. Parse Resume Sections

Read and identify all relevant sections from the resume:
- **Experience**: Each job/role with company, dates, responsibilities, and achievements
- **Education**: Degrees, institutions, fields of study, graduation dates
- **Skills**: Technical skills, tools, technologies, programming languages
- **Projects**: Personal or professional projects with descriptions and technologies used
- **Certifications**: Professional certifications, licenses, credentials

### 2. Match Resume Content to Job Requirements

For each resume section, identify matches to job requirements from the job analysis:

**For Experience Sections:**
- Match job titles, responsibilities, and achievements to job requirements
- Look for technical skills mentioned in experience descriptions
- Match years of experience to experience level requirements
- Identify relevant projects, accomplishments, or responsibilities that demonstrate required skills

**For Education Sections:**
- Match degree types and fields of study to education requirements
- Note if education level meets or exceeds requirements
- Identify relevant coursework or academic achievements

**For Skills Sections:**
- Directly match technical skills to job requirement items
- Identify proficiency levels if mentioned
- Note certifications that match job requirements

**For Projects Sections:**
- Match project technologies and descriptions to job requirements
- Identify projects that demonstrate required skills or experience
- Note project scale, complexity, or impact if relevant

**For Certifications Sections:**
- Directly match certifications to certification requirements
- Note certification dates and validity if relevant

### 3. Calculate Relevance Scores

For each match, assign a relevance score from 0-10:

- **9-10**: Perfect or near-perfect match - directly addresses a required item with strong evidence
- **7-8**: Strong match - addresses a required or preferred item with good evidence
- **5-6**: Moderate match - addresses a preferred or nice-to-have item, or partially addresses a required item
- **3-4**: Weak match - tangentially related or addresses only a small aspect of a requirement
- **1-2**: Minimal match - barely related or very indirect connection
- **0**: No match - does not address the requirement

Consider the following factors when scoring:
- Priority level of the requirement (required > preferred > nice-to-have)
- Specificity of the match (exact skill match > related skill > general experience)
- Strength of evidence (concrete achievements > listed skills > implied experience)
- Recency and relevance (recent experience > older experience)

### 4. Create Requirement Mappings

For each match, create requirement mappings that specify:
- **requirement**: The specific job requirement item being addressed (from job_analysis.json)
- **how_it_matches**: A clear explanation of how the resume content demonstrates this requirement

Include multiple requirement mappings if a single resume section addresses multiple requirements.

### 5. Extract Years of Experience

For experience sections, calculate years of experience demonstrated:
- Parse date ranges from job entries
- Calculate total years for each role
- Include this in the match object if applicable

### 6. Identify Strengths and Gaps

**Strengths:**
- Resume sections with high relevance scores (7-10)
- Requirements that are well-addressed with strong evidence
- Areas where the resume exceeds requirements
- Unique qualifications or experiences that stand out

**Gaps:**
- Required job requirements with no matches or very low relevance scores (0-3)
- Preferred requirements that are not addressed
- Missing experience levels, education, or certifications
- Areas where the resume falls short of requirements

### 7. Generate Summary

Create an overall assessment (2-3 sentences) describing:
- How well the resume matches the job requirements overall
- Key strengths that align with the role
- Notable gaps or areas for improvement

## Schema Reference

The output must validate against `schemas/resume-matches.json`. Key requirements:

**Required Fields:**
- `matches`: Array of match objects, each with:
  - `section` (required): Resume section type (e.g., "Experience", "Education", "Skills", "Projects", "Certifications")
  - `content` (required): The actual content from the resume that matches
  - `relevance` (required): Object with:
    - `score` (required): Number from 0-10
    - `explanation` (required): String explaining why this section is relevant

**Optional Fields:**
- `requirement_mappings`: Array of objects mapping specific job requirements to this resume section
  - Each mapping has `requirement` and `how_it_matches` (both required)
- `years_experience`: Number indicating years of experience demonstrated in this section
- `summary`: Overall assessment string
- `strengths`: Array of strings describing key strengths
- `gaps`: Array of strings describing areas where requirements are not fully addressed

Read the schema file at `schemas/resume-matches.json` for complete validation rules and structure.

## Output Format Example

```json
{
  "matches": [
    {
      "section": "Experience",
      "content": "Senior Software Engineer at TechCorp (2020-2024)\n- Led development of scalable microservices architecture using Python and Django\n- Reduced API response time by 40% through optimization\n- Mentored junior developers and established code review practices",
      "relevance": {
        "score": 9,
        "explanation": "Strong match for senior Python developer role with demonstrated experience in Django, scalability, and leadership"
      },
      "requirement_mappings": [
        {
          "requirement": "Python",
          "how_it_matches": "Directly mentioned in experience description as primary technology used"
        },
        {
          "requirement": "Django",
          "how_it_matches": "Explicitly listed as framework used for microservices development"
        },
        {
          "requirement": "5+ years of software development experience",
          "how_it_matches": "4 years at current role plus previous experience demonstrates required experience level"
        },
        {
          "requirement": "Senior-level expertise",
          "how_it_matches": "Job title is Senior Software Engineer with leadership and mentoring responsibilities"
        }
      ],
      "years_experience": 4
    },
    {
      "section": "Education",
      "content": "Bachelor of Science in Computer Science\nState University - 2018",
      "relevance": {
        "score": 8,
        "explanation": "Meets preferred education requirement with relevant degree"
      },
      "requirement_mappings": [
        {
          "requirement": "Bachelor's degree in Computer Science or related field",
          "how_it_matches": "Exact match - Bachelor of Science in Computer Science"
        }
      ]
    },
    {
      "section": "Skills",
      "content": "Technical Skills: Python, Django, PostgreSQL, AWS, Docker, Git",
      "relevance": {
        "score": 9,
        "explanation": "Direct matches for multiple required technical skills"
      },
      "requirement_mappings": [
        {
          "requirement": "Python",
          "how_it_matches": "Listed explicitly in technical skills"
        },
        {
          "requirement": "Django",
          "how_it_matches": "Listed explicitly in technical skills"
        },
        {
          "requirement": "PostgreSQL",
          "how_it_matches": "Listed explicitly in technical skills"
        },
        {
          "requirement": "AWS",
          "how_it_matches": "Listed explicitly in technical skills"
        }
      ]
    }
  ],
  "summary": "The resume demonstrates strong alignment with the job requirements. Key strengths include direct experience with all required technical skills (Python, Django, PostgreSQL, AWS), senior-level experience with leadership responsibilities, and relevant education. The candidate's experience in building scalable systems directly addresses the role's focus on scalability.",
  "strengths": [
    "Direct experience with all required technical skills (Python, Django, PostgreSQL, AWS)",
    "Senior-level role with demonstrated leadership and mentoring experience",
    "Proven track record in scalability and performance optimization",
    "Relevant education background (Computer Science degree)"
  ],
  "gaps": [
    "No explicit mention of security best practices or security-focused projects",
    "Limited evidence of experience with specific AWS services mentioned in job requirements"
  ]
}
```

## Execution Steps

1. Read the resume file from `inputs/resume.md`
2. Read the job analysis file from `outputs/{company}_{date}/analysis/job_analysis.json`
3. Parse all relevant sections from the resume
4. For each resume section, identify matches to job requirements from the job analysis
5. Calculate relevance scores (0-10) for each match based on priority, specificity, and evidence strength
6. Create requirement mappings showing how resume content addresses specific job requirements
7. Extract years of experience where applicable
8. Identify overall strengths and gaps
9. Generate a summary assessment
10. Structure the output according to the schema
11. Validate that all required fields are present
12. Write the JSON output to `outputs/{company}_{date}/analysis/resume_matches.json`
13. Ensure the output directory exists before writing

## Edge Cases

- **Missing resume sections**: If a section is missing from the resume (e.g., no Projects section), simply don't include matches for that section type
- **No matches found**: If a resume section doesn't match any requirements, don't include it in the matches array. However, note this in the gaps section if it's a required skill
- **Weak matches**: Include matches with low scores (1-4) if they're the only evidence for a requirement, but note the weakness in the explanation
- **Multiple roles matching same requirement**: Create separate match entries for each role if they both address the same requirement, as they may have different relevance scores
- **Ambiguous experience levels**: If years of experience are unclear from dates, estimate conservatively or omit the years_experience field
- **Partial requirement matches**: If a resume section partially addresses a requirement, include it with a moderate score and explain what aspects are covered
- **Overqualified candidates**: If the resume exceeds requirements, note this in strengths and assign high relevance scores
- **Missing required skills**: Always include these in the gaps array, even if there are no matches to report

