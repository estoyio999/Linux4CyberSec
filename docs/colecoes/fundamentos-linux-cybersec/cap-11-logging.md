# Capítulo 11 — Sistema de Logs

## Objetivos do capítulo
- Entender o pipeline de logging no Linux (rsyslog, journald, arquivos em `/var/log`).
- Configurar regras personalizadas, roteamento e filtragem.
- Automatizar rotação de logs com `logrotate`.
- Aplicar técnicas para ocultar rastros (com foco em entender e defender).

## rsyslog em ação
- Processo: `rsyslogd` recebe mensagens via `/dev/log`, UDP/TCP e módulos.
- Arquivo de configuração principal: `/etc/rsyslog.conf`.
- Diretório adicional: `/etc/rsyslog.d/*.conf` para regras customizadas.

### Sintaxe básica
```
<facility>.<priority>  destino
```
Exemplo:
```
authpriv.*,daemon.crit    /var/log/seguranca.log
```
Destinos possíveis: arquivos, terminais, usuários, servidores remotos (`@ip:514`).

### Envio remoto
```bash
# Em /etc/rsyslog.conf
action(type="omfwd" target="192.168.56.5" port="514" protocol="tcp" template="RSYSLOG_SyslogProtocol23Format")
```
Permite centralizar logs para análise forense.

### logrotate
Arquivo principal: `/etc/logrotate.conf`; configurações adicionais em `/etc/logrotate.d/`.

Exemplo para logs personalizados:
```
/var/log/seguranca.log {
  weekly
  rotate 8
  compress
  missingok
  notifempty
  postrotate
    invoke-rc.d rsyslog rotate > /dev/null 2>&1 || true
  endscript
}
```

## Técnicas de evasão e defesa
- **Modificação de logs**: hackers tentam editar ou remover entradas (`echo > /var/log/auth.log`). Defesas incluem `chattr +a`, envio remoto e validação com hashes.
- **Desativação do serviço**: `systemctl stop rsyslog`. Reforce com monitoramento (`systemctl is-active`).
- **Uso de `journalctl`**: explorar journald (`journalctl -xe`, `journalctl -f`) e configurar persistência em `/var/log/journal`.

## Exercícios propostos
1. Criar regra que envia alertas de tentativas SSH mal sucedidas para servidor remoto e envia e-mail.
2. Configurar `logrotate` personalizado para capturas `tcpdump` geradas automaticamente.
3. Simular invasor removendo logs, investigar e restaurar a partir de centralização.

## Complementos no repositório
- Seções de logging em `docs/05-redes-seguranca-e-monitoracao.md` e `docs/06-hardening-e-seguranca-defensiva.md`.
- Templates de relatório em `templates/` incluem espaços para anexar extratos de logs.
