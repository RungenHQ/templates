# pi-managed-starter

This is the first managed-pi starter for Rungen.

It is designed to work in two ways:
- Manually, by copying the repo to GitHub and importing it into Rungen.
- Through the Templates flow, where Rungen creates the GitHub repo for you from this starter.

## What is inside
- `rungen.json` for the managed-pi deployment contract.
- `AGENTS.md` and `SYSTEM.md` for repo-local agent guidance.
- `.pi/` for starter prompts and skills that ship with the repo.

## Manual use
1. Create a new GitHub repository.
2. Copy the contents of this folder into it.
3. Push the repo and import it in Rungen.
4. Deploy the imported service, then start runs from the Runs page.

## Template use
1. Open Templates in Rungen.
2. Pick the managed-pi starter.
3. Let Rungen create the GitHub repo, project, and service import for you.
4. Deploy the service and launch runs.

## Customize
- Keep repo-local guidance in `AGENTS.md`, `SYSTEM.md`, and `.pi/`.
- Add or adjust prompts and skills inside `.pi/`.
- Leave `rungen.json` as the source of truth for the managed-pi contract.

