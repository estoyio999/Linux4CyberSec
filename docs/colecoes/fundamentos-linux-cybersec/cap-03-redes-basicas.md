# Capítulo 3 — Análise e Gerência de Redes

## Objetivos do capítulo
- Identificar interfaces, endereços IP e parâmetros de rede.
- Alterar configurações manualmente (endereços, máscaras, gateway).
- Solicitar novos leases DHCP e falsificar endereços MAC.
- Utilizar ferramentas de diagnóstico (`netstat`, `ss`, `tcpdump`, `nmap`).

## Inventário de rede
| Comando | Propósito |
| --- | --- |
| `ip addr` / `ifconfig` | Lista interfaces, IP, máscara e MAC. |
| `ip link set eth0 down/up` | Desativa/ativa interface. |
| `ip route` / `route -n` | Visualiza tabela de roteamento. |
| `nmcli device status` | Integração com NetworkManager (quando habilitado). |

### Alterando IP e máscara
```bash
sudo ip addr add 192.168.56.20/24 dev eth0
sudo ip addr del 192.168.56.10/24 dev eth0
```
Para definir broadcast manualmente com `ifconfig`:
```bash
sudo ifconfig eth0 192.168.181.115 netmask 255.255.0.0 broadcast 192.168.1.255
```

### Renovando IP via DHCP
```bash
sudo dhclient -r eth0   # libera lease atual
sudo dhclient eth0      # solicita novo lease
```
Monitore `/var/lib/dhcp/` e `/var/log/syslog` para acompanhar registros do servidor.

### Spoofing de MAC
```bash
sudo ip link set eth0 down
sudo ip link set eth0 address 00:11:22:33:44:55
sudo ip link set eth0 up
```
Importante para testes de controle de acesso baseados em MAC e para ocultar a origem de um ataque em avaliações autorizadas.

## Visão de conexões e serviços
- `ss -tulpn` — substitui `netstat -tulpn` para listar sockets TCP/UDP e processos donos.
- `lsof -i :80` — identifica processo atrelado a uma porta específica.
- `tcpdump -i eth0` — captura pacotes brutos; combine com filtros (`tcp port 80`).
- `nmap` — scanner de portas e serviços, detalhado em capítulos posteriores (ver `docs/05-redes-seguranca-e-monitoracao.md`).

## Boas práticas ofensivas e defensivas
- **Ofensivo**: clonar parâmetros de rede de uma máquina autorizada para burlar NAC; monitorar tráfego ARP para man-in-the-middle.
- **Defensivo**: registrar todas as alterações de MAC e IP em scripts `bash` que escrevem logs datados; utilizar `arpwatch` em redes sensíveis.

## Exercícios propostos
1. Mapear todos os hosts ativos da sub-rede do laboratório usando `arp-scan` e registrar em planilha.
2. Criar script que restaure configurações padrão de rede após testes (útil para evitar perder conectividade).
3. Capturar handshake HTTP com `tcpdump`, analisar com Wireshark e identificar cabeçalhos relevantes.

## Conexões com outros capítulos
Este capítulo prepara terreno para tópicos de segurança, anonimato (capítulo 13) e redes sem fio (capítulo 14). Domine cada comando e mantenha um "diário de rede" registrando IP, MAC e rotas dos ambientes de teste.
