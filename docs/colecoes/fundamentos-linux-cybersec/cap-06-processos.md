# Capítulo 6 — Gerenciamento de Processos

## Objetivos do capítulo
- Identificar processos em execução e compreender estados (`S`, `R`, `Z`).
- Alterar prioridade (`nice`, `renice`) e enviar sinais (`kill`).
- Monitorar consumo em tempo real com `top`, `htop`, `atop`.

## Inspeção de processos
| Comando | Descrição |
| --- | --- |
| `ps aux` | Lista todos os processos com usuário, PID, %CPU/%MEM. |
| `ps -ef` | Formato estilo System V. |
| `pgrep nome` | Retorna PIDs de processos por nome. |
| `pstree -p` | Exibe hierarquia de processos. |

Filtrar com `grep` é prática recorrente:
```bash
ps aux | grep sshd | grep -v grep
```

## Prioridades e sinais
- Cada processo possui um valor `nice` (-20 a +19). Menor valor = maior prioridade.
- `nice -n -5 comando` executa com prioridade maior.
- `renice 10 -p 1234` altera prioridade de processo já em execução.

Sinais principais:
- `SIGTERM (15)` — encerra graciosamente.
- `SIGKILL (9)` — encerra imediatamente.
- `SIGSTOP`, `SIGCONT` — pausa e retoma processos.

```bash
kill -SIGTERM <PID>
kill -9 <PID>
```

## Monitoração
- **top**: interface padrão (`shift + p` ordena por CPU, `shift + m` por memória). Use `k` para matar processo.
- **htop**: interface colorida com filtros; permite enviar sinais via F9.
- **atop**: grava histórico de uso de recursos, útil em forense.
- **systemd-cgtop**: observa consumo por cgroups.

## Processos em segundo plano
- `comando &` executa em background.
- `jobs` lista tarefas suspensas.
- `fg %1`, `bg %1` traz processos para frente ou fundo.

## Segurança ofensiva e defensiva
- **Ofensivo**: ocultar processos maliciosos renomeando binários ou injetando em serviços confiáveis; monitorar `ps` para descobrir defesas ativas.
- **Defensivo**: usar `auditd` para registrar execuções críticas, habilitar `systemd` unit overrides para respawn controlado e gerar alertas quando processos inesperados surgirem.

## Exercícios propostos
1. Criar script que monitora processos cruciais e reinicia automaticamente se caírem.
2. Simular ataque com `reverse shell`, identificá-lo em `top` e eliminá-lo.
3. Configurar `systemd` service `Restart=always` para laboratório e observar comportamento após `kill -9`.

## Leituras complementares
Compare com `docs/06-hardening-e-seguranca-defensiva.md` (seção de controle de processos) e utilize `logs/processos/` para guardar saídas de monitoramento.
