# Maverik Tool Starter

This is your reusable pattern, copied from the Reels Script Generator. Same architecture every time: a static HTML page on GitHub Pages, talking to a small Cloudflare Worker that holds your Anthropic API key so it never sits exposed in public code.

```
Your browser  →  index.html (GitHub Pages)  →  worker.js (Cloudflare)  →  Anthropic API
```

## Files in this folder

- `index.html` — the frontend. Config block is at the top of the `<script>` tag.
- `worker.js` — the backend proxy. Deploys to Cloudflare, not GitHub.
- `README.md` — this file.

## Deploying a new tool from this template

### 1. Customize index.html
- Change the `<title>` and the `<h1>`
- Update the `SYSTEM` prompt to describe what this specific tool generates
- Update the `chips` presets
- Update `renderCards()` to match whatever fields your new SYSTEM prompt returns
- Leave `WORKER_URL` as the placeholder for now, you'll fill it in at step 4

### 2. Create a new GitHub repo
- One repo per tool keeps things clean and matches your existing Reels Generator setup
- Push `index.html` to the repo
- In the repo's Settings > Pages, set the source to your main branch
- GitHub gives you a live URL like `https://meadedm1.github.io/your-new-tool/`

### 3. Create a new Cloudflare Worker
- In the Cloudflare dashboard, go to Workers & Pages > Create > Worker
- Paste in `worker.js`
- Change `ALLOWED_ORIGIN` at the top to match your new GitHub Pages URL from step 2 exactly (this is what stops other sites from using your Worker to burn your API credits)
- Go to Settings > Variables and Secrets, add a secret named `ANTHROPIC_API_KEY` with your real key
- Deploy. Copy the Worker's URL, it'll look like `https://your-tool-name.theugcmaverik.workers.dev`

### 4. Connect the two
- Paste the Worker URL from step 3 into the `WORKER_URL` constant in `index.html`
- Push that change to GitHub
- Wait a minute for Pages to redeploy, then test the live site

## Checklist before you call it done

- [ ] `ALLOWED_ORIGIN` in the Worker matches your real GitHub Pages URL exactly (no trailing slash mismatch)
- [ ] API key is stored as a secret in Cloudflare, not pasted into worker.js
- [ ] `WORKER_URL` in index.html is the real deployed Worker URL, not the placeholder
- [ ] Tested the "Generate" button on the live GitHub Pages URL, not just locally
- [ ] Checked the browser console for CORS errors if generation fails silently

## Notes

- Each new tool should get its own Worker. If one tool's key ever needs rotating or one tool gets abused, it doesn't take the others down with it.
- The model used in `index.html` is `claude-sonnet-4-6`. Anthropic's available models change over time, so it's worth double-checking the current model name in Anthropic's docs before each new deploy.
