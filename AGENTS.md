# Repository Guidelines

## Product and Authority

REA is a local-only reverse-engineering tool with shared CLI/MCP workflows. Distinguish observations, derivations, inferences, and unknowns; preserve Evidence provenance and session semantics through both adapters. Provider support and setup live in [README.md](README.md#choosing-a-deep-analysis-provider) and its linked guides.

Follow the [contribution scope and fail-closed integration rule](CONTRIBUTING.md#contribution-scope). Do not integrate capabilities while material scope, authority, side-effect, or required verification questions remain unresolved. For new providers or capabilities involving target execution, injection, mutation, or protection recovery, seek the required maintainer scope decision before substantial implementation for integration. Welcome early proposals, small exploratory prototypes, and draft PRs that help resolve questions; they do not authorize integration.

Ghidra uses ephemeral databases without modifying executable bytes or controlling a GUI; Windows Ghidra P0 has no mutation authority. IDA analysis is read-only; never save or close an attached GUI database.

Configuration changes must be additive and idempotent, with backups. Installers must not install or upgrade unrelated software, including Homebrew, Node.js, npm, Java, or Ghidra. Ghidra and IDA are bring-your-own. `rea setup` must show its planned changes and require approval before writing files or installing Hopper.

Preserve caller-selected inputs and local evidence, including output, URLs, paths, digests, and metadata. Redact transport authentication credentials and explicitly marked sensitive values; do not infer secrecy from names or text patterns. Do not persist the ambient environment merely because a child inherits it.

## Architecture and Cleanup

Dependencies flow inward: domain semantics and contracts, provider adapters, shared application workflows, then CLI/MCP adapters. Keep engine protocols and provider-specific code out of domain/application layers. See [docs/architecture.mermaid](docs/architecture.mermaid) when changing composition.

Give each interpretation and resource a clear owner. Consumers must use that owner's parsed facts rather than reinterpret raw source. Preserve identity, omission, uncertainty, and source evidence across boundaries. Retain ownership of resources whose cleanup failed. Reuse `src/process/` supervision and identity primitives; a PID and executable path alone do not establish ownership after exit or reuse.

Keep proof of ownership separate from validity, editability, and readiness. Malformed owned data may still be safely removable. Unresolved identity or applicability must remain unknown rather than be treated as absent, foreign, or aligned.

Assess brittleness in touched code during implementation, debugging, review, and maintenance. Fix the producing representation or ownership rule before adding downstream repairs. Normalize once at the owning boundary using format-aware parsers. Remove superseded representations, aliases, wrappers, and redundant tests after verifying affected consumers. Introduce abstractions only when they remove observed complexity.

When fixing a bug, inspect the producing boundary and affected consumers for duplicated interpretation or unclear ownership. Complete any directly related refactor in the same change and remove superseded code. Passing regression tests alone does not establish that the brittleness is resolved. If related cleanup remains, explain what remains and why.

When addressing a review thread, inspect the producing boundary and affected consumers for brittleness. Complete directly related refactoring in the same change. Before resolving the thread, explain the root cause, structural fix, validation, and any remaining cleanup.

Parse unknown boundary values and model expected failures with the tagged error algebra and `Result`. Preserve partial facts and meaningful failure reasons. Keep identity and lookup values separate from display formatting; preserve source values and explicit unknowns when normalization loses information. Absolute paths, file URLs, and HTTP paths have different semantics.

Validate advertised JSON Schemas after SDK conversion against their declared dialect and actual clients. Bind handlers to named contracts with exact types; catalog ordering must never select an operation or schema.

## Performance

Design analysis workflows and agent interfaces for efficiency from the first
implementation. Correctness, useful answers, Evidence provenance, caller choices,
compatibility and safe resource bounds remain required.

Optimize in this order:

1. Agent tokens and context. Return the smallest sufficient answer with
   necessary provenance, coverage and limitations. Use task selection,
   summaries, paging and retained Evidence where supported; honor requested
   detail and keep complete Evidence accessible. Count the whole task,
   including schemas, extra calls and retries.
2. Analysis time. Reuse validated results and shared work. Cache keys must
   cover result-affecting inputs; define invalidation and lifetime. Validate
   freshness before reporting cached live state as current.
3. CPU and memory. Avoid unnecessary work and copies; bound concurrency,
   retained state and serialization growth.

For changes affecting these costs, measure representative completed tasks and
compare equivalent workflows before and after where available. Report token
usage, elapsed time, CPU time and peak process-family memory. Separate cold
and warm runs, label estimates, and state unmeasured costs. Fewer bytes alone
do not establish fewer tokens or faster analysis.

## Development

Use [.nvmrc](.nvmrc) and `package.json#packageManager` for the pinned toolchain. Cursor Cloud may place older Node.js first on `PATH`; environment setup installs the pinned toolchain under `/usr/local/bin`.

- `npm ci`: install locked dependencies.
- `npm run build:cached`: build the runtime and packaged skill.
- `npm run test:local -- PATH...`: source tests without building.
- `npm run test:focused -- PATH...`: exact tests with their required artifacts.
- `npm run check:fast`: cached typecheck/lint and the pre-push check.
- `npm run docs:check`: validate generated documentation.

Use relevant checks for the change. The complete local gate, `npm run check:pr`, is optional for broad changes or CI diagnosis. See [CONTRIBUTING.md](CONTRIBUTING.md) for development, generated-file ownership, and release conventions.

`docs/public/product-catalog.json`, `docs/verification/managed-conformance-*.json`, and `skills/` are ignored outputs. Edit source contracts and `.agents/skills/reverse-engineer-anything/`, then regenerate as needed. `.cache/mcp-tool-catalog.json` is test metadata; runtime builds and source checking do not consume it. Never commit binaries, provider project documents, credentials, `dist/`, `node_modules/`, or local planning artifacts.

## Tool and Test Changes

For tool design, follow [docs/tool-design.md](docs/tool-design.md): start from the analyst question, reuse existing contracts, preserve caller choices, and return useful evidence inline. Limits must follow real format, protocol, authority, or resource constraints. Account for representation expansion before allocation; derive completeness from examined coverage.

For test changes or provider claims, use [docs/testing.md](docs/testing.md). Prefer real public CLI/MCP workflows, then production-boundary integration, then producer goldens. Keep module tests for distinct semantics stronger workflows cannot reliably reproduce. Preserve malformed-input, cancellation, cleanup, permission, capacity, and target-identity coverage when pruning. Report unverified coverage explicitly; simulated providers do not establish real-provider behavior.

Use Conventional Commit subjects and PR titles. Mark breaking changes with `!` or a `BREAKING CHANGE:` footer. Describe behavior changes, relevant validation, and real-provider verification scope.

<!-- BEGIN:turborepo-agent-rules -->

# This is NOT the Turborepo you know

Turborepo configuration, task behavior, and CLI commands can vary between installed versions and may differ from your training data. Resolve the `turbo` package from this file's directory or relevant workspace; in monorepos, it may not be visible from the repository root. For example, run `node -p "require.resolve('turbo/package.json')"` from a workspace that depends on `turbo`.

Read `docs/README.md` inside that installed package first, then read the relevant pages from its `docs/` directory before changing Turborepo configuration or commands. Heed deprecation notices. These bundled docs match the installed package version and are available without network access.

This block is written and re-added by `turbo` before repository-scoped commands when an AI agent is detected. In the Turborepo source repository, its template is defined in `crates/turborepo-cli/src/cli/agent_guidance.rs`. Removing the managed block while updates are enabled means a later qualifying invocation will add it again. Set `"agentGuidance": false` in the root `turbo.json` or `turbo.jsonc` to opt out; this does not remove an existing block. Keep the block committed with your work to avoid an uncommitted change on the next agent invocation.
<!-- END:turborepo-agent-rules -->

<!-- security-kit:start (gerado por microsaas-security-kit/scripts/apply.py; edite fora destes marcadores) -->
## Protocolo de Segurança MicroSaaS & Hardening Universal

Este repositório segue o protocolo em 2 camadas e a **Matriz de Prontidão para Produção (7 Domínios / 7 Pilares)** do `microsaas-security-kit` (guias vigentes: `protocol/two_tier_guide.md` e `protocol/production_ready_matrix.md` do kit). Código que viole as regras abaixo não entra; se um pedido conflitar com elas, explique o risco e proponha a alternativa segura. Para o método geral (OWASP, STRIDE, auditoria de dependências), use também a skill `security-and-hardening`.

### Camada 1 — sempre (Hardening & Resiliência Universal)
1. **Dependências mínimas (Pilar 1 — Rule of 10 Lines)**: se dá para fazer em até ~10–15 linhas com a biblioteca padrão (Python: `urllib.request`, `json`, `hashlib`, `hmac`, `pathlib`, `secrets`, `logging`, `contextvars`, `ctypes`; JS/TS: `fetch`, `URL`, `Intl`, `crypto.subtle`, `FormData`), não adicione pacote. Pacote inevitável: consagrado, mantido e com lockfile validado (`uv lock --check`, `npm ci`, `pnpm/bun --frozen-lockfile`, `cargo check --locked`).
2. **Zero segredo no código & Fail-Fast no Boot (Pilar 2)**: nada de API key, token, senha ou URI com senha literal (`SECRET-01`, `SECRET-02`). Use variável de ambiente (`os.environ`, `process.env`), cofre nativo do SO (DPAPI/Credential Manager) ou Secret Manager, validando variáveis obrigatórias no boot (fail-fast — `blueprints/config/env_failfast.py`). Chave pública por design (ex.: `apiKey` web do Firebase) é aceita, desde que as Security Rules sejam restritivas.
3. **Docker sem root (Pilar 2)**: usuário sem privilégio (`appuser`, UID 10001) antes de `CMD`/`ENTRYPOINT` (`DOCKER-01`); suporte a `--read-only`.
4. **Anti-prompt injection & FinOps Básico em IA (Pilar 2 e 5)**: dado de terceiro nunca entra no prompt `system` (`AI-01`); vai no papel `user`, entre delimitadores XML sanitizados. Toda chamada de LLM deve declarar `max_tokens` / `max_output_tokens` explícito (`FINOPS-01`) e validar tamanho de prompt.
5. **Timeouts, Retry & Proibição de `catch`/`except` Vazio (Pilar 3, 4 e 6)**: toda chamada HTTP/externa exige `timeout` explícito (`RES-01`) e retry com exponential backoff (`blueprints/resilience/http_retry_timeout.py`). Proibido silenciar exceções (`except: pass` / `catch () {}` vazio — `OBS-01`), expor stack traces ao usuário ou gravar PII/segredos em logs.
6. **Segurança Universal Local/Windows (SQLite, Navegador, Subprocessos & Arquivos)**:
   - **SQLite Defensivo (`LOCAL-SQL-01`)**: toda conexão SQLite deve aplicar `PRAGMA trusted_schema = OFF; PRAGMA foreign_keys = ON; PRAGMA secure_delete = ON; PRAGMA temp_store = MEMORY;`. Proibido copiar `.db`/`.sqlite` ativo com `shutil.copy`/`fs.copyFile` (usar `backup()` ou `VACUUM INTO`).
   - **Navegador/WebView Hardened (`LOCAL-BROWSER-01`)**: proibido `--no-sandbox`, `--disable-web-security`, `--remote-allow-origins=*` (também via `appendSwitch` do Electron e `AdditionalBrowserArguments` do WebView2), `webPreferences` com `webSecurity`/`sandbox: false`, `nodeIntegration: true` ou `contextIsolation: false`, e portas CDP TCP fixas (`--remote-debugging-port=9222`); usar `--remote-debugging-pipe` e perfil efêmero.
   - **IPC sem Segredos em CLI (`LOCAL-IPC-01`)**: proibido passar senhas/chaves/tokens em argumentos de linha de comando (`argv` visível via PEB/WMI), inclusive em lista montada antes da chamada, `os.system(f"...")`, `mysql -p<senha>`, Node `child_process` e PowerShell; usar pipe anônimo (`stdin`, ex.: `--password-stdin`) ou arquivo com ACL restrita (`--secret id=...,src=...`).
   - **Anti-ZipSlip & NTFS Reparse Points (`LOCAL-FS-01`)**: zip com `safe_extract_zip` ou `validate_archive_member` por membro; tar com `extractall(dest, filter="data")`; rejeitar symlinks/*NTFS Reparse Points* antes de extrair arquivos ou abrir staging (`blueprints/windows_local/safe_archive.py`).

### Camada 2A — quando houver API, banco multi-tenant, IA em produção ou cloud (Matriz Executiva de 7 Domínios)
- **Segurança & Isolamento (Domínio 3)**: **IAM mínimo** (nada de `roles/owner`/`roles/editor`), **isolamento por `tenant_id`** (PostgreSQL com `ENABLE` + `FORCE ROW LEVEL SECURITY` — `SQL-RLS-01`; Firestore/Supabase com rules/policies autenticadas — `FIRESTORE-01`), **Qdrant com filtro obrigatório de `tenant_id`** (`QDRANT-01`), **APIs com validação estrita, rate limiting e autenticação** (`API-AUTH-01`; só `/health` e `/readiness` abertos).
- **Dados, Migrations & Idempotência (Domínio 2 / Pilar 3)**: alterações de banco via migrations versionadas e reversíveis (`UP`/`DOWN`), mutações críticas/financeiras em transações atômicas com `idempotency_key` (`blueprints/resilience/migrations_and_idempotency.sql`).
- **Observabilidade, Performance & FinOps (Domínios 5 e 6 / Pilares 4 e 5)**: logs estruturados em JSON com `correlation_id` e redação automática de PII (`blueprints/observability/json_logger.py`), endpoints `/health` e `/readiness` reais (`blueprints/observability/health_readiness.py`), paginação obrigatória (`limit` + `offset`/`cursor` — `API-PAGE-01`) e hard cap de orçamento de IA por tenant com fallback gracioso (`blueprints/finops/llm_cost_guard.py`).
- **Operação, Webhooks & LGPD (Domínio 7 / Pilar 6)**: webhooks autenticados via HMAC-SHA256 (`hmac.compare_digest`) e processados de forma assíncrona e idempotente; suporte a exclusão lógica (`deleted_at`) e rotina de anonimização de PII (LGPD).

### Camada 2B — quando houver App Desktop/GUI, Cofre Local SQLite, Automação Web ou Parsers de Arquivos no Windows
- **Cofre SQLite/SQLCipher & ACL NTFS (`LOCAL-SQL-02` / `LOCAL-ACL-01`)**: dados locais sensíveis/PII/financeiros criptografados com SQLCipher v4 pelo driver `sqlcipher3` (dependência opcional; o `sqlite3` da stdlib ignora `PRAGMA key` em silêncio, então abrir com chave aborta se `PRAGMA cipher_version` vier vazio), migração verificada por *Logical SHA-256 Fingerprint* e ACL NTFS restrita ao SID do usuário no arquivo, nos `-wal`/`-shm`/`-journal` e na pasta do cofre (`icacls.exe` da pasta do sistema, `/inheritance:r`, falha = erro — `blueprints/windows_local/sqlite_hardened_backup.py`).
- **Chaves em Hardware/SO & Blindagem de RAM (`LOCAL-CRED-01` / `LOCAL-MEM-01`)**: proibido guardar chaves mestras de cofres locais em `.env` no disco; selar via **Windows DPAPI (`CryptProtectData` com entropia/prompt)** — o que o blueprint implementa — ou Credential Manager (TPM 2.0 / Windows Hello via `NCrypt` não estão implementados no kit). Travar chave em RAM com `VirtualLock` + `WerRegisterExcludedMemoryBlock`, acessar só por `view()`/`borrow()` (memoryview, sem cópia em `bytes`), zerar no `close`/`finally` e aplicar **Auto-Lock por inatividade (15 min)** disparado por timer (`blueprints/windows_local/dpapi_tpm_vault.py`).
- **Sandbox de Navegador, Clipboard & Parsers (`LOCAL-SANDBOX-01` / `LOCAL-CLIP-01`)**: navegador automatizado atrás do **Pinned Egress Proxy** local (`PinnedEgressProxy`: só `CONNECT` para host da allowlist na 443, no IP fixado e público) com `--proxy-bypass-list=<-loopback>` (senão o Chromium manda loopback/link-local direto), WebRTC sem UDP fora do proxy e `--host-resolver-rules`; Clipboard sensível com `ExcludeClipboardContentFromMonitorProcessing` + `CanIncludeInClipboardHistory = 0` e limpeza agendada num processo que sobrevive à CLI (`blueprints/windows_local/browser_egress_guard.py`). Parsers de PDF/XLSX/ZIP em **Windows Job Objects** (processo criado suspenso e só retomado dentro do job; tetos de RAM/processos, `JOB_OBJECT_UILIMIT_ALL`, saída limitada a 64 KiB e `TerminateJobObject` no timeout — `blueprints/windows_local/job_object_sandbox.py`). O Job Object **não** isola rede nem sistema de arquivos: risco residual em `ADR-SEC-01`. Arquivos compactados: `safe_extract_zip` / `tar.extractall(filter="data")` (`blueprints/windows_local/safe_archive.py`).
- **Backups Verificados, Recovery Key & Riscos Residuais (`LOCAL-BACKUP-01` / `LOCAL-RECOVERY-01` / `ADR-SEC-01`)**: pacotes `.vbackup` com payload cifrado (`sqlcipher_export`; backup em texto puro só com `allow_plaintext=True`) + `HMAC-SHA256`, **teste de restauração imediato em diretório temporário**, restore atômico (temporário → verificação → remove `-wal`/`-shm` antigos → `os.replace`), **Recovery Key Offline Base32 com Checksum (`WVRK1-...`)** e documentação explícita dos riscos residuais do modelo de ameaça (`protocol/ADR-SEC-01_riscos_residuais_2b.md`).

### Playbooks por arquétipo e blocos do SDK (Kaizen)
- Antes de planejar ou codar, leia o playbook do arquétipo indicado no início da sessão (`microsaas-security-kit/playbooks/INDEX.md` lista todos). Sem arquétipo definido, escolha um no INDEX e grave o id em `.kaizen-archetype` na raiz.
- Antes de escrever utilitário do zero, procure em `microsaas-security-kit/blueprints/functional/`.
- Regra 1.2 (Anti-Bypass Guard): mudanças em `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, playbooks e templates só com aprovação humana explícita.

### Verificação
```bash
python "C:/Users/davi_/projetos/microsaas-security-kit/scripts/audit_project.py" . --layer auto --fail-on critical
```
Falso positivo: comente `security-kit: ignore <REGRA>` na própria linha, com o motivo.
<!-- security-kit:end -->
