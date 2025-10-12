# 01. Fundamentos do Sistema

O objetivo deste capítulo é estabelecer a base conceitual e prática para todo o restante do guia. Partimos da história do Kali Linux e sua relação com outras distribuições focadas em segurança, avançamos pelo processo de boot, detalhamos a hierarquia de diretórios e fechamos com governança de usuários e permissões. A ideia é que você entenda cada decisão de arquitetura e consiga explicar, justificar e automatizar a configuração de qualquer laboratório profissional.

## 1.1 Panorama das distribuições de segurança
O Kali Linux sucedeu o BackTrack em 2013, mantendo a missão de oferecer uma distribuição pronta para testes de intrusão. Ele é construído sobre Debian Testing, atualizando-se de forma contínua (rolling release). Compare com outras distros:
- **Parrot Security** – base Debian como o Kali, porém com foco adicional em privacidade, trazendo AppArmor e utilitários de anonimização habilitados por padrão.
- **BlackArch** – derivada do Arch Linux, ideal para usuários que desejam bleeding edge e controle total sobre pacotes, exigindo familiaridade maior com compilação.
- **Fedora Security Spin** – opção orientada a SELinux, boa para quem quer estudar o stack de segurança da Red Hat.

Ao escolher uma distro, leve em conta: disponibilidade de pacotes, ciclo de atualização, política de segurança (AppArmor, SELinux), suporte a hardware e comunidade. Este guia assume Kali, mas a maioria dos conceitos se aplica a qualquer Linux moderno.

## 1.2 Arquitetura de boot (UEFI, GRUB e initramfs)
Compreender o processo de boot auxilia na criação de ambientes forenses, hardening e testes de persistência.
1. **Firmware (BIOS ou UEFI)**: inicializa hardware e procura por um bootloader. Em ambientes UEFI, valide se o `Secure Boot` está desativado (o Kali não o suporta oficialmente) ou assine seus kernels manualmente.
2. **GRUB2**: localiza o kernel (`/boot/vmlinuz-*`) e o ramdisk (`/boot/initrd.img-*`). O arquivo `/etc/default/grub` controla opções como `GRUB_TIMEOUT`, resoluções (`GRUB_GFXMODE`) e parâmetros do kernel (`GRUB_CMDLINE_LINUX`). Após alterações, execute `sudo update-grub`.
3. **initramfs**: imagem temporária com drivers e scripts para montar o root filesystem. Em ambientes criptografados, ela contém scripts de desbloqueio LUKS.
4. **systemd**: assume o controle como PID 1, inicializando unidades conforme dependências.

Ataques comuns visam manipular GRUB (acrescentando `init=/bin/bash`) ou substituir initramfs para obter shells privilegiados. Proteja com senha no GRUB (`grub-mkpasswd-pbkdf2`) e bloqueie mídia removível no firmware quando necessário.

## 1.3 Hierarquia do Filesystem Hierarchy Standard (FHS)
O FHS organiza arquivos em hierarquia. Profissionais de segurança precisam saber onde procurar evidências ou esconderijos comuns usados por atacantes.
- `/bin`, `/sbin`, `/usr/bin`, `/usr/sbin`: binários essenciais e opcionais. Valide integridade com `debsums` ou `aide`.
- `/lib`, `/lib64`: bibliotecas compartilhadas. Rootkits costumam substituir `.so` para persistir.
- `/etc`: configurações estáticas. Monitore arquivos como `passwd`, `shadow`, `sudoers`, `ssh/sshd_config`. Use `etckeeper` para versionamento em Git.
- `/var`: dados variáveis. `/var/log` contém logs; `/var/spool/cron` guarda cronjobs; `/var/lib` mantém estados de serviços (ex.: `postgresql`, `neo4j`).
- `/opt`: aplicações de terceiros. Documente tudo em `docs/11-anexos-e-recursos.md`.
- `/srv`: arquivos servidos por serviços (web, ftp). Útil para isolar payloads.
- `/home`: diretórios de usuários. Avalie `dotfiles` (ex.: `.bashrc`, `.ssh/authorized_keys`) para identificar persistências.

### 1.3.1 Atributos estendidos e metadados
Além de `rwx`, arquivos possuem atributos e extended attributes.
- `lsattr` revela atributos como `i` (imutável) e `a` (append-only). Configure em logs críticos para impedir alterações:
  ```bash
  sudo chattr +a /var/log/auth.log
  ```
- Extended attributes (xattrs) suportam SELinux contexts, capabilities (`security.capability`) ou dados usados por malwares.
  ```bash
  getfattr -d /bin/ping
  ```

## 1.4 Shell essentials revisitado
Além dos comandos básicos, aprofunde conceitos:
- **Expansion**: o shell expande variáveis (`$VAR`), wildcards (`*`), brace expansion (`{a,b}`), command substitution (`$(...)`). Entender ordem evita resultados inesperados.
- **Globbing avançado**: habilite `shopt -s globstar` para usar `**` (match recursivo). Útil para buscar arquivos em árvores grandes.
- **Process substitution**: `diff <(ls /opt) <(ls /srv)` compara saídas sem criar arquivos temporários.
- **Job control**: `Ctrl+Z`, `bg`, `fg`, `jobs` — imprescindível ao manejar múltiplas tarefas.

## 1.5 Usuários, grupos e políticas de sudo
### 1.5.1 Contas e perfis
- Use `adduser` (interface amigável) ou `useradd` (baixo nível). Para contas temporárias, combine com `chage -E <data>` para expirar automaticamente.
- Registre contas de serviço (sem shell) com `useradd --system --shell /usr/sbin/nologin serviço`.
- Revise `/etc/login.defs` para políticas globais (comprimento mínimo, expiração).

### 1.5.2 sudo e auditoria
O `sudo` permite delegar privilégios. Crie arquivos em `/etc/sudoers.d/` com regras específicas.
```bash
analista ALL=(root) NOPASSWD: /usr/bin/nmap, /usr/bin/wireshark
```
A regra acima autoriza o usuário `analista` a executar apenas esses dois comandos como root sem senha. Combine com `Defaults logfile="/var/log/sudo-commands.log"` para auditoria.

### 1.5.3 Logs de autenticação
- Debian/Kali registra autenticações em `/var/log/auth.log`. Para acompanhar tentativas em tempo real:
  ```bash
  sudo tail -f /var/log/auth.log | ccze -A
  ```
- Utilize `journalctl -u ssh` para filtrar eventos de SSH.
- Para relatórios periódicos, configure `goaccess` ou scripts Python que extraiam IPs e contadores.

## 1.6 Estudo de caso: preparando laboratório corporativo
1. **Provisionamento**: Crie imagem base do Kali com hardening inicial (firewall, desativação de serviços). Congele como snapshot "golden".
2. **Controle de contas**: Crie grupos `redteam`, `blueteam`, `forense` e use ACLs para separar dados (`/opt/redteam`, `/opt/blueteam`).
3. **Monitoramento**: Habilite `auditd` para rastrear alterações em `/etc`, configure syslog remoto em `rsyslog` (`/etc/rsyslog.d/`).
4. **Documentação**: Armazene relatórios diários em `logs/` e mantenha `CHANGELOG.md` descrevendo mudanças estruturais.

## 1.7 Exercícios aprofundados
1. Documente o processo de boot do seu Kali: habilite logging detalhado em `GRUB_CMDLINE_LINUX="initcall_debug"`, reinicie e analise `dmesg`. Identifique drivers carregados e veja como eles poderiam ser abusados.
2. Configure `etckeeper` (`sudo apt install etckeeper git`), comite o estado inicial de `/etc`, modifique `sshd_config` e registre a mudança. Restabeleça a configuração com `git checkout` e documente no laboratório.
3. Crie usuários: `analista`, `atacante`, `forense`. Configure sudo granular, aplique `faillock` para bloquear múltiplas falhas (`pam_faillock.so`) e produza relatório em Markdown com os comandos e arquivos alterados.
4. Utilize `find / -nouser -o -nogroup` para localizar arquivos sem proprietário ou grupo — comum em sistemas comprometidos — e normalize permissões registrando antes/depois.

> **Referências recomendadas:** *Kali Linux Revealed* (cap. 3-5), `man hier`, Debian Policy Manual (seções de packaging) e documentação do `systemd`.
