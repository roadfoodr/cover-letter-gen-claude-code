# Letter Composer Skill

## Purpose

This skill synthesizes all analysis artifacts (job analysis, resume matches, GitHub matches) along with sample cover letters to generate a tailored cover letter and reasoning document. 

**PRIMARY GOAL: Emulates style from sample letters** - The cover letter must match the tone, length, and writing style observed in the sample letters. This is not optional - it is the core requirement for generating an authentic cover letter that sounds like it was written by the same person who wrote the sample letters.

The skill generates both a cover letter (markdown format) and a reasoning document that explains content choices and documents how style matching was achieved.

## Input

**Primary Input Files:**
- `outputs/{company}_{date}/analysis/job_analysis.json` - Structured job requirements, culture signals, and key themes
- `outputs/{company}_{date}/analysis/resume_matches.json` - Resume sections matched to job requirements with relevance scores
- `outputs/{company}_{date}/analysis/github_matches.json` - GitHub repositories matched to job skills with relevance scores

**Style Reference (CRITICAL):**
- All files in `inputs/sample_letters/` directory - These are the primary reference for tone, length, and writing style. You MUST analyze these thoroughly before composing the cover letter.

**Optional Input Files:**
- `outputs/{company}_{date}/feedback.md` - Refinement instructions for iterating on a draft (if present, incorporate while preserving core style matching)
- `config.yaml` - Configuration file that may contain preferences like tone, length, emphasis areas (use as fallback if sample letters are missing)

The company name, date, and timestamp (in `HH-MM` format) will be provided in the execution context. The timestamp should be used for both `cover_letter_{time}.md` and `reasoning_{time}.md` filenames to ensure they are paired together. You should read all the analysis files and sample letters.

## Output

**Primary Output Files:**
- `outputs/{company}_{date}/cover_letter_{time}.md` - The generated cover letter in markdown format. This must match the tone, length, and style of the sample letters. The timestamp `{time}` is in `HH-MM` format (24-hour format, e.g., `14-30` for 2:30 PM) and matches the reasoning document timestamp.

- `outputs/{company}_{date}/reasoning_{time}.md` - Explanation of content choices for each paragraph, including:
  - Purpose of each paragraph
  - Source materials that informed the content (job_analysis, resume_matches, github_matches, sample_letters)
  - How each paragraph matches the style from sample letters
  - Specific style elements applied (tone, length, structure)

**Optional Output File:**
- `outputs/{company}_{date}/analysis/composed_letter_metadata.json` - Optional metadata structure validating against `schemas/composed-letter.json`. This can include paragraph-level metadata and style notes documenting the style matching process.

## Processing Instructions

### CRITICAL: Style Matching from Sample Letters

**This is the most important step. Before composing the cover letter, you MUST thoroughly analyze all sample letters in `inputs/sample_letters/` to extract and match:**

#### 1. Tone Analysis

Analyze the overall tone and voice across all sample letters:

- **Identify the overall tone**: Professional, enthusiastic, conversational, formal, warm, confident, humble, etc.
- **Note specific word choices**: Pay attention to vocabulary level, formality of language, use of technical vs. accessible terms
- **Observe phrasing patterns**: How sentences are structured, use of transitions, sentence length variety
- **Observe how the writer addresses the reader**: Direct address style, formality of salutation and closing
- **Match the exact tone level**: If samples are moderately enthusiastic, don't be overly casual or overly formal. If samples are warm and personal, maintain that warmth. If samples are professional and reserved, maintain that restraint.
- **Pay attention to sentence structure and formality level**: Note use of contractions, passive vs. active voice, first person usage patterns

#### 2. Length Analysis

Measure and match the length characteristics:

- **Count words in each sample letter**: Calculate total word count for each sample
- **Count paragraphs in each sample letter**: Note the typical number of paragraphs
- **Calculate average length across all samples**: If multiple samples exist, calculate average word count and paragraph count
- **Identify typical paragraph length**: Note how long individual paragraphs tend to be (word count per paragraph)
- **Match the target length category**: Based on samples, determine if the style is "concise" (short, focused), "moderate" (balanced), or "detailed" (comprehensive, longer)
- **Ensure the generated letter falls within the same length range**: The generated letter should have a similar word count and paragraph count to the samples (within 10-15% variation is acceptable, but aim for the same range)

#### 3. Style Analysis

Extract structural and stylistic patterns:

- **Opening patterns**: How do they introduce themselves? How do they express interest in the position? What's the first sentence structure?
- **Paragraph structure and flow**: How are paragraphs organized? What's the typical flow from introduction to body to closing?
- **How they present qualifications**: Do they use direct statements ("I have 5 years of experience...") or storytelling ("In my role at X, I...")? Do they lead with achievements or skills?
- **Use of specific examples vs. general statements**: Do samples include concrete examples, metrics, or project names? Or are they more general?
- **Closing patterns**: How do they reiterate interest? How do they sign off? What's the tone of the closing?
- **Formatting preferences**: Paragraph spacing, use of line breaks, salutation style, signature format
- **Voice and perspective**: First person usage patterns ("I", "my", "me"), active vs. passive voice, how they describe their experience

#### 4. Style Matching Requirements

- **The generated cover letter MUST match the tone, length, and style patterns observed in the sample letters**
- **If multiple sample letters exist**: Identify common patterns and use those consistently. Look for recurring elements across samples.
- **If sample letters vary in style**: Use the most common pattern, or create a blend that feels authentic. Document the style choices in the reasoning document.
- **Every paragraph should feel like it could have been written by the same person who wrote the sample letters**: The voice, tone, and style should be consistent throughout.

### Content Composition

After completing the style analysis, compose the cover letter following these content guidelines while maintaining the extracted style:

#### 1. Identify Strongest Content to Highlight

- **Resume matches**: Identify the highest relevance scores (7-10) from resume_matches.json. These are the strongest qualifications to emphasize.
- **GitHub projects**: If github_matches.json contains matches with relevance scores of 5 or higher, identify the most relevant projects to reference.
- **Job requirements**: Prioritize addressing required items from job_analysis.json, especially those with high emphasis themes.

#### 2. Map Job Requirements to Evidence

- **Create connections**: Link specific job requirements to resume experience and GitHub projects
- **Prioritize required items**: Focus on required job requirements over preferred or nice-to-have items
- **Address key themes**: Incorporate the key themes from job_analysis.json, especially those with "high" emphasis
- **Show culture alignment**: Reference culture signals (values, work style, team dynamics) from job_analysis.json where relevant

#### 3. Compose Paragraphs

Structure the cover letter following the paragraph patterns observed in sample letters:

- **Opening paragraph**: Express interest and connection to the role (matching sample letter opening style exactly - same tone, structure, and approach)
- **Body paragraphs (typically 2-3)**: 
  - Highlight relevant experience from resume matches (using sample letter's approach to presenting qualifications - same style of describing experience)
  - Demonstrate skills through GitHub projects if applicable (matching how samples reference projects, if they do)
  - Show understanding of company culture/themes (matching sample letter's approach to demonstrating company knowledge)
- **Closing paragraph**: Reiterate interest and call to action (matching sample letter closing patterns exactly - same tone and structure)

**While composing, continuously refer back to the style analysis to ensure:**
- Word choice matches the tone
- Sentence structure matches the patterns
- Paragraph length matches the samples
- Voice and perspective match the samples
- Overall flow matches the samples

#### 4. Generate Reasoning Document

Create a comprehensive reasoning document that explains:

- **For each paragraph**:
  - Paragraph number and purpose (introduction, experience highlight, skills demonstration, closing, etc.)
  - Source materials that informed the content (job_analysis, resume_matches, github_matches, sample_letters)
  - Specific content choices and why they were made
  - How the paragraph matches the style from sample letters (tone, structure, voice, length)
  - Specific style elements applied (e.g., "Used direct statement style matching Sample A", "Matched paragraph length of 85 words from samples")

- **Overall style matching summary**:
  - Extracted tone characteristics and how they were applied
  - Target length and how it was achieved
  - Key style patterns adopted (opening style, paragraph structure, closing style)
  - Any style choices made when samples varied

## Schema Reference

If generating the optional metadata file, the output must validate against `schemas/composed-letter.json`. Key requirements:

**Optional Fields:**
- `paragraphs`: Array of paragraph metadata objects, each with:
  - `paragraph_number` (required): Integer, 1-based paragraph index
  - `purpose` (required): String describing the paragraph's purpose
  - `source_materials` (optional): Array of strings from enum ["job_analysis", "resume_matches", "github_matches", "sample_letters"]
- `style_notes`: Object with:
  - `tone` (optional): String describing overall tone
  - `length` (optional): String describing target length category
  - `key_style_elements` (optional): Array of strings describing style elements adopted

**Note:** The actual cover letter is markdown format, not JSON. The metadata file is optional and for analysis purposes only.

Read the schema file at `schemas/composed-letter.json` for complete validation rules and structure.

## Output Format Example

### Cover Letter Example Structure

```markdown
[Date]

[Hiring Manager Name]  
[Company Name]  
[Company Address]

Dear [Hiring Manager Name],

[Opening paragraph - matching sample letter opening style exactly]

[Body paragraph 1 - highlighting relevant experience, matching sample letter's approach to presenting qualifications]

[Body paragraph 2 - demonstrating skills through projects or achievements, matching sample letter's style]

[Body paragraph 3 - showing understanding of company culture/themes, matching sample letter's approach]

[Closing paragraph - reiterating interest, matching sample letter closing patterns exactly]

Sincerely,  
[Your Name]
```

### Reasoning Document Example Structure

```markdown
# Cover Letter Reasoning

## Style Analysis Summary

**Tone**: [Extracted tone from samples, e.g., "Professional with moderate enthusiasm"]
**Target Length**: [Extracted length, e.g., "Moderate - 400-450 words, 4-5 paragraphs"]
**Key Style Elements**: 
- [Style element 1, e.g., "Direct statement style for presenting qualifications"]
- [Style element 2, e.g., "Warm but professional closing"]
- [Style element 3, e.g., "Use of specific examples with metrics"]

## Paragraph-by-Paragraph Analysis

### Paragraph 1: Introduction
**Purpose**: Express interest and connection to the role
**Source Materials**: job_analysis, sample_letters
**Content Choices**: [Explanation of what was included and why]
**Style Matching**: [How this paragraph matches sample letter style - tone, structure, length]

### Paragraph 2: Experience Highlight
**Purpose**: Highlight relevant experience matching job requirements
**Source Materials**: resume_matches, job_analysis, sample_letters
**Content Choices**: [Explanation of which resume matches were highlighted and why]
**Style Matching**: [How this paragraph matches sample letter's approach to presenting qualifications]

[... continue for all paragraphs ...]
```

## Execution Steps

1. Read all input files:
   - Read analysis JSONs from `outputs/{company}_{date}/analysis/` directory
   - Read ALL files in `inputs/sample_letters/` directory
   - Read optional feedback.md if present
   - Read optional config.yaml if present

2. **CRITICAL STEP: Analyze Sample Letters for Style Matching**
   - Read ALL files in `inputs/sample_letters/` directory
   - Extract tone characteristics (word choice, formality, enthusiasm level, sentence structure)
   - Measure length (word count, paragraph count, average paragraph length) for each sample
   - Calculate averages if multiple samples exist
   - Identify style patterns (opening/closing, paragraph structure, voice, formatting, how qualifications are presented)
   - Document these patterns as style guidelines that MUST be followed
   - If no sample letters exist, use default professional style and note this in reasoning document (or use config.yaml preferences if available)

3. Analyze content from analysis files:
   - Identify strongest resume matches (highest relevance scores)
   - Identify most relevant GitHub projects (if available)
   - Map job requirements to available evidence
   - Identify key themes and culture signals to address

4. Compose cover letter STRICTLY following the extracted style patterns:
   - Match tone word-for-word approach (use similar vocabulary, formality level, enthusiasm)
   - Match target length (within same range as samples - aim for average if multiple samples)
   - Match paragraph structure and flow (same number of paragraphs, similar paragraph purposes)
   - Match opening and closing patterns (same style, same approach)
   - Match voice and perspective (same use of first person, active/passive voice)
   - Match how qualifications are presented (same style of describing experience)
   - Ensure every sentence feels like it could have been written by the same person who wrote the samples

5. Generate reasoning document explaining:
   - Style analysis summary (tone, length, key style elements extracted)
   - Each paragraph's purpose and source materials
   - How each paragraph matches the style from sample letters
   - Specific style elements applied (tone, length, structure, voice)
   - Any style choices made when samples varied

6. Optionally generate metadata JSON file:
   - Create paragraph-level metadata
   - Document style notes (tone, length, key style elements)
   - Validate against `schemas/composed-letter.json`

7. Write outputs to specified paths:
   - Write cover letter to `outputs/{company}_{date}/cover_letter_{time}.md` (where `{time}` is the timestamp in `HH-MM` format provided in the execution context)
   - Write reasoning to `outputs/{company}_{date}/reasoning_{time}.md` (using the same timestamp as the cover letter)
   - Optionally write metadata to `outputs/{company}_{date}/analysis/composed_letter_metadata.json`
   - Ensure output directory exists before writing

## Edge Cases

- **Missing sample letters**: Use default professional style, but note this clearly in the reasoning document. If config.yaml has style preferences (tone, length), use those as fallback. The reasoning document should explain that no sample letters were available for style matching.

- **Single sample letter**: Match that letter's style exactly (tone, length, structure, voice). Use it as the definitive style guide.

- **Multiple sample letters with varying styles**: Identify the most common patterns across samples, or create a blend that feels authentic. Document the style choices in reasoning. For example, if 3 samples use direct statements and 1 uses storytelling, use direct statements but note the variation.

- **Sample letters with very different lengths**: Calculate the average length, or use the length of the most recent/relevant sample. Document the choice in reasoning. Aim for consistency with the most common length pattern.

- **No GitHub matches**: Focus on resume only, but maintain style matching from samples. The absence of GitHub content should not affect the style.

- **Weak resume matches**: Emphasize transferable skills while maintaining sample letter style. Use the same tone and approach even when content is more general.

- **Feedback file present**: Incorporate refinement instructions while preserving the core style matching from sample letters. Only deviate from style matching if feedback explicitly requests style changes (e.g., "make it more formal" or "shorten it"). Otherwise, apply feedback within the established style framework.

- **Sample letters reference different types of roles**: Focus on the writing style patterns rather than role-specific content. Extract the style elements (tone, structure, voice) that are consistent across samples regardless of role type.

- **Sample letters have inconsistent formatting**: Use the most common formatting pattern, or the formatting from the most recent sample. Document the choice in reasoning.

