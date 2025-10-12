# 09. Forense e Resposta a Incidentes

Responder a incidentes requer técnica, metodologia e disciplina. Vamos aprofundar a coleta em múltiplas plataformas (on-premise, nuvem, dispositivos móveis), análise avançada e reporte profissional alinhado a frameworks reconhecidos.

## 9.1 Metodologias de IR
- **ISO/IEC 27035**: Preparação → Identificação → Avaliação → Resposta → Lições.
- **SANS PICERL**: Prepare → Identify → Contain → Eradicate → Recover → Lessons Learned.
- **NIST SP 800-61**: diretrizes detalhadas, templates de comunicação.
- Defina papéis: IR Lead, Comunicador, Analista Forense, Analista Malware, Legal.
- Estabeleça RACI (Responsible, Accountable, Consulted, Informed) por tipo de incidente.

## 9.2 Preparação
- Playbooks específicos (`incidentes/playbooks/`): ransomware, phishing, BEC, insider.
- Kits de coleta (USB com scripts, write blockers, doc templates).
- Estrutura de comunicação (Matrix, Signal, Bridge PSTN), listas de contatos.
- Contratos com provedores (cloud, MSSP) e acordos de confidencialidade.

## 9.3 Aquisição de evidências
### 9.3.1 On-premise
- **Disco**: `dc3dd`, `guymager`, `ewfacquire`. Use `--segment` para dividir arquivos grandes.
- **Memória**: `LiME`, `AVML`, `DumpIt` (Windows). Documente offset, perfil.
- **Rede**: `tcpdump`, `netsniff-ng`, `Arkime`.

### 9.3.2 Cloud
- AWS: use `aws ec2 create-snapshot`, `aws s3 cp`, `AWS IR Lambda`. Ferramentas: `dfir-aws-ir-tools`, `ThreatResponseIR`.
- Azure: `az vm run-command invoke`, `Azure Security Center`. Utilize `Azure Disk Snapshot Tool`.
- GCP: `gcloud compute disks snapshot`, `gcloud alpha compute packets capture create`.
- Preserve logs: CloudTrail, GuardDuty, Azure Activity, GCP Audit.

### 9.3.3 Contêineres e Kubernetes
- Capture `kubectl get pod -o yaml`, `kubectl cp` para exportar logs.
- `kubectl exec -- tar` para coletar diretórios em pods.
- Ferramentas: `Falco`, `Sysdig Inspect`, `kube-forensics`, `Chainguard forensics`.

### 9.3.4 Dispositivos móveis
- Android: `ADB`, `Magisk` (root). Ferramentas open source: `AFLogical`, `Andriller`.
- iOS: `libimobiledevice`, `Elcomsoft`. Necessário perfil corporativo para adquirir.
- Documente consentimento e políticas legais.

## 9.4 Análise
### 9.4.1 Memória (Volatility 3)
- Scripts: `windows.pslist`, `windows.dlllist`, `windows.malfind`, `linux.bash`, `linux.psscan`.
- YARA inline: `volatility3 -f mem.raw windows.vadyarascan --yara-rules foo.yar`.
- Detecte injeção, threads suspeitas, handles.

### 9.4.2 Disco
- `Sleuthkit`: `fls`, `icat`, `mmls`, `istat`. Combine com timeline (`mactime`).
- `Autopsy`: interface com ingest modules, timeline, partições, carving.
- `Velociraptor`: coletores prontos (`Linux.KapeFiles.Targets`). Permite queries e tags.
- `Plaso`/`log2timeline`: timeline unificada. Filtre com `psort` (`--slice` para janela de tempo específica).

### 9.4.3 Artefatos específicos
- Windows: Prefetch (AppCompatCache), Shimcache, Amcache, Registry (NTUSER.DAT), SRUM, event logs.
- Linux: `.bash_history`, `audit.log`, `syslog`, `journalctl`, `auth.log`, `wtmp`, `btmp`.
- macOS: `Unified Logs`, `TCC` (permissões), `FSEvents`.
- Cloud: GuardDuty findings, VPC Flow Logs, CloudTrail, Azure Activity Logs.

### 9.4.4 Análise de malware
- Sandboxes: `Cuckoo`, `CAPEv2`, `ThreatARIUM`.
- Ferramentas: `Ghidra`, `IDA Free`, `Radare2`, `Capa`, `Detect It Easy`.
- Use VMs isoladas, Snapshots, `Procmon`, `Wireshark`.

## 9.5 Contenção, erradicação e recuperação
- Isolamento: `iptables -I OUTPUT`, VLAN, desabilitar interfaces, snapshot antes.
- Erradicação: remover binários, revogar credenciais, bloquear IPs/domínios.
- Recuperação: restauração de backups, verificação de integridade (`hashes`, `aide`).
- Monitoramento pós-incidente: alertas custom, hunts.

## 9.6 Relatórios e comunicação
- Estrutura: Resumo executivo, cronologia, escopo, impacto, indicadores, análise técnica, recomendações.
- Storytelling: correlacione eventos e decisões, explique impacto em linguagem de negócio.
- Mantenha anexos (PCAP, imagens, logs) referenciados com hash SHA256.

## 9.7 Indicadores e compartilhamento
- Formatos: STIX/TAXII, OpenIOC, JSON.
- Plataformas: MISP, OpenCTI, IntelMQ.
- Compartilhe com comunidades (CIRCL, CERTs) conforme NDA/contratos.

## 9.8 Estudo de caso: ransomware em ambiente híbrido
1. Alerta via EDR (CrowdStrike) detecta encriptação. IR aciona playbook.
2. Contenção: isolamento de subredes, revogação de tokens.
3. Coleta: snapshots AWS, discos on-prem, memdump.
4. Análise: identificados `LockBit` artefatos, exfiltração SFTP.
5. Erradicação: patch de RDP, credenciais rotacionadas, backups restaurados.
6. Relatório: lições aprendidas, novos controles (MFA, EDR expandido, DLP, NSM).
7. Atualização: `docs/06` e `docs/10` para reforçar defesas.

## 9.9 Exercícios avançados
1. Execute simulação tabletop do incidente acima; registre decisões, tempos, pontos de falha. Atualize playbooks.
2. Analise dump de memória com Volatility 3, extraia payload, reverse com Ghidra, identifique C2. Documente.
3. Realize forense em contêiner comprometido: `docker commit`, `docker save`, `syft/grype` para SBOM, `falco` logs, timeline de `kubectl`.
4. Crie pipeline que converte timeline `plaso` em narrativa executiva (Markdown), incluindo gráficos (matplotlib) e anexos criptografados.

> **Referências:** NIST SP 800-61r2, *Incident Response & Computer Forensics* (3rd Ed.), DFIR Madness (Giulia Marchi), blog Digital Forensics (Hacking Articles), sans.org recursos IR.
