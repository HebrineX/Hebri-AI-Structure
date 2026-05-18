# Prompts Reutilizables

Un prompt reutilizable es un brief operativo versionado para una tarea que se repite.
Vive en `.github/prompts/` con extensión `.prompt.md` y puede ser invocado desde
Copilot Chat o referenciado como template al trabajar con cualquier agente.

La regla: si escribiste el mismo contexto tres veces, es un prompt reutilizable.

---

## Estructura de un prompt file

```markdown
---
description: "[una línea de qué hace este prompt]"
---

[instrucciones del prompt, con variables ${input:nombre:descripcion} si aplica]
```

---

## Prompts base del repo

### arrancar-proyecto.prompt.md

Hace las preguntas necesarias para definir la estructura correcta del proyecto
antes de crear ningún archivo.

```markdown
---
description: "Definir el tipo y escala de un proyecto nuevo"
---

Antes de crear cualquier archivo, necesito entender el proyecto.
Respondé estas preguntas:

1. ¿Cuál es el objetivo principal del proyecto? (una frase)
2. ¿Qué stack usás? (lenguaje, framework, herramienta de test)
3. ¿Es un proyecto solo o de equipo?
4. ¿Tenés idea de cuántas fases o cuánta complejidad va a tener?
   - Chico (script, herramienta simple)
   - Mediano (servicio o módulo con varias capacidades)
   - Grande (sistema con múltiples módulos y roadmap largo)
   - No sé todavía
5. ¿Hay integraciones externas? (APIs, bases de datos, herramientas de infra)
6. ¿El proyecto tiene reglas de dominio configurables? (YAML rules, políticas, etc.)

Con esas respuestas te propongo la estructura de carpetas, AGENTS.md,
y el primer README-PROGRESPJ.md ajustados a tu caso.
```

---

### plan-fase.prompt.md

```markdown
---
description: "Planear una nueva fase antes de empezar a implementar"
---

Estoy por arrancar la Fase ${input:numero:Número de fase} del proyecto.

Antes de escribir código, necesito:

1. Definir el objetivo de la fase en una frase.
2. Listar los gaps de la fase anterior que se resuelven aquí.
3. Listar los gaps nuevos que se difieren.
4. Escribir requirements.md con formato EARS.
5. Escribir design.md con archivos afectados y decisiones.
6. Escribir tasks.md con trazabilidad a requirements.
7. Definir el criterio de cierre (qué tests deben pasar, qué build debe correr).

Contexto del proyecto:
${input:contexto:Describí brevemente el proyecto y el estado actual}
```

---

### plan-slice.prompt.md

```markdown
---
description: "Planear un slice concreto dentro de una fase"
---

Slice: ${input:nombre:Nombre del slice}
Fase: ${input:fase:Fase a la que pertenece}

Producir:
1. Requirements en formato EARS (mínimo R1, R2).
2. Tasks con trazabilidad a requirements.
3. Criterio de verificación (comando).
4. Gaps identificados durante el análisis.

No implementar todavía. Solo la spec.
```

---

### cierre-fase.prompt.md

```markdown
---
description: "Cerrar formalmente una fase con evidencia"
---

Voy a cerrar la Fase ${input:numero:Número de fase}.

Checklist a verificar:
1. ¿Todos los tests pasan? → correr el comando de verificación.
2. ¿El build de release corre limpio?
3. ¿Todos los requirements tienen al menos un test?
4. ¿Los gaps diferidos están registrados en README-PROGRESPJ.md?
5. ¿La documentación refleja el estado actual (README, QUICKSTART, guías)?
6. ¿El tag de versión está creado?

Producir:
- Resumen de cierre de fase con evidencia concreta.
- Lista de gaps diferidos a la siguiente fase.
- Próximos pasos sugeridos.
```

---

## Cuándo agregar un prompt nuevo

Un prompt nuevo se agrega cuando:
- El mismo brief se escribió más de dos veces en conversaciones distintas.
- Hay una tarea que siempre necesita el mismo contexto base.
- Un prompt existente se usó de forma distinta a su descripción original.

Un prompt se actualiza cuando:
- Un paso del proceso cambió.
- El stack cambió.
- El prompt produjo un malentendido que se hubiera evitado con más precisión.

Un prompt se borra cuando:
- La tarea que cubría ya no existe.
- Fue reemplazado por un prompt más preciso.
