# Acoplamiento con Harness Templates

Esta biblia define el *por qué* y el *cómo* — la metodología, los principios
y los patrones. Un harness template define el *qué concreto* — los archivos,
carpetas y configuraciones que implementan esa metodología en un proyecto real.

---

## La relación

```
Hebri-AI-Structure (esta biblia)
  └── Define: principios, patrones, formatos, biblioteca de gaps

Harness Template (repo separado)
  └── Implementa: AGENTS.md base, estructura de carpetas, templates de specs,
                  prompts pre-cargados, scripts de setup

Proyecto nuevo
  └── Toma: el harness template como base
  └── Referencia: esta biblia para entender las decisiones
```

El harness template no duplica la metodología — la implementa. Si hay conflicto
entre el template y la biblia, la biblia tiene precedencia.

---

## Contenido de este volumen

| Archivo | Contenido |
|---|---|
| `como-definir-tipo-de-proyecto.md` | Las preguntas que determinan qué estructura usar |
| `como-acoplar.md` | Cómo usar esta biblia junto a un harness template concreto |

---

## Cuándo usar esta sección

- Al arrancar un proyecto nuevo.
- Al evaluar si un harness template existente se alinea con esta metodología.
- Al crear un harness template nuevo basado en esta biblia.
