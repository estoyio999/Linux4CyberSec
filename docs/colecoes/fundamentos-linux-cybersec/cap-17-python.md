# Capítulo 17 — Python para Hackers

## Objetivos do capítulo
- Relembrar fundamentos de Python 3 aplicados à segurança.
- Trabalhar com tipos de dados, estruturas de controle e módulos padrão.
- Criar clientes/servidores TCP simples e ferramentas de automação.

## Fundamentos
### Tipos e coleções
- **Strings**, **inteiros**, **floats**: manipulação básica (`len`, `split`, `format`).
- **Listas** (`[]`), **tuplas** (`()`), **dicionários** (`{}`): armazenam dados flexíveis.
- Compreensões de lista para gerar coleções rapidamente.

### Estruturas de controle
```python
for porta in range(1, 1025):
    try:
        sock = socket.socket()
        sock.settimeout(0.5)
        sock.connect((alvo, porta))
        print(f"Porta {porta} aberta")
    except socket.error:
        pass
```

### Funções e módulos
- Organizar código em funções reaproveitáveis (`def`).
- Usar módulos padrão: `os`, `sys`, `subprocess`, `socket`, `struct`, `itertools`.
- Instalar bibliotecas adicionais com `pip`/`venv` quando necessário.

## Projetos de referência
1. **Scanner TCP** — varre portas e registra resultados em arquivo/log.
2. **Listener** — abre socket e aguarda conexões (base para backdoors controlados).
3. **Ferramentas de automação** — interagem com serviços (MySQL, SSH) para coleta de dados.

## Boas práticas
- Utilizar `argparse` para tratar argumentos de linha de comando.
- Exibir mensagens claras e salvar logs (`logging` module).
- Tratar exceções (`try/except`) para evitar travamentos.
- Criar ambientes virtuais (`python -m venv .venv`) para isolar dependências.

## Segurança ofensiva e defensiva
- **Ofensivo**: automatizar exploits, scanners personalizados, backdoors criptografados.
- **Defensivo**: construir ferramentas de triagem, parsing de logs, integração com APIs de threat intelligence.

## Exercícios propostos
1. Adaptar scanner TCP para gerar saída em JSON e enviar para servidor central.
2. Criar script que consome API REST (simulada em `labs/api`) e gera alertas.
3. Escrever sniffer simples com `scapy` que conte pacotes por protocolo.

## Recursos adicionais
- `docs/07-operacoes-ofensivas-e-pentest.md` inclui seções de scripting.
- `scripts/` neste repositório possui exemplos reais; contribua com novas ferramentas documentadas.
