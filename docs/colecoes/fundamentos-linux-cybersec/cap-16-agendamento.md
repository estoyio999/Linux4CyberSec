# Capítulo 16 — Automação com Agendamento de Tarefas

## Objetivos do capítulo
- Utilizar `cron` e `at` para agendar comandos e scripts.
- Configurar timers no `systemd` como alternativa moderna.
- Criar rotinas automatizadas para coleta de dados, backups e manutenção.

## Cron
- Arquivo de usuário: `crontab -e`.
- Sintaxe: `min hora dia_mês mês dia_semana comando`.
- Exemplo (executa script a cada 30 minutos):
```
*/30 * * * * /usr/local/bin/coleta-logs.sh >> /var/log/coleta.log 2>&1
```

Arquivos em `/etc/cron.{hourly,daily,weekly,monthly}` executam scripts conforme o intervalo. Lembre de adicionar permissões de execução.

### Boas práticas
- Usar caminhos absolutos.
- Redirecionar saída/erros para arquivo.
- Monitorar `/var/log/syslog` ou `journalctl -u cron`. No Debian/Kali, serviço é `cron.service`.

## at
- Execução única no futuro: `echo "nmap -sV alvo" | at 23:00`.
- `atq` lista tarefas pendentes; `atrm <job>` remove.

## systemd timers
Criar arquivo `minha-tarefa.service`:
```
[Unit]
Description=Coleta de indicadores

[Service]
Type=oneshot
ExecStart=/usr/local/bin/coleta-indicadores.sh
```
Timer correspondente `minha-tarefa.timer`:
```
[Unit]
Description=Executa coleta a cada 15 minutos

[Timer]
OnCalendar=*:0/15
Persistent=true

[Install]
WantedBy=timers.target
```
Ativar:
```bash
sudo systemctl enable --now minha-tarefa.timer
sudo systemctl list-timers
```

## Segurança
- **Ofensivo**: cron jobs podem ser usados para persistência (ex.: `*/5 * * * * /tmp/backdoor`).
- **Defensivo**: auditar `crontab -l` de todos os usuários, monitorar `/etc/crontab` e timers. Use `sudo` restrito para impedir modificações não autorizadas.

## Exercícios propostos
1. Implementar cron job que sincroniza logs com servidor remoto via `rsync` diariamente.
2. Criar timer `systemd` que roda script de detecção de mudanças em diretórios críticos.
3. Simular persistência maliciosa com cron e desenvolver script para removê-la automaticamente.

## Referências
- `man 5 crontab`, `man systemd.timer`.
- Capítulos de hardening em `docs/06-hardening-e-seguranca-defensiva.md` listam controles para agendamentos.
