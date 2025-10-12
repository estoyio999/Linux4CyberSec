# 03. Gerenciamento de Pacotes e Serviços

Controlar software e serviços é crucial para manter ambientes estáveis, auditáveis e seguros. Neste capítulo vamos além do uso básico do APT: exploramos preferências, hooks, criação de pacotes `.deb`, monitoramento de serviços com systemd e estratégias de rollback. O objetivo é permitir que você gerencie um parque de ferramentas complexo sem surpresas.

## 3.1 Anatomia do APT
O APT é composto por vários componentes:
- **`/etc/apt/sources.list` e `/etc/apt/sources.list.d/*.list`**: definem repositórios. Prefira arquivos separados por fornecedor (`offsec.list`, `community.list`).
- **`/etc/apt/preferences` ou `preferences.d/`**: definem prioridades (pinning). Use para travar versões críticas.
- **Hooks** (`/etc/apt/apt.conf.d/`): scripts que executam antes/depois de operações. Por exemplo, `99notify` para enviar alerta após upgrade.

### 3.1.1 Pinning avançado
Exemplo completo que trava versão do `metasploit` e permite testes com `kali-experimental` apenas sob demanda:
```
Package: metasploit-framework
Pin: version 6.3.*
Pin-Priority: 700

Package: *
Pin: release a=kali-experimental
Pin-Priority: 100
```
- Prioridade > 1000 força a versão mesmo se outra mais nova existir.
- Prioridade entre 100 e 500 prefere repositório, mas não impede upgrades quando a versão é maior.

### 3.1.2 Hooks e automações pós-upgrade
Crie arquivo `/etc/apt/apt.conf.d/90kali-audit`:
```conf
DPkg::Post-Invoke { "bash /usr/local/bin/apt-audit.sh"; };
```
O script pode gerar lista de pacotes atualizados e enviar para Slack.

### 3.1.3 Cache e limpeza
- `apt-cache policy <pacote>` mostra prioridade e origem.
- `apt list --upgradable` lista updates pendentes.
- Limpe caches com `apt clean` e `apt autoremove`. Em ambientes com pouca banda, use `apt-cacher-ng` para caching central.

## 3.2 Empacotamento `.deb` artesanal
Quando mantemos scripts internos, empacotá-los facilita distribuição.
1. Estruture diretório:
   ```bash
   mkdir -p pacote/usr/local/bin pacote/DEBIAN
   cp scripts/meu-script.sh pacote/usr/local/bin/meu-script
   chmod 755 pacote/usr/local/bin/meu-script
   ```
2. Crie `DEBIAN/control`:
   ```
   Package: meu-script
   Version: 1.0.0
   Section: utils
   Priority: optional
   Architecture: all
   Maintainer: Equipe Sec <sec@example.com>
   Depends: bash, curl
   Description: Script interno para coleta de evidências.
   ```
3. Opcional: `postinst`, `prerm`, `postrm` em `DEBIAN/` para ações complementares.
4. Construa o pacote: `dpkg-deb --build pacote`.
5. Distribua via repositório local (`reprepro`, `aptly`) e assine com GPG.

Documente pacotes internos em `docs/11-anexos-e-recursos.md` para rastreabilidade.

## 3.3 Observabilidade de instalação
- `apt history log` (`/var/log/apt/history.log`) detalha datas e pacotes instalados/removidos.
- `journalctl -u apt-daily.service` mostra execuções automáticas.
- Em ambientes compliance, exporte logs para SIEM (`/etc/rsyslog.d/apt.conf`).

## 3.4 systemd em profundidade
### 3.4.1 Unidades, targets e overrides
- Unidades residem em `/lib/systemd/system/` (distribuição) e `/etc/systemd/system/` (personalizadas). Use `systemctl edit <serviço>` para criar overrides sem tocar no arquivo original.
- Targets substituem runlevels (`multi-user.target`, `graphical.target`). Configure serviços para iniciarem em targets específicos.
- `systemctl list-dependencies --reverse` identifica o que depende de determinado serviço.

### 3.4.2 Diagnóstico
- `systemctl status` e `journalctl -u` são básicos.
- `systemd-analyze blame` mostra tempo de inicialização por serviço.
- `systemd-analyze critical-chain` identifica gargalos.
- `coredumpctl list` + `coredumpctl gdb` analisa processos que falharam.

### 3.4.3 Serviços do usuário
Systemd também gerencia serviços do usuário (`systemctl --user`). Útil para rodar agentes sem root.
- Crie `~/.config/systemd/user/notes-sync.service` para sincronizar notas via `rclone`.
- habilite com `systemctl --user enable --now notes-sync.service`.

### 3.4.4 Sandboxing de serviços
Restrinja serviços para reduzir superfície de ataque:
```ini
[Service]
PrivateTmp=yes
ProtectSystem=full
ProtectHome=yes
NoNewPrivileges=yes
CapabilityBoundingSet=
```
Combine com `DynamicUser=yes` para criar usuário efêmero.

## 3.5 Rollback e snapshots
- **APT**: use `apt-mark hold` para congelar pacote. Em caso de regressão, `apt-get install pacote=versão`. Mantenha snapshots do repo (`aptly snapshot create`).
- **Btrfs/ZFS**: snapshots instantâneos. Exemplo Btrfs:
  ```bash
  sudo btrfs subvolume snapshot -r /@ /@_pre-update
  ```
- **Timeshift**: interface gráfica para snapshots; configure em `cron` ou `systemd`.
- **VM**: snapshots antes de grandes mudanças. Documente em `labs/notas/<lab>.md`.

## 3.6 Ambientes multi-nível
### 3.6.1 Chroot e containers para staging
Antes de instalar pacote experimental, crie chroot:
```bash
sudo debootstrap kali-rolling /srv/chroots/kali http://http.kali.org/kali
sudo chroot /srv/chroots/kali /bin/bash
```
Testes ocorrem em isolamento, sem afetar host principal.

### 3.6.2 Podman/Docker com systemd
Execute serviços containerizados com systemd:
```ini
# /etc/systemd/system/portia.service
[Service]
ExecStart=/usr/bin/podman run --rm --name portia -p 8000:8000 portia:latest
ExecStop=/usr/bin/podman stop portia
Restart=always
```
`systemctl enable portia` integra contêiner ao fluxo de boot.

## 3.7 Estudo de caso: mantendo repositórios internos
1. **Repositório local**: use `reprepro` para hospedar pacotes criados pela equipe. Gere GPG próprio e distribua chave pública.
2. **CI/CD**: pipeline de build compila pacotes, roda testes, atualiza snapshot do repo e publica sumário.
3. **Fleet management**: `ansible` ou `saltstack` aplicam updates de forma controlada, consultando repositório interno.
4. **Auditoria**: `osquery` coleta versão dos pacotes instalados em cada host, comparando com baseline.

## 3.8 Exercícios avançados
1. Crie repositório local com `aptly`, publique na rede interna e configure o Kali para consumir pacotes dele. Documente upload, assinatura e sincronização.
2. Escreva unidade systemd para serviço customizado com sandbox (ProtectSystem, PrivateDevices, NoNewPrivileges). Gere override que altera apenas diretórios de logs sem modificar arquivo original.
3. Simule falha de update: atualize pacote essencial, identifique regressão e execute rollback usando snapshot Btrfs e reinstalação com versão anterior.
4. Automatize relatório semanal de pacotes instalados via hook APT, enviando resumo em JSON para API interna.

> **Referências:** `man sources.list`, Debian Policy (Cap. 4-5), *The Debian Administrator's Handbook*, documentação systemd (systemd.service, systemd.unit), blog do Lennart Poettering.
