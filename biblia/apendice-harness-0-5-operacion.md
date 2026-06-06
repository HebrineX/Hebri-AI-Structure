# Apéndice · Operación, Presupuesto y Auditoría de Harness 0.8.2

> Anterior: [Apéndice · Ejemplo end-to-end](./apendice-ejemplo-end-to-end.md)

Este apéndice define cómo auditar, regularizar y operar proyectos que usan
`Hebri-AI-Harness 0.8.2`.

No reemplaza al harness. Explica cómo verificar que un proyecto lo está
respetando.

---

## 1 · Criterio de Cumplimiento

| Veredicto | Criterio |
|---|---|
| `cumple` | Estructura 0.8.2 presente, binding correcto, contrato declarado, state/registry coherentes, preflight/approvals/gates/evidencia/cierre de agentes reales |
| `parcial` | Estructura instalada, pero evidencia P0 incompleta o estado inconsistente |
| `no cumple` | Falta `.hebrinex/`, faltan controles P0, o el flujo ejecuta sin contrato/aprobación |

Un proyecto migrado puede quedar en `parcial`: la estructura existe, pero los
ciclos viejos no tienen evidencia P0. Eso es aceptable solo si se marca como
`legacy_unverified` y el cumplimiento estricto empieza desde el próximo ciclo.

---

## 2 · Auditoría de Estructura

Archivos mínimos de `Hebri-AI-Harness 0.8.2`:

```text
.hebrinex/PROJECT_BINDING.yaml
.hebrinex/AGENTS.md
.hebrinex/scripts/validate-harness.ps1
.hebrinex/orquestador/harness-manifest.txt
.hebrinex/orquestador/context-budget.yaml
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
.hebrinex/orquestador/adapters/codex.md
.hebrinex/orquestador/adapters/claude-code.md
.hebrinex/orquestador/adapters/gemini.md
.hebrinex/orquestador/adapters/qwen.md
.hebrinex/orquestador/adapters/deepseek.md
.hebrinex/orquestador/sdd/progress/state.yaml
.hebrinex/orquestador/sdd/progress/registry.yaml
.hebrinex/orquestador/sdd/progress/templates/preflight-template.md
.hebrinex/orquestador/sdd/progress/templates/reentry-checklist.md
.hebrinex/orquestador/sdd/progress/templates/approval-envelope.md
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
- `orquestador/memory/memory-registry.yaml` existe y declara capas activas.
- `orquestador/memory/local/session-pin.md` existe y permite re-entry liviano.
- `orquestador/entrypoints/` existe para primer mensaje, re-entry y debug logs.
- `orquestador/adapters/` existe para IAs especificas y fallback generico.
- `orquestador/context-budget.yaml` existe y los entrypoints respetan sus
  límites.
- `scripts/validate-harness.ps1 -RunNegativeTests` pasa antes de cerrar una
  migración o release del harness.
- `infoHebri.md` no existe dentro de `.hebrinex/`, no aparece en el manifest y
  no se usa como contexto operativo.

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

En 0.8.2, antes de revisar código o progreso, validar:

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
- En 0.8.2, el re-entry liviano debe leer `session-pin.md`, `memory-registry.yaml` y `memory-routing.yaml` antes de state/registry.
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

Además de la evidencia base, `0.8.2` agrega controles que solo aplican si la
tarea toca ese tipo de decisión:

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

## 10 · Roadmap 3.2.0 / 0.8.2

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
| Adapters multi-IA | Hacer portable el contrato | Codex, Claude, Gemini, Qwen, DeepSeek, Cursor, Copilot y Generic AI || Evidencia histórica | Evitar changelogs o release notes incompletos | `git log`, `PROGRESS.md`, registry, matriz de eventos |
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

Criterio de éxito: el Harness 0.8.2 se respeta sin que el operador tenga que
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
