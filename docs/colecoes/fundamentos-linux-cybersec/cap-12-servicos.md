# Capítulo 12 — Utilizando e Abusando Serviços

## Objetivos do capítulo
- Controlar serviços com `systemctl` e scripts legados (`service`).
- Configurar rapidamente um servidor Apache e publicar conteúdo.
- Usar OpenSSH para tunelar, automatizar e construir laboratórios com dispositivos embarcados (como um "Raspberry Spy Pi").
- Interagir com bancos de dados MySQL/PostgreSQL em testes de intrusão.

## Gerenciamento de serviços
```bash
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```
`journalctl -u apache2` complementa a análise com logs.

### Servidor web mínimo
1. Instalar `apache2` (se necessário).
2. Editar `/var/www/html/index.html` com conteúdo personalizado.
3. Acessar `http://localhost` para ver a página.
4. Revisar permissões e ativar módulos (`sudo a2enmod rewrite`).

### OpenSSH e Raspberry Spy Pi
- Preparar Raspberry Pi com Raspbian/Kali ARM, habilitar SSH, conectar câmera.
- Criar script que captura imagens ou vídeo quando movimento é detectado, envia via SSH/SCP para estação base.
- Usar `ssh -L` e `ssh -R` para criar túneis seguros.

### Interagindo com MySQL
```bash
sudo systemctl start mysql
sudo mysql_secure_installation          # define senha root e opções de segurança
mysql -u root -p
SHOW DATABASES;
USE database;
SELECT * FROM tabela LIMIT 10;
```
Explore como extrair dados de visitação com `SELECT`, criar usuários e testar bancos desprotegidos com segurança em ambientes controlados.

### PostgreSQL + Metasploit
- Iniciar serviço: `sudo systemctl start postgresql`.
- Configurar usuário e banco (`sudo -u postgres createuser msf -P`).
- Integrar com `msfconsole` (`db_connect` / `db_status`).

## Segurança ofensiva e defensiva
- **Ofensivo**: explorar serviços mal configurados, criar backdoors via `systemd` timer ou `cron`, pivotar usando `ssh -D` (proxy SOCKS).
- **Defensivo**: limitar exposição, aplicar hardening (chroot, TLS), monitorar credenciais padrão.

## Exercícios propostos
1. Transformar Apache em honeypot básico registrando tudo que chega no `access.log` e `error.log`.
2. Automatizar envio de capturas da câmera do Raspberry Pi para `logs/imagens/` com timestamp.
3. Criar usuário de banco de dados com privilégios mínimos e testar injeções controladas em laboratório.

## Referências
- `docs/07-operacoes-ofensivas-e-pentest.md` aprofunda ataques a serviços.
- `docs/06-hardening-e-seguranca-defensiva.md` traz checklists de proteção.
