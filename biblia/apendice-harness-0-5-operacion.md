# Apéndice · Operación y Auditoría de Harness 0.6.0

> Anterior: [Apéndice · Ejemplo end-to-end](./apendice-ejemplo-end-to-end.md)

Este apéndice define cómo auditar, regularizar y operar proyectos que usan
`Hebri-AI-Harness 0.6.0`.

No reemplaza al harness. Explica cómo verificar que un proyecto lo está
respetando.

---

## 1 · Criterio de Cumplimiento

| Veredicto | Criterio |
|---|---|
| `cumple` | Estructura 0.6.0 presente, contrato declarado, state/registry coherentes, preflight/approvals/gates/evidencia/cierre de agentes reales |
| `parcial` | Estructura instalada, pero evidencia P0 incompleta o estado inconsistente |
| `no cumple` | Falta `.hebrinex/`, faltan controles P0, o el flujo ejecuta sin contrato/aprobación |

Un proyecto migrado puede quedar en `parcial`: la estructura existe, pero los
ciclos viejos no tienen evidencia P0. Eso es aceptable solo si se marca como
`legacy_unverified` y el cumplimiento estricto empieza desde el próximo ciclo.

---

## 2 · Auditoría de Estructura

Archivos mínimos de `Hebri-AI-Harness 0.6.0`:

```text
.hebrinex/AGENTS.md
.hebrinex/orquestador/method/session-contract.md
.hebrinex/orquestador/sdd/progress/state.yaml
.hebrinex/orquestador/sdd/progress/registry.yaml
.hebrinex/orquestador/sdd/progress/templates/preflight-template.md
.hebrinex/orquestador/sdd/progress/templates/approval-envelope.md
.hebrinex/orquestador/sdd/progress/templates/clarification-checklist.md
.hebrinex/orquestador/sdd/progress/templates/analysis-checklist.md
.hebrinex/orquestador/sdd/progress/templates/blast-radius.md
.hebrinex/orquestador/sdd/progress/templates/task-graph.yaml
.hebrinex/orquestador/sdd/progress/templates/agent-profile-template.yaml
.hebrinex/orquestador/sdd/progress/templates/detractor-pass.md
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

## 10 · Roadmap 3.0.0 / 0.6.0

Principio central:

```text
El sistema no escala creando más agentes.
Escala con roles mínimos, perfiles parametrizados, evidencia verificable y contradicción técnica controlada.
```

### 10.1 · P0 Obligatorio

| Bloque | Objetivo | Slices |
|---|---|---|
| Anti-confirmation bias | Evitar que el sistema confirme errores del usuario | Pedido vs hecho vs inferencia, desafío técnico, bloqueo por evidencia faltante |
| Clarification gate | No planear con contexto insuficiente | Preguntas bloqueantes, supuestos aceptados, gate antes de plan |
| Analysis checklist | Analizar antes de implementar | Requisitos, constraints, riesgos, evidencia esperada |
| Blast radius gate | Declarar alcance antes de tocar archivos | Módulos, read-set/write-set, comandos, red, git, rollback |
| Task graph/waves | Ordenar dependencias y paralelismo | Secuencial vs paralelo, límite 1 leader + 4 subagentes, bloqueos |
| Roles mínimos + perfiles | Sumar precisión sin multiplicar agentes | `interpreter`, `leader`, `executor`, `reviewer`, `auditor`, `reporter` |
| Registry Kanban | Hacer visible el estado real | `todo`, `ready`, `in_progress`, `review`, `blocked`, `done`, `legacy_unverified` |
| Detractor pass | Detectar errores de agentes antes del cierre | Tesis, objeciones, evidencia, severidad, qué falsaría la objeción |

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

Criterio de éxito: el harness 0.6.0 se respeta sin que el operador tenga que
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
