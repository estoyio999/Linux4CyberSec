# 06. Hardening e Segurança Defensiva

Hardening não é apenas uma lista de comandos: envolve modelagem de ameaças, priorização e monitoramento contínuo. Este capítulo introduz metodologias avançadas, PAM, MAC (AppArmor/SELinux), integridade de módulos e hunting proativo com OSQuery e eBPF.

## 6.1 Modelagem de ameaça e priorização
- **Threat Modeling**: defina ativos, ameaças e controles mitigadores. Ferramentas: Microsoft Threat Modeling Tool, `trike`.
- **MITRE D3FEND** complementa ATT&CK com contramedidas. Use planilhas para mapear técnicas ofensivas (cap. 07) versus defensivas.
- Avalie risco com frameworks FAIR ou CVSS adaptado a contexto interno.

## 6.2 Pilares de hardening
1. **Superfície mínima**: remova pacotes desnecessários (`apt purge telnet`).
2. **Configuração segura**: alinhe com benchmarks CIS, ANSSI, NSA.
3. **Monitoramento**: `auditd`, `osquery`, `falco`.
4. **Resposta**: playbooks e runbooks (cap. 09).

## 6.3 PAM avançado
- Edite `/etc/pam.d/common-auth` para incluir `pam_faillock`:
  ```
  auth required pam_faillock.so preauth silent deny=5 unlock_time=900
  auth [success=1 default=bad] pam_unix.so
  auth [default=die] pam_faillock.so authfail
  ```
- `pam_google_authenticator` para 2FA. Configure via `google-authenticator` e adicione `auth required pam_google_authenticator.so nullok`.
- `pam_exec` permite executar scripts para autorizações customizadas (ex.: blocos para IPs não aprovados).
- `pam_python` habilita lógica em Python.

## 6.4 AppArmor e SELinux
### 6.4.1 AppArmor profiles
- Use `aa-genprof` e `aa-logprof` para gerar/ajustar perfis.
- Perfis podem operar em modos `complain`, `enforce`. Mantenha repositório `security/apparmor/` com versões versionadas.
- Para restringir `netcat`, exemplos de regras: `network inet stream, deny network raw`. Documente.

### 6.4.2 SELinux policies
- Habilite SELinux (cap. 01). Use `semanage permissive -a httpd_t` ao ajustar políticas.
- Escreva módulo `.te` e compile com `checkmodule` + `semodule_package`.
- Use `audit2allow` para sugerir regras com base em logs do auditd.

## 6.5 Integridade e IMA/EVM
- **Integrity Measurement Architecture (IMA)** mede hashes de arquivos e aplica políticas.
  - Configure `/etc/ima/ima-policy`: `measure func=BPRM_CHECK mask=MAY_EXEC uid=0`.
  - Ative via kernel cmdline: `ima_appraise=fix ima_template_fmt=ima-ng`.
- **EVM** protege extended attributes (ex.: xattrs de SELinux). Necessita HMAC ou chaves X509.

## 6.6 Módulos do kernel e assinatura
- Habilite assinatura obrigatória (`CONFIG_MODULE_SIG_FORCE`). Em Kali (Debian), configure `/etc/modprobe.d/blacklist.conf` para bloquear módulos inseguros.
- Utilize `modinfo` para inspecionar dependências e parâmetros.
- Implemente script que verifica módulos carregados (`lsmod`) contra whitelist.

## 6.7 Monitoramento proativo
### 6.7.1 OSQuery
- Query packs: `packs/osquery-attck.conf`, `packs/orbit`. Execute `osqueryctl` ou `osqueryd` em modo daemon.
- Integre com FleetDM ou Kolide para gestão central.
- Exemplos:
  ```sql
  SELECT * FROM listening_ports WHERE port IN (22, 5901, 3389);
  SELECT * FROM processes WHERE on_disk = 0;
  ```

### 6.7.2 eBPF e Falco
- `bcc` scripts (`opensnoop`, `execsnoop`, `tcpconnect`). Ajuste para monitorar sinais específicos (ex.: execuções de binários em `/tmp`).
- Falco: crie regras personalizadas detectando `curl`/`wget` baixando binários diretamente para `/tmp`.
- `tracee` (Aqua Security) oferece políticas prontas.

### 6.7.3 Threat hunting
- Combine `osquery`, `falco`, `auditd` e logs de aplicação.
- Utilize `Sigma` para converter detecções em regras para Splunk/Elastic.
- `MITRE CAR (Cyber Analytics Repository)` sugere analíticas.

## 6.8 Gestão de segredos
- `pass`, `gopass` integrados com Git. GPG com subkeys dedicadas.
- `sops` (Mozilla) para cifrar YAML/JSON (ex.: playbooks Ansible). Integre com KMS (AWS, GCP) ou PGP.
- `HashiCorp Vault`: configure `policies`, `leases`, `approle`. Use `vault agent` para sidecars.
- Resistência a exfiltração: habilite `noexec` em `/tmp`, configure `umask` segura (`umask 027`).

## 6.9 Hunting orientado a MITRE ATT&CK
- Crie matriz que relaciona técnicas de ATT&CK (Txxxx) com controles implementados (AppArmor, auditd, OSQuery queries).
- Priorize TTPs com maior probabilidade conforme seu contexto (ex.: T1059 – Command and Scripting).
- Configure detecções correspondentes em SIEM, testadas com `Atomic Red Team`.

## 6.10 Estudo de caso: endurecimento de host exposto
1. **Baseline**: `lynis`, `openSCAP`, `cis-cat`. Registre resultados.
2. **Aplicação**: AppArmor + Falco monitorando `modprobe`, `insmod` e execuções suspeitas.
3. **Resposta**: `Wazuh` envia alertas para TheHive; playbook `incidentes/playbooks/rootkit.md` descreve ações.
4. **Validação**: scripts `osquery` em cron descrevendo `processes` e `users`. Compare com baseline e notifique divergências.
5. **Relatório**: documente alterações em `CHANGELOG.md`, atualize `docs/06` com lições aprendidas.

## 6.11 Exercícios avançados
1. Implemente `pam_faillock`, `pam_tally2` e 2FA com `google-authenticator`. Simule ataques de força bruta e valide bloqueios e alertas.
2. Escreva perfil AppArmor para `netcat`, `socat` e `python3 -m http.server`. Documente bloqueios e permissões permitidas.
3. Configure IMA/EVM com chaves TPM2, medindo binários críticos. Gere relatório com arquivos reavaliados e eventos FIM.
4. Crie pipeline OSQuery + Falco + Wazuh que detecta execução de scripts em `/tmp`, coleta evidências e envia alerta para Slack/Matrix.

> **Referências:** CIS Benchmarks Debian, NSA Hardened Linux Guide, MITRE D3FEND, *Linux Hardening in Hostile Networks* (Kyle Rankin).
