# Hebri-AI-Structure

Metodología personal de trabajo con inteligencia artificial aplicada a
proyectos de software.

> **Para IAs que lean este repo:** leer primero [`AGENTS.md`](./AGENTS.md),
> después la carpeta [`biblia/`](./biblia/) en el orden de los volúmenes.

---

## Contenido

La metodología vive en [`biblia/`](./biblia/), un volumen por archivo.

| Volumen | Tema | Cuándo leer |
|---|---|---|
| [Vol 01](./biblia/vol-01-modelo-de-trabajo.md) | Modelo de trabajo | Duda sobre el approach general |
| [Vol 02](./biblia/vol-02-subagentes.md) | Subagentes, roles mínimos y perfiles | Antes de delegar |
| [Vol 03](./biblia/vol-03-sdd.md) | Specification-Driven Development | Antes de planear fase o feature |
| [Vol 04](./biblia/vol-04-arquitectura-repo.md) | Arquitectura de repo | Antes de definir estructura |
| [Vol 05](./biblia/vol-05-prompts.md) | Prompts | Antes de escribir prompts |
| [Vol 06](./biblia/vol-06-gap-tracking.md) | Gap tracking | Al identificar gaps |
| [Vol 07](./biblia/vol-07-harness.md) | Acoplamiento con harness | Al arrancar un proyecto |
| [Vol 08](./biblia/vol-08-mcps-y-autonomia.md) | MCPs, tool use y autonomía | Antes de dar acceso a herramientas |
| [Vol 09](./biblia/vol-09-roles-cerrados.md) | Roles cerrados de harness | Al pasar a SDD con aprobación |
| [Apéndice](./biblia/apendice-ejemplo-end-to-end.md) | Ejemplo end-to-end | Onboarding o duda práctica |
| [Apéndice Harness 0.16](./biblia/apendice-harness-0-5-operacion.md) | Operación, auditoría y roadmap de harness | Auditar cumplimiento, agentes, seguridad, migración, approvals, hooks, MCP, locks o uso de tokens |

> El archivo monolito original `BIBLIA.md` se conserva como redirección por
> compatibilidad con links externos viejos.

---

## Ruta práctica

- **Proyecto nuevo** → [Vol 07 · Cómo definir el tipo de proyecto](./biblia/vol-07-harness.md#cómo-definir-el-tipo-de-proyecto)
- **Planear fase o slice** → [Vol 03 · Fases vs Slices](./biblia/vol-03-sdd.md#fases-vs-slices)
- **Delegar a subagentes** → [Vol 02 · Roles mínimos y perfiles](./biblia/vol-02-subagentes.md#roles-mínimos-y-perfiles-parametrizados)
- **Definir estructura de repo** → [Vol 04 · Mapa de responsabilidades](./biblia/vol-04-arquitectura-repo.md#mapa-de-responsabilidades)
- **Registrar algo que falta** → [Vol 06 · Gap tracking](./biblia/vol-06-gap-tracking.md)
- **Configurar acceso a herramientas** → [Vol 08 · MCPs y autonomía](./biblia/vol-08-mcps-y-autonomia.md)
- **Orquestar entre roles (leader, spec_author, implementer, reviewer)** → [Vol 09 · Roles cerrados](./biblia/vol-09-roles-cerrados.md)
- **Configurar ciclos multiagente con límite operativo** → [Vol 09 · Protocolo multiagente](./biblia/vol-09-roles-cerrados.md#protocolo-multiagente)
- **Auditar cumplimiento del Harness 0.16.0** → [Apéndice · Operación, Seguridad y Auditoría de Harness 0.16.0](./biblia/apendice-harness-0-5-operacion.md)
- **Ver un caso completo** → [Apéndice · Ejemplo end-to-end](./biblia/apendice-ejemplo-end-to-end.md)

---

## Principio base

> El chat coordina. El repositorio conserva la verdad.

---

## Relación con Hebri-AI-Harness

Este repo define el criterio: modelo mental, roles, SDD, autonomía, prompts,
gaps y economía de contexto.

El harness operativo vive aparte:
**[Hebri-AI-Harness](https://github.com/HebrineX/Hebri-AI-Harness)**.

Referencia operativa actual: **Hebri-AI-Harness 0.16.0**, con contrato de
sesión, binding de proyecto, resolución estricta del `.hebrinex`, re-entry
post-compactación, controles P0 estructurados, preflight, approvals, tool
policy, state, registry, audit trail, gate logs, cierre explícito de agentes,
anti-confirmation bias, roles mínimos con perfiles parametrizados, auditor,
reporter, detractor pass, gates de evidencia histórica, deploy/migración,
drift de referencias, CI/pipeline, backlog, cierre con cross-links, adapters multi-IA, entrypoints de re-entry, memoria estratificada gobernada por orquestador y manifest estructural del harness, runtime `/harness`, integración Claude reentry e instruction builder.

En 0.10.0 el harness deja de tratar a los agentes como prompts sueltos y los
gobierna por contratos verificables: Agent Contract System, autoridad
`harness_only`, runtime profiles, context packs, tool packs, playbooks,
failure modes, rubrics, security profiles, handoff contracts y lifecycle
registry. También suma seguridad informática verificable, migrador
CheckOnly/Apply, backup obligatorio, contrato post-migración aplicado y
validadores específicos para agentes, seguridad y migración. Conserva el orden
0.9.0 de prompts/registries y los controles de economía de contexto, runtime,
portabilidad multi-IA, drift de instrucciones y regularización de migraciones:
`context-budget.yaml`, kernel liviano de sesión,
`memory-closure-checklist.md`, validador local
`scripts/validate-harness.ps1`, regularizadores de `state.yaml` y
`registry.yaml`, compatibilidad PowerShell 5.1 en el builder de instrucciones
y pruebas negativas para impedir que documentacion personal/local entre en el
harness operativo.

En 0.11.0 el harness suma enforcement ejecutable: state machine, agent runtime,
fixtures positivas/negativas, CLI contract 0.2 y validadores integrados en CI.
En 0.12.0 materializa el `SI` del operador: `hebrinex approve` crea envelopes
con expiración y hash de la acción exacta, el Command Gateway valida
`ApprovalId` reales, bloquea approvals falsos/vencidos/mismatched, rechaza
symlinks en Apply y mata el árbol completo de procesos ante timeout. También
suma hooks reales de Claude Code (`SessionStart`, `PreToolUse`), instalador de
hooks, `scripts/lib/hebri-common.psm1`, reporte de locks en `status`, adapters
condensados con `_shared-core.md` y ruta de migración `0.11.0 -> 0.12.0`.

En 0.13.0 el harness agrega un daemon MCP local `hebrinex` (`mcp/server.mjs`)
que expone el enforcement como tools stdio: `run_command`,
`preflight_approve`, `approval_check`, `session_contract`, `gate_check`,
`memory_route` y `close_cycle_check`. No reemplaza políticas: envuelve los
scripts PowerShell existentes y fuerza que la ejecución pase por gateway,
approval store, gates y cierre. También consolida `agents/<rol>.md` como
fuente única de roles: desde bloques marcados se generan contratos YAML,
prompts de rol y `role_defaults` con `scripts/build-instructions.ps1
-WriteOutputs`; el modo default del builder detecta drift y falla si alguien
edita derivados a mano.

En 0.14.0 el harness convierte la economia de contexto en evidencia medible:
`hebrinex usage` reporta `kernel_tokens`, `docs_tree_tokens`,
`savings_docs_pct`, `full_tree_tokens`, `savings_pct` y uso por perfil. El
README del harness declara un ahorro conservador de 90% frente a la
documentacion operativa completa; la medicion de release marca 94% en
`savings_docs_pct` y 99% contra el arbol completo del manifest. El validador
`validate-release.ps1` recalcula ese claim y falla si deriva mas de 5 puntos.
MCP suma `session_usage` y `mcp/model-pricing.yaml` para consultar uso/costo
sin inventar precios ni transcripts.

En 0.15.0 el harness vuelve ejecutables los locks y endurece la autonomia:
`hebrinex lock -Acquire|-Release|-List` gobierna locks con TTL y conflicto de
paths; Claude suma hooks reales `WriteGuard`, `Stop` y `PreCompact`; el
Command Gateway agrega rate limiting para `Apply`; MCP suma `role_assume`,
`lock_acquire` y `lock_release` con rol del daemon; y queda documentado el
limite residual de `RoleId` autodeclarado en CLI directo.

En 0.16.0 el harness mejora la portabilidad real entre hosts: agrega
integraciones instalables para Claude, Cursor y Copilot; genera agentes nativos
Claude desde las fuentes canonicas del harness; expone `agent_audit` y
`agent_review` en el daemon MCP con backends configurables; documenta soporte
por host en `orquestador/portability/mcp-hosts.md`; endurece la matriz de
adapters con madurez, soporte de hooks y soporte de role agents; y suma la ruta
oficial de migracion `0.15.0 -> 0.16.0`. El claim de ahorro queda conservador:
el README del harness declara 90% y la medicion vigente reporta
`savings_docs_pct=94`.

Regla de separación:

- `Hebri-AI-Structure` explica qué hacer y por qué.
- `Hebri-AI-Harness` implementa cómo se opera en proyectos reales.
- Si hay conflicto, se registra el gap y se decide si corresponde cambiar la
  biblia, el harness o ambos.

---

## Lectura mínima

No cargues toda la biblia por defecto. Elegí la ruta mínima:

| Objetivo | Leer |
|---|---|
| Arrancar proyecto | Vol 01 + Vol 04 + Vol 07 |
| Delegar agentes | Vol 02 + Vol 09 |
| Usar SDD | Vol 03 + Vol 09 |
| Mejorar prompts | Vol 05 |
| Registrar gaps | Vol 06 |
| Configurar tools/autonomía/modelos | Vol 08 |
| Auditar, presupuestar o regularizar Harness 0.16.0 | Apéndice Harness 0.16 |
| Validar un flujo completo | Apéndice |

---

## Uso y propuestas

Este repo es una metodología personal. Si querés usarla, adaptarla o
sumar algo (un volumen, una corrección, un ejemplo, una crítica),
escribime y lo charlamos.

## Versión y cambios

Versión actual: **3.8.0**. Ver [CHANGELOG.md](./CHANGELOG.md).
Resumen humano de este salto: [infoHebriBiblia.md](./infoHebriBiblia.md).

## Créditos

Construido sobre **[The AI Biblia](https://github.com/Nicolas-Melluso/The-AI-Biblia)**
de [Nicolás Ezequiel Melluso](https://www.linkedin.com/in/nicolas-ezequiel-melluso/).
