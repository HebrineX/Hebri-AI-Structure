# Changelog

Todos los cambios notables a esta metodología se documentan en este archivo.

El formato sigue [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y la
versión sigue [Semantic Versioning](https://semver.org/lang/es/).

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

- `LICENSE` (MIT).
- `CONTRIBUTING.md` con flujo de propuestas y criterio de cierre.
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
