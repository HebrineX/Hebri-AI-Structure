# Apéndice · Operación, Seguridad y Auditoría de Harness 0.16.0

> Anterior: [Apéndice · Ejemplo end-to-end](./apendice-ejemplo-end-to-end.md)

Este apéndice define cómo auditar, regularizar y operar proyectos que usan
`Hebri-AI-Harness 0.16.0`.

No reemplaza al harness. Explica cómo verificar que un proyecto lo está
respetando.

---

## 1 · Criterio de Cumplimiento

| Veredicto | Criterio |
|---|---|
| `cumple` | Estructura 0.16.0 presente, binding correcto, contrato declarado, state/registry coherentes, Agent Contract System activo, runtime enforcement activo, seguridad deny-by-default, approval store/gateway/hooks/MCP/integraciones host aplicados si corresponden, medicion de uso disponible, migración aplicada si corresponde, preflight/approvals/gates/evidencia/cierre de agentes reales |
| `parcial` | Estructura instalada, pero evidencia P0 incompleta o estado inconsistente |
| `no cumple` | Falta `.hebrinex/`, faltan controles P0, o el flujo ejecuta sin contrato/aprobación |

Un proyecto migrado puede quedar en `parcial`: la estructura existe, pero los
ciclos viejos no tienen evidencia P0. Eso es aceptable solo si se marca como
`legacy_unverified` y el cumplimiento estricto empieza desde el próximo ciclo.

---

## 2 · Auditoría de Estructura

Archivos mínimos de `Hebri-AI-Harness 0.16.0`:

```text
.hebrinex/PROJECT_BINDING.yaml
.hebrinex/AGENTS.md
.hebrinex/prompts/migration/migrar-harness-0-8-10-a-0-9-0.prompt.md
.hebrinex/scripts/validate-harness.ps1
.hebrinex/scripts/validate-agent-contracts.ps1
.hebrinex/scripts/validate-security-policy.ps1
.hebrinex/scripts/validate-migration.ps1
.hebrinex/scripts/validate-state-machine.ps1
.hebrinex/scripts/validate-agent-runtime.ps1
.hebrinex/scripts/validate-command-gateway.ps1
.hebrinex/scripts/validate-mcp.ps1
.hebrinex/scripts/audit-harness.ps1
.hebrinex/scripts/install-host-integrations.ps1
.hebrinex/scripts/regularize-state.ps1
.hebrinex/scripts/regularize-registry.ps1
.hebrinex/scripts/state-machine.ps1
.hebrinex/scripts/agent-runtime.ps1
.hebrinex/scripts/command-gateway.ps1
.hebrinex/scripts/claude-pretooluse-hook.ps1
.hebrinex/scripts/install-claude-hooks.ps1
.hebrinex/scripts/lib/hebri-common.psm1
.hebrinex/.mcp.json
.hebrinex/mcp/package.json
.hebrinex/mcp/package-lock.json
.hebrinex/mcp/README.md
.hebrinex/mcp/model-pricing.yaml
.hebrinex/mcp/agents-backend.yaml
.hebrinex/mcp/agent-backends.mjs
.hebrinex/mcp/server.mjs
.hebrinex/mcp/smoke.mjs
.hebrinex/.claude/agents/auditor-detractor.md
.hebrinex/.claude/agents/reviewer.md
.hebrinex/agents/worker.md
.hebrinex/orquestador/harness-manifest.txt
.hebrinex/orquestador/context-budget.yaml
.hebrinex/orquestador/prompt-registry.yaml
.hebrinex/orquestador/registry-index.yaml
.hebrinex/orquestador/adapter-registry.yaml
.hebrinex/orquestador/context-profile-registry.yaml
.hebrinex/orquestador/gate-registry.yaml
.hebrinex/orquestador/policy-registry.yaml
.hebrinex/orquestador/template-registry.yaml
.hebrinex/orquestador/agents/agent-registry.yaml
.hebrinex/orquestador/agents/capability-registry.yaml
.hebrinex/orquestador/agents/lifecycle-registry.yaml
.hebrinex/orquestador/agents/model-adapter-profiles.yaml
.hebrinex/orquestador/agents/role-contracts/leader.yaml
.hebrinex/orquestador/agents/role-contracts/implementer.yaml
.hebrinex/orquestador/agents/role-contracts/reviewer.yaml
.hebrinex/orquestador/agents/role-contracts/auditor.yaml
.hebrinex/orquestador/agents/role-contracts/reporter.yaml
.hebrinex/orquestador/agents/role-contracts/spec-author.yaml
.hebrinex/orquestador/agents/role-contracts/worker.yaml
.hebrinex/orquestador/agents/security-profiles/read-only.yaml
.hebrinex/orquestador/agents/security-profiles/write-scoped.yaml
.hebrinex/orquestador/agents/security-profiles/reviewer-readonly.yaml
.hebrinex/orquestador/agents/security-profiles/auditor-blocking.yaml
.hebrinex/orquestador/agents/runtime-profiles/
.hebrinex/orquestador/agents/context-packs/
.hebrinex/orquestador/agents/tool-packs/
.hebrinex/orquestador/agents/playbooks/
.hebrinex/orquestador/agents/failure-modes/
.hebrinex/orquestador/agents/evaluation-rubrics/
.hebrinex/orquestador/agents/handoff-contracts/
.hebrinex/orquestador/security/permission-registry.yaml
.hebrinex/orquestador/security/command-risk-registry.yaml
.hebrinex/orquestador/security/write-scope-registry.yaml
.hebrinex/orquestador/security/network-policy.yaml
.hebrinex/orquestador/security/secrets-policy.yaml
.hebrinex/orquestador/security/supply-chain-policy.yaml
.hebrinex/orquestador/security/escalation-policy.yaml
.hebrinex/orquestador/security/logging-policy.yaml
.hebrinex/orquestador/security/threat-model.yaml
.hebrinex/orquestador/migration/migration-registry.yaml
.hebrinex/orquestador/migration/versions/0.9.0-to-0.10.0.yaml
.hebrinex/orquestador/migration/versions/0.8.10-to-0.10.0.yaml
.hebrinex/orquestador/migration/versions/0.10.11-to-0.11.0.yaml
.hebrinex/orquestador/migration/versions/0.11.0-to-0.12.0.yaml
.hebrinex/orquestador/migration/versions/0.12.0-to-0.13.0.yaml
.hebrinex/orquestador/migration/versions/0.13.0-to-0.14.0.yaml
.hebrinex/orquestador/migration/versions/0.14.0-to-0.15.0.yaml
.hebrinex/orquestador/migration/versions/0.15.0-to-0.16.0.yaml
.hebrinex/orquestador/migration/contracts/post-migration-contract.yaml
.hebrinex/orquestador/migration/reports/migration-report.template.yaml
.hebrinex/orquestador/method/session-contract.md
.hebrinex/orquestador/method/harness-resolution.md
.hebrinex/orquestador/method/evidence-reconstruction.md
.hebrinex/orquestador/method/changelog-policy.md
.hebrinex/orquestador/method/deploy-migration-policy.md
.hebrinex/orquestador/method/reference-drift-policy.md
.hebrinex/orquestador/method/ci-pipeline-policy.md
.hebrinex/orquestador/method/backlog-policy.md
.hebrinex/orquestador/method/audit-reporting-policy.md
.hebrinex/orquestador/method/final-report-evidence-policy.md
.hebrinex/orquestador/method/ai-preset-policy.md
.hebrinex/orquestador/method/memory-layer-policy.md
.hebrinex/orquestador/method/adapter-contract.md
.hebrinex/orquestador/method/context-loading-policy.md
.hebrinex/orquestador/memory/memory-registry.yaml
.hebrinex/orquestador/memory/memory-routing.yaml
.hebrinex/orquestador/memory/local/session-pin.md
.hebrinex/orquestador/entrypoints/first-message.md
.hebrinex/orquestador/entrypoints/reentry-light.md
.hebrinex/orquestador/entrypoints/reentry-full.md
.hebrinex/orquestador/entrypoints/debug-log-intake.md
.hebrinex/orquestador/entrypoints/compactation-recovery.md
.hebrinex/orquestador/adapters/generic-ai.md
.hebrinex/orquestador/adapters/_shared-core.md
.hebrinex/orquestador/adapters/codex.md
.hebrinex/orquestador/adapters/claude-code.md
.hebrinex/orquestador/adapters/gemini.md
.hebrinex/orquestador/adapters/qwen.md
.hebrinex/orquestador/adapters/deepseek.md
.hebrinex/orquestador/integrations/claude/agents/auditor-detractor.md
.hebrinex/orquestador/integrations/claude/agents/reviewer.md
.hebrinex/orquestador/integrations/cursor/rules/hebrinex.mdc
.hebrinex/orquestador/integrations/copilot/copilot-instructions.md
.hebrinex/orquestador/portability/mcp-hosts.md
.hebrinex/orquestador/sdd/progress/state.yaml
.hebrinex/orquestador/sdd/progress/registry.yaml
.hebrinex/orquestador/sdd/progress/templates/preflight-template.md
.hebrinex/orquestador/sdd/progress/templates/reentry-checklist.md
.hebrinex/orquestador/sdd/progress/templates/approval-envelope.md
.hebrinex/orquestador/sdd/progress/approvals/
.hebrinex/orquestador/sdd/progress/templates/clarification-checklist.md
.hebrinex/orquestador/sdd/progress/templates/analysis-checklist.md
.hebrinex/orquestador/sdd/progress/templates/blast-radius.md
.hebrinex/orquestador/sdd/progress/templates/task-graph.yaml
.hebrinex/orquestador/sdd/progress/templates/agent-profile-template.yaml
.hebrinex/orquestador/sdd/progress/templates/detractor-pass.md
.hebrinex/orquestador/sdd/progress/templates/changelog-reconstruction-checklist.md
.hebrinex/orquestador/sdd/progress/templates/release-history-matrix.yaml
.hebrinex/orquestador/sdd/progress/templates/deploy-migration-checklist.md
.hebrinex/orquestador/sdd/progress/templates/reference-drift-matrix.yaml
.hebrinex/orquestador/sdd/progress/templates/ci-pipeline-history.yaml
.hebrinex/orquestador/sdd/progress/templates/backlog-classification-matrix.yaml
.hebrinex/orquestador/sdd/progress/templates/audit-report-contract.md
.hebrinex/orquestador/sdd/progress/templates/final-report-crosslink-checklist.md
.hebrinex/orquestador/sdd/progress/templates/ai-preset-contract.md
.hebrinex/orquestador/sdd/progress/templates/memory-closure-checklist.md
.hebrinex/orquestador/sdd/progress/templates/verification-matrix.yaml
.hebrinex/orquestador/sdd/progress/templates/final-report.md
.hebrinex/orquestador/sdd/progress/templates/agent-closure.md
.hebrinex/orquestador/sdd/progress/evidence/usage-baseline-0.15.0.yaml
.hebrinex/orquestador/method/agent-role-taxonomy.md
.hebrinex/agents/auditor.md
.hebrinex/agents/reporter.md
.hebrinex/orquestador/sdd/progress/cycles/_template/audit.jsonl
.hebrinex/orquestador/sdd/progress/cycles/_template/gate-log.yaml
.hebrinex/orquestador/policies/tool-policy.yaml
.hebrinex/orquestador/policies/command-taxonomy.md
.hebrinex/orquestador/policies/write-set-policy.md
.hebrinex/orquestador/policies/secret-denylist.md
.hebrinex/orquestador/policies/schemas/project-binding.schema.yaml
.hebrinex/orquestador/policies/schemas/context-budget.schema.yaml
.hebrinex/orquestador/policies/schemas/memory-registry.schema.yaml
.hebrinex/orquestador/policies/schemas/registry.schema.yaml
```

También debe cumplirse:

- `.hebrinex/` está en `.gitignore`.
- `.hebrinex/` no está trackeado por Git del proyecto consumidor.
- `PROJECT_BINDING.yaml` está en `bound` si es proyecto consumidor.
- `project_root` coincide con la raíz real del proyecto.
- Un harness local externo no se usa como autoridad operativa.
- `bash .hebrinex/init.sh` pasa o deja un bloqueo explícito.
- `orquestador/harness-manifest.txt` existe y coincide con la estructura
  esperada por `init.sh`.
- `orquestador/registry-index.yaml` enumera los registries canónicos.
- `orquestador/prompt-registry.yaml` declara los prompts oficiales y no deja
  prompts operativos sueltos sin clasificación.
- `orquestador/adapter-registry.yaml`, `context-profile-registry.yaml`,
  `gate-registry.yaml`, `policy-registry.yaml` y `template-registry.yaml`
  existen o el proyecto declara explícitamente que está en migración parcial.
- `orquestador/agents/agent-registry.yaml` existe, declara
  `agent_definition_authority: harness_only` y bloquea roles faltantes.
- Cada rol registrado tiene role contract, security profile, runtime profile,
  context pack, tool pack, playbook, failure modes, evaluation rubric y
  handoff esperado.
- `orquestador/security/` existe y aplica defaults deny-by-default para red,
  secretos, supply chain, comandos destructivos, git remoto y privilegios.
- `orquestador/migration/migration-registry.yaml` existe y sólo declara rutas
  oficiales de migración del harness.
- Una migración aplicada tiene backup, reporte y
  `post-migration-contract.yaml` activo; si no, queda `partial` o `blocked`.
- `orquestador/memory/memory-registry.yaml` existe y declara capas activas.
- `orquestador/memory/local/session-pin.md` existe y permite re-entry liviano.
- `orquestador/entrypoints/` existe para primer mensaje, re-entry y debug logs.
- `orquestador/adapters/` existe para IAs especificas y fallback generico.
- `orquestador/context-budget.yaml` existe y los entrypoints respetan sus
  límites.
- `scripts/validate-harness.ps1 -RunNegativeTests` pasa antes de cerrar una
  migración o release del harness.
- La documentacion personal/local no existe dentro de `.hebrinex/`, no aparece
  en el manifest y no se usa como contexto operativo.

---

## 3 · Auditoría de Contrato

Revisar que la sesión muestre:

```text
Contrato de sesión:
- Harness detectado
- Fuente del harness
- Harness path
- Project root
- Binding
- External write scope
- Modo
- Rol del chat: intérprete
- Leader visible
- Subagentes activos
- Fase/Slice activo
- Estado SDD
- Próxima acción
- Aprobación requerida
```

Incumplimientos típicos:

- Falta `PROJECT_BINDING.yaml` o apunta a otro proyecto.
- Se opera con un `.hebrinex` de otra carpeta.
- Se usa una fuente `source_template` como autoridad de proyecto.
- El chat se presenta como leader sin aprobación.
- Se editan archivos sin preflight.
- Se ejecutan comandos sin `SI`.
- El leader implementa.
- El implementer aprueba.
- El reviewer edita código.
- Se cierra un ciclo sin `G6_agent_closure_complete`.

---

## 3.1 · Auditoría de Binding y Re-entry

En 0.16.0, antes de revisar código o progreso, validar:

```text
Binding:
- binding_mode: bound | source_template
- project_root: [ruta]
- harness_path: [ruta]
- repo_remote: [url o none]
- harness_instance_id: [id]
```

Reglas:

- `source_template` solo es válido para editar el repo fuente del harness o
  copiarlo hacia un proyecto.
- Un proyecto consumidor requiere `bound`.
- Si hubo compactación, cambio de cwd o cambio de proyecto, approvals viejos
  expiran.
- El agente debe ejecutar re-entry: contrato, binding, state, registry, locks,
  agentes abiertos y handoffs.
- En 0.16.0, el re-entry liviano debe leer `session-pin.md`, `memory-registry.yaml` y `memory-routing.yaml` antes de state/registry.
- La memoria completa no se carga para debug diario; requiere motivo y aprobacion cuando aplica.

---

## 4 · Auditoría de Evidencia

Para cada ciclo activo o cerrado debe existir evidencia verificable:

```text
progress/cycles/C-XXX/audit.jsonl
progress/cycles/C-XXX/gate-log.yaml
progress/cycles/C-XXX/<slice>/verification-matrix.yaml
progress/cycles/C-XXX/<slice>/final-report.md
progress/cycles/C-XXX/<slice>/agent-closure.md
```

Si un ciclo histórico no tiene estos archivos:

- no inventar evidencia;
- no marcarlo como `done` P0;
- marcarlo como `legacy_unverified`;
- registrar desde qué ciclo empieza cumplimiento estricto.

### 4.1 · Evidencia Condicional 0.8.x

Además de la evidencia base, `0.16.0` conserva los controles 0.8.x/0.9.x/0.10.x
y agrega verificación de runtime, approvals, gateway y hooks si la tarea toca
ese tipo de decisión:

| Caso | Artefacto requerido |
|---|---|
| Changelog, release notes o historia | `changelog-reconstruction-checklist.md` + `release-history-matrix.yaml` |
| Deploy o migración | `deploy-migration-checklist.md` |
| Cierre de versión o migración de harness | `reference-drift-matrix.yaml` |
| CI/pipeline | `ci-pipeline-history.yaml` |
| Roadmap P0/P1/P2 | `backlog-classification-matrix.yaml` |
| Auditor + reporter | `audit-report-contract.md` |
| Cierre de fase/ciclo | `final-report-crosslink-checklist.md` |
| Cierre de memoria | `memory-closure-checklist.md` |
| Presets Codex/Claude/Gemini | `ai-preset-contract.md` |
| Memoria/re-entry | `memory-registry.yaml`, `memory-routing.yaml`, `session-pin.md` |
| Presupuesto de contexto | `context-budget.yaml` + salida del validador |
| Adapters IA | `orquestador/adapters/<tool>.md` o `generic-ai.md` |
| Prompts oficiales | `prompt-registry.yaml` + ruta real del prompt |
| Registries del orquestador | `registry-index.yaml` + registry específico |
| Agentes/roles | `agent-registry.yaml` + `role-contracts/<rol>.yaml` |
| Capabilities | `capability-registry.yaml` + security/runtime profile |
| Seguridad informática | `orquestador/security/*.yaml` + `validate-security-policy.ps1` |
| Migración 0.10.0+ | `migration-registry.yaml`, route file, backup, reporte y contrato post-migración |
| Runtime enforcement 0.11.0 | `validate-state-machine.ps1`, `validate-agent-runtime.ps1`, fixtures positivas/negativas |
| Approval/Gateway 0.12.0 | approval envelope real, `validate-command-gateway.ps1`, resultado con `approval_status` |
| Hooks Claude 0.12.0 | `settings.template.json`, `install-claude-hooks.ps1`, `claude-pretooluse-hook.ps1` y policy de hooks |

**Regla:** si el caso aplica y el artefacto no existe, el cierre queda
`blocked` o se declara explícitamente como no aplicable con evidencia.

---

## 5 · Regularización P0

Cuando una auditoría devuelve `cumple: parcialmente`, el siguiente trabajo es
regularizar, no seguir desarrollando.

Reglas:

- No tocar código de producto.
- No inventar approvals ni gate logs.
- No borrar historial sin explicación.
- Reconciliar `state.yaml`, `registry.yaml`, `registry.md` y `blocked.md`.
- Crear artefactos faltantes de estructura.
- Marcar ciclos viejos sin evidencia como `legacy_unverified`.
- Crear carpeta real para el ciclo activo.
- Pasar `init.sh`.

Formato mínimo de resultado:

```text
Regularización P0:
- Ciclo activo:
- Ciclos legacy_unverified:
- State/registry reconciliados:
- Approvals reales:
- Gates reales:
- Agent closures:
- Validación:
- Bloqueos:
```

---

## 6 · Preset Codex

```md
Usá Hebri-AI-Harness como contrato operativo obligatorio.

Modo: automatico.
Chat visible: interprete.
Leader visible: obligatorio.
No edites, ejecutes comandos, uses red, git, tools con efecto ni cambies
estado SDD sin preflight P0 y mi SI.

Si recibís logs/debug, no implementes directo. Clasificá el input, separá
hechos de inferencias, indicá impacto, hipótesis y siguiente paso.

Preflight P0:
- Approval ID:
- Acción propuesta:
- CWD:
- Project root:
- Harness path:
- Binding status:
- Read-set:
- Write-set:
- External write scope:
- Comando/tool:
- Red/git/externo:
- Riesgo:
- Verificación:
- Evidencia esperada:
- Requiere SI:
```

---

## 7 · Preset Claude Code

```md
Este proyecto opera bajo Hebri-AI-Harness.

Leer primero:
- .hebrinex/PROJECT_BINDING.yaml
- .hebrinex/AGENTS.md
- .hebrinex/orquestador/method/session-contract.md
- .hebrinex/orquestador/method/harness-resolution.md
- .hebrinex/orquestador/method/operating-modes.md
- .hebrinex/orquestador/method/multiagent-protocol.md

El chat es intérprete. El leader debe estar visible. Máximo 5 agentes activos:
leader + 4 subagentes. No mezclar roles.

No escribir, correr comandos, usar red, git ni cambiar estado sin preflight y
SI. No cerrar ciclos con agentes abiertos. Si falta binding o hay mismatch,
bloquear antes de operar.
```

---

## 8 · Preset Gemini

```md
Trabajá siempre siguiendo Hebri-AI-Harness como contrato operativo.

Modo: automatico.
Chat: interprete.
Leader: visible.
Roles separados.

Ante logs, errores o debug:
- declarar contrato de sesión;
- validar PROJECT_BINDING, project_root y harness_path;
- clasificar input;
- separar hechos e inferencias;
- proponer hipótesis;
- no actuar sin preflight;
- esperar SI antes de cualquier efecto.
```

---

## 9 · Matriz Biblia ↔ Harness

| Concepto en la biblia | Archivo operativo en harness |
|---|---|
| Modelo de trabajo | `.hebrinex/AGENTS.md`, `session-contract.md` |
| Binding de proyecto | `PROJECT_BINDING.yaml`, `harness-resolution.md` |
| Estructura esperada | `orquestador/harness-manifest.txt`, `init.sh` |
| Unidad mínima de contexto | `orquestador/context-profiles.md` |
| Explorer/Worker | `agents/`, `prompts/explorar.prompt.md`, `worker.prompt.md` |
| SDD | `orquestador/sdd/specs/`, templates SDD |
| Fases y slices | `PROGRESS.md`, `state.yaml`, specs por feature |
| Roles cerrados | `agents/*.md`, prompts por rol, `multiagent-protocol.md` |
| Ownership | `permissions.md`, locks, `write-set-policy.md` |
| Approval humano | `approval-envelope.md`, `preflight-template.md` |
| Tool use | `tool-policy.yaml`, `command-taxonomy.md` |
| Seguridad/secretos | `secret-denylist.md`, `risk-criteria.md` |
| Evidencia | `evidence-schema.md`, `audit.jsonl`, `gate-log.yaml` |
| Cierre | `final-report.md`, `agent-closure.md` |
| Gaps | `gap-library.md`, `blocked.md`, `future-p1.md` |
| Changelog/release | `changelog-policy.md`, `release-history-matrix.yaml` |
| Deploy/migración | `deploy-migration-policy.md`, `deploy-migration-checklist.md` |
| Drift de referencias | `reference-drift-policy.md`, `reference-drift-matrix.yaml` |
| CI/pipeline | `ci-pipeline-policy.md`, `ci-pipeline-history.yaml` |
| Backlog | `backlog-policy.md`, `backlog-classification-matrix.yaml` |
| Auditor/reporter | `audit-reporting-policy.md`, `audit-report-contract.md` |
| Adapters IA | `ai-preset-policy.md`, `adapter-contract.md`, `orquestador/adapters/` |

---

## 10 · Roadmap Operativo 3.8.x / 0.16.0

Principio central:

```text
El sistema no escala creando más agentes.
Escala con roles mínimos, perfiles parametrizados, evidencia verificable y contradicción técnica controlada.
```

### 10.1 · P0 Obligatorio

| Bloque | Objetivo | Slices |
|---|---|---|
| Binding de proyecto | Evitar que un proyecto use el harness de otro | `source_template`, `bound`, `project_root`, `harness_path`, mismatch bloqueante |
| Re-entry post-compactación | Recuperar contrato después de compactación o cambio de cwd | Expirar approvals, validar binding, reconstruir state/registry/locks/agentes |
| Anti-confirmation bias | Evitar que el sistema confirme errores del usuario | Pedido vs hecho vs inferencia, desafío técnico, bloqueo por evidencia faltante |
| Clarification gate | No planear con contexto insuficiente | Preguntas bloqueantes, supuestos aceptados, gate antes de plan |
| Analysis checklist | Analizar antes de implementar | Requisitos, constraints, riesgos, evidencia esperada |
| Blast radius gate | Declarar alcance antes de tocar archivos | Módulos, read-set/write-set, comandos, red, git, rollback |
| Task graph/waves | Ordenar dependencias y paralelismo | Secuencial vs paralelo, límite 1 leader + 4 subagentes, bloqueos |
| Roles mínimos + perfiles | Sumar precisión sin multiplicar agentes | `interpreter`, `leader`, `executor`, `reviewer`, `auditor`, `reporter` |
| Registry Kanban | Hacer visible el estado real | `todo`, `ready`, `in_progress`, `review`, `blocked`, `done`, `legacy_unverified` |
| Detractor pass | Detectar errores de agentes antes del cierre | Tesis, objeciones, evidencia, severidad, qué falsaría la objeción |
| Manifest estructural | Evitar drift entre estructura e `init.sh` | `orquestador/harness-manifest.txt` como fuente validable |
| Memoria estratificada | Evitar re-entry manual constante | `memory-registry.yaml`, routing y capas local/diaria/ciclo/proyecto/completa |
| Entry/re-entry | Volver al contrato sin leer todo | `first-message`, `reentry-light`, `reentry-full`, `debug-log-intake` |
| Adapters multi-IA | Hacer portable el contrato | Codex, Claude, Gemini, Qwen, DeepSeek, Cursor, Copilot y Generic AI |
| Evidencia histórica | Evitar changelogs o release notes incompletos | `git log`, `PROGRESS.md`, registry, matriz de eventos |
| Deploy/migración | Documentar cambios de entorno sin omisiones | entorno, comando, evidencia, versión/ciclo, rollback |
| Drift de referencias | Mantener coherencia de versiones | `HARNESS_VERSION`, binding, README, prompts, changelog e `init.sh` |
| CI/pipeline | No colapsar iteraciones relevantes | historial de intentos, logs, commits y decisión final |
| Cierre con cross-links | Evitar `done` sin rastreabilidad | gate log, audit trail, verification matrix, agent closure, locks y gaps |

### 10.2 · P1 de Robustez

| Bloque | Objetivo |
|---|---|
| Archive formal | Separar specs/ciclos activos, cerrados y legacy |
| Risk-before-close | Revisar riesgo residual, rollback, deuda nueva e impacto |
| Audit event schema | Volver validable `audit.jsonl` |
| Agent registry YAML | Separar agentes activos, slots, ownership, handoffs y closures |
| Piloto auditor/reporter | Medir si la separación reduce ruido y mejora decisiones |
| Ejemplos comparativos | Documentar `cumple`, `parcial`, `no cumple`, `legacy_unverified`, usuario equivocado y agente equivocado |

### 10.3 · P2 de Escala

| Bloque | Objetivo |
|---|---|
| Worktrees para paralelismo | Aislar cambios cuando haya ejecución concurrente real |
| Presets por herramienta | Mejorar adopción en Codex, Claude Code, Gemini y otros |
| Compatibilidad externa parcial | Interoperar sin abandonar contrato Hebri |
| Micro-specs junto a código | Documentar decisiones cerca del módulo cuando convenga |
| Fundamento para IA futura | Usar memoria metodológica, perfiles cognitivos y contradicción interna controlada |

### 10.4 · Proyecto Piloto Recomendado

Nombre sugerido:

```text
Hebri-AI-Portfolio
```

Objetivo: crear un portfolio técnico que muestre stack tecnológico, experiencia
y conocimiento en IA, usando el harness como contrato operativo.

Stack recomendado:

```text
Next.js + TypeScript + Tailwind
```

Secciones sugeridas:

- Inicio: quién sos y qué construís.
- Stack tecnológico: frontend, backend, bases de datos, DevOps e IA.
- Metodología IA: Hebri-AI-Structure, Hebri-AI-Harness, SDD, roles cerrados,
  auditor, reporter y detractor.
- Proyectos: biblia, harness y demos.
- Laboratorio IA: agentes, token optimization, prompt engineering, AI
  Engineering, auditorías y evaluaciones.
- Contacto y links.

Qué debe probar:

- `.hebrinex/` instalado y fuera de Git.
- Contrato de sesión.
- Clarification gate.
- Analysis checklist.
- Blast radius.
- Task graph.
- Auditor.
- Reporter.
- Detractor pass.
- Registry Kanban.
- Cierre con evidencia.

Criterio de éxito: el Harness 0.16.0 se respeta sin que el operador tenga que
reencauzarlo constantemente, el portfolio queda funcional y los roles
especializados aportan claridad sin aumentar el límite de agentes.

---

## 11 · Sincronización Versionada Biblia ↔ Harness

Cuando cambia el harness:

1. Actualizar `HARNESS_VERSION` en este repo.
2. Actualizar README con la referencia operativa.
3. Actualizar Vol 07 si cambia estructura o relación biblia/harness.
4. Actualizar Vol 08 si cambia tool policy, permisos o autonomía.
5. Actualizar Vol 09 si cambian roles, gates, handoffs o cierre de agentes.
6. Actualizar prompts operativos si cambia el contrato de ejecución.
7. Actualizar este apéndice si cambian auditoría, regularización o presets.
8. Registrar en CHANGELOG.
9. Ejecutar CI.

Regla: si el harness evoluciona y la biblia no refleja el cambio, el repo está
desfasado aunque los índices pasen.

---

## 12 · Controles 0.8.3 a 0.16.0

Además de los controles base, la versión operativa 0.16.0 exige revisar:

| Versión | Control | Evidencia esperada |
|---|---|---|
| 0.8.3 | `detractor-senior` pre-implementación | checklist/veredicto aceptar, simplificar, bloquear o pedir evidencia |
| 0.8.4 | adapters declarativos multi-IA | `adapter-matrix.yaml` + `check-adapter-drift.ps1` |
| 0.8.5 | runtime `/harness` | `active-session` no autoritativo + budgets runtime |
| 0.8.6 | Claude reentry | brief generado, hooks en warn/enforce y estado no autoritativo |
| 0.8.7 | instruction builder/drift | `build-instructions.ps1` y `validate-drift.ps1 -RunNegativeTests` |
| 0.8.8 | regularización de migraciones | `regularize-state.ps1`, `regularize-registry.ps1`, backups `.bak` y builder compatible PowerShell 5.1 |
| 0.8.9 | hardening de migración | regularizer para listas inline/multilínea/ausentes, drift operativo sin historia falsa y budgets con margen |
| 0.8.10 | budget soft gates | warnings/evidencia hasta 2x, bloqueo sobre hard limit y fallback PowerShell en `init.sh` |
| 0.9.0 | orden de prompts y registries | `prompt-registry.yaml`, `registry-index.yaml`, registries canónicos y prompt de migración `0.8.10 -> 0.9.0` |
| 0.10.0 | agentes, seguridad y migración aplicada | `agent-registry.yaml`, `capability-registry.yaml`, `orquestador/security/`, migrador, backup, contrato post-migración y validadores |
| 0.11.0 | runtime enforcement | `state-machine.ps1`, `agent-runtime.ps1`, schemas/templates runtime y validadores negativos |
| 0.12.0 | approvals, gateway y hooks reales | `hebrinex approve`, approval store, Command Gateway v0.4, hooks Claude, shared core de adapters y ruta `0.11.0 -> 0.12.0` |
| 0.13.0 | daemon MCP y fuente única de roles | `.mcp.json`, `mcp/server.mjs`, `mcp/smoke.mjs`, `validate-mcp.ps1`, `agents/<rol>.md`, headers GENERATED, drift-check del builder y ruta `0.12.0 -> 0.13.0` |
| 0.14.0 | uso de tokens medido y MCP de consumo | `hebrinex usage`, `usage-baseline-0.14.0.yaml`, `savings_docs_pct`, `savings_pct`, `session_usage`, `mcp/model-pricing.yaml`, `validate-release.ps1`, `validate-cli.ps1`, `validate-mcp.ps1` y ruta `0.13.0 -> 0.14.0` |
| 0.15.0 | locks ejecutables y autonomia endurecida | `hebrinex lock`, hooks Claude `WriteGuard`/`Stop`/`PreCompact`, rate limiting del gateway, `role_assume`, `lock_acquire`, `lock_release` y ruta `0.14.0 -> 0.15.0` |
| 0.16.0 | portabilidad host y agentes derivados | `install-host-integrations.ps1`, `.claude/agents/`, reglas Cursor, instrucciones Copilot, `agent_audit`, `agent_review`, `mcp/agents-backend.yaml`, `orquestador/portability/mcp-hosts.md`, `adapter-matrix.yaml` con madurez y ruta `0.15.0 -> 0.16.0` |

Criterio de auditoría: si el agente usa memoria, presets o adapters como autoridad en vez de binding/state/registry/evidencia, el cumplimiento es parcial o bajo.

---

## 13 · Corrección 0.8.8: Regularización de Migraciones

`Hebri-AI-Harness 0.8.8` existe para resolver dos fallas prácticas detectadas
en migraciones reales:

- `build-instructions.ps1` no puede depender de APIs modernas como
  `HashData` o `ToHexString`, porque PowerShell 5.1 en Windows usa .NET
  Framework 4.8.
- Proyectos que preservan `state.yaml` y `registry.yaml` de 0.8.2 pueden
  quedar con schema drift frente al validador 0.8.7+.

La solución metodológica no es regenerar estado a ciegas. El orden correcto es:

1. Preservar archivos locales de proyecto: `PROGRESS.md`, contexto, specs,
   ciclos, approvals, locks, `state.yaml`, `registry.yaml` y `registry.md`.
2. Actualizar infraestructura del harness desde el repo oficial.
3. Ejecutar regularizadores en modo check-only.
4. Mostrar diferencias propuestas y pedir `SI`.
5. Aplicar con backup `.bak`.
6. Validar harness, drift, adapters, builder e `init.sh`.

Comandos esperados desde la raíz del proyecto consumidor:

```powershell
.\.hebrinex\scripts\regularize-state.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\regularize-registry.ps1 -Root .\.hebrinex
```

Si el reporte es correcto:

```powershell
.\.hebrinex\scripts\regularize-state.ps1 -Root .\.hebrinex -Apply
.\.hebrinex\scripts\regularize-registry.ps1 -Root .\.hebrinex -Apply
```

Validación mínima posterior:

```powershell
.\.hebrinex\scripts\validate-harness.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\check-adapter-drift.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\build-instructions.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-drift.ps1 -Root .\.hebrinex -RunNegativeTests
```

Después:

```bash
bash .hebrinex/init.sh
```

Regla de cierre: si `state.yaml` y `registry.yaml` cuentan historias
distintas, el proyecto queda `parcial` o `blocked` hasta reconciliar ciclo
activo, roles, gates, locks y evidencia.

---

## 14 · Corrección 0.8.9: Hardening de Migraciones

`Hebri-AI-Harness 0.8.9` corrige fallas detectadas al aplicar 0.8.8 en
proyectos reales:

- `regularize-state.ps1` debe agregar gates requeridos aunque
  `required_gates` esté como lista inline, lista YAML multilínea o no exista.
- `init.sh` debe bloquear drift operativo activo, no referencias históricas
  válidas en `PROGRESS.md`, APRs, roadmap o notas de migración.
- `memory_bootstrap` y `leader_light` deben tener margen suficiente para
  metadatos mínimos sin abandonar el objetivo de ahorro.

IDs canónicos para desbloquear proyectos:

```yaml
required_gates:
  - G3A_detractor_senior_pre_implementation
  - G5I_memory_consistency_complete
  - G6_agent_closure_complete
```

Regla: si un proyecto conserva historia de versiones anteriores, esa historia
no se borra ni se ofusca para pasar validaciones. Se corrige el validador para
distinguir contrato operativo vigente de evidencia histórica.

---

## 15 · Corrección 0.8.10: Budget Soft Gates

`Hebri-AI-Harness 0.8.10` cambia la semántica de presupuestos de contexto:

```text
used <= budget        -> OK
budget < used <= 2x   -> warning registrado, no bloquea
used > 2x             -> bloqueo duro
```

Motivo: el budget existe para sostener ahorro de tokens, no para cortar una
migración por pocos tokens de diferencia. Si un prompt oficial o una nota
mínima excede el límite recomendado, se registra como evidencia y se sigue.
Solo se bloquea cuando el consumo duplica el presupuesto, porque ahí ya no es
desvío menor sino pérdida real de control de contexto.

Regla operativa:

- No compactar manualmente cada proyecto por warnings de budget.
- Registrar el warning en el reporte de migración o gate log.
- Si supera 2x, detenerse y reducir contexto, prompt o archivos cargados.
- `init.sh` debe poder correr con `pwsh`, `powershell.exe` o `powershell`.

---

## 16 · Actualización 0.9.0: Orden de Prompts y Registries

`Hebri-AI-Harness 0.9.0` no cambia el principio de operación: binding,
state, registry, gates, locks y evidencia siguen siendo autoridad. La mejora
principal es reducir desorden y drift:

- prompts separados por responsabilidad (`roles`, `workflows`, `session`,
  `migration`, `audit`, `adapters`, `runtime`, `bootstrap`);
- `prompt-registry.yaml` como índice de prompts oficiales;
- `registry-index.yaml` como índice de registries canónicos;
- registries explícitos para adapters, perfiles de contexto, gates, policies
  y templates;
- prompt de migración `migrar-harness-0-8-10-a-0-9-0.prompt.md`;
- validación local para evitar que un prompt suelto gobierne por encima del
  contrato del harness.

La migración correcta desde 0.8.10:

1. Preservar `state.yaml`, `registry.yaml`, ciclos, locks, approvals,
   memoria local/proyecto y evidencia.
2. Agregar la nueva estructura de prompts y registries.
3. Correr validadores locales.
4. Revisar drift de referencias.
5. Cerrar con reporte de migración y evidencia.

Regla: 0.9.0 ordena el harness estable. La estructura de Agent Contract
System y autoridad `harness_only` pertenece a 0.10.0 y se audita en la sección
siguiente, junto con su contrato de migración propio.

---

## 17 · Actualización 0.10.0: Agent Contract System y Seguridad

`Hebri-AI-Harness 0.10.0` convierte los agentes en contratos gobernados por el
harness. La IA no define agentes, roles, permisos, capabilities ni
escalaciones.

Estructura mínima:

```text
orquestador/agents/
  agent-registry.yaml
  capability-registry.yaml
  lifecycle-registry.yaml
  role-contracts/
  security-profiles/
  runtime-profiles/
  context-packs/
  tool-packs/
  playbooks/
  failure-modes/
  evaluation-rubrics/
  handoff-contracts/
orquestador/security/
  permission-registry.yaml
  command-risk-registry.yaml
  write-scope-registry.yaml
  network-policy.yaml
  secrets-policy.yaml
  supply-chain-policy.yaml
  escalation-policy.yaml
  logging-policy.yaml
  threat-model.yaml
orquestador/migration/
  migration-registry.yaml
  versions/
  contracts/post-migration-contract.yaml
  reports/
```

Reglas duras:

- `agent_definition_authority: harness_only`;
- `ai_may_define_agents: false`;
- `prompt_may_define_roles: false`;
- rol ausente en `agent-registry.yaml` = bloqueo;
- contrato de rol ausente = bloqueo;
- security/runtime profile ausente = bloqueo;
- prompt o documentación en conflicto = gana el registry;
- capability desconocida = bloqueo;
- red, secretos, supply chain, git remoto, destructivo y privilegiado =
  deny-by-default.

Validación mínima:

```powershell
.\.hebrinex\scripts\validate-agent-contracts.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-security-policy.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-migration.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\audit-harness.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-harness.ps1 -Root .\.hebrinex -RunNegativeTests
```

Migración:

- CheckOnly no escribe.
- Apply requiere backup verificado antes del primer write.
- Se preservan `state.yaml`, `registry.yaml`, ciclos, locks, approvals,
  memoria local/proyecto y evidencia.
- El cierre requiere `post-migration-contract.yaml` activo, reporte escrito y
  validadores OK.

Regla conceptual final: el harness no se comporta como agente. El harness
define, limita, migra y audita agentes. Los agentes ejecutan su rol con
autosuficiencia estructural, pero sin autoridad para redefinirse.

---

## 18 · Actualización 0.11.0: Enforcement Release

`Hebri-AI-Harness 0.11.0` convierte contratos ya definidos en decisiones
ejecutables. La regla deja de ser sólo documental: el harness puede responder
allow/block ante estados, roles y capabilities.

Componentes:

```text
scripts/state-machine.ps1
scripts/agent-runtime.ps1
scripts/validate-state-machine.ps1
scripts/validate-agent-runtime.ps1
orquestador/runtime/schemas/state-machine-decision.schema.json
orquestador/runtime/schemas/agent-runtime-decision.schema.json
orquestador/runtime/templates/state-machine-decision.template.json
orquestador/runtime/templates/agent-runtime-decision.template.json
orquestador/testing/fixtures/positive/runtime-implementer-write.yaml
orquestador/testing/fixtures/negative/runtime-reviewer-write.yaml
orquestador/testing/fixtures/negative/state-active-to-closed.yaml
orquestador/migration/versions/0.10.11-to-0.11.0.yaml
```

Criterios de auditoría:

- `state-machine` permite transiciones registradas y bloquea transiciones
  inválidas o terminales.
- `agent-runtime` valida que el rol exista, tenga contrato y pueda ejercer la
  capability pedida.
- Reviewer no escribe, implementer no aprueba y rol desconocido bloquea.
- CI y `validate-harness.ps1 -RunNegativeTests` invocan validadores de runtime.
- La migración `0.10.11 -> 0.11.0` preserva state, registry, ciclos, locks,
  approvals, memoria local/proyecto y evidencia.

Comandos mínimos:

```powershell
.\.hebrinex\scripts\validate-state-machine.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-agent-runtime.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-harness.ps1 -Root .\.hebrinex -RunNegativeTests
```

---

## 19 · Actualización 0.12.0: Approval Store, Gateway y Hooks

`Hebri-AI-Harness 0.12.0` materializa la aprobación humana y endurece la
ejecución de comandos. El `SI` del operador ya no debe quedar sólo en el chat
cuando el harness puede persistirlo.

Componentes:

```text
scripts/hebrinex.ps1                    # cli_contract_version=0.3, comando approve
scripts/command-gateway.ps1             # result schema 0.4, approval_status
scripts/lib/hebri-common.psm1           # YAML, redacción, approvals, locks
scripts/claude-reentry.ps1
scripts/claude-pretooluse-hook.ps1
scripts/install-claude-hooks.ps1
orquestador/integrations/claude/hooks-policy.md
orquestador/integrations/claude/settings.template.json
orquestador/adapters/_shared-core.md
orquestador/migration/versions/0.11.0-to-0.12.0.yaml
```

Flujo de aprobación:

```text
1. Preflight declara acción exacta.
2. Operador responde SI.
3. `hebrinex approve -Apply -CommandText <accion exacta>` crea APR-*.yaml.
4. `command-gateway.ps1 -ApprovalId <APR>` valida estado, TTL y SHA256.
5. Si el comando no coincide, expiró o no existe, se bloquea.
```

Bloqueos esperados:

- `approval_not_found`: el ID no existe.
- `approval_expired`: el TTL venció.
- `approval_not_approved`: el envelope no está aprobado.
- `approval_command_mismatch`: el hash de la acción no coincide.
- `symlink_not_allowed_in_apply`: Apply detectó reparse point bajo el root.

Hooks Claude:

- `SessionStart` ejecuta `claude-reentry.ps1` y genera brief liviano con
  binding, contrato, ciclo activo y locks.
- `PreToolUse` para Bash/PowerShell ejecuta `claude-pretooluse-hook.ps1`.
- Si el gateway permite read-only seguro, el hook responde `allow`.
- Si detecta patrón bloqueado, responde `ask` y fuerza decisión explícita.
- Si el caso es ambiguo, no decide y deja actuar al flujo normal del host.

Reglas de seguridad:

- Los hooks no reemplazan al harness. Sólo acercan el contrato al host.
- Un hook no es evidencia; la evidencia es el approval envelope, salida del
  gateway, logs, state/registry, gate log y validadores.
- Apply nunca debe atravesar symlinks/junctions.
- Un timeout debe matar el árbol completo de procesos.
- Un `ApprovalId` no se reutiliza para otro comando.

Validación mínima:

```powershell
.\.hebrinex\scripts\validate-command-gateway.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-cli.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-migration.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-harness.ps1 -Root .\.hebrinex -RunNegativeTests
```

Criterio de cierre: 0.12.0 está aplicado sólo si el harness puede crear un
approval real, el gateway lo valida, los casos negativos bloquean, los hooks
son instalables y la ruta `0.11.0-to-0.12.0` aparece en el migration registry.

---

## 20 · Actualización 0.13.0: Daemon MCP y Fuente Única de Roles

`Hebri-AI-Harness 0.13.0` agrega una interfaz MCP local y reduce drift entre
roles narrativos, contratos YAML, prompts y defaults de capabilities.

Componentes:

```text
.mcp.json
mcp/package.json
mcp/package-lock.json
mcp/README.md
mcp/server.mjs
mcp/smoke.mjs
scripts/validate-mcp.ps1
agents/worker.md
agents/<rol>.md
orquestador/instruction-builder/instruction-registry.yaml
orquestador/migration/versions/0.12.0-to-0.13.0.yaml
```

Tools MCP esperadas:

| Tool | Control esperado |
|---|---|
| `run_command` | Ejecuta sólo por Command Gateway; si bloquea, la tool falla con razón y preflight |
| `preflight_approve` | Crea approval envelope sólo después del `SI` explícito |
| `approval_check` | Valida id, estado, expiración y hash exacto del comando |
| `session_contract` | Devuelve contrato mínimo sin cargar todo el harness |
| `gate_check` | Clasifica gates G5B..G5I desde `git status/diff` read-only |
| `memory_route` | Decide entrypoint por estado real, no por memoria conversacional |
| `close_cycle_check` | Bloquea `done` si faltan evidencia, locks, handoffs o cierre |

Fuente única de roles:

```text
agents/<rol>.md
  -> orquestador/agents/role-contracts/<rol>.yaml
  -> prompts/roles/<rol>.prompt.md
  -> role_defaults.<rol> en capability-registry.yaml
```

Reglas:

- editar `agents/<rol>.md`, no los derivados;
- regenerar con `scripts/build-instructions.ps1 -WriteOutputs`;
- validar drift con `scripts/build-instructions.ps1` sin `-WriteOutputs`;
- los contratos y prompts derivados deben llevar aviso `GENERATED`;
- si el builder detecta drift, el release no está listo.

Validación mínima:

```powershell
.\.hebrinex\scripts\validate-mcp.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\build-instructions.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-drift.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-agent-contracts.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-harness.ps1 -Root .\.hebrinex -RunNegativeTests
```

Si hay Node y dependencias instaladas:

```bash
cd .hebrinex/mcp
npm ci --no-audit --no-fund
node smoke.mjs
```

Criterio de cierre: 0.13.0 está aplicado sólo si el daemon MCP existe,
`validate-mcp.ps1` pasa, el smoke valida las 7 tools, el builder no detecta
drift de roles, la ruta `0.12.0-to-0.13.0` aparece en el migration registry y
`validate-harness.ps1 -RunNegativeTests` termina sin abortos ni falsos
positivos por manifest.

---

## 21 · Actualización 0.14.0: Uso Medido y Ahorro de Tokens

`Hebri-AI-Harness 0.14.0` convierte la economia de contexto en evidencia
medible. El claim publico del README del harness es conservador: ahorro medido
del 90% frente a la documentacion operativa completa. La medicion de release
registra `savings_docs_pct=94` y `savings_pct=99`.

Componentes:

```text
scripts/hebrinex.ps1
scripts/validate-cli.ps1
scripts/validate-release.ps1
scripts/validate-mcp.ps1
mcp/model-pricing.yaml
mcp/server.mjs
mcp/smoke.mjs
orquestador/context-budget.yaml
orquestador/method/cli-contract.md
orquestador/method/context-loading-policy.md
orquestador/sdd/progress/evidence/usage-baseline-0.14.0.yaml
orquestador/migration/versions/0.13.0-to-0.14.0.yaml
```

Markers obligatorios de `hebrinex usage`:

| Marker | Qué valida |
|---|---|
| `kernel_tokens` | Tamaño del kernel liviano usado como numerador |
| `docs_tree_tokens` | Denominador del claim publico del README |
| `savings_docs_pct` | Ahorro medido frente a documentacion operativa completa |
| `full_tree_tokens` | Denominador del arbol completo del manifest |
| `savings_pct` | Ahorro medido frente al arbol completo |
| `profile_<name>_tokens` | Presupuesto real o `dynamic` por perfil |
| `writes=false` | Garantia de que la medicion es read-only |

Validacion minima:

```powershell
.\.hebrinex\scripts\hebrinex.ps1 usage -Root .\.hebrinex
.\.hebrinex\scripts\validate-release.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-cli.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-mcp.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-migration.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-harness.ps1 -Root .\.hebrinex -RunNegativeTests
```

Criterio de cierre: 0.14.0 está aplicado sólo si `hebrinex usage` existe,
emite todos los markers, el README declara `ahorro medido: 90% (hebrinex
usage)`, `validate-release.ps1` recalcula `savings_docs_pct` y bloquea drift,
`session_usage` aparece en el MCP, `mcp/model-pricing.yaml` existe, la ruta
`0.13.0-to-0.14.0` está registrada y `validate-harness.ps1 -RunNegativeTests`
termina sin fallas.

---

## 22 · Actualización 0.15.0: Locks, Hooks y Autonomía Endurecida

`Hebri-AI-Harness 0.15.0` vuelve operativos los locks y reduce acciones con
efecto sin control.

Componentes:

```text
scripts/hebrinex.ps1
scripts/validate-command-gateway.ps1
scripts/validate-mcp.ps1
scripts/validate-release.ps1
orquestador/sdd/progress/locks/
orquestador/integrations/claude/
mcp/server.mjs
mcp/smoke.mjs
orquestador/migration/versions/0.14.0-to-0.15.0.yaml
orquestador/sdd/progress/evidence/usage-baseline-0.15.0.yaml
```

Controles esperados:

- `hebrinex lock -Acquire|-Release|-List` bloquea escrituras concurrentes por
  path y TTL.
- Claude Code incorpora `WriteGuard`, `Stop` y `PreCompact`.
- Command Gateway limita frecuencia de `Apply`.
- MCP expone `role_assume`, `lock_acquire` y `lock_release`.
- El rol asumido por MCP no reemplaza contracts ni capabilities.

Criterio de cierre: 0.15.0 está aplicado sólo si locks, hooks, gateway,
MCP role identity, migration route y baseline de uso pasan validadores con
pruebas negativas.

---

## 23 · Actualización 0.16.0: Portabilidad Host y Role Agents Derivados

`Hebri-AI-Harness 0.16.0` mejora la capacidad de tomar distintos hosts de IA
y acercarlos a un agente moderno sin dejar que el host defina autoridad.

Componentes:

```text
scripts/install-host-integrations.ps1
.claude/agents/auditor-detractor.md
.claude/agents/reviewer.md
mcp/agents-backend.yaml
mcp/agent-backends.mjs
orquestador/integrations/claude/agents/
orquestador/integrations/cursor/rules/hebrinex.mdc
orquestador/integrations/copilot/copilot-instructions.md
orquestador/portability/mcp-hosts.md
orquestador/portability/adapter-matrix.yaml
orquestador/testing/fixtures/negative/claude-agent-write-tool.md
orquestador/migration/versions/0.15.0-to-0.16.0.yaml
```

Controles esperados:

- Los agentes nativos de Claude son derivados de fuentes canonicas del harness.
- Cursor y Copilot reciben reglas/instrucciones, no autoridad para crear roles.
- `agent_audit` y `agent_review` pueden usar backend configurado, pero deben
  devolver evidencia y respetar capabilities.
- `mcp/agents-backend.yaml` declara backend real o fallback; no se simula
  soporte inexistente.
- La matriz de adapters declara madurez, hooks, role agents y via recomendada.
- La fixture negativa `claude-agent-write-tool.md` bloquea escritura desde
  agente read-only.
- `init.sh` debe correr como ejecutable POSIX (`100755`) en GitHub Actions.

Validacion minima:

```powershell
.\.hebrinex\scripts\hebrinex.ps1 usage -Root .\.hebrinex
.\.hebrinex\scripts\validate-release.ps1 -Root .\.hebrinex
.\.hebrinex\scripts\validate-cli.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-mcp.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-fixtures.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-security-policy.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\validate-migration.ps1 -Root .\.hebrinex -RunNegativeTests
.\.hebrinex\scripts\audit-harness.ps1 -Root .\.hebrinex -RunNegativeTests
```

Criterio de cierre: 0.16.0 está aplicado sólo si las integraciones host son
instalables o reportan CheckOnly claro, los adapters no quedan en estado
`unknown`, las tools MCP de agentes respetan backend/capability/evidencia, la
ruta `0.15.0-to-0.16.0` está registrada y GitHub Actions corre limpio.
