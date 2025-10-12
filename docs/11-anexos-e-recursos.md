# 11. Anexos e Recursos

Esta seção consolida convenções, recursos de estudo, conferências, programas de bug bounty, glossário e templates adicionais. Utilize-a como referência rápida e como checklist de atualização periódica.

## 11.1 Convenções do repositório
- Diretórios padrão:
  - `docs/`: capítulos numerados e materiais auxiliares (`00-roteiro-editorial.md`, `glossario.md`, `timeline-kali.md`, `plano-estudos.md`).
  - `labs/`: exercícios, notas (`notas/`), artefatos (`artifacts/`), checklists e avaliações.
  - `scripts/`: automatizações versionadas por domínio (`redteam/`, `blueteam/`, `forense/`, `infra/`).
  - `incidentes/`: playbooks, IOC lists (`iocs.csv`), relatórios pós-incidente.
  - `containers/`: Dockerfiles, manifests Kubernetes, políticas de segurança.
  - `templates/`: modelos de relatórios, playbooks, tmux, checklists.
- Naming:
  - Arquivos Markdown em `lowercase-com-hifen.md`.
  - Logs em `logs/YYYY-MM-DD-contexto.log`.
  - Scripts shell com extensão `.sh`, Python `.py`, Go `main.go`, Rust `main.rs`.
- Versionamento: branches por feature (`feature/capitulo-06-hardening`), tags por releases (`v1.0`), pastas `draft/` para conteúdo em revisão.

## 11.2 Recursos de estudo e certificações
- **Certificações**:
  - Offensive Security: OSCP, OSEP, OSEE, OSED, OSWE.
  - SANS: GPEN, GXPN, GCFA, GCFE, GCTI.
  - eLearnSecurity: eJPT, eCPPT, eWPT, eCIR.
  - Red Team Alliance, ZeroPoint: RTR, BTL1.
- **Cursos online**: TryHackMe, Hack The Box Academy, TCM Security, Pentester Academy, Pluralsight (Linux Hardening), Udemy (Kubernetes Security).
- **Livros**:
  - *Linux Command Line and Shell Scripting Bible*.
  - *UNIX and Linux System Administration Handbook*.
  - *Red Team Ops* (SpecterOps).
  - *Incident Response & Computer Forensics*.
  - *Cloud Native Security* (O'Reilly).
- **Blogs**: Red Canary, Elastic, Mandiant, Palo Alto Unit 42, HackTricks, DFIR Report, Aqua Security, Wiz Research.

## 11.3 Conferências e comunidades
- Conferências internacionais: DEF CON, Black Hat, RSA, ShmooCon, SANS Summits, Blue Team Con, ElasticON, KubeCon.
- Brasil: Roadsec, H2HC, BSides São Paulo, NullByte Security Conference, You Sh0t the Sheriff.
- Comunidades: OWASP, DCG (DefCon Groups), Meetups de DFIR, Slack/Discord (SegInfo, Tempest, Hack The Box BR).

## 11.4 Programas de bug bounty e plataformas
- Plataformas: HackerOne, Bugcrowd, Synack, Intigriti, YesWeHack.
- Recursos: disclose.io (políticas safe harbor), Bugcrowd University, Intigriti Academy.
- Procuração: mantenha lista de programas relevantes por setor (fintech, gov, cloud) em `docs/programas-bugbounty.md`.

## 11.5 Glossário (ver `docs/glossario.md`)
Inclui termos técnicos, abreviações, TTPs ATT&CK, D3FEND, comandos e conceitos relacionados a Linux e cybersec.

## 11.6 Linha do tempo Kali/Linux (ver `docs/timeline-kali.md`)
Histórico das versões com destaques de ferramentas, kernel e mudanças relevantes para ofensiva/defensiva.

## 11.7 Guia de estudos modular (`docs/plano-estudos.md`)
Planos de 4, 8 e 12 semanas combinando capítulos, labs, leituras e exercícios avaliados.

## 11.8 Modelos adicionais
- `templates/relatorio-pentest.md`: relatório técnico/executivo.
- `incidentes/templates/playbook.md`: playbook IR.
- `templates/checklist-hardening.csv`: checklist para validação.
- `templates/tmux-redteam.yaml`: layout tmux (adicionar).
- `templates/cartao-runbook.md`: resumo (criar).
- `templates/relatorio-forense.md`: modelo narrativo (criar).

## 11.9 Ferramentas recomendadas (seleção expandida)
### Ofensivas
`nmap`, `masscan`, `amass`, `naabu`, `httpx`, `ffuf`, `sqlmap`, `Responder`, `Kerbrute`, `Sliver`, `Mythic`, `Havoc`, `Metasploit`, `PowerSploit`, `SharpHound`, `BloodHound`, `GoWitness`, `Aquatone`, `Burp Suite`, `ZAP`, `Maltego`, `SpiderFoot`.

### Defensivas
`suricata`, `zeek`, `wazuh`, `osquery`, `velociraptor`, `falco`, `crowdsec`, `auditbeat`, `filebeat`, `logstash`, `elastic`, `thehive`, `cortex`, `sigma-cli`.

### Forense
`autopsy`, `sleuthkit`, `volatility3`, `rekall`, `plaso`, `bulk_extractor`, `kape`, `xmount`, `guymager`, `dc3dd`, `log2timeline`, `chainsaw`.

### Produtividade
`tmux`, `zellij`, `wezterm`, `iTerm2`, `Obsidian`, `Joplin`, `Atypical/zk`, `draw.io`, `Mermaid`, `PlantUML`, `mkdocs`, `asciinema`, `doitlive`.

## 11.10 Recursos de atualização contínua
- RSS feeds (Feedly) com blogs de segurança.
- Alertas CVE (`cvedetails`, `nvd.nist`, `cert.br`).
- Mailing lists (Full Disclosure, Bugtraq, Debian Security Announce).
- Monitoramento de pacotes (`apt list --upgradable`, `pip list --outdated`).

## 11.11 Manutenção
- Revisão trimestral de ferramentas (adicionar/remover obsoletas).
- Atualização semestral de labs (novos CVEs, ferramentas).
- Auditoria anual de políticas (playbooks, templates, glossário).
- Documente mudanças em `CHANGELOG.md` e abra issues para melhorias.

> **Nota:** A seção de anexos deve evoluir com contribuições da comunidade. Use issues/pull requests para sugerir novos recursos, sempre verificando licenças e direitos de uso.
