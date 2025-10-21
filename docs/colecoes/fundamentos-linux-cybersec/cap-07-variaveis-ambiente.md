# Capítulo 7 — Variáveis de Ambiente e Shell

## Objetivos do capítulo
- Compreender variáveis globais e locais (`env`, `set`).
- Customizar `PATH`, aliases e funções no `~/.bashrc` e `/etc/profile`.
- Utilizar variáveis para automatizar scripts e tornar ferramentas mais portáveis.

## Conceitos-chave
- **Variáveis de ambiente**: disponíveis para processos filhos (`export VAR=valor`).
- **Variáveis locais**: visíveis somente no shell atual (`VAR=valor`).
- **Shell parameters**: `$0`, `$1`, `$?`, `$#` utilizados em scripts.

### Inspeção e modificação
```bash
env | sort          # lista variáveis exportadas
printenv PATH
export PATH="$PATH:/opt/tools/bin"
unset HISTFILE      # desabilita histórico temporariamente
```

### Configuração persistente
- `~/.bashrc`: inicialização para shells interativos; definir aliases, funções e variáveis de usuário.
- `~/.profile` ou `~/.bash_profile`: lido em login shells; ideal para ajustes de PATH.
- `/etc/profile`, `/etc/bash.bashrc`: configurações globais (use com cautela e documente alterações).

Exemplo de alias e função inspirados no capítulo:
```bash
alias ll='ls -halF'
mkcd() {
  mkdir -p "$1" && cd "$1"
}
```

### Variáveis úteis para operações de segurança
- `HISTCONTROL=ignoreboth`: evita duplicatas e comandos iniciados com espaço no histórico.
- `HISTFILESIZE` e `HISTSIZE`: controlar tamanho do histórico.
- `PROMPT_COMMAND='history -a'`: registra comandos imediatamente.
- `LC_ALL=C`: força localidade determinística, útil para scripts.

## Segurança ofensiva e defensiva
- **Ofensivo**: manipular `PATH` para executar binários maliciosos quando um administrador chama comandos genéricos (`ls`, `cat`).
- **Defensivo**: fixar PATH explícito em scripts com privilégios e limpar variáveis não confiáveis (`sudo` chama `env_reset`).

## Exercícios propostos
1. Criar perfil customizado que destaca usuário/host e diretório atual no prompt (`PS1`).
2. Desenvolver script que utiliza variáveis de ambiente para guardar credenciais temporárias e remove automaticamente após uso.
3. Configurar `dircolors` para facilitar identificação de tipos de arquivo durante análises forenses.

## Relação com capítulos posteriores
Variáveis são fundamentais para Bash scripting (capítulo 8) e automação com cron (capítulo 16). Centralize suas customizações em arquivos versionados (`templates/bashrc`).
