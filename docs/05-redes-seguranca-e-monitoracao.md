# 05. Redes, Segurança e Monitoração

Redes são o campo de batalha primário para profissionais de cybersegurança. Neste capítulo exploramos desde a teoria do TCP/IP até monitoramento avançado com NetFlow, engenharia de tráfego, tunelamentos evasivos e técnicas de detecção. A meta é dominar redes ofensivas e defensivas com profundo entendimento técnico.

## 5.1 Fundamentos de protocolos e pilha TCP/IP
### 5.1.1 Modelo OSI vs TCP/IP
- OSI possui 7 camadas (Física → Aplicação). TCP/IP simplifica em 4 (Link, Internet, Transporte, Aplicação).
- Ataques se manifestam em diferentes camadas: ARP spoofing (Link), IP spoofing (Internet), SYN flood (Transporte), SQL Injection (Aplicação).

### 5.1.2 TCP handshake detalhado
- `SYN` → `SYN+ACK` → `ACK`. Monitore com `tcpdump 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'`.
- Flags relevantes: `RST` (reset), `FIN` (encerramento), `URG`/`PSH`. Ferramentas de EDR detectam combinações inusitadas.
- `tcpdump -nnvvS` exibe seq/ack numbers para análise aprofundada.

### 5.1.3 Fragmentação e evasão
- IP fragmentation (`-f`, `-ff` no nmap) pode contornar firewalls antigos. IDS modernos fazem reassembly. Teste defesas com `scapy`:
  ```python
  send(IP(dst="10.10.0.5")/fragment(ICMP()/"dados"))
  ```

## 5.2 Coleta de dados de rede
### 5.2.1 NetFlow, sFlow e IPFIX
- **NetFlow** (Cisco) coleta metadados (src/dst IP, portas, bytes). Utilize `nfcapd` (`sudo apt install nfdump`).
- **sFlow** captura amostras de pacotes. Ferramentas: `sflowtool`, `pmacct`.
- **IPFIX** (IETF) padroniza fluxos; `pmacct + InfluxDB` para dashboards.

### 5.2.2 Ferramentas open source
- `pmacct` – coleta NetFlow/sFlow, integra com Kafka/Redis.
- `ntopng` – interface web para análise em tempo real.
- `Arkime (antigo Moloch)` – captura massiva, indexa e permite buscas.

## 5.3 Varredura e fingerprinting avançados
### 5.3.1 Nmap e NSE estendidos
- Scripts NSE customizados: crie em `/usr/share/nmap/scripts/` ou `~/.nmap/scripts/`. Use Lua para automatizar fingerprinting específico (ex.: detectar versões de APIs proprietárias).
- `nmap --script vuln --script-args=vuln.runall,path=/tmp/nmap-scripts` para rodar todos scripts vulnerabilidade.

### 5.3.2 Frameworks de enumeração
- `Recon-ng`, `SpiderFoot` e `Amass` são moduláveis. Integre API keys (Shodan, Censys, VirusTotal) para ampliar descoberta.
- `zdns` para resolução massiva, `massdns` para resoluções assíncronas.

### 5.3.3 Fingerprinting de TLS/HTTP
- `ja3`/`ja3s` fingerprints TLS; use `zeek` para coletar.
- `httprobe`/`httpx` com `-tls-grab` exibem certificados e cipher suites.
- `tls-scan` (do Salesforce) testa configurações TLS.

## 5.4 Engenharia de tráfego
### 5.4.1 QoS e traffic shaping
- `tc` (part of `iproute2`) manipula filas de pacotes. Exemplo para simular latência:
  ```bash
  sudo tc qdisc add dev eth0 root netem delay 150ms 20ms distribution normal
  ```
- Combine com `iptables -t mangle` para marcar pacotes: `iptables -t mangle -A PREROUTING -s 10.0.0.0/24 -j MARK --set-mark 1`.

### 5.4.2 Bridging e tap devices
- Crie pontes para sniffing transparente: `ip link add name br0 type bridge`, `ip link set eth1 master br0`.
- Utilize `tshark -i br0 -f "not arp"` para capturar tráfego.

## 5.5 Tunelamento e pivoting avançados
- **DNS tunneling**: `iodine`, `dnscat2`. Detecte com análise de entropia de consultas, tamanho de payload.
- **ICMP tunneling**: `ptunnel-ng`. Firewalls muitas vezes liberam ICMP; monitore `type 8/0` com payloads grandes.
- **VPN improvisada**: `ssh -w` (tun interface). Configure `PermitTunnel yes` em `sshd_config`.
- **SMB relay/pivot**: `impacket-smbrelayx` com `ntlmrelayx` pode encaminhar autenticações.
- **Port Knocking**: implemente `knockd` para abrir portas dinamicamente.

## 5.6 Monitoramento e detecção
### 5.6.1 Suricata/Zeek tuning
- Ajuste `suricata.yaml` para perfis multi-core (`detect-thread-ratio`).
- Crie regras customizadas: `/etc/suricata/rules/local.rules`.
- `zeek` scripts: `event zeek_init() { Log::write(...); }`. Use frameworks (`Intel::`, `Notice::`, `Input::`).
- Exportação para Elastic: Filebeat módulo `suricata`/`zeek`.

### 5.6.2 Detecção de beaconing
- `RITA` (Rapid Incident Detection) analisa logs de Zeek para identificar beaconing e entropia elevada.
- `yaralyzer`, `flare-floss` para payloads maliciosos extraídos de PCAPs.

### 5.6.3 Decepção e honeypots
- `cowrie`, `dionaea`, `tpotce` (multi-honeypot). Configure VLAN ou DMZ isolada.
- `OpenCanary` envia notificações em tempo real.

## 5.7 Segurança wireless
- Modos de operação: monitor, managed, master. Ferramentas `airmon-ng`, `airodump-ng`, `aircrack-ng`.
- WPA3, SAE handshake, downgrade attacks (force WPA2). Use `hcxdumptool` + `hashcat` para password cracking.
- 802.1X (WPA-Enterprise) – explore com `wpa_supplicant`, `eapol_test` e ataques `EvilAP` (`hostapd-wpe`).
- WIDS/WIPS: `Kismet`, `Wazuh` (módulos), `airodump-ng` automatizado.

## 5.8 Segurança para IoT/ICS
- Protocolos Modbus, DNP3, OPC-UA. Ferramentas: `modpoll`, `plcscan`.
- Emulações com `Conpot`, `OpenPLC`. Combine com Snort/Suricata regras específicas.
- Segmentação e DMZ industrial; monitoramento com `Zeek` especializado.

## 5.9 Estudo de caso: arquitetura de monitoramento
1. **Coleta**: `Suricata` (alertas), `Zeek` (metadados), NetFlow via `pmacct`.
2. **Ingestão**: Logstash/Vector envia para Elastic e ClickHouse.
3. **Análise**: Dashboards Kibana (detecção de varreduras, beaconing), `Grafana` para fluxos.
4. **Resposta**: Scripts `SOAR` (TheHive + Cortex) automatizam bloqueios via `nftables` ou `SDN`.
5. **Detecção proativa**: regressões com `mlsploit`, `PyOD` em dados de NetFlow.

## 5.10 Exercícios avançados
1. Configure ambiente com Suricata + Zeek + NetFlow, gere tráfego (HTTP normal, DNS tunneling, beaconing). Crie relatórios comparando detecção em cada ferramenta.
2. Simule túnel DNS com `iodine` e escreva assinaturas Suricata que identifiquem exfiltração (ex.: tamanho dos subdomínios, uso de Base32).
3. Utilize `pmacct` para enviar NetFlow a Kafka, processe com Python (Flink/Spark) e gere alertas de DDoS baseados em volume.
4. Monte honeypot `cowrie`, analise sessões capturadas e gere indicadores para Suricata e Firewall.

> **Referências:** *TCP/IP Illustrated* (Stevens), RFCs 7011-7015 (IPFIX), livros *Practical Packet Analysis*, *Industrial Network Security*, documentação Suricata/Zeek.
