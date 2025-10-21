# Capítulo 13 — Tornando-se Seguro e Anônimo

## Objetivos do capítulo
- Compreender camadas de anonimato (MAC spoofing, VPN, proxies, Tor).
- Proteger comunicação com criptografia (GPG, e-mail seguro).
- Limpar rastros em sistemas comprometidos e conduzir operações discretas.

## Estratégia de anonimato em camadas
1. **Hardware/OS**: iniciar a partir de mídia limpa, trocar endereços MAC.
2. **Rede local**: utilizar VPN de confiança ou rede corporativa previamente autorizada.
3. **Internet**: encadear proxies/Tor para ocultar IP de origem (ciente das limitações de velocidade e riscos legais).
4. **Aplicações**: usar navegadores hardened (Tor Browser) e práticas de OPSEC (evitar logins pessoais, metadados).

### Tor, proxies e VPN
- Iniciar serviço Tor: `sudo systemctl start tor` (ou usar Tor Browser Bundle).
- Encadear proxy HTTP/SOCKS: `proxychains4 nmap -sT <alvo>`.
- Configurar VPN com `openvpn` (`sudo openvpn --config arquivo.ovpn`). Documentar logs e certificações.

### Criptografia e e-mail seguro
- Gerar par de chaves GPG (`gpg --full-generate-key`).
- Compartilhar chave pública (`gpg --armor --export`).
- Criptografar mensagem: `gpg --encrypt --sign --armor -r destinatario arquivo.txt`.
- Considerar uso de e-mail com suporte a PGP e armazenar chaves com cuidado.

### Higienização de logs
- Identificar histórico (`~/.bash_history`, `~/.mysql_history`, `~/.ssh/known_hosts`).
- Limpar logs com responsabilidade em ambientes de teste; evita exposição de técnicas durante relatórios.
- Em ambientes reais, limpar evidências pode ser ilegal — utilize somente em labs.

## Boa conduta
O autor enfatiza: anonimato é ferramenta de proteção para pesquisadores, não licença para atividades ilícitas. Siga autorizações formais, respeite leis e mantenha trilha auditável em laboratórios.

## Exercícios propostos
1. Configurar cadeia VPN → Tor → Proxy local e avaliar latência versus anonimato.
2. Automatizar rotação de identidades Tor (`torify torsocks`) num script de coleta.
3. Trocar e-mails criptografados entre duas máquinas do laboratório usando GPG e registrar processo em `docs/`.

## Recursos adicionais
- Seção de anonimato em `docs/05-redes-seguranca-e-monitoracao.md`.
- Playbook de OPSEC pode ser criado em `templates/`. Documente sempre que a cadeia for utilizada em testes.
