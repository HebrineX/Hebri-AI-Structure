# Estructura de un Gap

Un gap bien definido tiene campos que permiten entenderlo sin contexto adicional.
El objetivo es que cualquier persona o agente que lea el gap entienda qué es,
por qué no está resuelto, y cuándo se espera resolverlo.

---

## Campos de un gap

| Campo | Obligatorio | Descripción |
|---|---|---|
| `id` | Sí | Identificador estable (Gap #1, Gap #2...) |
| `descripción` | Sí | Qué falta en una frase |
| `contexto` | Sí | Por qué existe este gap, qué lo originó |
| `motivo de diferimiento` | Sí | Por qué no se resuelve ahora |
| `estado` | Sí | Identificado / Diferido / En análisis / Resuelto / Descartado |
| `destino` | Si diferido | A qué fase o slice se mueve |
| `resuelto por` | Si resuelto | Referencia al PR, commit o tarea |
| `stack/capa` | Recomendado | Dev / Infra / DevOps / Docs / Testing |

---

## Formato

```markdown
## Gap #[N] — [título corto]

**Estado:** [Identificado | Diferido a Fase X | En análisis | Resuelto | Descartado]
**Capa:** [Dev | Infra | DevOps | Docs | Testing]

**Descripción:**
[Qué falta o está incompleto]

**Contexto:**
[Por qué existe este gap. Qué decisión lo originó o qué lo hizo visible]

**Motivo de diferimiento:**
[Por qué no se resuelve en la fase/slice actual]

**Destino:** Fase [N] / Slice [X] / Sin asignar

**Resuelto por:** [PR #XX / Commit / Tarea — si aplica]
```

---

## Dónde viven los gaps en el proyecto

Los gaps de un proyecto viven en el propio proyecto, no en esta biblia.
Esta biblia solo define el formato y la biblioteca de gaps comunes por stack.

Ubicaciones recomendadas dentro del proyecto:
- **README-PROGRESPJ.md** — gaps activos del proyecto con estado visible
- **Sección `##Gaps conocidos`** en el README principal — para proyectos simples
- **`docs/gaps.md`** — para proyectos con muchos gaps que merecen archivo propio

La elección depende de la escala del proyecto. Lo que no cambia es que los gaps
deben estar escritos en alguno de esos lugares, no en la memoria del equipo.

---

## Ejemplo completo

```markdown
## Gap #2 — HttpMethod no se enriquece

**Estado:** Diferido a Fase 2.5
**Capa:** Dev

**Descripción:**
WafEvent.HttpMethod siempre es null. El motor de reglas YAML puede evaluar ese
campo, pero sin enricher nunca tiene valor.

**Contexto:**
La regla yaml `threat-allowed-malicious-ip` evalúa HttpMethod para distinguir
entre GET y POST. Sin este campo, la regla nunca discrimina correctamente.
El enricher requiere acceso al log de acceso de nginx, lo que quedó fuera del
scope de Fase 2 para no bloquear el motor de reglas.

**Motivo de diferimiento:**
El acceso al log de acceso necesita decisión de arquitectura sobre dónde corre
el enricher (en el pipeline del worker o en un servicio separado). Esa decisión
se toma en Fase 2.5 cuando se evalúe el volumen real de logs.

**Destino:** Fase 2.5

**Resuelto por:** —
```

---

## Gestión de gaps con IA

Cuando trabajás con un agente y aparece un gap durante la sesión:

1. No detengas el trabajo principal para resolver el gap.
2. Registrá el gap inmediatamente con el formato mínimo (descripción + contexto + motivo).
3. Al cerrar la sesión, actualizá README-PROGRESPJ.md con el gap nuevo.
4. El agente no resuelve gaps no planificados por iniciativa propia.

El agente puede *identificar* gaps durante la exploración — eso es valioso.
No puede *resolver* gaps fuera del scope sin aprobación.
