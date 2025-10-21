# Capítulo 5 — Controle de Permissões

## Objetivos do capítulo
- Entender a matriz de permissões (proprietário, grupo, outros) e modos especiais.
- Aplicar `chmod`, `chown`, `chgrp` e ACLs.
- Reconhecer riscos de permissões mal configuradas e explorá-los em labs.

## Revisão de conceitos
| Símbolo | Leitura | Significado |
| --- | --- | --- |
| `r` | read | Permite ler arquivo/listar diretório. |
| `w` | write | Permite modificar arquivo/criar/remover no diretório. |
| `x` | execute | Executa binário ou acessa diretório. |

Permissões são representadas em octal (`chmod 750 arquivo`) ou simbólico (`chmod u=rw,g=x,o= arquivo`).

### Mudança de proprietário e grupo
```bash
sudo chown usuario:grupo arquivo
sudo chgrp equipe diretório
```
O capítulo reforça a necessidade de limitar acesso a scripts sensíveis e binários de privilégio elevado.

### Modos especiais
- **Setuid (s)**: executa com permissões do dono. Útil para utilitários que precisam de privilégios, mas podem ser vetor de escalada.
- **Setgid (s)**: herda grupo do diretório. Importante para diretórios colaborativos.
- **Sticky bit (t)**: impede que usuários removam arquivos alheios em diretórios compartilhados (ex.: `/tmp`).

```bash
chmod 4755 /usr/local/bin/meu_binario   # setuid
chmod 2770 /srv/projetos                # setgid
chmod 1777 /compartilhado               # sticky
```

### ACLs e atributos estendidos
- `setfacl -m u:analista:rwx /dados` — atribui permissões adicionais além de `chmod`.
- `getfacl /dados` — exibe ACLs.
- `lsattr`, `chattr` — protegem arquivos críticos (`chattr +a /var/log/auth.log`).

## Segurança ofensiva e defensiva
- **Ofensivo**: localizar arquivos setuid vulneráveis (`find / -perm -4000`) e explorar scripts com permissões excessivas.
- **Defensivo**: implementar baseline de permissões, monitorar alterações com `auditd`, usar `tripwire`/`aide`.

## Exercícios propostos
1. Criar laboratório com diretório compartilhado (`/srv/lab-permissoes`) e simular equipe com grupos distintos.
2. Analisar binários setuid nativos do Kali, documentando propósito e riscos.
3. Configurar ACLs para permitir que usuários não privilegiados coletem logs específicos sem acesso root.

## Referências adicionais
- `man chmod`, `man chown`, `man setfacl`.
- Capítulos correlatos no guia principal: `docs/04-filesystems-permissoes-e-criptografia.md`.
