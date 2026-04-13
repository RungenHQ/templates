# pi-managed-starter

This is the first managed-pi starter for Rungen.

It is designed to work in two ways:
- Manually, by copying the repo to GitHub and importing it into Rungen.
- Through the Templates flow, where Rungen creates the GitHub repo for you from this starter.

## What is inside
- `rungen.json` for the managed-pi deployment contract.
- `AGENTS.md` and `SYSTEM.md` for repo-local agent guidance.
- `.pi/` for starter prompts and skills that ship with the repo.

## How it works

This template has no Dockerfile and no application code — that's intentional.

[Pi](https://pi.dev) is a terminal-based coding agent (similar to Claude Code). You can install it locally with `npm install -g @mariozechner/pi-coding-agent`, but you don't need to — Rungen handles that for you.

When you deploy this template, here's what Rungen does behind the scenes:

1. **Reads `rungen.json`** — the `"preset": "pi"` build mode tells Rungen to auto-generate a Dockerfile instead of expecting one in the repo.
2. **Builds a container** — based on `node:20-slim`, installs the pi runtime globally, and copies your repo files (SYSTEM.md, AGENTS.md, `.pi/` skills) into the image.
3. **Creates a Cloud Run Job** — not a long-running service, but a job. Each "run" is a single job execution.
4. **Executes runs** — when you start a run, Rungen triggers the Cloud Run Job with your prompt injected via environment variables. The pi agent executes the prompt using your repo-local instructions, then uploads any artifacts to cloud storage.

### What each file becomes in the container

| File | Role |
|------|------|
| `rungen.json` | Deployment contract — tells Rungen how to build and what packages to install |
| `SYSTEM.md` | System prompt loaded by the pi agent at runtime |
| `AGENTS.md` | Working rules and response guidelines for the agent |
| `.pi/` | Starter prompts and skills available to the agent during runs |

### Adding pi packages

To install additional pi packages into the container, add them to `runtime.pi.packages` in `rungen.json`:

```json
"runtime": {
  "pi": {
    "packages": ["npm:@some-org/some-package@1.0.0"]
  }
}
```

Packages must pin an exact version (npm) or a commit/tag (git).

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

The `.pi/` folder is where you shape the agent's behavior. Here's the structure from a working example:

```
.pi/
├── README.md              # Describes the assets in this folder
├── settings.json          # Default provider and model
├── agent/
│   └── models.json        # Custom model definitions (provider, cost, context window)
├── prompts/
│   ├── quickstart.md      # Prompt: how to respond when the user asks for help
│   └── release-checklist.md  # Prompt: pre-deploy validation checklist
└── skills/
    ├── repo-audit.md      # Skill: verify the starter is ready to deploy
    └── support-playbook.md   # Skill: guide the user to the shortest path to success
```

### Settings and models

`.pi/settings.json` sets the default provider and model:

```json
{
  "defaultProvider": "openrouter",
  "defaultModel": "minimax/minimax-m2.5:free"
}
```

`.pi/agent/models.json` registers custom model definitions so the agent can use providers beyond the built-in ones:

```json
{
  "providers": {
    "openrouter": {
      "baseUrl": "https://openrouter.ai/api/v1",
      "api": "openai-completions",
      "apiKey": "OPENROUTER_API_KEY",
      "models": [
        {
          "id": "minimax/minimax-m2.5:free",
          "name": "MiniMax M2.5 (OpenRouter free)",
          "input": ["text"],
          "contextWindow": 196608,
          "maxTokens": 512
        }
      ]
    }
  }
}
```

If your model needs an API key, add the key name (e.g. `OPENROUTER_API_KEY`) to the `secrets` array in `rungen.json` so it gets injected at runtime.

### Prompts vs skills

- **Prompts** (`.pi/prompts/`) — reusable instructions the agent follows for specific scenarios. Example: `quickstart.md` tells the agent to always lead with the immediate next step and keep answers short.
- **Skills** (`.pi/skills/`) — triggered when the user asks for a specific capability. Example: `repo-audit.md` runs a checklist to verify the starter is ready to deploy.

### Tips

- Keep repo-local guidance in `AGENTS.md`, `SYSTEM.md`, and `.pi/`.
- Add or adjust prompts and skills inside `.pi/` to change the agent's behavior without redeploying.
- Leave `rungen.json` as the source of truth for the managed-pi contract.

