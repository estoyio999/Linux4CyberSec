# 08. Labs e Projetos

Os laboratórios são o coração do aprendizado aplicado. Aqui você encontrará atividades progressivas com objetivos claros, cenários realistas, entregáveis e métricas de avaliação. Cada lab pode ser combinado em programas de treinamento e trilhas de certificação internas.

## 8.1 Estrutura de documentação
- `labs/notas/<lab>.md`: diário de bordo com comandos, decisões e descobertas.
- `labs/artifacts/<lab>/`: PCAPs, dumps, relatórios, scripts.
- `labs/checklists/<lab>.md`: critérios de validação.
- `labs/avaliacao/<lab>.json`: rubrica com métricas (tempo, impacto, qualidade).

## 8.2 Níveis de maturidade
- **Fundamental**: foco em infraestrutura local, configuração, comandos.
- **Intermediário**: integrações de ferramentas, automações, relatórios.
- **Avançado**: operações completas (Red/Blue/Purple), ambientes distribuídos.

## 8.3 Laboratórios fundamentais
### Lab 01 – Provisionamento e hardening inicial
- **Objetivo**: instalar, atualizar, aplicar hardening mínimo, configurar snapshot.
- **Tempo estimado**: 4h.
- **Critérios**: checklist CIS (nível 1) com 80% de aderência, log `lynis` anexado.
- **Entrega**: `lab01.md`, snapshot documentado, script `scripts/update-kali.sh`.

### Lab 02 – Automação de varreduras
- Pipeline `nmap` + `httpx` + `waybackurls`.
- Geração de relatório `Markdown -> PDF` via `pandoc`.
- Integração com Slack (webhook) e repositório Git (branch por execução).

### Lab 03 – Segmentação e firewall
- Configuração `nftables`, `firewalld` ou `ufw`.
- Monitoramento com Suricata + dashboard `Grafana`.
- Testes de bypass com `socat`, `sshuttle`, `chisel`.

## 8.4 Laboratórios intermediários
### Lab 04 – Red Team em Active Directory
- Infra: DetectionLab ou realce com `Vagrant`.
- Execução: `powerview`, `SharpHound`, `Rubeus`, `CME`.
- Entregáveis: grafos `BloodHound`, relatório com ATT&CK mapping, scripts utilizados.

### Lab 05 – Blue Team: detecção em tempo real
- Deploy `Suricata`, `Zeek`, `Wazuh`, `Elastic`.
- Simulações com `Caldera`, `Atomic Red Team`, `PurpleSharp`.
- Ajuste de regras, playbook `TheHive`.

### Lab 06 – Hardening extremo
- Aplicação CIS nível 2/ANSSI.
- Automação com Ansible (`infra/hardening/`).
- Comparativo com baseline OSQuery.

### Lab 07 – DevSecOps para ferramentas internas
- Empacotar script em `.deb`, pipeline CI (`GitLab`), publicação em repositório `aptly`.
- Documentar testes (`bats`, `pytest`), lint (`shellcheck`, `black`).
- Deploy automatizado em hosts via `Ansible`.

## 8.5 Laboratórios avançados
### Lab 08 – Operação Purple Team integrada
- Roteiro Kill Chain completo, com defesas D3FEND pareadas.
- Ferramentas: GoPhish, Sliver/Mythic, Suricata, OSQuery, Elastic.
- Avaliação: matriz ATT&CK preenchida e runbooks atualizados.

### Lab 09 – Forense multi-plataforma
- Aquisição de imagem Windows, Linux e nuvem.
- Análise com Volatility, Sleuthkit, Velociraptor.
- Relatório com storytelling forense.

### Lab 10 – ICS/OT Security Challenge
- Simulação com OpenPLC, Modbus, HMI vulnerável.
- Ataques: replay, write coils, DoS.
- Defesa: segmentação, monitoramento custom com Zeek + Suricata (regras ICS).

### Lab 11 – Criação de ferramenta OSS
- Desenvolver CLI (Go/Python/Rust) integrando APIs (Shodan, Vulners, VirusTotal).
- Testes, containerização, documentação, publicação GitHub + pacote Debian + PyPI/GoReleaser.

## 8.6 Projetos de longo prazo
- **Plataforma Threat Hunting**: pipelines OSQuery → Kafka → Elastic → detections.
- **Infra Red Team permanente**: C2 (Mythic) + infra rotativa + opsec.
- **SOC simulado**: TheHive, Cortex, Shuffle (SOAR) com detections automáticas.
- **Programa de bug bounty interno**: scripts para triagem, templates de relatórios e treinamento.

## 8.7 Métricas e avaliação
- Use `labs/avaliacao` com JSON como:
  ```json
  {
    "lab": "Lab 04",
    "criterios": [
      {"item": "Enumeração completa", "peso": 0.3},
      {"item": "Exploit documentado", "peso": 0.3},
      {"item": "Relatório de qualidade", "peso": 0.4}
    ],
    "score": 0.0
  }
  ```
- Atualize com scripts Python para calcular média e gerar gráficos (ex.: `matplotlib`).

## 8.8 Calendário sugerido
Crie planos em `docs/plano-estudos.md` com durações (4, 8, 12 semanas), combinando labs por nível de maturidade. Inclua checkpoints de revisão e apresentações.

## 8.9 Exercícios adicionais
1. Adapte Lab 04 para incluir credenciais comprometidas e danos controlados; documente mitigação.
2. Expanda Lab 08 com frameworks MITRE D3FEND, detalhando detecções e contramedidas implementadas.
3. Desenvolva rubrica de avaliação para Lab 11 focando boas práticas de código seguro.
4. Construa script que gera scoreboard dos labs realizados, exibindo progresso por equipe.

> **Referências:** MITRE Cyber Analytics Repository, Purple Team Exercises Framework (MITRE ENGENUITY), blog da Red Canary (detecções), Offensive Security Proving Grounds.
