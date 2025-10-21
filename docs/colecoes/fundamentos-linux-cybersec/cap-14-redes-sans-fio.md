# Capítulo 14 — Redes Sem Fio e Bluetooth

## Objetivos do capítulo
- Reconhecer arquitetura Wi-Fi (BSS, ESSID, canais, segurança WEP/WPA/WPA2).
- Configurar placa em modo monitor (`airmon-ng`, `iwconfig`).
- Realizar reconhecimento com `airodump-ng`, `airodump-ng` e ataques controlados (deauth, captura de handshake).
- Detectar e interagir com dispositivos Bluetooth (`hciconfig`, `hcitool`).

## Wi-Fi em modo monitor
```bash
sudo airmon-ng check kill   # interrompe processos que interferem
sudo airmon-ng start wlan0  # inicia modo monitor
sudo airodump-ng wlan0mon   # coleta beacons e estações
```
Analisar colunas: BSSID, PWR, Beacons, #Data, canal, cifra (WEP/WPA), ESSID.

### Ataques de laboratório
- **Captura de handshake**: `airodump-ng -c 6 --bssid <AP> -w captura wlan0mon` + `aireplay-ng --deauth 5 -a <AP> -c <cliente> wlan0mon`.
- **WEP cracking**: coletar IVs e usar `aircrack-ng captura.cap`.
- **WPA/WPA2**: capturar handshake e quebrar com `hashcat` (`-m 22000`).

Todos os testes devem ocorrer em rede própria ou ambiente autorizado.

## Bluetooth básico
- `hciconfig` — lista adaptadores.
- `hcitool scan` — descobre dispositivos próximos.
- `bluetoothctl` — interface para pairing/conexão.
- Ferramentas avançadas: `bluelog`, `bettercap` para sniffing e MITM.

## Segurança ofensiva e defensiva
- **Ofensivo**: identificar APs desprotegidos, ataques de rogue AP, captura de chaves fracas.
- **Defensivo**: habilitar WPA2/WPA3, usar senhas extensas, monitorar deauth flood, implementar WIDS/WIPS.

## Exercícios propostos
1. Configurar laboratório com dois APs e comparar tráfego gerado por diferentes cifras (WEP vs WPA2).
2. Scriptar recon Wi-Fi que gera relatório HTML com dados do `airodump-ng`.
3. Mapear dispositivos Bluetooth num ambiente controlado e testar conexões autenticadas/inseguras.

## Componente no repositório
- Ver `docs/05-redes-seguranca-e-monitoracao.md` (seção wireless) e `labs/wireless/` para roteiros práticos (crie se inexistente).
