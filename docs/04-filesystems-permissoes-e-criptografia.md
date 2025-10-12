# 04. Filesystems, Permissões e Criptografia

Este capítulo aprofunda a gestão de armazenamento em Linux com foco em segurança: exploramos internals de sistemas de arquivos, atributos avançados, técnicas de criptografia em múltiplas camadas e estratégias defensivas para proteger evidências e evitar persistências ocultas.

## 4.1 Internals de filesystems
### 4.1.1 Journaling e inodes
- `ext4` utiliza journaling para reduzir corrupção. O modo padrão (`ordered`) registra metadados antes dos dados. Ajuste em `/etc/fstab` com `data=journal` quando integridade for crítica (custo de performance).
- Inodes armazenam metadados (permissões, timestamps). Use `stat arquivo` para ver detalhes. Ext4 suporta nanosegundos, útil para detectar timestomping.
- `debugfs` permite inspeção granular:
  ```bash
  sudo debugfs -R 'stat <inode>' /dev/sda2
  ```

### 4.1.2 Atributos estendidos e extended timestamps
- `stat -c %x %y %z arquivo` mostra acessos, modificações e alterações de metadados.
- `touch -a -m` pode manipular timestamps; detecte inconsistências comparando logs (`journalctl`) e `stat`.
- Extended attributes (`getfattr`, `setfattr`) guardam dados arbitrários; malwares utilizam xattrs para persistir payloads.

### 4.1.3 Ferramentas de carving
- `foremost`, `scalpel`, `photorec` recuperam arquivos de discos, baseados em assinaturas.
- `bulk_extractor` varre superficialmente buscando padrões (e-mails, URLs, cartões). Combine com `hashdeep` para correlacionar com listas de hash.

## 4.2 Permissões avançadas
### 4.2.1 Bits especiais e sticky bit
- `chmod 1777 /tmp` garante que apenas o proprietário remove seus arquivos (sticky bit = 1).
- `setuid` (`4xxx`) e `setgid` (`2xxx`) executam com permissões do proprietário/grupo. Audite com `find / -perm -4000` e `-2000`. Ferramentas como `linpeas` enumeram automaticamente.

### 4.2.2 ACLs e máscaras
Cada ACL tem uma máscara que funciona como "permissão máxima". Ajuste com `setfacl -m m::rwx arquivo`. Se a máscara for `r-x`, qualquer permissão maior será aparada. Use `getfacl arquivo` para verificar.

### 4.2.3 Capabilities e bounding sets
- Capabilities se aplicam tanto a binários quanto a processos. `getpcaps <PID>` (do pacote `libcap2-bin`) mostra capabilities ativas.
- Restrinja capabilities globais com `sysctl`. Ex.: `fs.suid_dumpable = 0` impede que processos setuid gerem core dumps com dados sensíveis.

## 4.3 Criptografia
### 4.3.1 LUKS1 vs LUKS2
- **LUKS1**: cabeçalho fixo, 8 slots de chave, PBKDF2.
- **LUKS2**: cabeçalho flexível (JSON), backup integrado, suporta Argon2, tokens YubiKey/Tang, autenticadores TPM2.
- Atualize com `cryptsetup convert --type luks2 /dev/sdxY` (faça backup antes!).

### 4.3.2 Keyslots e políticas
- Adicione múltiplas senhas/chaves:
  ```bash
  sudo cryptsetup luksAddKey /dev/sdb1
  ```
- Utilize `cryptsetup luksDump` para exibir slots ocupados e KDFs. Em ambientes corporativos, combine com `clevis` para desbloqueio automatizado em servidores (TPM/Tang).

### 4.3.3 fscrypt e eCryptfs
- `fscrypt` (kernel 5.4+) permite criptografia por diretório com chaves derivadas do login. Configure `/etc/fscrypt.conf` e inicialize com `fscrypt setup`. Útil para proteger `~/Documentos` sem cifrar disco inteiro.
- `eCryptfs` é legado (usado pelo `ecryptfs-mount-private`); ainda relevante para compatibilidade.

### 4.3.4 Criptografia em rede
- Combine `sshfs` + `LUKS` para montar volumes remotos cifrados localmente.
- Para sincronização, use `rclone` com `crypt` (cifrando antes de enviar para nuvem).

## 4.4 Integração com sistemas distribuídos
- **GlusterFS**/`cephfs`: suportam replicação. Integre com TLS, controle de acesso e quotas.
- **ZFS**: snapshots, envio incremental (`zfs send/recv`), compressão, dedupe. Combine com `zfs allow` para delegar permissões.
- **Btrfs**: snapshots baratos, subvolumes. Configure `btrfs scrub` para detectar corrupção.

## 4.5 Ocultação e detecção
### 4.5.1 Técnicas ofensivas comuns
- `LD_PRELOAD` para carregar bibliotecas maliciosas (persistência ou ocultação de processos). Detecte comparando `ldd` e ou `strace`.
- Alternate data streams em NTFS montado (`setfattr -n user.stream -v "payload" arquivo`). Monitorar atributos.
- `timestomping` altera timestamps; correlacione com `journalctl` e `last -f`.

### 4.5.2 Contramedidas
- Habilite `auditd` para monitorar execuções de `chmod`, `chown`, `setfacl`.
- Use `inotifywait`/`fswatch` para detecção em tempo real.
- `tripwire`/`aide` criam baseline de hashes. Armazene base criptografada fora do host analisado.

## 4.6 Gestão de backups seguros
- Combine snapshots (`btrfs`, `zfs`) com backups offsite (borg, restic). Configure repos cifrados.
- Documente políticas: RPO (Recovery Point Objective), RTO (Recovery Time Objective).
- Teste restauração regularmente (executar `borg extract --dry-run`).

## 4.7 Estudo de caso: cofre de evidências
1. Volume LUKS2 em disco dedicado (`/dev/sdb`), com 3 keyslots (analistas, processo judicial, emergência).
2. Sistema de arquivos ext4 com `acl`, `user_xattr` habilitados.
3. ACLs que separam times (`setfacl -m g:redteam:rwx, g:blueteam:rx`).
4. Logs de acesso via `auditd` e `inotifywatch` exportados para Elastic.
5. Snapshot diário Btrfs + envio incremental criptografado com `borg` para cofre offline.

## 4.8 Exercícios avançados
1. Compare `xfs`, `ext4` e `btrfs` montando três VMs, executando `fio` e `bonnie++` e registrando prós/contra para logs e workloads forenses.
2. Configure volume LUKS2 com desbloqueio via `clevis` (TPM2). Documente fluxo manual e automatizado, incluindo rotação de chaves.
3. Detecte arquivos com atributos suspeitos (`+i`, capabilities incomuns), crie relatório com `find` + `getcap` + `getfattr`, e automatize envio diário.
4. Monte imagem `.E01` com `ewfmount`, calcule timeline com `fls` + `mactime`, identifique sinais de timestomping comparando com `journalctl` exportado.

> **Referências:** EXT4 docs, *Linux Kernel Documentation* (fscrypt, security), NIST SP 800-88 (sanitização), SANS Digital Forensics Poster.
