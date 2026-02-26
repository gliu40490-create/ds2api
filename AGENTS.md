# AGENTS.md

## Cursor Cloud specific instructions

### Overview

DS2API is a reverse-engineering proxy converting DeepSeek Web into OpenAI and Claude compatible API endpoints. It has two services:

| Service | Port | Command |
|---------|------|---------|
| Python Backend (FastAPI) | 5001 | `python dev.py --backend` or `python3 -m uvicorn app:app --host 0.0.0.0 --port 5001 --reload` |
| WebUI Frontend (Vite/React) | 5173 | `cd webui && npm run dev` |

Both can be started together with `python dev.py`.

### Development commands

- **Install all deps**: `python dev.py --install` (runs pip + npm install)
- **Unit tests**: `python3 tests/test_unit.py` (32 tests, no network needed)
- **API integration tests**: `python3 tests/test_all.py` (requires running server + real DeepSeek account)
- **Build WebUI**: `cd webui && npx vite build` (outputs to `static/admin/`)
- **Lint**: No dedicated linter configured; follow PEP 8 for Python, 4-space indent for JS/React

### Gotchas

- `~/.local/bin` must be on PATH for `uvicorn` and other pip-installed scripts to be found directly. The update script handles this.
- A `config.json` is required at the repo root for the backend to start (copy from `config.example.json`). Without real DeepSeek account credentials, chat completions will fail with "Account login failed" but all other endpoints (models list, auth, admin UI) work fine.
- The admin key defaults to `DS2API_ADMIN_KEY` env var, falling back to `"your-admin-secret-key"` from `.env.example`.
- No database is used; all config is in `config.json` or env vars.
- The `transformers` library warns about missing PyTorch/TensorFlow; this is benign since only the tokenizer is used.
- WebUI production build outputs to `static/admin/` and is served by the backend; during dev, Vite runs separately on port 5173.
