# Capítulo 10 — Gerenciamento de Filesystem e Dispositivos de Armazenamento

## Objetivos do capítulo
- Identificar discos e partições (`lsblk`, `fdisk`, `parted`).
- Montar e desmontar sistemas de arquivos manualmente.
- Compreender pontos de montagem, UUID e permissões relacionadas.
- Diagnosticar erros de disco e monitorar uso (`df`, `du`).

## Inventário de dispositivos
| Comando | Descrição |
| --- | --- |
| `lsblk -f` | Lista discos, partições, UUID, tipo de FS e pontos de montagem. |
| `fdisk -l` | Exibe tabela de partições (MBR/GPT). |
| `blkid` | Mostra UUIDs e labels. |
| `df -h` | Utilização de espaço por filesystem. |
| `du -sh diretório` | Tamanho de diretório específico. |

### Montagem manual
```bash
sudo mount /dev/sdb1 /mnt/usb
sudo umount /mnt/usb
```
Para montar automaticamente, edite `/etc/fstab` utilizando UUID:
```
UUID=xxxx-xxxx /mnt/dados ext4 defaults,noatime 0 2
```

### Ferramentas adicionais
- `mkfs.ext4 /dev/sdb1` — formata partição.
- `tune2fs -l /dev/sdb1` — exibe parâmetros ext4.
- `fsck /dev/sdb1` — checagem de consistência (execute com partição desmontada).
- `mount -o loop imagem.iso /mnt/iso` — monta imagem ISO para análise.

## Segurança ofensiva e defensiva
- **Ofensivo**: montar rapidamente imagens obtidas em incursões, alterar `fstab` para persistência.
- **Defensivo**: aplicar `noexec`, `nodev`, `nosuid` em partições sensíveis; monitorar integridade com `inotify` e `auditd`.

## Exercícios propostos
1. Criar imagem `loop` (`dd if=/dev/zero of=disco.img bs=1M count=1024`), formatar em ext4 e montar para armazenar evidências.
2. Configurar `tmpfs` em `/tmp` e analisar impacto na performance/segurança.
3. Simular corrupção de filesystem, reparar com `fsck` e documentar processo.

## Complementos no repositório
- Ver `docs/04-filesystems-permissoes-e-criptografia.md` para criptografia (`LUKS`, `eCryptfs`).
- `labs/storage/` pode conter exercícios práticos com VMs e discos virtuais.
