# Capítulo 15 — Kernel Linux e Módulos Carregáveis

## Objetivos do capítulo
- Entender a arquitetura do kernel e o papel de módulos (`.ko`).
- Listar, carregar e descarregar módulos dinamicamente.
- Ajustar parâmetros com `sysctl` e arquivos em `/proc`.
- Compreender riscos de rootkits baseados em kernel e como monitorá-los.

## Visualização e gerenciamento de módulos
| Comando | Função |
| --- | --- |
| `uname -r` | Exibe versão do kernel em uso. |
| `lsmod` | Lista módulos carregados. |
| `modprobe módulo` | Carrega módulo e dependências. |
| `modprobe -r módulo` | Remove módulo em execução. |
| `modinfo módulo` | Exibe descrição, versão, dependências e parâmetros. |

### Carregamento manual
```bash
sudo modprobe rtl8187
sudo modinfo rtl8187 | grep version
```
Para carregar módulo customizado: compilar código (`make`) e copiar para `/lib/modules/$(uname -r)/extra/`, rodar `depmod -a`.

### Tunables do kernel
- `/proc/sys/` expõe parâmetros em tempo real. Exemplo: `cat /proc/sys/net/ipv4/ip_forward`.
- `sysctl -w net.ipv4.ip_forward=1` ativa roteamento; persistir em `/etc/sysctl.conf`.

### Segurança
- **Ofensivo**: carregar módulos maliciosos para keylogging/persistência; esconder processos/portas.
- **Defensivo**: usar `lsmod`, `dmesg` e `journalctl -k` para identificar carregamentos suspeitos; ativar `module signing` e `module locking` (`sysctl kernel.modules_disabled=1`).

## Exercícios propostos
1. Compilar módulo simples "hello world", carregar e verificar mensagens em `dmesg`.
2. Habilitar IP forwarding, configurar `iptables` para roteamento e avaliar impacto.
3. Pesquisar rootkits famosos (ex.: `phalanx`, `reptile`) e elaborar checklist de detecção.

## Recursos adicionais
- Capítulo correspondente em `docs/10-kernel-containers-e-nuvem.md`.
- `labs/kernel/` pode abrigar experimentos com módulos customizados (garanta ambiente isolado antes de testar).
