# 00. Roteiro Editorial (Guia Expandido)

Este roteiro descreve os tópicos avançados que serão incorporados a cada capítulo para transformar o material no guia mais completo de Linux para profissionais de cybersegurança.

## Capítulo 01 – Fundamentos do Sistema
- Histórias de distribuição do Kali e comparação com outras distros de segurança.
- Anatomia do processo de boot (BIOS/UEFI, GRUB, initramfs) e implicações para segurança.
- Estrutura FHS com foco em artefatos de investigação e ocultação de malwares.
- Gestão de contas de serviço, sudo granular e logs de autenticação.

## Capítulo 02 – Shell e Automatização
- Teoria do shell: parsing, expansão, precedência de operadores.
- Construção de frameworks internos (bibliotecas Bash) e padrões de projeto em scripts.
- Integração com ferramentas de mensageria (Slack, Mattermost) e APIs REST.
- Automação resiliente: locking, idempotência, tratamento de erro avançado.

## Capítulo 03 – Pacotes e Serviços
- Anatomia do APT (sources.list, preferências, hooks em `/etc/apt/apt.conf.d`).
- Empacotamento .deb artesanal com `dpkg-deb` e `debuild` para distribuir ferramentas internas.
- Observabilidade de serviços com `systemd-analyze`, `journalctl --user`, `coredumpctl`.
- Estratégias de rollback e gestão de snapshots multi-nível.

## Capítulo 04 – Filesystems, Permissões e Criptografia
- Journaling, inodes, atributos estendidos e carvers forenses.
- Comparação de LUKS1 vs LUKS2, políticas de keyslots e múltiplos fatores.
- Escalonamento com soluções corporativas (eCryptfs, fscrypt, CEPH, GlusterFS).
- Técnicas de ocultação (timestomping, alternate data streams) e contramedidas defensivas.

## Capítulo 05 – Redes, Segurança e Monitoração
- Arquitetura TCP/IP versus OSI, análise de estados, handshake detalhado.
- Monitoramento ativo com NetFlow, sFlow, IPFIX e ferramentas open source.
- Engenharia de tráfego (QoS, tc, iptables mangle) para testes controlados.
- Casos avançados de pivoting (DNS tunneling, ICMP exfiltration) e detecção correspondente.

## Capítulo 06 – Hardening e Segurança Defensiva
- Modelos de ameaça, risk scoring e priorização baseada em MITRE D3FEND.
- PAM avançado (2FA, pam_python, maps baseados em hosts). 
- Políticas de integridade com IMA/EVM e assinaturas de módulos.
- Playbooks de caça a ameaças com OSQuery e eBPF.

## Capítulo 07 – Operações Ofensivas e Pentest
- Planejamento operacional (kill chain, diamond model) e uso de frameworks (MITRE ATT&CK).
- Engenharia social e infraestrutura de campanha (phishing, SMS, voz).
- Emulação de adversário, uso de C2 (Sliver, Mythic, Covenant) com opsec avançada.
- Pós-exploração persistente e evasão de EDRs.

## Capítulo 08 – Labs e Projetos
- Laboratórios com múltiplas fases, incluindo Purple Team e automação de relatórios.
- Projetos de contribuição comunitária (scripts open source, pacotes .deb).
- Indicadores de maturidade e critérios de avaliação por nível (iniciante, intermediário, avançado).

## Capítulo 09 – Forense e Resposta a Incidentes
- Processo completo de IR alinhado à ISO 27035 e SANS.
- Aquisição em nuvem e dispositivos móveis.
- Artefatos específicos de Windows, Linux, macOS e contêineres.
- Storytelling forense: construção de linha do tempo e apresentação executiva.

## Capítulo 10 – Kernel, Containers e Nuvem
- Debug de kernel com `kgdb`, perf, ftrace, SystemTap.
- Escapes de contêiner e endurecimento (seccomp customizado, SELinux policies).
- Segurança em Kubernetes (OPA, Kyverno, runtime security, supply chain).
- Estratégias multi-cloud, IAM complexo, Zero Trust e arquiteturas defensivas.

## Capítulo 11 – Anexos e Recursos
- Glossário avançado, mapa mental do guia, roteiros de estudo por certificação.
- Lista comentada de conferências, CTFs e programas de bug bounty.
- Modelos adicionais (SLA de pentest, políticas de uso aceitável, runbooks de atualização).

## Novos itens planejados
- Glossário detalhado (`docs/glossario.md`).
- Linha do tempo do Linux/Kali para cybersec (`docs/timeline-kali.md`).
- Guia de estudos modular (`docs/plano-estudos.md`).
