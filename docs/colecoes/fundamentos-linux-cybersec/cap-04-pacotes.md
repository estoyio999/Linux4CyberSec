# Capítulo 4 — Adicionando e Removendo Software

## Objetivos do capítulo
- Trabalhar com o sistema de pacotes Debian/Kali (`apt`, `dpkg`, `apt-cache`).
- Instalar, atualizar e remover ferramentas com segurança.
- Compilar a partir do código-fonte quando não houver pacote disponível.
- Gerenciar repositórios, chaves GPG e dependências.

## Fluxo padrão com `apt`
```bash
sudo apt update              # atualiza lista de pacotes
sudo apt install nmap        # instala pacote
sudo apt upgrade             # aplica atualizações pendentes
sudo apt remove pacote       # remove binários
sudo apt purge pacote        # remove também arquivos de configuração
```
Utilize `apt show` para examinar metadados (descrição, dependências, localização dos arquivos).

### Pesquisas e diagnóstico
- `apt-cache search termo` — busca por nome ou descrição.
- `dpkg -L pacote` — lista arquivos instalados por um pacote.
- `dpkg -S /caminho/arquivo` — identifica qual pacote possui determinado arquivo.

### Repositórios e chaves
Edite `/etc/apt/sources.list` ou use arquivos separados em `/etc/apt/sources.list.d/` para organizar repositórios externos, sempre validando a procedência. Ao adicionar um mirror, importe a chave pública correspondente (`curl ... | gpg --dearmor | sudo tee /usr/share/keyrings/...`).

## Instalação a partir do código-fonte
1. Clonar ou baixar o tarball (`git clone`, `wget`).
2. Instalar dependências de compilação (`build-essential`, `libssl-dev`, etc.).
3. Executar `./configure`, `make`, `sudo make install`.
4. Registrar no CHANGELOG e monitorar binários instalados (normalmente em `/usr/local/bin`).

## Gestão de serviços pós-instalação
- Verificar se o pacote criou usuários/grupos dedicados.
- Revisar arquivos em `/etc` para hardening.
- Criar playbooks de remoção (quais diretórios devem ser limpos, como revogar chaves ou certificados gerados durante testes).

## Exercícios propostos
1. Instalar e configurar o `apache2`, publicar uma página simples e remover sem deixar resquícios.
2. Compilar manualmente uma ferramenta (por exemplo, `hydra`) e comparar com a versão empacotada.
3. Criar script que verifica semanalmente pacotes desatualizados e envia relatório para `logs/`.

## Integração com o repositório
Veja `scripts/gerenciar-pacotes.sh` (caso crie) para automatizar auditorias. Documente qualquer repositório adicional em `docs/11-anexos-e-recursos.md` e mantenha hashes dos artefatos baixados.
