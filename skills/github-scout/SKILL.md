# GitHub Scout Skill

## Purpose

This skill identifies GitHub repositories that demonstrate skills relevant to job requirements. It analyzes public repositories to find projects that reinforce qualifications and provide concrete evidence of technical capabilities. The matched repositories will be used to strengthen cover letters with real-world project examples.

## Input

**Primary Input File:** `outputs/{company}_{date}/analysis/job_analysis.json`

This file contains the structured job analysis from the job-analyzer skill, including:
- Job requirements (categorized by Technical Skills, Experience Level, Education, Certifications)
- Culture signals (values, work style, team dynamics)
- Key themes (main focus areas for the role)
- Summary of the role

**Secondary Input File:** `config.yaml`

This configuration file contains:
- `github_username`: The GitHub username to fetch repositories for (required)
- Optional preferences (not used by this skill)

**Optional Input File:** `cache/github/repos.json`

If this file exists, it contains previously cached GitHub API responses. You should check this file first before making API calls to avoid rate limiting and improve performance.

The company name and date will be provided in the execution context, and you should read the corresponding job analysis file and configuration file.

## Output

**Output File:** `outputs/{company}_{date}/analysis/github_matches.json`

You must write the matching results to this exact path. The output directory structure should be created if it doesn't exist.

The output must be valid JSON that strictly conforms to the schema defined in `schemas/github-matches.json` (Draft 7 JSON Schema).

## Processing Instructions

### 1. Read Configuration

Read `config.yaml` to obtain the GitHub username. If the username is missing or empty, you cannot proceed with fetching repositories. In this case, output an empty matches array and note this in the summary.

### 2. Fetch GitHub Repository Data

**Check Cache First:**
- Check if `cache/github/repos.json` exists
- If it exists and contains recent data (within the last 24 hours), use the cached data
- If cache is stale or missing, proceed to fetch from GitHub API

**Fetch from GitHub API:**
- Use the GitHub username from config.yaml
- Fetch public repositories for the user
- You can use web search or API calls to retrieve repository data
- For each repository, collect:
  - Repository name
  - Repository URL (full GitHub URL)
  - Description
  - Primary programming language
  - Number of stars
  - Last updated timestamp (ISO 8601 format)
  - README content (if available, for better matching)

**Cache the Results:**
- After fetching, save the raw repository data to `cache/github/repos.json`
- This allows future runs to use cached data and avoid API rate limits
- Ensure the cache directory exists before writing

### 3. Match Repositories to Job Requirements

For each repository, analyze how well it demonstrates job requirements from the job analysis:

**For Technical Skills:**
- Match programming languages used in repositories to required technical skills
- Match frameworks, tools, and technologies mentioned in repository descriptions or READMEs
- Look for projects that use the same tech stack as required by the job

**For Key Themes:**
- Match repository themes and focus areas to job themes
- For example, if the job emphasizes "Scalability", look for repositories that demonstrate scalable architectures
- If the job emphasizes "Security", look for repositories with security-focused features

**For Experience Level:**
- Consider repository complexity, size, and sophistication as indicators of experience level
- Well-maintained, documented repositories with good structure may indicate senior-level work

### 4. Calculate Relevance Scores

For each repository, assign a relevance score from 0-10:

- **9-10**: Perfect or near-perfect match - directly demonstrates multiple required skills with strong evidence
- **7-8**: Strong match - demonstrates required or preferred skills with good evidence
- **5-6**: Moderate match - demonstrates preferred or nice-to-have skills, or partially demonstrates required skills
- **3-4**: Weak match - tangentially related or demonstrates only a small aspect of requirements
- **1-2**: Minimal match - barely related or very indirect connection
- **0**: No match - does not demonstrate any relevant requirements

Consider the following factors when scoring:
- Number of matching skills (more matches = higher score)
- Priority level of matched requirements (required > preferred > nice-to-have)
- Quality indicators (stars, recent updates, documentation, complexity)
- Alignment with key themes from job analysis
- Specificity of the match (exact technology match > related technology)

### 5. Create Skill Mappings

For each repository with a relevance score of 3 or higher, create skill mappings that specify:
- **skill**: The specific job requirement item being demonstrated (from job_analysis.json)
- **how_it_demonstrates**: A clear explanation of how the repository demonstrates this skill

Include multiple skill mappings if a single repository demonstrates multiple requirements.

### 6. Identify Key Features

For repositories with relevance scores of 5 or higher, identify key features or aspects that make them relevant:
- Specific technical implementations
- Architecture patterns
- Problem-solving approaches
- Scale or complexity indicators
- Any notable achievements or characteristics

### 7. Generate Summary

Create an overall assessment (2-3 sentences) describing:
- How well the GitHub projects reinforce qualifications overall
- Key strengths demonstrated through repositories
- Notable gaps if repositories don't cover certain required skills

## Schema Reference

The output must validate against `schemas/github-matches.json`. Key requirements:

**Required Fields:**
- `matches`: Array of match objects, each with:
  - `repository` (required): Object with:
    - `name` (required): Repository name
    - `url` (required): Full GitHub repository URL
    - `description` (required): Repository description
    - `language` (optional): Primary programming language
    - `stars` (optional): Number of stars
    - `updated_at` (optional): Last update timestamp (ISO 8601 format)
  - `relevance` (required): Object with:
    - `score` (required): Number from 0-10
    - `explanation` (required): String explaining why this repository is relevant

**Optional Fields:**
- `skill_mappings`: Array of objects mapping specific job skills to this repository
  - Each mapping has `skill` and `how_it_demonstrates` (both required)
- `key_features`: Array of strings describing relevant features of the project
- `summary`: Overall assessment string

Read the schema file at `schemas/github-matches.json` for complete validation rules and structure.

## Output Format Example

```json
{
  "matches": [
    {
      "repository": {
        "name": "scalable-api-server",
        "url": "https://github.com/username/scalable-api-server",
        "description": "High-performance REST API server built with Python and Django, designed for horizontal scaling",
        "language": "Python",
        "stars": 42,
        "updated_at": "2024-12-15T10:30:00Z"
      },
      "relevance": {
        "score": 9,
        "explanation": "Strong match demonstrating Python and Django skills required for the role, with explicit focus on scalability which is a key theme"
      },
      "skill_mappings": [
        {
          "skill": "Python",
          "how_it_demonstrates": "Primary language used throughout the repository with production-ready code"
        },
        {
          "skill": "Django",
          "how_it_demonstrates": "Built using Django framework with proper project structure and best practices"
        },
        {
          "skill": "Scalability",
          "how_it_demonstrates": "Explicitly designed for horizontal scaling with architecture patterns that support growth"
        }
      ],
      "key_features": [
        "Horizontal scaling architecture",
        "Production-ready codebase with tests",
        "Comprehensive documentation",
        "Performance optimizations"
      ]
    },
    {
      "repository": {
        "name": "aws-deployment-tools",
        "url": "https://github.com/username/aws-deployment-tools",
        "description": "CI/CD scripts and infrastructure as code for deploying applications to AWS",
        "language": "Python",
        "stars": 18,
        "updated_at": "2024-11-20T14:22:00Z"
      },
      "relevance": {
        "score": 7,
        "explanation": "Demonstrates AWS experience and DevOps practices, which are preferred skills for the role"
      },
      "skill_mappings": [
        {
          "skill": "AWS",
          "how_it_demonstrates": "Contains infrastructure as code and deployment scripts specifically for AWS services"
        }
      ],
      "key_features": [
        "Infrastructure as code",
        "CI/CD automation",
        "AWS service integration"
      ]
    }
  ],
  "summary": "GitHub projects strongly reinforce qualifications, particularly in Python, Django, and scalability - all key requirements. The repositories demonstrate production-ready code, modern architecture patterns, and AWS experience. The projects align well with the role's emphasis on building scalable systems."
}
```

## Execution Steps

1. Read the configuration file from `config.yaml` to obtain the GitHub username
2. Check if `cache/github/repos.json` exists and is recent (within 24 hours)
3. If cache exists and is fresh, read repository data from cache
4. If cache is missing or stale, fetch repository data from GitHub API using the username
5. Save fetched repository data to `cache/github/repos.json` for future use
6. Read the job analysis file from `outputs/{company}_{date}/analysis/job_analysis.json`
7. For each repository, analyze how it matches job requirements:
   - Compare repository languages, technologies, and descriptions to required technical skills
   - Match repository themes to job key themes
   - Consider repository quality indicators (stars, updates, documentation)
8. Calculate relevance scores (0-10) for each repository based on matches
9. Create skill mappings for repositories with relevance scores of 3 or higher
10. Identify key features for repositories with relevance scores of 5 or higher
11. Generate an overall summary assessment
12. Structure the output according to the schema
13. Validate that all required fields are present
14. Write the JSON output to `outputs/{company}_{date}/analysis/github_matches.json`
15. Ensure the output directory exists before writing

## Edge Cases

- **Missing GitHub username**: If `github_username` is missing or empty in config.yaml, output an empty matches array `[]` and include a note in the summary explaining that no GitHub username was configured
- **No repositories found**: If the user has no public repositories, output an empty matches array and note this in the summary
- **API failures**: If GitHub API calls fail, check if cached data exists and use it even if stale. If no cache exists, output an empty matches array and note the API failure in the summary
- **Repositories with no matches**: Only include repositories with relevance scores of 1 or higher. Repositories with score 0 should be excluded from the matches array
- **Empty repositories**: Repositories with no code, only README, or very minimal content should receive lower relevance scores (1-3) unless they demonstrate specific skills through documentation
- **Private repositories**: Only analyze public repositories. Private repositories cannot be accessed via the public API
- **Forked repositories**: Include forked repositories if they have been significantly modified or if they demonstrate relevant skills, but note in the explanation if it's a fork
- **Very old repositories**: Consider recency when scoring - repositories updated within the last year are more relevant than those not updated in several years
- **Multiple repositories matching same skill**: Include all relevant repositories even if they demonstrate the same skills, as they may show different aspects or levels of expertise
- **Ambiguous language detection**: If a repository's primary language is unclear, analyze the codebase structure or README to determine the main technologies used

