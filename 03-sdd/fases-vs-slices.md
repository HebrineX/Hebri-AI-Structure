# Fases vs Slices

El libro de referencia habla de "slices" como unidad de trabajo. En proyectos
reales de mediana y gran escala, una sola dimensión no alcanza. La distinción
entre fases y slices permite planear a dos escalas simultáneamente — y es lo que
hace que un roadmap sea legible sin perder granularidad operativa.

---

## Definiciones

### Fase
Una fase es una unidad de trabajo de **escala global**. Agrupa una capacidad
completa o un conjunto de capacidades relacionadas que, al terminar, llevan el
sistema a un estado cualitativamente distinto al anterior.

- Tiene nombre y número.
- Tiene estado: pendiente / en progreso / completa.
- Su cierre deja evidencia verificable (build limpio, tests verdes, tag).
- Puede contener varios slices.
- Puede diferir gaps explícitamente a la siguiente fase.

### Slice
Un slice es una unidad de trabajo de **escala granular**. Es la parte más pequeña
que puede implementarse, verificarse y mergearse de forma independiente.

- Tiene un objetivo acotado.
- Tiene criterio de aceptación propio.
- Cubre uno o más requirements de la fase.
- Puede estar dentro de una fase o ser la unidad principal en proyectos simples.

---

## Cuándo usar qué

| Tipo de proyecto | Unidad de planificación recomendada |
|---|---|
| Script o herramienta pequeña | Solo slices |
| Servicio o módulo con varias capacidades | Fases + slices internos |
| Sistema con múltiples integraciones y roadmap largo | Fases con sub-fases + slices |
| Proyecto que puede escalar a monumental | Empezar con slices, introducir fases cuando aparezca la necesidad de agrupar |

La escala se define al arrancar. Un proyecto que hoy parece pequeño puede
volverse monumental — por eso la estructura debe soportar ambos modos desde
el principio, aunque al inicio solo se use el más liviano.

---

## La relación entre fases y slices

```
Fase 1 — Capacidad base
  ├── Slice 1.1 — Ingestión de eventos
  ├── Slice 1.2 — Clasificación básica
  └── Slice 1.3 — Reporter de resumen

Fase 2 — Motor de reglas
  ├── Slice 2.1 — YAML rule catalog
  ├── Slice 2.2 — Rule evaluator
  ├── Slice 2.3 — Clasificación guiada por reglas
  └── Slice 2.4 — Reporter agrupado por regla

Fase 2.5 — Enrichment (diferida de Fase 2)
  └── Slice 2.5.1 — HttpMethod enricher
```

Las fases son la vista para el roadmap. Los slices son la vista para el
día a día de implementación.

---

## Estado visible de fases

El estado de las fases debe ser visible en el README del proyecto
(o en `README-PROGRESPJ.md` si el proyecto usa ese archivo separado).

Formato mínimo:

```markdown
## Estado del proyecto

| Fase | Descripción | Estado |
|---|---|---|
| Fase 1 | Capacidad base | ✅ Completa |
| Fase 2 | Motor de reglas YAML | ✅ Completa |
| Fase 2.5 | Enrichment HTTP | 🔄 En progreso |
| Fase 3 | Threat Intelligence | ⏳ Pendiente |
```

---

## Versionado por fases y slices

El versionado es una posibilidad, no una norma. Cuando se usa, la convención
recomendada es:

- **Por fase:** `v[major].[fase].[patch]` — ej: `v0.2.0-phase2`
- **Por slice:** sin tag formal, o tag ligero de referencia
- **Semántico:** `vMAJOR.MINOR.PATCH` cuando el proyecto tiene API pública

No se impone un esquema. Se define al arrancar el proyecto y se mantiene
consistente. Lo importante es que el tag sea verificable y represente un
estado real (build limpio, tests verdes).

---

## Gaps entre fases

Cuando una fase cierra y deja cosas diferidas, esos gaps quedan registrados
explícitamente — no como silencio sino como estado documentado.

```markdown
## Gaps diferidos de Fase 2 → Fase 2.5

| # | Descripción | Motivo del diferimiento |
|---|---|---|
| Gap #1 | HttpMethod no se enriquece | Requiere acceso a log de acceso (fuera del scope Fase 2) |
| Gap #2 | Regex como operador de regla | Complejidad adicional, diferida para evaluar uso real |
```

Ver `../06-gap-tracking/` para el sistema completo de gestión de gaps.
