# Changelog

## [3.3.2] — 2026-06-17

### Added
- La biblia adopta `Hebri-AI-Harness 0.8.9` como referencia operativa.
- El apéndice documenta el hardening 0.8.9: regularizer robusto para
  `required_gates`, drift operativo sin falsos positivos históricos y
  presupuestos con margen controlado.

### Changed
- README, índice, `BIBLIA.md`, `AGENTS.md`, prompts operativos y
  `HARNESS_VERSION` quedan alineados con `Hebri-AI-Harness 0.8.9`.
- Vol 07 diferencia 0.8.8 como regularización inicial y 0.8.9 como
  endurecimiento contra fallas de migración repetibles.

### Fixed
- Se explicita que `init.sh` no debe bloquear historia válida en `PROGRESS.md`,
  APRs, roadmap o notas de migración.
- Se documenta que los presupuestos livianos deben tener margen operativo
  suficiente para metadatos mínimos sin abandonar el objetivo de ahorro.

## [3.3.1] — 2026-06-17

### Added
- La biblia adopta `Hebri-AI-Harness 0.8.8` como referencia operativa.
- El apéndice de operación documenta `regularize-state.ps1` y
  `regularize-registry.ps1` para corregir schema drift durante migraciones
  preservando estado local.
- Vol 07 incorpora 0.8.8 como capa de regularización de migraciones y
  compatibilidad PowerShell 5.1.

### Changed
- README, índice, `BIBLIA.md`, `AGENTS.md`, prompts operativos y
  `HARNESS_VERSION` quedan alineados con `Hebri-AI-Harness 0.8.8`.
- La matriz 0.8.x diferencia 0.8.7 como instruction builder/drift y 0.8.8
  como regularización check-only/apply para `state.yaml` y `registry.yaml`.

### Fixed
- Se documenta que el builder de instrucciones no debe depender de APIs
  incompatibles con PowerShell 5.1.
- Se explicita que migrar `state.yaml`/`registry.yaml` no implica regenerar ni
  sobrescribir historia del proyecto: primero check-only, luego `SI`, luego
  `-Apply` con backup.

## [3.3.0] — 2026-06-16

### Added
- La biblia adopta `Hebri-AI-Harness 0.8.7` como referencia operativa.
- Vol 07 documenta la linea 0.8.3-0.8.7: detractor senior, adapters portables, runtime `/harness`, Claude reentry e instruction builder.
- Vol 08 incorpora runtime/budget como control de autonomia y ahorro de contexto.
- Vol 09 separa `detractor-senior` y `reporter` como perfiles/roles con limites claros.
- El apendice de operacion suma matriz de controles 0.8.3 a 0.8.7.

### Changed
- README, indice, `BIBLIA.md`, `AGENTS.md`, prompts operativos y `HARNESS_VERSION` quedan alineados con `Hebri-AI-Harness 0.8.7`.
- La referencia metodologica pasa de economia de contexto 0.8.2 a runtime y drift validator 0.8.7.

### Rationale
- El harness debe ser vehiculo completo y portable entre IAs, sin depender de memoria conversacional ni de presets divergentes.

## [3.2.1] — 2026-06-06

### Added
- La biblia adopta `Hebri-AI-Harness 0.8.2` como referencia operativa.
- Vol 07 documenta `context-budget.yaml`, `scripts/validate-harness.ps1`,
  schemas livianos, `memory-closure-checklist.md` y exclusión material de
  `infoHebri.md` del harness operativo.
- Vol 08 incorpora presupuestos de contexto, pruebas negativas y regla de no
  cargar memoria completa ni documentación personal sin aprobación explícita.
- Vol 09 asigna al leader y al auditor de costo responsabilidad sobre
  presupuesto de contexto, memoria activa y cierre de memoria.

### Changed
- README, índice, `BIBLIA.md`, `AGENTS.md`, prompts operativos y
  `HARNESS_VERSION` quedan alineados con `Hebri-AI-Harness 0.8.2`.
- El apéndice de operación pasa a cubrir operación, presupuesto y auditoría de
  Harness 0.8.2.

### Fixed
- Se corrigen filas y líneas pegadas en gates y prompts operativos que podían
  inducir lecturas ambiguas del contrato.

## [3.2.0] — 2026-06-05

### Added
- La biblia adopta `Hebri-AI-Harness 0.8.0` como referencia operativa.
- Vol 07 documenta memoria estratificada gobernada por orquestador: local, diaria, ciclo, proyecto y completa.
- Vol 08 incorpora `memory-registry.yaml`, `memory-routing.yaml`, entrypoints de reentry y adapters multi-IA como controles de autonomia/contexto.
- Vol 09 alinea roles cerrados con memoria contractual: el leader decide capas activas, el auditor contrasta memoria contra evidencia y el reporter no altera veredictos.
- El apendice de operacion del harness pasa a 0.8.0 e incluye estructura `orquestador/memory/`, `orquestador/entrypoints/` y `orquestador/adapters/`.

### Changed
- `HARNESS_VERSION`, README, AGENTS, BIBLIA, indice interno y prompts operativos quedan alineados a `Hebri-AI-Harness 0.8.0`.
- La recuperacion post-compactacion deja de depender de reentry manual largo y pasa a cargar `session-pin`, registry/routing de memoria y entrypoint correspondiente.

### Rationale
- La memoria de una IA no es deterministica ni portable. La biblia formaliza que la memoria valida vive en archivos del harness y que el orquestador, no el modelo, decide que capas se cargan.
Todos los cambios notables a esta metodología se documentan en este archivo.

El formato sigue [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y la
versión sigue [Semantic Versioning](https://semver.org/lang/es/).

---

## [3.1.1] — 2026-05-29

### Añadido

- La biblia adopta `Hebri-AI-Harness 0.7.9` como referencia operativa.
- Vol 07 documenta `orquestador/harness-manifest.txt` como manifest
  estructural del harness y aclara que el orquestador vigente vive en
  `.hebrinex/orquestador/`.
- Vol 08 incorpora los controles 0.7.x: evidencia histórica, deploy/migración,
  drift de referencias, CI/pipeline, backlog, cierre con cross-links y presets
  por IA.
- Vol 09 actualiza gates, perfiles y responsabilidades de auditor/reporter
  para incluir el perfil `pipeline` y los gates condicionales 0.7.x.

### Modificado

- README, índice de la biblia, `BIBLIA.md`, `AGENTS.md`, prompts operativos y
  `HARNESS_VERSION` quedan alineados con `Hebri-AI-Harness 0.7.9`.
- El apéndice de operación del harness pasa de 0.7.0 a 0.7.9 y separa con más
  claridad qué es estructura mínima, qué es manifest y qué controles son
  condicionales por tipo de tarea.
- Se refuerza que la biblia no reemplaza al harness: explica el criterio, pero
  la implementación exacta vive en el repo `Hebri-AI-Harness`.

---

## [3.1.0] — 2026-05-29

### Añadido

- La biblia adopta `Hebri-AI-Harness 0.7.0` como referencia operativa.
- Vol 07 incorpora el principio de binding de proyecto: `source_template`
  como fuente libre copiable y `bound` como harness vinculado al proyecto.
- Vol 08 documenta re-entry post-compactación, expiración de approvals y
  preflight con `project_root`, `harness_path`, `binding_status` y scope
  externo.
- Vol 09 asigna al leader, auditor y reporter responsabilidades explícitas
  para validar binding, evitar contaminación entre proyectos y reportar
  mismatch sin ocultarlo.

### Modificado

- README, índice de la biblia, `BIBLIA.md`, `AGENTS.md`, prompts operativos y
  `HARNESS_VERSION` quedan alineados con `Hebri-AI-Harness 0.7.0`.
- El apéndice de operación del harness pasa de auditoría 0.6.0 a operación
  0.7.0, con reglas de resolución estricta, bootstrap seguro y re-entry.
- La metodología aclara que la biblia explica el principio; la implementación
  exacta vive en el repo `Hebri-AI-Harness`.

---

## [3.0.0] — 2026-05-23

### Añadido

- La biblia adopta `Hebri-AI-Harness 0.6.0` como referencia operativa.
- Vol 02 redefine `Explorer/Worker` como familias base y agrega el principio
  de roles mínimos con perfiles parametrizados.
- Vol 08 incorpora independencia técnica, anti-confirmation bias y detractor
  pass como controles contra errores del usuario y de los agentes.
- Vol 09 amplía el modelo de roles cerrados con `interpreter`, `leader`,
  `executor`, `reviewer`, `auditor` y `reporter`, más perfiles como
  `detractor`, `cost`, `security`, `architecture`, `operator` y `technical`.
- El apéndice de operación y auditoría documenta el roadmap `3.0.0/0.6.0`,
  fases/slices P0-P2 y el piloto recomendado `Hebri-AI-Portfolio`.

### Modificado

- README, índice de la biblia, `BIBLIA.md`, `AGENTS.md` y `HARNESS_VERSION`
  quedan alineados con la versión operativa `Hebri-AI-Harness 0.6.0`.
- La metodología pasa a tratar la contradicción técnica controlada como parte
  del cierre de decisiones importantes, sin aumentar el límite de agentes.

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
