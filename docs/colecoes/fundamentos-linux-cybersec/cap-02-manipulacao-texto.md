# Capítulo 2 — Manipulação de Texto

## Objetivos do capítulo
- Visualizar e recortar arquivos grandes com comandos nativos.
- Criar filtros com `grep`, `cut`, `sort`, `uniq` e `wc`.
- Usar `sed` e `awk` para transformar conteúdo.
- Preparar wordlists e arquivos de configuração para ataques ou auditorias.

## Ferramentas principais
| Comando | Função | Dicas |
| --- | --- | --- |
| `head`, `tail` | Mostram início/fim do arquivo | `tail -f` acompanha logs em tempo real. |
| `less` | Paginação interativa | `/termo` para buscar, `F` para seguir novas linhas. |
| `grep` | Filtra linhas por padrão | `-i` ignora maiúsculas, `-r` busca recursiva. |
| `cut`, `awk` | Selecionam colunas | Use `-d` para informar delimitador. |
| `sort`, `uniq` | Ordenam e removem duplicatas | Combine `sort | uniq -c` para contar ocorrências. |
| `wc` | Conta linhas/palavras/caracteres | `wc -l` é útil para saber o tamanho de listas. |

## Fluxos comuns
### Sanitizar logs
```bash
grep -i "denied" /var/log/auth.log | cut -d' ' -f1-4,9- | tee denied.log
```
Explicação: filtra entradas com "denied", conserva timestamp e mensagem, e grava resultado num arquivo para análise posterior.

### Preparar wordlists
```bash
cat /usr/share/wordlists/rockyou.txt | tr '[:upper:]' '[:lower:]' | sort | uniq > wordlist-normalizada.txt
```
Listas customizadas aumentam significativamente a eficácia de ataques de força bruta; ajuste-as ao alvo e mantenha registros para auditoria.

### Gerar relatórios rápidos
```bash
awk '{print $1}' /etc/passwd | sort | uniq > usuarios.txt
```
Esse padrão aparece novamente em capítulos de automação (bash e Python).

## Expressões regulares básicas
- `.` corresponde a qualquer caractere.
- `*` significa "zero ou mais repetições".
- `[0-9]` representa faixa numérica (0–9). Combine com `+` para um ou mais caracteres.
- Use `grep -E` para habilitar regex estendida.

## Casos de uso ofensivos e defensivos
- **Ofensivo**: extrair versões de serviços em banners (`nc host 80 | grep "Server"`).
- **Defensivo**: varrer logs de firewall em busca de IPs suspeitos e gerar bloqueios automatizados.

## Exercícios propostos
1. Criar script que receba arquivo log e devolva as cinco mensagens mais recorrentes com contagem.
2. Converter um CSV simples em tabela Markdown usando `awk`.
3. Combinar `grep` com regex para identificar senhas em arquivos `.pcap` convertidos para texto.

## Complementos no repositório
- Consulte `docs/02-shell-e-automatizacao.md` para aprofundar pipelines e expressões regulares.
- Use os laboratórios em `labs/text-manipulation/` (crie se necessário) para praticar com arquivos reais.
