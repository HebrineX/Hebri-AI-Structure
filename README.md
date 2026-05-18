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
| [Vol 02](./biblia/vol-02-subagentes.md) | Subagentes (Explorer/Worker) | Antes de delegar |
| [Vol 03](./biblia/vol-03-sdd.md) | Specification-Driven Development | Antes de planear fase o feature |
| [Vol 04](./biblia/vol-04-arquitectura-repo.md) | Arquitectura de repo | Antes de definir estructura |
| [Vol 05](./biblia/vol-05-prompts.md) | Prompts | Antes de escribir prompts |
| [Vol 06](./biblia/vol-06-gap-tracking.md) | Gap tracking | Al identificar gaps |
| [Vol 07](./biblia/vol-07-harness.md) | Acoplamiento con harness | Al arrancar un proyecto |
| [Vol 08](./biblia/vol-08-mcps-y-autonomia.md) | MCPs, tool use y autonomía | Antes de dar acceso a herramientas |
| [Vol 09](./biblia/vol-09-roles-cerrados.md) | Roles cerrados de harness | Al pasar a SDD con aprobación |
| [Apéndice](./biblia/apendice-ejemplo-end-to-end.md) | Ejemplo end-to-end | Onboarding o duda práctica |

> El archivo monolito original `BIBLIA.md` se conserva como redirección por
> compatibilidad con links externos viejos.

---

## Ruta práctica

- **Proyecto nuevo** → [Vol 07 · Cómo definir el tipo de proyecto](./biblia/vol-07-harness.md#cómo-definir-el-tipo-de-proyecto)
- **Planear fase o slice** → [Vol 03 · Fases vs Slices](./biblia/vol-03-sdd.md#fases-vs-slices)
- **Delegar a subagentes** → [Vol 02 · Explorer/Worker](./biblia/vol-02-subagentes.md#explorerworker)
- **Definir estructura de repo** → [Vol 04 · Mapa de responsabilidades](./biblia/vol-04-arquitectura-repo.md#mapa-de-responsabilidades)
- **Registrar algo que falta** → [Vol 06 · Gap tracking](./biblia/vol-06-gap-tracking.md)
- **Configurar acceso a herramientas** → [Vol 08 · MCPs y autonomía](./biblia/vol-08-mcps-y-autonomia.md)
- **Orquestar entre roles (leader, spec_author, implementer, reviewer)** → [Vol 09 · Roles cerrados](./biblia/vol-09-roles-cerrados.md)
- **Ver un caso completo** → [Apéndice · Ejemplo end-to-end](./biblia/apendice-ejemplo-end-to-end.md)

---

## Principio base

> El chat coordina. El repositorio conserva la verdad.

---

## Uso y propuestas

Este repo es una metodología personal. Si querés usarla, adaptarla o
sumar algo (un volumen, una corrección, un ejemplo, una crítica),
escribime y lo charlamos.

## Versión y cambios

Versión actual: **2.1.0**. Ver [CHANGELOG.md](./CHANGELOG.md).

## Créditos

Construido sobre **[The AI Biblia](https://github.com/Nicolas-Melluso/The-AI-Biblia)**
de [Nicolás Ezequiel Melluso](https://www.linkedin.com/in/nicolas-ezequiel-melluso/).
