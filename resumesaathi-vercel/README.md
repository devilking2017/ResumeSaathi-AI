# ResumeSaathi AI

Prepare Smarter. Interview Better. Upload a resume, get personalised interview questions with sample answers, and practise with an AI mock interview. English, Hindi (Devanagari), Hindi (Roman) and Hinglish are supported.

## Folder structure

```
public/index.html      -> the whole website (SaaS-style, mobile friendly)
api/*.js, api/mock/*.js -> Vercel serverless functions (Gemini calls happen here)
api/health.js          -> open /api/health on your live site to check key, model and speed
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

## If you see "AI service ne request reject ki"

The error now shows Google's HTTP status and reason. Common fixes:

- Use a key from https://aistudio.google.com/apikey. Update `GEMINI_API_KEY` in Vercel and **Redeploy**.
- The server uses the Google AI Studio endpoint. Only if your key is a Vertex AI key set `GEMINI_API_TYPE=vertex`.
- Make sure the key was pasted with no spaces/quotes in Vercel.
- HTTP 404 (model not available): the server tries `gemini-3.8-flash`, then `gemini-flash-latest`, then `gemini-flash-lite-latest` (it also moves to the next model when one is busy/rate-limited). To force a model set `GEMINI_MODEL=<model name>` in Vercel (Google retires old models, e.g. gemini-2.5-flash is closed to new users).
- Environment variable changes only apply after a new deployment.

## Timeout / "busy" problems - how to diagnose in 30 seconds

1. Open `https://YOUR-SITE.vercel.app/api/health` (also linked as "AI status check" in the site footer).
   - `"ok": true` -> Gemini works; `latencyMs` shows how fast it is.
   - `"ok": false` -> the `message` field says exactly why (wrong key, model not available, busy, timeout).
2. Server limits built in: each AI call gets at most 50 seconds in total (Vercel limit is 60s, set in `vercel.json`), the next model is tried automatically, and the browser retries busy/rate-limit/timeout errors by itself and then re-tries failed parts once.
3. If you still see "Server time out ho gaya (Vercel function limit)": your Vercel plan may cap functions below 60s. In Vercel > Settings > Functions check the max duration / enable Fluid Compute, then redeploy. Choosing 10 questions also makes every call shorter.
4. Free Gemini keys have per-minute limits. 30 questions create about 8 AI calls, so 10 questions is the safest on the free tier.
5. Optional env vars: `GEMINI_MODEL` (force one model), `GEMINI_ATTEMPT_TIMEOUT_MS` (default 28000), `GEMINI_DEADLINE_MS` (default 50000).
