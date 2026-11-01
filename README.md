docker compose up -d

docker compose logs -f litellm
docker compose down

After adding or changing models in `config.yaml`, run this from the `claude-litellm` folder to reload the configuration:

```bash
docker compose restart litellm
```

http://localhost:4000

In your terminal:

export ANTHROPIC_BASE_URL=http://localhost:4000
export ANTHROPIC_AUTH_TOKEN=sk-local-claude

Choose a model:

```bash
claude --model gpt-5.4-ptu
claude --model gpt-5.6-terra-ptu
```

---

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

Your setup would be:

my-project/
├── .claude/
│   └── settings.local.json   ← Claude Code → LiteLLM
├── src/
└── ...

### Switching back to normal Claude

If you configured LiteLLM globally, remove its `env` entries, custom `model`, and `modelPicker` from `~/.claude/settings.json`. Disable any project-local overrides too:

mv .claude/settings.local.json .claude/settings.local.json.disabled

### One improvement I would make is to keep two config files:

.claude/
├── settings.litellm.json
└── settings.local.json


mkdir -p ~/bin
~/bin/claude-mode
Make it executable:
chmod +x ~/bin/claude-mode
Then add ~/bin to your PATH in ~/.zshrc:
export PATH="$HOME/bin:$PATH"
Reload:
source ~/.zshrc
After that you can run from any folder:
claude-mode custom
claude-mode normal


~/bin/
└── claude-mode

~/claude-litellm/
├── docker-compose.yml
├── config.yaml
└── .env

~/projects/my-project/
└── .claude/
    └── settings.local.json
