# ResumeSaathi AI

Prepare Smarter. Interview Better. Upload a resume, get personalised interview questions with sample answers, and practise with an AI mock interview. English, Hindi (Devanagari), Hindi (Roman) and Hinglish are supported.

## Folder structure

```
public/index.html      -> the whole website (SaaS-style, mobile friendly)
api/*.js, api/mock/*.js -> Vercel serverless functions (Gemini calls happen here)
lib/core.js            -> shared prompts + Gemini integration (server only)
vercel.json            -> function timeout (60s) + security headers
```

## Deploy: GitHub -> Vercel

1. Create a new **empty GitHub repository** and upload / push all these files (`.gitignore` already protects `.env` files).
2. Go to https://vercel.com/new, import the repository. Leave the framework as **Other** and do not change build settings.
3. Before pressing Deploy open **Environment Variables** and add:
   - Name: `GEMINI_API_KEY`
   - Value: your key from https://aistudio.google.com/apikey
4. Deploy. If you add the variable later, press **Redeploy** (Deployments > ... > Redeploy).

The key is only used inside `/api/*` functions and is never sent to the browser. Do **not** paste the key into any file in the repo.

## Run locally (optional)

```
npm i -g vercel
cp .env.example .env.local   # then put your key inside
vercel dev
```

## Notes and limits

- Free Gemini tier has rate limits. If you see "limit poori ho gayi", wait a minute and retry.
- Scanned (image-only) PDFs are not read because OCR is not included. Use a DOCX or paste text.
- PDF/DOCX are read in the browser using pdf.js and mammoth loaded from cdnjs.
- Resumes are not stored. Everything stays in the browser tab memory; the server does not log resume content.
- Other Indian languages are listed as "coming soon" and fall back to English until tested. Add or enable one in `lib/core.js` (`LANGUAGES`).
- "Print / Save PDF" uses the browser print dialog.
- All generated content is AI practice material, not guaranteed interview questions or verified facts.
