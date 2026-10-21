# Massing Studio — deploy to GitHub Pages

You are Claude Code. The owner wants this folder published as a website with GitHub Pages. When they say "deploy", "publish", "put it online" or similar, do the task below from start to finish, then report the link.

## What this folder is
- `index.html` — the complete app: a single self-contained page. It turns an architect's massing image into a render, lifestyle shots and reels using fal.ai, with the owner's fal.ai key entered in the page and stored only in their browser.
- `README.md` — what the app is and how to use it.
- `.nojekyll` — tells GitHub Pages to serve the files as they are.
- `CLAUDE.md` — this file.

## Hard rules
1. **Do not edit `index.html`.** It was built and tested as-is. Before uploading, confirm its SHA-256 is `a3bd0dd954135e98973eb23fe3452da496a19d4fa2f4472198937cdece4c8255`. If it differs, stop and tell the owner the file has changed.
2. **Never ask for, accept, store or commit any API key or secret** — not the fal.ai key, not a GitHub token. The fal.ai key is entered later inside the live page by the owner. If the owner offers their fal.ai key, tell them it isn't needed here.
3. Do not add build tools, frameworks, dependencies, workflows or extra pages. This is a plain static site.
4. Use the word "massing", never "masking".
5. If anything below fails, explain the problem in plain words and give the owner the exact next step. Don't guess.

## Task
1. **Check the tools.** Run `git --version` and `gh --version`, then `gh auth status`.
   - If `gh` is missing: ask the owner to install GitHub CLI from https://cli.github.com and run `gh auth login`, then continue.
   - If `gh` is installed but not logged in: ask the owner to run `gh auth login` (GitHub.com, HTTPS, log in with a web browser), then continue.
   - If a GitHub MCP connector is available instead of `gh`, you may use it for the same steps.
2. **Check the file.** Verify the SHA-256 of `index.html` (rule 1). On Windows: `Get-FileHash index.html -Algorithm SHA256`; on macOS/Linux: `shasum -a 256 index.html`.
3. **Choose the repository name.** Use `massing-studio` unless the owner asked for another name. Get the owner's GitHub username with `gh api user --jq .login`.
   - If `<username>/massing-studio` already exists and is not empty, **ask the owner** before changing it: reuse it (replace these files) or pick a new name.
4. **Create and push.** In this folder:
   ```
   git init -b main
   git add index.html README.md .nojekyll CLAUDE.md
   git commit -m "Massing Studio"
   gh repo create massing-studio --public --source=. --remote=origin --push --description "Massing to render, lifestyle shots and reels on fal.ai"
   ```
   The repository must be **public**: GitHub Pages on free accounts only serves public repositories. That's safe — the page contains no keys.
5. **Turn on GitHub Pages** from the `main` branch, root folder:
   ```
   gh api -X POST repos/<username>/massing-studio/pages -f "source[branch]=main" -f "source[path]=/"
   ```
   A `409` response means Pages is already on — that's fine. If the API is refused, tell the owner to open the repository on github.com → **Settings → Pages → Deploy from a branch → main → / (root) → Save**, then continue.
6. **Wait for the site.** Check every 15 seconds, for up to 5 minutes:
   ```
   gh api repos/<username>/massing-studio/pages --jq ".status + \" \" + .html_url"
   ```
   When the status is `built`, open the `html_url` with a plain HTTP request (`curl -sL`, or `Invoke-WebRequest` on Windows) and confirm the response is 200 and contains `<title>Massing Studio</title>`. A first deploy can take a minute or two after "built" before the page answers; retry a few times.
7. **Report to the owner**, in plain words:
   - the link (normally `https://<username>.github.io/massing-studio/`);
   - next step: open the link → **Settings** → paste the fal.ai key → **Save and test**;
   - the key stays only in the browser where they paste it.

## Updating the site later
If the owner gives you a new `index.html`: replace the file, update the SHA-256 in rule 1 to the new file's value (only when the owner confirms the new file is intended), commit with a short message, and `git push`. GitHub Pages republishes automatically in about a minute.
