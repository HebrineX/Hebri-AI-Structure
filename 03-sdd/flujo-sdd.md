# Flujo SDD

SDD (Specification-Driven Development) es la disciplina de ordenar la intención
antes de que el código la opaque. En el contexto de trabajo con IA, cumple una
función crítica: la IA no elige — ejecuta lo que el humano aprobó.

---

## Por qué el formalismo es necesario

El formalismo de SDD no es burocracia — es el mecanismo que garantiza que cada
decisión sea tuya. Sin spec aprobada, la IA completa los huecos con su propio
criterio. A veces acierta. En sistemas reales, a veces implementa la lógica
correcta para el problema equivocado.

La puerta humana antes de implementar existe por esto: un spec puede estar
perfectamente escrito y aun así resolver el problema equivocado. Solo el humano
sabe el tradeoff correcto para este negocio, este contexto, esta deuda técnica.

---

## El flujo

```
pending
  └── spec_author escribe requirements.md, design.md, tasks.md
        └── spec_ready
              └── ── HUMANO APRUEBA (puerta obligatoria) ──
                    └── in_progress
                          └── implementer ejecuta tasks aprobadas
                                └── reviewer verifica contra spec
                                      └── done (con evidencia)
```

Ningún paso puede saltarse. Si el scope cambia después de la aprobación,
el estado vuelve a `spec_ready` para re-aprobación.

---

## Los artefactos de cada paso

### requirements.md
Qué necesidad existe, a quién le importa, y bajo qué condiciones debe comportarse
el sistema. Usa formato EARS para requirements verificables. Ver `ears-requirements.md`.

### design.md
Qué archivos se tocan, qué decisiones se toman, qué alternativas se descartan
y por qué, qué riesgos existen, qué queda fuera del alcance.

### tasks.md
Lista ejecutable de pasos con trazabilidad a requirements. Cada task referencia
al menos un requirement. Cada requirement tiene al menos un test.

```markdown
- [ ] T1 — [descripción]. Cubre: R1.
- [ ] T2 — [descripción]. Cubre: R1, R2.
- [ ] T3 — test [nombre]. Cubre: R1.
```

---

## Formato de aprobación

Antes de mover a `in_progress`, el humano deja registro explícito:

```
Estado: aprobado
Aprobado por: [nombre]
Fecha: [fecha]
Alcance aprobado: R1, R2, R3
No objetivos aceptados: [lista]
Condición de cierre: todos los R con test verde y build limpio
```

Si no hay registro de aprobación, el implementer no arranca.

---

## Escala del formalismo según el proyecto

El flujo completo aplica a features con impacto real o complejidad media-alta.
Para correcciones menores o tareas de 1 solo paso, el formalismo se reduce:

| Escala | Artefactos mínimos |
|---|---|
| Corrección puntual | Un comment con objetivo + criterio de verificación |
| Feature pequeña (1-2 behaviors) | requirements.md simplificado + tasks.md |
| Feature mediana | requirements.md + design.md + tasks.md |
| Fase completa | Todo el flujo + trace + checklist de cierre |

La escala se define al arrancar el proyecto. Ver
`../07-acoplamiento-con-harness/como-definir-tipo-de-proyecto.md`.

---

## Regla de mantenimiento

Si cambia un requirement después de aprobado:
1. Actualizar `requirements.md`.
2. Actualizar `design.md` si afecta decisiones.
3. Actualizar `tasks.md`.
4. Re-aprobar el alcance.

El reviewer bloquea el cierre si el código cambió pero la cadena
requirement → task → test quedó desactualizada.
