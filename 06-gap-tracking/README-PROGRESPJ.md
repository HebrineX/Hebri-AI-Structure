# README-PROGRESPJ — Template de Progreso

Este archivo es el estado vivo del proyecto. Refleja dónde está el trabajo,
qué falta y hacia dónde va. Se actualiza al cerrar cada fase o slice.

> **Nota:** Este es el template. Al usarlo en un proyecto, reemplazar todos
> los `[placeholders]` con la información real del proyecto.

---

## [Nombre del Proyecto]

**Descripción:** [una línea]
**Stack:** [lenguaje, framework, herramienta de test]
**Tipo:** [Simple / Mediano / Grande]

---

## Estado general

<!-- Elegir el modelo según el proyecto: por fases, por slices, o ambos -->

### Modelo por fases (proyectos medianos y grandes)

| Fase | Descripción | Estado | Tests |
|---|---|---|---|
| Fase 1 | [descripción] | ✅ Completa | [N] passed |
| Fase 2 | [descripción] | 🔄 En progreso | [N] passed |
| Fase 3 | [descripción] | ⏳ Pendiente | — |

### Modelo por slices (proyectos simples o fases granulares)

| Slice | Descripción | Estado |
|---|---|---|
| Slice 1 | [descripción] | ✅ Completo |
| Slice 2 | [descripción] | 🔄 En progreso |
| Slice 3 | [descripción] | ⏳ Pendiente |

---

## Gaps conocidos

| # | Descripción | Estado | Destino |
|---|---|---|---|
| Gap #1 | [descripción] | Diferido | Fase [X] |
| Gap #2 | [descripción] | Identificado | Sin asignar |
| Gap #3 | [descripción] | Resuelto | — (cerrado en Fase [X]) |

Ver `../../Hebri-AI-Structure/06-gap-tracking/estructura.md` para el formato
completo de cada gap.

---

## Criterios de cierre por fase

<!-- Completar antes de arrancar cada fase -->

### Fase [N] — [nombre]
- [ ] [N] tests pasando
- [ ] Build de release limpio
- [ ] Gaps diferidos documentados
- [ ] Documentación actualizada
- [ ] Tag creado: `v[versión]`

---

## Historial de cierres

### Fase 1 — [nombre] ✅
- Fecha de cierre: [fecha]
- Tests: [N] passed, 0 failed
- Comando: `[comando de verificación]`
- Tag: `v[versión]`
- Gaps diferidos: Gap #1, Gap #2

---

## Próximos pasos

1. [Próximo trabajo concreto]
2. [Segundo próximo paso]
3. [Decisión pendiente antes de arrancar Fase X]
