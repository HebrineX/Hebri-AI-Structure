# Apéndice · Operación y Auditoría de Harness 0.5.0

> Anterior: [Apéndice · Ejemplo end-to-end](./apendice-ejemplo-end-to-end.md)

Este apéndice define cómo auditar, regularizar y operar proyectos que usan
`Hebri-AI-Harness 0.5.0`.

No reemplaza al harness. Explica cómo verificar que un proyecto lo está
respetando.

---

## 1 · Criterio de Cumplimiento

| Veredicto | Criterio |
|---|---|
| `cumple` | Estructura 0.5.0 presente, contrato declarado, state/registry coherentes, preflight/approvals/gates/evidencia/cierre de agentes reales |
| `parcial` | Estructura instalada, pero evidencia P0 incompleta o estado inconsistente |
| `no cumple` | Falta `.hebrinex/`, faltan controles P0, o el flujo ejecuta sin contrato/aprobación |

Un proyecto migrado puede quedar en `parcial`: la estructura existe, pero los
ciclos viejos no tienen evidencia P0. Eso es aceptable solo si se marca como
`legacy_unverified` y el cumplimiento estricto empieza desde el próximo ciclo.

---

## 2 · Auditoría de Estructura

Archivos mínimos de `Hebri-AI-Harness 0.5.0`:

```text
.hebrinex/AGENTS.md
.hebrinex/orquestador/method/session-contract.md
.hebrinex/orquestador/sdd/progress/state.yaml
.hebrinex/orquestador/sdd/progress/registry.yaml
.hebrinex/orquestador/sdd/progress/templates/preflight-template.md
.hebrinex/orquestador/sdd/progress/templates/approval-envelope.md
.hebrinex/orquestador/sdd/progress/templates/verification-matrix.yaml
.hebrinex/orquestador/sdd/progress/templates/final-report.md
.hebrinex/orquestador/sdd/progress/templates/agent-closure.md
.hebrinex/orquestador/sdd/progress/cycles/_template/audit.jsonl
.hebrinex/orquestador/sdd/progress/cycles/_template/gate-log.yaml
.hebrinex/orquestador/policies/tool-policy.yaml
.hebrinex/orquestador/policies/command-taxonomy.md
.hebrinex/orquestador/policies/write-set-policy.md
.hebrinex/orquestador/policies/secret-denylist.md
```

También debe cumplirse:

- `.hebrinex/` está en `.gitignore`.
- `.hebrinex/` no está trackeado por Git del proyecto consumidor.
- `bash .hebrinex/init.sh` pasa o deja un bloqueo explícito.

---

## 3 · Auditoría de Contrato

Revisar que la sesión muestre:

```text
Contrato de sesión:
- Harness detectado
- Fuente del harness
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

- El chat se presenta como leader sin aprobación.
- Se editan archivos sin preflight.
- Se ejecutan comandos sin `SI`.
- El leader implementa.
- El implementer aprueba.
- El reviewer edita código.
- Se cierra un ciclo sin `G6_agent_closure_complete`.

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
- Read-set:
- Write-set:
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
- .hebrinex/AGENTS.md
- .hebrinex/orquestador/method/session-contract.md
- .hebrinex/orquestador/method/operating-modes.md
- .hebrinex/orquestador/method/multiagent-protocol.md

El chat es intérprete. El leader debe estar visible. Máximo 5 agentes activos:
leader + 4 subagentes. No mezclar roles.

No escribir, correr comandos, usar red, git ni cambiar estado sin preflight y
SI. No cerrar ciclos con agentes abiertos.
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

---

## 10 · Plan de Aplicaciones Futuras

Este plan lista mejoras detectadas al comparar el enfoque actual con patrones
SDD públicos y prácticas de spec-driven development. No activa obligaciones
nuevas por sí mismo: cada punto debe pasar por diseño, aprobación y versión del
harness o de la biblia antes de volverse contrato operativo.

### 10.1 · Candidatas para Hebri-AI-Harness

Estas mejoras pertenecen al vehículo operativo porque afectan ejecución,
gates, estado, evidencia o control de agentes.

| Prioridad | Mejora | Impacto esperado |
|---|---|---|
| P0 | Clarification gate antes del plan | Evita planes prematuros cuando el pedido está incompleto o ambiguo |
| P0 | Checklist de análisis antes de implementación | Fuerza revisión de requisitos, constraints, riesgos y evidencia antes de editar |
| P0 | Blast radius gate | Declara archivos, módulos, dependencias y riesgos antes de tocar código |
| P0 | Task dependency graph por ciclo/slice | Ordena tareas, bloqueos y paralelismo sin romper el límite de agentes |
| P0 | Estados Kanban estructurados en `registry.yaml` | Mejora trazabilidad de `todo`, `ready`, `in_progress`, `review`, `blocked`, `done` |
| P1 | Archive formal de specs y ciclos cerrados | Reduce ruido operativo sin perder historial |
| P1 | Risk-before-merge gate | Impide cerrar cambios sin revisar impacto, rollback y deuda nueva |
| P1 | Audit event schema más estricto | Hace validable el `audit.jsonl` y reduce evidencia narrativa débil |
| P1 | Agent registry YAML | Separa agentes activos, slots, ownership y handoffs del texto libre |
| P2 | Worktrees para agentes paralelos | Aísla cambios cuando haya ejecución concurrente real |
| P2 | Presets/extensiones por herramienta | Mejora adopción en Codex, Claude Code, Gemini y otros clientes |
| P2 | Compatibilidad parcial con formatos externos de agentes | Facilita interoperabilidad sin abandonar el contrato Hebri |

Regla de activación: una mejora de harness solo se vuelve obligatoria cuando
existe archivo operativo, template, gate o schema versionado en
`Hebri-AI-Harness` y la biblia actualiza su referencia de versión.

### 10.2 · Candidatas para Hebri-AI-Structure

Estas mejoras pertenecen a la biblia porque explican metodología, criterios de
decisión, ejemplos y relación entre conceptos. No deben convertirse en runtime.

| Prioridad | Mejora | Impacto esperado |
|---|---|---|
| P0 | Capítulo de clarification-first SDD | Define cuándo preguntar, cuándo inferir y cuándo bloquear por falta de contexto |
| P0 | Guía de blast radius metodológico | Explica cómo medir alcance antes de aprobar cambios |
| P0 | Modelo de task graph y waves | Formaliza cómo dividir fases sin perder ownership ni trazabilidad |
| P1 | Criterios de archivo histórico | Distingue specs vivas, cerradas, legacy y deprecated |
| P1 | Guía de riesgos antes de merge/cierre | Estándar metodológico para rollback, validación y deuda residual |
| P1 | Ejemplos comparativos de cumplimiento | Muestra casos `cumple`, `parcial`, `no cumple` y `legacy_unverified` |
| P2 | Glosario SDD/Harness | Reduce ambigüedad entre spec, cycle, slice, gate, handoff y evidence |
| P2 | Patrones de adopción por tipo de proyecto | Ajusta la metodología a SaaS, API, CLI, IA, frontend o docs |
| P2 | Micro-specs junto a código como patrón opcional | Documenta cuándo conviene ubicar specs cerca del módulo afectado |

Regla de activación: una mejora de biblia solo cambia la operación cuando el
harness incorpora su equivalente como contrato. Hasta entonces es criterio,
guía o roadmap.

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
