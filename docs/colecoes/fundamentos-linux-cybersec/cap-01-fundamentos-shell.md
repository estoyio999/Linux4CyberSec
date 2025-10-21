# Capítulo 1 — Iniciando com o Básico

## Objetivos do capítulo
- Navegar pela hierarquia de diretórios do Linux usando terminal.
- Diferenciar comandos de inspeção (`pwd`, `ls`, `whoami`) de comandos de manipulação.
- Criar, copiar, mover e remover arquivos com segurança.
- Entender o conceito de superusuário e quando usar `sudo`.

## Conceitos essenciais
### Terminal vs. interface gráfica
Dominar o terminal é essencial porque:
- scripts e ferramentas de segurança costumam ser distribuídos como executáveis de linha de comando;
- logs e artefatos forenses ficam em `/var/log` e outros diretórios acessados via shell;
- automações (bash, Python) executam comandos que dependem do terminal.

### Navegação e inspeção
| Ação | Comando | Observações |
| --- | --- | --- |
| Descobrir diretório atual | `pwd` | Retorna o caminho absoluto. |
| Listar conteúdo | `ls`, `ls -al` | Use `-a` para visualizar arquivos ocultos. |
| Entrar em diretório | `cd /caminho` | `cd -` alterna entre diretórios. |
| Identificar usuário atual | `whoami` | Complementa a saída de `id`. |

Para localizar arquivos, explore `locate`, `find` e `which`, aproveitando o banco de dados do `locate` e expressões do `find` para buscas mais granulares (permissões, datas, tipos).

### Manipulação de arquivos
- **Criação**: `touch arquivo`, `cat > arquivo` (modo interativo). Use `Ctrl+D` para finalizar.
- **Visualização**: `cat`, `less`, `head`, `tail`. Combine com `grep` para filtrar.
- **Cópia e movimento**: `cp origem destino`, `mv origem destino`. Explique a diferença entre renomear e mover.
- **Remoção**: `rm arquivo`, `rm -r diretório`. Use `rm -rf` com extremo cuidado e valide com `ls` antes de executar comandos destrutivos.

### Pipes e redirecionamentos
O capítulo introduz `|`, `>`, `>>` para conectar comandos e armazenar resultados:
```bash
ps aux | grep apache2 > processos-apache.txt
```
Esse padrão é reutilizado em capítulos posteriores para criar pipelines de coleta de evidências.

### Privilégios elevados
`sudo` é a forma controlada de executar comandos administrativos sem alternar para a conta `root`. Edite `/etc/sudoers` com `visudo`, documente comandos sensíveis e, mesmo no Kali, prefira trabalhar como usuário comum escalando privilégios somente quando necessário.

## Boas práticas destacadas
- Trabalhe com duas abas: uma para comandos, outra para logs/edição.
- Use histórico (`history`, `Ctrl+R`) para reduzir erros de digitação.
- Adote nomenclatura clara (por exemplo, `projeto_lab01.sh`) para facilitar buscas.

## Exercícios propostos
1. Mapear diretórios principais do sistema com `tree` ou `find / -maxdepth 2` e registrar observações.
2. Criar estrutura de pastas em `~/labs/cap01/`, adicionar arquivos, copiar para `/tmp` e remover com segurança.
3. Redirecionar saídas de `ifconfig` e `netstat` para arquivos e comparar com `diff`.

## Relação com o guia Linux for Cybersec
Os mesmos fundamentos aparecem em `docs/01-fundamentos-do-sistema.md`. Use este capítulo como revisão rápida antes de avançar para scripts e laboratórios. Ao dominar esses comandos, você acompanha tópicos avançados sem depender de interfaces gráficas.
