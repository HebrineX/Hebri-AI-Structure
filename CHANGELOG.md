# Changelog

Todos los cambios notables a esta metodología se documentan en este archivo.

El formato sigue [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y la
versión sigue [Semantic Versioning](https://semver.org/lang/es/).

---

## [2.7.1] — 2026-05-23

### Añadido

- Apéndice de operación y auditoría incorpora un plan de aplicaciones futuras
  separado entre mejoras candidatas para `Hebri-AI-Harness` y mejoras
  metodológicas para `Hebri-AI-Structure`.

---

## [2.7.0] — 2026-05-23

### Añadido

- README declara `Hebri-AI-Harness 0.5.0` como referencia operativa actual.
- Vol 07 documenta el harness mínimo recomendado con controles P0:
  `session-contract`, `state.yaml`, `registry.yaml`, preflight, approval
  envelope, audit trail, gate logs, verification matrix, final report y
  cierre explícito de agentes.
- Vol 08 agrega controles P0 de tool use: `tool-policy.yaml`,
  `command-taxonomy.md`, `write-set-policy.md`, `secret-denylist.md`,
  `preflight-template.md` y `approval-envelope.md`.
- Vol 09 actualiza el protocolo multiagente con chat intérprete, leader
  visible, gates `G0` a `G7`, `G6_agent_closure_complete` y regla
  `legacy_unverified` para ciclos históricos sin evidencia P0.
- Nuevo apéndice de operación y auditoría de harness 0.5.0, con criterios
  `cumple/parcial/no`, regularización P0, presets para Codex/Claude/Gemini,
  matriz biblia ↔ harness y sincronización versionada.
- `HARNESS_VERSION` fija la versión operativa del harness que la biblia debe
  reflejar.

### Modificado

- Prompts operativos `lider`, `implementer`, `reviewer`, `worker` y
  `cierre-fase` ahora reconocen `Hebri-AI-Harness 0.5.0`.
- `.github/copilot-instructions.md` corrige 9 volúmenes y elimina la
  exigencia obsoleta de markdownlint/link checker.
- CI valida referencias en `README.md`, `biblia/README.md`, `BIBLIA.md` y
  cantidad esperada de volúmenes.
- CI valida apéndices y que `README.md`, Vol 07 y `CHANGELOG.md` mencionen la
  versión indicada en `HARNESS_VERSION`.
- Apéndice end-to-end actualizado con preflight, approval envelope, artefactos
  P0, gates `G0` a `G7` y cierre explícito de agentes.
- `.gitignore` deja de ignorar `.github/`, porque prompts y workflows son
  parte versionada de este repo.

---

## [2.6.0] — 2026-05-19

### Añadido

- README y `biblia/README.md` ahora incluyen rutas de lectura mínima para
  evitar cargar toda la biblia por defecto.
- Vol 05 agrega reglas de prompts versionados y separación entre prompt
  liviano y spec larga.
- Vol 07 formaliza la relación entre `Hebri-AI-Structure` y
  `Hebri-AI-Harness`, más un protocolo de evolución biblia ↔ harness.
- Vol 08 agrega perfiles de contexto por rol para reducir tokens.
- Vol 07 ahora documenta el harness mínimo recomendado con modos,
  registry, locks, gates, handoffs, políticas y guía de AI Engineering.
- Vol 08 agrega modos `manual` y `automático`, arquitectura de runtime LLM,
  resiliencia de llamadas a modelos, validación de salidas, cache y economía
  de tokens.
- Vol 09 agrega protocolo multiagente: límite operativo de 5 agentes activos
  totales (leader + 4 subagentes), registry, locks, gates y handoffs por ciclo.
- Prompt `/lider` actualizado para respetar modo, slots, registry y aprobación
  explícita antes de mutar estado.

### Modificado

- README actualizado a versión 2.6.0 y con ruta práctica hacia el protocolo
  multiagente.
- AGENTS.md alineado con el CI actual: se elimina la referencia operativa a
  markdownlint/lychee como checks obligatorios y queda la consistencia de TOC.
- Gap H-01 pasa de "Identificado" a "Publicado · En validación" por la
  publicación de `Hebri-AI-Harness` como repo independiente.

---

## [2.5.0] — 2026-05-18

### Eliminado

- Eliminados `markdownlint` y `lychee` (link checker) del workflow de GitHub Actions (`ci.yml`) y borrado `.markdownlint.json`. Las validaciones estrictas de formato entorpecían la operación metodológica del repositorio. Mantenida la validación de `toc-consistency`.

---

## [2.1.0] — 2026-05-18

### Añadido

- **Vol 09 — Roles Cerrados de Harness**: define los 4 roles del flujo
  con harness (leader, spec_author, implementer, reviewer), su tabla de
  permisos, contrato de handoff, regla anti-teléfono-descompuesto y mapa
  de pivoteo. Recupera el concepto del repo base "The AI Biblia" que en
  v2.0.0 había quedado solo implícito.
- Vol 04 ahora describe la estructura completa de la carpeta
  `.github/orquestador/` (context/, sdd/, prompts/, pipelines/, policies/).
- Cuatro prompts operativos nuevos, uno por rol cerrado:
  `lider.prompt.md`, `spec-author.prompt.md`, `implementer.prompt.md`,
  `reviewer.prompt.md`. Nombres ASCII-safe para evitar problemas en
  filesystems y bash.

### Modificado

- Vol 02 ahora apunta a Vol 09 con criterio explícito de cuándo migrar de
  explorer/worker a los 4 roles cerrados.
- Vol 03 referencia Vol 09 para los roles que sostienen el flujo SDD.
- Cadena de navegación reordenada: Vol 08 → Vol 09 → Apéndice.
- README ahora trae una sección "Uso y propuestas" en lugar de las
  secciones de Cómo contribuir / Licencia.

### Eliminado

- `LICENSE` y `CONTRIBUTING.md`. El repo es metodología personal — quien
  quiera usarla o sumar algo, lo charla directamente con el autor.

---

## [2.0.0] — 2026-05-18

### Cambios estructurales

- `BIBLIA.md` se partió en 8 archivos bajo `biblia/` (uno por volumen) más un
  apéndice. El monolito sigue presente como índice/redirección para no romper
  links externos.
- Renombrado `README-PROGRESPJ.md` → `PROGRESS.md` en todas las referencias.

### Añadido

- `.editorconfig` y `.markdownlint.json`.
- CI en `.github/workflows/`: link checker (lychee) y markdownlint.
- **Vol 08 — MCPs, tool use y niveles de autonomía**: cubre permisos de
  herramientas, escala de autonomía del agente, recuperación de errores y
  economía de contexto.
- **Apéndice — Ejemplo end-to-end**: slice ficticio recorrido completo,
  desde la intención hasta el cierre con gaps registrados.
- Vol 06 extendido con bibliotecas de gaps para **Docs**, **Testing** y
  **Security/Compliance**.
- Diagramas Mermaid reemplazando los principales bloques ASCII (ciclo de
  trabajo, flujo SDD, árbol de fuente de verdad).
- Nuevos prompts operativos: `explorar`, `worker`, `registrar-gap`,
  `revisar-spec`, `brief`.

### Modificado

- Vol 07 ahora marca el harness template como gap formal (Gap H-01) usando
  el formato de Vol 06. Se planificará como repo aparte (`Hebri-AI-Harness`).
- Prompts existentes con variantes neutrales de herramienta (no solo VS Code
  Copilot).
- `AGENTS.md` raíz: ahora incluye comandos concretos de validación del repo.

### Corregido

- Typo `PROGRESPJ` → `PROGRESS` en todas las referencias.
- Links rotos hacia anchors de la antigua `BIBLIA.md`.

---

## [1.0.0] — 2026-05 (versión inicial)

- 7 volúmenes en archivo único `BIBLIA.md`.
- `AGENTS.md`, `README.md`, 4 prompts en `.github/prompts/`.
- Base: [The AI Biblia](https://github.com/Nicolas-Melluso/The-AI-Biblia)
  de Nicolás Ezequiel Melluso.
