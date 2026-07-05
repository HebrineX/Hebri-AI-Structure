# Biblia — Índice

Metodología de trabajo con IA aplicada a proyectos de software.

| Volumen | Tema | Cuándo leer |
|---|---|---|
| [Vol 01](./vol-01-modelo-de-trabajo.md) | Modelo de trabajo | Duda sobre el approach general |
| [Vol 02](./vol-02-subagentes.md) | Subagentes, roles mínimos y perfiles | Antes de delegar trabajo |
| [Vol 03](./vol-03-sdd.md) | Specification-Driven Development | Antes de planear fase o feature |
| [Vol 04](./vol-04-arquitectura-repo.md) | Arquitectura de repo | Antes de definir estructura |
| [Vol 05](./vol-05-prompts.md) | Prompts operativos | Antes de escribir prompts |
| [Vol 06](./vol-06-gap-tracking.md) | Gap tracking | Al identificar gaps |
| [Vol 07](./vol-07-harness.md) | Acoplamiento con harness | Al arrancar un proyecto |
| [Vol 08](./vol-08-mcps-y-autonomia.md) | MCPs, tool use y niveles de autonomía | Antes de dar acceso a herramientas |
| [Vol 09](./vol-09-roles-cerrados.md) | Roles cerrados de harness (leader, spec_author, implementer, reviewer) | Al pasar de explorer/worker a SDD con aprobación |
| [Apéndice](./apendice-ejemplo-end-to-end.md) | Ejemplo end-to-end trabajado | Onboarding o duda práctica |
| [Apéndice Harness 0.13](./apendice-harness-0-5-operacion.md) | Operación, seguridad y auditoría de Harness 0.13.0 | Auditar cumplimiento, agentes, seguridad, migración, approvals, hooks o MCP |

---

**Principio base:** El chat coordina. El repositorio conserva la verdad.

---

## Lectura mínima por intención

| Intención | Ruta mínima |
|---|---|
| Arrancar proyecto nuevo | Vol 01 + Vol 04 + Vol 07 |
| Diseñar un flujo con agentes | Vol 02 + Vol 09 |
| Trabajar con specs aprobables | Vol 03 + Vol 09 |
| Escribir o refactorizar prompts | Vol 05 |
| Registrar deuda o faltantes | Vol 06 |
| Dar tools, modelos o autonomía | Vol 08 |
| Auditar o regularizar Harness 0.13.0 | Apéndice Harness 0.13 |
| Ver un recorrido completo | Apéndice |

No cargues todos los volúmenes salvo auditoría metodológica completa. La
unidad mínima de contexto también aplica a esta biblia.
