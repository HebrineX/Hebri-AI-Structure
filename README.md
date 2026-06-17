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
| [Apéndice Harness 0.8](./biblia/apendice-harness-0-5-operacion.md) | Operación, auditoría y roadmap de harness | Auditar cumplimiento, regularizar P0 o configurar presets |

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
- **Auditar cumplimiento del Harness 0.8.8** → [Apéndice · Operación, Presupuesto y Auditoría de Harness 0.8.8](./biblia/apendice-harness-0-5-operacion.md)
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

Referencia operativa actual: **Hebri-AI-Harness 0.8.8**, con contrato de
sesión, binding de proyecto, resolución estricta del `.hebrinex`, re-entry
post-compactación, controles P0 estructurados, preflight, approvals, tool
policy, state, registry, audit trail, gate logs, cierre explícito de agentes,
anti-confirmation bias, roles mínimos con perfiles parametrizados, auditor,
reporter, detractor pass, gates de evidencia histórica, deploy/migración,
drift de referencias, CI/pipeline, backlog, cierre con cross-links, adapters multi-IA, entrypoints de re-entry, memoria estratificada gobernada por orquestador y manifest estructural del harness, runtime `/harness`, integración Claude reentry e instruction builder.

En 0.8.8 se consolidan controles de economía de contexto, runtime,
portabilidad multi-IA, drift de instrucciones y regularización de migraciones:
`context-budget.yaml`, kernel liviano de sesión,
`memory-closure-checklist.md`, validador local
`scripts/validate-harness.ps1`, regularizadores de `state.yaml` y
`registry.yaml`, compatibilidad PowerShell 5.1 en el builder de instrucciones
y pruebas negativas para impedir que documentación personal como
`infoHebri.md` entre en el harness operativo.

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
| Auditar, presupuestar o regularizar Harness 0.8.8 | Apéndice Harness 0.8 |
| Validar un flujo completo | Apéndice |

---

## Uso y propuestas

Este repo es una metodología personal. Si querés usarla, adaptarla o
sumar algo (un volumen, una corrección, un ejemplo, una crítica),
escribime y lo charlamos.

## Versión y cambios

Versión actual: **3.3.1**. Ver [CHANGELOG.md](./CHANGELOG.md).

## Créditos

Construido sobre **[The AI Biblia](https://github.com/Nicolas-Melluso/The-AI-Biblia)**
de [Nicolás Ezequiel Melluso](https://www.linkedin.com/in/nicolas-ezequiel-melluso/).
