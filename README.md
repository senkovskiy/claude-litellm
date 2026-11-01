# Claude Code + LiteLLM Proxy

Run a local [LiteLLM](https://www.litellm.ai/) proxy in Docker to let Claude Code and the Claude Code VS Code extension use models exposed by an OpenAI-compatible API.

The proxy runs at `http://localhost:4000`. It routes the models configured in `config.yaml` using the provider credentials kept in your local `.env` file.

## Requirements

- Docker Desktop
- Claude Code CLI or the Claude Code VS Code extension
- An OpenAI-compatible API endpoint and API key

## Setup

1. Create your local environment file:

   ```bash
   cp .env.example .env
   ```

2. Edit `.env` and add your provider credentials:

   ```env
   API_KEY="your-api-key"
   API_BASE="https://your-provider.example/v1"
   LITELLM_MASTER_KEY="sk-local-claude"
   ```

   Keep `.env` private. It is excluded from Git by `.gitignore`.

3. Start the proxy:

   ```bash
   docker compose up -d
   ```

4. Confirm that it is running at <http://localhost:4000>.

## Use with Claude Code

For the current terminal session, configure Claude Code to use the local proxy:

```bash
export ANTHROPIC_BASE_URL=http://localhost:4000
export ANTHROPIC_AUTH_TOKEN=sk-local-claude
```

Then select a configured model:

```bash
claude --model gpt-5.4-ptu
claude --model gpt-5.6-terra-ptu
```

The model ID must match a `model_name` entry in `config.yaml`.

## Manage the proxy

```bash
# View logs
docker compose logs -f litellm

# Reload config.yaml after adding or changing models
docker compose restart litellm

# Stop the proxy
docker compose down
```

## Project files

- `config.yaml` — LiteLLM model routing configuration.
- `.env` — local API credentials; do not commit this file.
- `.env.example` — safe template for creating `.env`.
- `docker-compose.yml` — Docker Compose service definition.

## Configure Claude Code and VS Code

The Claude Code CLI and VS Code extension share these settings.

### Global settings (all projects)

Merge these fields into `~/.claude/settings.json`, preserving your other settings:

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://localhost:4000",
    "ANTHROPIC_AUTH_TOKEN": "sk-local-claude",
    "ANTHROPIC_MODEL": "gpt-5.6-terra-ptu",
    "ANTHROPIC_DEFAULT_MODEL": "gpt-5.6-terra-ptu",
    "CLAUDE_CODE_ENABLE_GATEWAY_MODEL_DISCOVERY": "1"
  },
  "model": "gpt-5.6-terra-ptu",
  "modelPicker": {
    "options": [
      { "model": "gpt-5.4-ptu", "label": "gpt-5.4-ptu" },
      { "model": "gpt-5.6-terra-ptu", "label": "gpt-5.6-terra-ptu" }
    ]
  }
}
```

`ANTHROPIC_MODEL` selects the startup model; `ANTHROPIC_DEFAULT_MODEL` also sets the dropdown's Default option. Model IDs must match a `model_name` in `config.yaml`.

Gateway discovery only includes IDs containing `claude` or `anthropic`; GPT models need the explicit `modelPicker` entries above. A discovered model already represented by a built-in alias, such as Opus, does not get a separate gateway row.

After saving these settings, or restarting LiteLLM after a model change, press **⌘⇧P → Developer: Reload Window** in VS Code, then reopen the Claude Code model dropdown.

### Project-local settings (optional)

Use `.claude/settings.local.json` for project-specific `env` overrides. Projects otherwise inherit the global settings. Remove an old local `ANTHROPIC_MODEL` entry to inherit the global model. Keep `modelPicker` in the global file; Claude Code ignores it in project-local settings.

Example project layout:

```text
my-project/
├── .claude/
│   └── settings.local.json
├── src/
└── ...
```

### Switching back to normal Claude

If you configured LiteLLM globally, remove its `env` entries, custom `model`, and `modelPicker` from `~/.claude/settings.json`. Disable any project-local overrides too:

```bash
mv .claude/settings.local.json .claude/settings.local.json.disabled
```
