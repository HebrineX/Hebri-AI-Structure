# Cómo Acoplar la Biblia con un Harness Template

El acoplamiento entre esta biblia y un harness template tiene una dirección clara:
el template implementa la biblia, no al revés. Si hay conflicto, la biblia gana.

---

## Qué aporta cada parte

### Esta biblia aporta:
- El modelo mental (4 capas, ciclo de trabajo, fuente de verdad)
- El proceso (SDD, fases vs slices, gap tracking)
- Las convenciones por stack
- La biblioteca de gaps comunes
- Los formatos (EARS, brief operativo, checklist de cierre)

### El harness template aporta:
- La estructura de carpetas lista para copiar
- Un `AGENTS.md` base pre-cargado con los comandos del stack
- Templates de `requirements.md`, `design.md`, `tasks.md` con la estructura correcta
- Prompts `.prompt.md` pre-cargados en `.github/prompts/`
- Un `README-PROGRESPJ.md` inicial
- Scripts de setup si aplica (`init.sh`, `setup.ps1`, etc.)

---

## El flujo de arranque de un proyecto nuevo

```
1. Responder preguntas de tipo de proyecto
   → 07-acoplamiento-con-harness/como-definir-tipo-de-proyecto.md

2. Elegir el harness template que corresponde al tipo

3. Copiar el harness template como base del nuevo repo

4. Personalizar AGENTS.md con:
   - Nombre del proyecto
   - Stack específico y comandos de verificación
   - Reglas operativas particulares

5. Completar README-PROGRESPJ.md con:
   - Fases o slices iniciales identificados
   - Gaps iniciales (tomados de la biblioteca de esta biblia)

6. Agregar las reglas o convenciones del stack desde:
   - 04-repo-architecture/stacks/dotnet.md
   - 04-repo-architecture/stacks/python.md
   - 04-repo-architecture/stacks/yaml-domain.md (si aplica)

7. Empezar el trabajo con el ciclo de trabajo definido en:
   - 01-modelo-de-trabajo/ciclo-de-trabajo.md
```

---

## Cómo referenciar la biblia desde el proyecto

En el `AGENTS.md` del proyecto, agregar una línea de referencia:

```markdown
## Metodología
Este proyecto sigue la metodología definida en Hebri-AI-Structure.
Para entender el por qué de la estructura, ver: [link al repo de la biblia]
```

No copiar el contenido de la biblia al proyecto — referenciarla. Si la biblia
se actualiza, los proyectos se benefician automáticamente.

---

## Cuándo actualizar la biblia vs cuándo actualizar el harness

**Actualizar la biblia cuando:**
- Cambió la metodología o el proceso.
- Apareció un nuevo patrón que se repite en varios proyectos.
- Se identificó un anti-patrón nuevo con impacto real.
- Se agregó soporte para un nuevo stack.

**Actualizar el harness template cuando:**
- Cambió la estructura de carpetas recomendada.
- Se actualizaron los prompts base.
- Se cambió el formato de un template (requirements, design, tasks).
- Se agregó soporte para un nuevo tipo de proyecto.

**Actualizar el proyecto individual cuando:**
- Cambió algo específico de ese proyecto.
- Se adoptó una nueva convención que no aplica a todos los proyectos.

---

## Señal de desacoplamiento sano

El proyecto está bien acoplado a la biblia cuando:
- Cualquier persona nueva puede entender la estructura leyendo solo `AGENTS.md`
  y la biblia referenciada.
- Un agente puede retomar el trabajo leyendo solo los artefactos del repo.
- Los gaps están registrados y tienen referencia a la biblioteca de la biblia.
- El checklist de cierre coincide con el definido en `03-sdd/checklist-cierre.md`.
