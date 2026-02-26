# AGENTS.md

## Cursor Cloud specific instructions

### Overview

DS2API is a single Python (FastAPI) application with an embedded React admin WebUI (`webui/`). It converts DeepSeek Web into OpenAI/Claude compatible API endpoints. No external services (databases, Redis, etc.) are required.

### Running the application

- **Backend only**: `python3 -m uvicorn app:app --host 0.0.0.0 --port 5001 --reload`
- **Backend + Frontend dev**: `python dev.py` (starts uvicorn on :5001 and Vite on :5173)
- **Frontend only**: `cd webui && npm run dev`
- A `config.json` must exist in the project root. Copy from `config.example.json` if missing. For dev without real DeepSeek accounts, use: `{"keys": ["test-api-key-001"], "accounts": []}`
- The `DS2API_ADMIN_KEY` env var (or the value in `.env`) controls admin WebUI login. Default in `.env.example` is `your-admin-secret-key`.

### Testing

- **Unit tests** (no network required): `python3 tests/test_unit.py` (32 tests)
- **API integration tests** (requires running server): `python3 tests/test_all.py --quick`
- **Test runner script**: `./tests/run_tests.sh unit` or `./tests/run_tests.sh all`
- See `tests/README.md` for full details.

### Building the WebUI

- `cd webui && npm run build` outputs to `static/admin/`. This is also done by `./scripts/build-webui.sh`.
- In CI, GitHub Actions auto-builds on merge to `main`; manual builds are only needed for local testing.

### Gotchas

- Python packages install to `~/.local/bin` (user install). Ensure `PATH` includes `$HOME/.local/bin`.
- No dedicated Python or JS linter is configured. The project follows PEP 8 (Python) and 4-space indentation (JS/React) per `CONTRIBUTING.en.md`.
- The `transformers` library logs a warning about missing PyTorch/TensorFlow; this is expected and harmless -- only the tokenizer is used.
- API calls to `/v1/chat/completions` will return `"No accounts configured"` unless real DeepSeek accounts are added to `config.json`.
