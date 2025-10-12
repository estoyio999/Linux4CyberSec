# 02. Shell e Automatização

O shell é o centro nervoso das operações em Linux. Neste capítulo tratamos o Bash como uma linguagem completa, analisamos o processo de parsing, construímos bibliotecas reutilizáveis e integramos automações com APIs, mensageria e controles de erro robustos. Ao final, você terá fundamentos para montar pipelines de automação industriais.

## 2.1 Anatomia do Bash
Quando digitamos um comando, o Bash passa por etapas: tokenização, expansão, substituição e execução.
- **Tokenização**: separa a linha nos elementos básicos considerando aspas simples/dobras, barras invertidas e operadores.
- **Expansões**: ocorrem na ordem: brace, til (`~`), parâmetros/variáveis, substituição de comandos (`$(...)`), globbing e word splitting. Saber a ordem evita acidentes. Exemplo:
  ```bash
  FILES=(/etc/ssh/{sshd_config,ssh_config})
  printf '%s\n' "${FILES[@]}"
  ```
- **Precedência de operadores**: `()` subshell, `{}` agrupamento, `&&`/`||` controle condicional, `|` pipeline, `;` separador.
- **Inteiros e aritmética**: `(( expr ))` executa operações C-like, enquanto `let` é legado.

## 2.2 Boas práticas estruturais
### 2.2.1 Layout de scripts profissionais
Crie um template `scripts/_template.sh` contendo seções padrão:
```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly LOG_DIR="${SCRIPT_DIR%/scripts}/logs"
mkdir -p "$LOG_DIR"

log() { printf '[%(%F %T)T] [%s] %s\n' -1 "$1" "$2" | tee -a "$LOG_DIR/${0##*/}.log"; }
fail() { log ERR "$1"; exit 1; }
usage() { cat <<USAGE
Uso: ${0##*/} -t target [-p porta] [--json]
USAGE
exit 2; }

trap 'fail "Execução interrompida"' INT TERM

# parsing com getopt longo
OPTS=$(getopt -o t:p: --long target:,port:,json -- "$@") || usage
```
Explore `getopt` em vez de `getopts` quando precisar de parâmetros longos (`--target`).

### 2.2.2 Modularização
Organize funções utilitárias em um diretório `lib/`:
```bash
# lib/colors.sh
info() { printf '\e[1;34m[INFO]\e[0m %s\n' "$*"; }
warn() { printf '\e[1;33m[WARN]\e[0m %s\n' "$*"; }
```
Importe com `source "${SCRIPT_DIR}/../lib/colors.sh"`. Isso facilita a criação de padrões internos.

### 2.2.3 Idempotência e locking
Scripts que alteram estado precisam ser idempotentes e evitar execução simultânea:
```bash
LOCKFILE="/tmp/${0##*/}.lock"
exec 9>"$LOCKFILE"
if ! flock -n 9; then
  fail "Outra instância em execução"
fi
```
Combine com verificações antes/depois (`hash antigo vs. novo`, `systemctl is-active`) para não aplicar a mesma mudança duas vezes.

## 2.3 Integração com APIs e mensageria
### 2.3.1 HTTP com curl e jq
Monte pipelines robustos com `curl` + `jq`:
```bash
API_KEY=${SHODAN_KEY:?"Defina SHODAN_KEY"}
response=$(curl -sf "https://api.shodan.io/shodan/host/1.1.1.1?key=${API_KEY}") || fail "Erro na API"
open_ports=$(jq '.ports' <<<"$response")
log INFO "Portas abertas: ${open_ports}"
```
- `-s` silencioso, `-f` falha se HTTP >= 400.
- `jq` parseia JSON; use filtros, `map`, `select` para análises complexas.

### 2.3.2 Notificações (Slack/Mattermost)
Integre com webhooks para alertar equipe:
```bash
send_slack() {
  local message=$1
  curl -s -X POST -H 'Content-type: application/json' \
    --data "{\"text\":\"${message//"/\\"}\"}" "$SLACK_WEBHOOK_URL" >/dev/null
}
```
Agregue contexto (host, resumo da varredura) para facilitar triagem.

### 2.3.3 Automação com Python e mensagem estruturada
Integre Bash e Python quando lógica ficar complexa:
```bash
report=$(python3 <<'PY'
import json
from pathlib import Path

data = json.loads(Path('scans/result.json').read_text())
criticos = [item for item in data if item['cvss'] >= 8]
print(json.dumps(criticos))
PY
)
```
Passe dados entre linguagens preservando formatos (JSON, CSV, YAML).

## 2.4 Observabilidade em automações
- Direcione logs para arquivos com rotação (`logrotate.d/scripts`). Registre parâmetros de entrada, duração e resultado.
- Use `set -x` condicional (`[[ ${DEBUG:-0} -eq 1 ]] && set -x`) para rastrear comandos quando depurar.
- Utilize `PS4='+ ${BASH_SOURCE}:${LINENO}:${FUNCNAME[0]} '` para melhorar rastreamento do modo trace.
- Acompanhe CPU/memória de scripts longos com `time` e `ps -o`. Para pipelines intensivos, considere `pv` (Pipe Viewer) para progresso.

## 2.5 Pipelines avançados
### 2.5.1 GNU Parallel e clusters
`parallel` distribui tarefas em múltiplos núcleos ou hosts:
```bash
parallel --sshloginfile servidores.txt --nonall 'sudo apt update && sudo apt -y upgrade'
```
Use `--jobs 0` para usar todos os núcleos, `--bar` para progresso, `--results` para capturar saídas.

### 2.5.2 Make, Taskfile e Ansible
- **Make**: defina dependências. Exemplo em `Makefile`:
  ```make
  scans/%.xml: targets/%.txt
  	nmap -iL $< -oX $@

  relatorio.pdf: relatorio.md
  	pandoc $< -o $@
  ```
- **Taskfile (go-task)**: YAML moderno para tarefas multiplataforma.
- **Ansible**: automatize redes completas. Mantenha inventories em `automation/inventory` e playbooks versionados.

### 2.5.3 Cron e systemd timers
Substitua cronjobs por timers para melhor controle:
```ini
# /etc/systemd/system/update-kali.service
[Service]
Type=oneshot
ExecStart=/usr/local/bin/update-kali.sh

# /etc/systemd/system/update-kali.timer
[Timer]
OnCalendar=Mon,Fri *-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```
Habilite com `systemctl enable --now update-kali.timer`.

## 2.6 Segurança em automação
- Evite armazenar segredos em scripts; use `pass`, `gopass` ou `vault`. Carregue com `source <(pass show ... )` e limpe variáveis (`unset`).
- Use `sudo -S` apenas quando absolutamente necessário. Prefira `sudo` configurado para comandos específicos.
- Construa ambiente controlado (virtualenv, pyenv) para dependências Python, registrando `requirements.txt`.

## 2.7 Estudo de caso: pipeline de varredura colaborativo
1. **Entrada**: lista de alvos atualizada por API (repositório interno).
2. **Processamento**: scripts Bash acionam `nmap`, `naabu`, `httpx`; resultados em JSON.
3. **Agregação**: Python + Pandas normalizam dados; `jq` extrai campos críticos.
4. **Notificação**: Slack e e-mail com resumo e link para relatório HTML.
5. **Armazenamento**: MongoDB/Elastic para histórico, acompanhado de dashboards Kibana.
6. **Auditoria**: logs versionados em Git (branch `scans/execucao-YYYYMMDD`).

## 2.8 Exercícios avançados
1. Implemente biblioteca em `lib/` com funções `retry`, `timeout`, `with_lock`. Use-a em um script que chama APIs externas sujeitas a falhas intermitentes.
2. Construa cronologia de varreduras: script que cria branch Git por execução, comita resultados e abre pull request automático (via CLI do GitHub/GitLab).
3. Integre `ansible` e `bash`: playbook que distribui script de coleta de logs, executa e retorna arquivos cifrados. Documente chaves usadas e armazenadas em `pass`.
4. Desenvolva pipeline que envia alertas para Mattermost com gráfico ASCII (usando `spark`/`gnuplot`) mostrando evolução de vulnerabilidades críticas.

> **Referências:** *Bash Hackers Wiki*, `Advanced Bash-Scripting Guide`, `Modern Bash` (Chris Johnson), documentação GNU Parallel.
