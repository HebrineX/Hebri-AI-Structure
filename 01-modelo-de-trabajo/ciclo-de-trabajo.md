# Ciclo de Trabajo

El ciclo de trabajo es la unidad mínima de progreso real con IA. No es una
conversación — es un loop deliberado que deja artefactos concretos en cada vuelta.

---

## El ciclo

```
Intención → Contexto → Plan → Ejecución → Verificación → Registro → Siguiente iteración
```

Cada paso tiene un propósito específico. Saltarse uno no acelera el trabajo —
lo fragiliza.

---

## Descripción de cada paso

### Intención
Define el resultado deseado en una frase verificable. No "mejorar el sistema" —
sino "implementar la clasificación por reglas YAML para la cubeta SqlInjection".

La intención es el criterio de éxito de la iteración. Si al terminar no podés
contrastarla contra lo producido, la intención estaba mal definida.

### Contexto
Entregá la porción del proyecto que el agente necesita para este trabajo específico.
Ver `unidad-minima-de-contexto.md` para el formato exacto.

El contexto no es acumulativo entre sesiones salvo que se gestione explícitamente.
Cada sesión nueva empieza desde lo que está escrito, no desde lo que se recordó.

### Plan
Antes de ejecutar, acordar los pasos. El plan evita trabajo impulsivo y hace
visible dónde puede aparecer un problema antes de que aparezca.

El plan puede ser corto: una lista de 3 a 7 pasos concretos con los archivos
afectados y el criterio de salida de cada uno.

### Ejecución
Producción de artefactos concretos: código, documentación, specs, tests, scripts.
La ejecución es la capa donde la IA agrega más velocidad — pero solo si el plan
y el contexto son sólidos.

Durante la ejecución: un cambio a la vez, ownership explícito, sin saltar pasos.

### Verificación
Corroborar que el resultado cumple el criterio definido en la intención. La
verificación es ejecutable: un comando, una suite de tests, una revisión contra
una spec.

No se cierra una iteración sin evidencia de verificación.

### Registro
Mover el conocimiento del chat al repositorio. Esto incluye:
- Decisiones tomadas → `design.md` o ADR
- Gaps identificados → sección de gaps del proyecto
- Estado actualizado → `README-PROGRESPJ.md` o sección de fases
- Contexto para la siguiente sesión → `progress/current.md`

### Siguiente iteración
Con el registro hecho, la próxima sesión tiene base. El ciclo no reinicia desde
cero — avanza desde el último estado conocido.

---

## Ejemplo completo

```
Intención:
  Agregar soporte de operador "contains" al motor de reglas YAML.

Contexto:
  Stack: .NET 10, xUnit.
  Motor: YamlRuleEvaluator en src/NetworkSentinel.Analyzers/Rules/.
  Operadores existentes: equals, startsWith, endsWith (ver design.md §3).
  Restricción: no modificar la interfaz IYamlRuleOperator.

Plan:
  1. Agregar ContainsOperator : IYamlRuleOperator.
  2. Registrar en OperatorRegistry.
  3. Agregar test ContainsOperator_MatchesSubstring.
  4. Agregar test ContainsOperator_NoMatch.
  5. Actualizar tabla de operadores en RULES-AUTHORING.md.

Ejecución:
  [implementación paso a paso]

Verificación:
  dotnet test --filter "FullyQualifiedName~ContainsOperator"
  → 2 tests passed

Registro:
  - design.md §3: tabla de operadores actualizada.
  - RULES-AUTHORING.md: sección de operadores actualizada.
  - Gap #4 cerrado: "operador contains pendiente".

Siguiente:
  Próximo: operador "regex" (Gap #5, diferido a Fase 2.5).
```

---

## Por qué el registro es la capa que más se saltea

Es la que parece opcional cuando todo salió bien. Pero es la que hace que el
trabajo sea acumulable. Sin registro, cada sesión es un silo. Con registro, el
repositorio crece como sistema de conocimiento y cualquier agente (o persona)
puede retomar el trabajo sin depender de memoria oral.
