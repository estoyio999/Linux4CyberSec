# Capítulo 8 — Bash Scripting

## Objetivos do capítulo
- Escrever scripts Bash reutilizáveis com variáveis, condicionais e loops.
- Criar ferramentas simples (scanner de portas, automação de logs).
- Tratar entrada do usuário e validar parâmetros.

## Estrutura básica de um script
```bash
#!/bin/bash

set -euo pipefail

if [[ $# -lt 1 ]]; then
  echo "Uso: $0 <alvo>"
  exit 1
fi

for porta in {1..1024}; do
  timeout 1 bash -c "</dev/tcp/$1/$porta" && echo "Porta $porta aberta"
done
```
Explore variantes deste scanner usando `nc` ou `nmap` como dependências opcionais para ganhar velocidade e recursos extras.

### Boas práticas destacadas
- Usar `#!/bin/bash` no topo (shebang).
- Tornar scripts executáveis com `chmod +x script.sh`.
- Salvar logs com `tee` ou redirecionamentos para auditoria.
- Documentar variáveis no começo do arquivo.

### Ferramentas de apoio
- `getopts` para tratar flags (`-h`, `-v`).
- `trap` para capturar sinais (`Ctrl+C`) e executar limpeza.
- Funções reutilizáveis para organizar código.

### Exemplo: Coletor de logs com rotação manual
```bash
#!/bin/bash
# coleta-logs.sh
fonte="/var/log/auth.log"
destino="/var/log/auditoria"
mkdir -p "$destino"

arquivo="$destino/auth-$(date +%F-%H%M%S).log"
sudo tail -n 500 "$fonte" > "$arquivo"
```
Combine com `cron` (capítulo 16) para automatizar execuções.

## Segurança ofensiva e defensiva
- **Ofensivo**: scripts para bruteforce, coleta de informações e pivoting.
- **Defensivo**: automatizar atualizações, backups de configurações e verificações de integridade.

## Exercícios propostos
1. Converter o scanner simples em uma versão multi-thread usando `xargs -P`.
2. Criar script que testa conectividade com hosts listados em arquivo e gera relatório HTML.
3. Implementar menu interativo (usando `select` ou `case`) que agrupa comandos administrativos frequentes.

## Recursos adicionais
- `shellcheck` para análise estática de scripts.
- `man bash`, `help` interno.
- Conteúdo complementar em `docs/02-shell-e-automatizacao.md`.
