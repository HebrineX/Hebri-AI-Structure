# Gap Tracking

Un gap es cualquier cosa que sabés que falta, que está incompleta, o que decidiste
no hacer todavía — pero que quedó registrada explícitamente para no perder el hilo.

El objetivo del gap tracking es uno: **nunca analizar lo mismo dos veces**.
Lo que se entiende queda escrito. Lo que se decide diferir queda nombrado con motivo.

---

## Contenido de este volumen

| Archivo | Contenido |
|---|---|
| `estructura.md` | Qué campos tiene un gap bien definido, cómo se organiza por proyecto |
| `README-PROGRESPJ.md` | Template de progreso con fases y slices para cualquier proyecto |
| `por-stack/dev.md` | Biblioteca de gaps comunes en proyectos de desarrollo |
| `por-stack/infra.md` | Biblioteca de gaps comunes en proyectos de infraestructura |
| `por-stack/devops.md` | Biblioteca de gaps comunes en proyectos de CI/CD y DevOps |

---

## La diferencia entre gap y tarea

| | Gap | Tarea |
|---|---|---|
| **Qué es** | Algo que falta o está incompleto, aún no listo para especificar | Algo concreto con spec aprobada |
| **Cuándo aparece** | Durante análisis, cierre de fase, o efecto de una decisión | Cuando el gap tiene spec y está aprobado |
| **Tiene fecha?** | No necesariamente | Sí |
| **Tiene owner?** | No necesariamente | Sí |
| **Ejemplo** | "HttpMethod no se enriquece todavía" | "Implementar HttpMethodEnricher" |

Un gap puede estar semanas o meses sin convertirse en tarea. Lo importante es
que esté nombrado y tenga motivo de diferimiento.

---

## Flujo de vida de un gap

```
Identificado → Registrado → [Diferido a fase X | En análisis | Resuelto]
```

Un gap nunca desaparece en silencio. Si se resuelve, se marca como resuelto
con referencia a qué lo cerró. Si se descarta, se marca como descartado con
el motivo.
