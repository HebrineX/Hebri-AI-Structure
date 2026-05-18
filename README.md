# Hebri-AI-Structure

Metodología personal de trabajo con inteligencia artificial aplicada a proyectos de software.

> **Para IAs que lean este repo:** leer primero `AGENTS.md`.

---

## Qué es esto

Una biblia de metodología — no un harness ejecutable. Documenta cómo pienso, cómo planifico
y cómo trabajo con IA en proyectos reales. El harness concreto para cada proyecto viene de
un repo de templates separado; esta biblia define el *por qué* y el *cómo* detrás de esos templates.

El objetivo central es uno: **nunca analizar lo mismo dos veces**. Todo lo que se entiende
queda escrito. Todo lo que se decide queda registrado. Todo lo que falta queda nombrado.

---

## Índice

| # | Volumen | Qué cubre |
|---|---|---|
| 01 | [Modelo de trabajo](./01-modelo-de-trabajo/) | Las 4 capas, ciclo de trabajo, unidad mínima de contexto |
| 02 | [Subagentes](./02-subagentes/) | Explorer/Worker, ownership, paralelismo, anti-patrones |
| 03 | [SDD](./03-sdd/) | Fases vs slices, flujo SDD, EARS, checklist de cierre |
| 04 | [Arquitectura de repo](./04-repo-architecture/) | Mapa de responsabilidades, fuente de verdad, stacks |
| 05 | [Prompts](./05-prompts/) | Brief operativo, prompts reutilizables, anti-patrones |
| 06 | [Gap tracking](./06-gap-tracking/) | Qué es un gap, cómo se estructura, biblioteca por stack |
| 07 | [Acoplamiento con harness](./07-acoplamiento-con-harness/) | Cómo usar esta biblia + un harness template |

---

## Ruta práctica

**Si arrancás un proyecto nuevo:**
→ Empezá por `07-acoplamiento-con-harness/como-definir-tipo-de-proyecto.md`

**Si estás planeando una fase o slice:**
→ Empezá por `03-sdd/fases-vs-slices.md`

**Si querés delegar trabajo a subagentes:**
→ Empezá por `02-subagentes/explorer-worker.md`

**Si estás definiendo la estructura de un repo:**
→ Empezá por `04-repo-architecture/mapa-responsabilidades.md`

**Si encontraste algo que falta en el proyecto:**
→ Empezá por `06-gap-tracking/estructura.md`

---

## Principio base

> El chat coordina. El repositorio conserva la verdad.

Todo lo que importa vive en archivos. Lo que no está escrito no existe para la próxima sesión,
para el próximo agente, ni para el próximo integrante del equipo.

---

## Relación con harness templates

Esta biblia se acopla con repositorios de harness que implementan la estructura concreta.
Ver `07-acoplamiento-con-harness/` para entender cómo funciona ese puente.

---

## Créditos y base intelectual

Este repositorio está construido sobre los conceptos y estructura de
**[The AI Biblia](https://github.com/Nicolas-Melluso/The-AI-Biblia)**
de [Nicolás Ezequiel Melluso](https://www.linkedin.com/in/nicolas-ezequiel-melluso/).

La Biblia Moderna es un libro práctico sobre uso moderno de inteligencia artificial,
subagentes, SDD, AGENTS.md, GitHub, prompt engineering y harness engineering.

**Lo que este repo toma de The AI Biblia:**
- El modelo de 4 capas (contexto / tarea / verificación / memoria)
- El ciclo de trabajo y la unidad mínima de contexto
- Los patrones Explorer/Worker y el sistema de roles
- SDD con requirements EARS, design y tasks trazables
- La estructura de AGENTS.md como contrato operativo
- El mapa de responsabilidades por archivo
- El enfoque de prompts reutilizables versionados

**Lo que este repo agrega o adapta:**
- Fases vs slices como dos escalas de planificación simultáneas
- YAML como lenguaje de dominio (patrón no cubierto en la Biblia)
- Sistema de gap tracking con biblioteca por stack (Dev / Infra / DevOps)
- README-PROGRESPJ.md como artefacto de progreso separado del README principal
- Stacks específicos: .NET 10 / xUnit y Python / pytest
- Resolución explícita de la tensión GitHub-first vs portable (fuente de verdad única)
- Preguntas de arranque para definir escala desde el primer día
