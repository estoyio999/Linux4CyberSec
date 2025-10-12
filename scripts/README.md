# Scripts

Armazene automatizações reutilizáveis. Seguem convenções:
- Cabeçalho com descrição, autor, data e dependências.
- Uso de `set -euo pipefail` (Bash) ou virtualenv (Python).
- Logs direcionados para `../logs/` com timestamp.

Execute `shellcheck` e `black`/`ruff` (para Python) antes de versionar.
