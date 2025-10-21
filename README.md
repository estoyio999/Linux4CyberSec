# Guia Completo de Linux para Cybersec

Este repositório oferece o guia mais abrangente de Linux aplicado à segurança ofensiva e defensiva, com foco no Kali Linux. Cada capítulo foi escrito como um capítulo de livro: introduções conceituais, detalhamentos técnicos, exemplos comentados, estudos de caso e exercícios práticos. O conteúdo cobre desde a instalação básica até kernel, containers e nuvem, com laboratórios integrados e materiais de apoio.

## Como navegar
- Leia os capítulos em ordem numérica para evoluir do básico ao avançado, consolidando fundamentos antes de técnicas complexas.
- Cada capítulo traz **Objetivos de aprendizagem**, seções explicativas aprofundadas, **Estudos de caso** e **Exercícios**.
- Utilize os laboratórios descritos em `docs/08-labs-e-projetos.md` para colocar em prática o conteúdo e registrar resultados em `labs/notas/`.
- Consulte os materiais complementares em `docs/` (roteiro editorial, glossário, linha do tempo e planos de estudo) para orientar sua evolução.

## Estrutura principal
- [`docs/00-roteiro-editorial.md`](docs/00-roteiro-editorial.md) – visão geral da expansão editorial e tópicos avançados.
1. [`docs/01-fundamentos-do-sistema.md`](docs/01-fundamentos-do-sistema.md)
2. [`docs/02-shell-e-automatizacao.md`](docs/02-shell-e-automatizacao.md)
3. [`docs/03-gerenciamento-de-pacotes-e-servicos.md`](docs/03-gerenciamento-de-pacotes-e-servicos.md)
4. [`docs/04-filesystems-permissoes-e-criptografia.md`](docs/04-filesystems-permissoes-e-criptografia.md)
5. [`docs/05-redes-seguranca-e-monitoracao.md`](docs/05-redes-seguranca-e-monitoracao.md)
6. [`docs/06-hardening-e-seguranca-defensiva.md`](docs/06-hardening-e-seguranca-defensiva.md)
7. [`docs/07-operacoes-ofensivas-e-pentest.md`](docs/07-operacoes-ofensivas-e-pentest.md)
8. [`docs/08-labs-e-projetos.md`](docs/08-labs-e-projetos.md)
9. [`docs/09-forense-e-resposta-a-incidentes.md`](docs/09-forense-e-resposta-a-incidentes.md)
10. [`docs/10-kernel-containers-e-nuvem.md`](docs/10-kernel-containers-e-nuvem.md)
11. [`docs/11-anexos-e-recursos.md`](docs/11-anexos-e-recursos.md)

## Materiais complementares
- [`docs/glossario.md`](docs/glossario.md) – termos e siglas recorrentes.
- [`docs/timeline-kali.md`](docs/timeline-kali.md) – marcos históricos do Kali Linux.
- [`docs/plano-estudos.md`](docs/plano-estudos.md) – trilhas de estudo (4, 8 e 12 semanas).
- [`docs/colecoes/fundamentos-linux-cybersec`](docs/colecoes/fundamentos-linux-cybersec) – coleção original em português com fundamentos de Linux aplicados a cybersec, organizada em capítulos com exercícios práticos.

## Convenções gerais
- Os comandos assumem terminal Bash em Kali Linux; adicione `sudo` quando requer privilégios elevados.
- Execute técnicas potencialmente destrutivas em ambientes isolados (VMs, snapshots). Documente todas as ações em notas de laboratório.
- Consulte `AGENTS.md` para diretrizes de contribuição e mantenha o repositório atualizado com pull requests bem documentados.

Boas leituras, bons labs e compartilhe melhorias com a comunidade!
