# Glossário de Termos

Este glossário centraliza siglas, comandos e conceitos recorrentes usados no guia.

## Conceitos gerais
- **ACL (Access Control List)** – Lista que define permissões adicionais além do modelo tradicional `rwx`.
- **ATT&CK** – Matriz de técnicas da MITRE descrevendo comportamentos adversários.
- **D3FEND** – Catálogo MITRE com contramedidas defensivas mapeadas para técnicas ofensivas.
- **Kill Chain** – Modelo que descreve as fases de um ataque (Lockheed Martin).
- **Threat Hunting** – Busca proativa por sinais de comprometimento antes de alertas automáticos.

## Ferramentas
- **Ansible** – Ferramenta de automação baseada em YAML; gerencia infraestrutura via SSH.
- **AutoRecon** – Framework de reconhecimento automatizado em pentests.
- **BloodHound** – Aplicativo que mapeia relações em Active Directory.
- **Falco** – Motor de detecção em tempo real via eBPF.
- **OSQuery** – Framework que expõe estado do sistema operacional via SQL.

## Redes
- **NetFlow** – Protocolo que sumariza fluxos de rede (origem, destino, portas, bytes).
- **Pivoting** – Técnica de usar host comprometido para alcançar outras redes/serviços.
- **SYN Flood** – Ataque de negação de serviço explorando o handshake TCP incompleto.
- **TTL (Time To Live)** – Campo IP que limita saltos de pacote.

## Forense
- **Chain of Custody** – Cadeia de custódia; documentação que garante integridade de provas.
- **EWF (Expert Witness Format)** – Formato de imagem forense `.E01` com metadados.
- **Prefetch** – Artefato Windows que registra execução de programas.
- **Timeline** – Linha do tempo agregando eventos de sistemas para reconstrução.

## Kernel e Containers
- **eBPF** – Extended Berkeley Packet Filter; executa bytecode no kernel com segurança.
- **Namespace** – Mecanismo que isola recursos (PID, NET, MNT) em Linux (contêineres).
- **cgroup** – Control groups; limita uso de recursos (CPU, memória) por processos.

## Segurança ofensiva
- **C2 (Command and Control)** – Infraestrutura usada para controlar máquinas comprometidas.
- **Kerberoasting** – Ataque que extrai tickets de serviço Kerberos para quebrar offline.
- **OPSEC** – Operational Security; práticas para minimizar exposição do operador.

## Segurança defensiva
- **AppArmor/SELinux** – Sistemas MAC (Mandatory Access Control) em Linux.
- **Falhas Zero-Day** – Vulnerabilidades desconhecidas publicamente.
- **MDR/EDR/XDR** – Managed/Endpoint/Extended Detection and Response.

> Atualize este glossário sempre que novos termos aparecerem nos capítulos ou laboratórios.
