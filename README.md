# Personal Brand Job Post Automation (n8n)

n8n workflow that turns a job webhook payload into a LinkedIn personal-profile post: Gemini writes the caption, builds a branded poster prompt, edits your logo into a job image, then publishes image + caption to LinkedIn.

## What it does

1. Receives a job listing via authenticated webhook (`POST`)
2. Validates required fields (`title`, `link`, body)
3. Generates a LinkedIn caption with Gemini 2.5 Flash
4. Builds an image-generation prompt, downloads your brand logo from Google Drive, and creates a poster with Gemini image edit
5. Posts the image and caption to your personal LinkedIn profile

## Import

1. In n8n: **Workflows → Import from File**
2. Select `n8n-linkedin-job-post-automation.json`
3. Replace every `REPLACE_WITH_YOUR_...` / `[YOUR BRAND ...]` placeholder (see sticky notes in the canvas)
4. Connect credentials (checklist below)
5. Activate the workflow and copy the **Production** webhook URL into your job board / trigger system

## Webhook payload

`POST` JSON body. Required: `title`, `link`.

| Field | Type | Notes |
| --- | --- | --- |
| `title` | string | Required |
| `link` | string | Required application URL |
| `description` | string | |
| `company_name` | string | |
| `hide_company_name` | boolean | If true, caption never names the company |
| `company_industry` | string | Used when company name is hidden |
| `location` | string | |
| `employment_type` | string | `full_time` or `part_time` |
| `salary_type` | string | e.g. `negotiable` or a fixed value |
| `skills_required` | string or array | |
| `key_responsibilities` | string or array | |
| `education_level` | string | |
| `experience_level.level` | string | `junior`, `mid`, or `senior` |
| `slug` | string | Optional |

Example:

```json
{
  "title": "Senior Frontend Engineer",
  "link": "https://example.com/jobs/senior-frontend",
  "description": "Build and ship product UI with React.",
  "company_name": "Acme",
  "hide_company_name": false,
  "company_industry": "SaaS",
  "location": "Remote",
  "employment_type": "full_time",
  "salary_type": "negotiable",
  "skills_required": ["React", "TypeScript", "CSS"],
  "key_responsibilities": ["Own UI features", "Partner with design"],
  "education_level": "Bachelor",
  "experience_level": { "level": "senior" },
  "slug": "senior-frontend"
}
```

## Credentials

| Credential | Used by |
| --- | --- |
| HTTP Basic Auth | Job Post Webhook |
| Google PaLM / Gemini API | Generate Caption, Generate Poster Prompt, Generate Job Poster |
| Google Drive OAuth2 | Download Brand Logo |
| LinkedIn OAuth2 API | Post to LinkedIn |

## Placeholders to replace

- `[YOUR BRAND NAME]`, `[YourBrandHashtag]`, `REPLACE_WITH_YOUR_EMAIL` — caption prompt
- `REPLACE_WITH_PRIMARY_HEX`, `REPLACE_WITH_SECONDARY_HEX`, `REPLACE_WITH_YOUR_EMAIL` — poster prompt
- `REPLACE_WITH_YOUR_LOGO_FILE_ID` — Google Drive file ID of your logo PNG
- Webhook Basic Auth username/password — your choice

## Notes

- Wait nodes add short pauses between Gemini / LinkedIn calls to reduce rate-limit failures.
- LinkedIn posting uses `continueOnFail`; check execution history if posts seem missing.
- LinkedIn personal-profile API share limits still apply — monitor usage if you post often.
