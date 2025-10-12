# 10. Kernel, Containers e Nuvem

Neste capítulo vamos além do uso cotidiano do Linux e exploramos internals do kernel, técnicas de observabilidade e exploração, segurança de contêineres e estratégias em nuvem. O objetivo é preparar você para ambientes híbridos modernos.

## 10.1 Kernel debugging e observabilidade
### 10.1.1 Ferramentas de baixo nível
- `dmesg -w` monitora mensagens do kernel em tempo real.
- `perf` coleta estatísticas: `perf top`, `perf record`. Use para analisar syscalls.
- `ftrace` (`/sys/kernel/debug/tracing`): ativa tracepoints:
  ```bash
  echo function > /sys/kernel/debug/tracing/current_tracer
  echo do_sys_open > /sys/kernel/debug/tracing/set_ftrace_filter
  cat /sys/kernel/debug/tracing/trace
  ```
- `bpftrace`: linguagem high-level para eBPF. Exemplo: `bpftrace -e 'tracepoint:syscalls:sys_enter_execve { printf("%s %s\n", comm, str(args->filename)); }'`

### 10.1.2 Depuração com kgdb/kdump
- Habilite `CONFIG_KGDB`. Configure console serial para debug remoto.
- `kdump`: captura memória após crash (kexec). Configure `/etc/kdump.conf` e `kdump-tools`.

### 10.1.3 Livepatch
- Kernel livepatch (`kpatch`, `kgraft`) permite aplicar patches sem reboot. Útil em servidores críticos.

## 10.2 Exploração e mitigação
- Use `linux-exploit-suggester`, `lse` para enumerar vulnerabilidades.
- Mitigações: `kernel.unprivileged_userns_clone=0`, `kernel.kptr_restrict=2`, `kernel.dmesg_restrict=1`, `kernel.yama.ptrace_scope=2`.
- Hardened Memory Allocators (Hardened Malloc, `libsafe`), stack canaries, SMAP/SMEP.

## 10.3 Containers em profundidade
### 10.3.1 Namespaces e cgroups
- Namespaces: `pid`, `net`, `mnt`, `ipc`, `uts`, `user`. Experimente com `unshare -Urn bash`.
- cgroups v2: controle de CPU, memória. `systemd-cgls` para visualizar.

### 10.3.2 Segurança e escapes
- Escapes comuns: `docker.sock`, `privileged`, `cap_sys_admin`, `runC` exploits (CVE-2019-5736).
- Hardening Docker Compose:
  ```yaml
  services:
    app:
      image: kalilinux/kali-rolling
      cap_drop: ["ALL"]
      read_only: true
      security_opt:
        - no-new-privileges:true
        - seccomp:./seccomp.json
        - apparmor:docker-default
  ```
- Controle de imagens: `cosign`, `notary`, `in-toto` para assinaturas.
- Escaneie imagens: `trivy`, `grype`, `dockle`, `anchore`.

### 10.3.3 Podman, Buildah, Skopeo
- Podman rootless, compatível com Docker CLI.
- Buildah constrói imagens sem daemon.
- Skopeo gerencia registries (copy, inspect, sync).

### 10.3.4 Kubernetes security
- RBAC mínimo, `kubectl auth can-i` para testar.
- Network Policies (Calico, Cilium) para segmentação.
- Pod Security Standards (Baseline, Restricted) substituindo PodSecurityPolicy.
- Admission Controllers: OPA Gatekeeper, Kyverno.
- Runtime security: Falco, Tracee, Tetragon.
- Supply chain: `Sigstore`, `COSIGN`, `OPA` para policies CI/CD.

## 10.4 Nuvem
### 10.4.1 IAM e Zero Trust
- IAM (AWS, Azure, GCP): políticas JSON, condicionais, tags, roles temporárias (STS/AssumeRole).
- Zero Trust: verificação contínua, acesso baseado em identidade e contexto.
- Ferramentas: `CloudSploit`, `Prowler`, `ScoutSuite`, `Steampipe`.

### 10.4.2 Segurança multi-cloud
- Inventário: `Steampipe` (Postgres + plugins), `CloudMapper` (AWS), `Azure Security Benchmark`, `GCP SCC`.
- Logging: centralize em SIEM com pipelines (Kinesis, EventHub, Pub/Sub).
- Resposta: `AWS SSM`, `Azure Runbooks`, `GCP Cloud Functions`. Scripts com `boto3`, `azure-mgmt`, `google-api-python-client`.

### 10.4.3 Infraestrutura como código
- Terraform: `terraform validate`, `tflint`, `checkov`. Mantenha módulos em `infra/terraform/`.
- Ansible: `ansible-lint`, inventários dinâmicos.
- Pipeline: CI com `pre-commit`, `terragrunt`, `terraform cloud`.

### 10.4.4 Controles específicos
- **AWS**: GuardDuty, Macie, Security Hub, Config, CloudTrail.
- **Azure**: Defender for Cloud, Sentinel, Azure Policy, Log Analytics.
- **GCP**: Security Command Center, Forseti, BeyondCorp Enterprise.
- Siga CIS Benchmarks específicos (AWS Foundational, Azure, GCP).

## 10.5 Estudo de caso: plataforma multi-cloud segura
1. Infra como código com Terraform + Terragrunt, integrando AWS, Azure, GCP.
2. Hardened AMIs com Packer, scripts Ansible.
3. Observabilidade: Prometheus, Grafana, Loki. Logs centralizados no Elastic.
4. Segurança: IAM mínimo, Prowler/ScoutSuite agendados, pipeline de alertas (Security Hub → SNS → Slack).
5. Resposta: Playbooks com AWS SSM, Azure Automation, Cloud Functions.
6. Auditoria: CloudTrail, Azure Activity, GCP Audit exportados para BigQuery/ELK.

## 10.6 Exercícios avançados
1. Use `bpftrace` para traçar syscalls `openat` e `execve`, gerar relatório de processos suspeitos. Combine com Falco.
2. Crie perfil seccomp restritivo para container que executa scanner (permitindo apenas syscalls essenciais). Teste com `oci-seccomp-bpf-hook`.
3. Avalie conta AWS com `Prowler`, exporte resultados, automatize correções com `CloudFormation`/`Terraform`.
4. Implante cluster Kubernetes (k3s), configure NetworkPolicy, RBAC e Falco. Execute ataque de Pod escape (CVE-2022-0492) em ambiente isolado e documente detecção.

> **Referências:** *Linux Observability with eBPF*, *Kubernetes Security* (Liz Rice), `AWS Well-Architected`, `Google Cloud Armor`, blog Aqua Security, Sysdig, Falco, SIG-Security.
