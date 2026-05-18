# Brief Operativo

El brief operativo es el formato estándar para pedirle trabajo a un agente.
No es un prompt de chat — es un contrato de tarea. Tiene forma de ticket bien escrito,
no de pregunta abierta.

La diferencia entre un prompt de chat y un brief operativo es que el brief elimina
ambigüedad antes de que el agente empiece a trabajar.

---

## Estructura

```
Objetivo:
  [una frase verificable — qué se produce, no qué se intenta]

Contexto:
  Stack: [lenguaje, framework, versión]
  Fase activa: [si aplica]
  Estado actual: [qué existe hoy en el código]
  Decisiones relevantes: [referencias a design.md o ADR si aplica]

Restricciones:
  - No tocar [archivo o carpeta].
  - No modificar [interfaz o contrato].
  - No asumir [X] — está definido en [ruta].

Archivos relevantes:
  - [ruta] — [para qué sirve en esta tarea]
  - [ruta] — [para qué sirve en esta tarea]

Salida esperada:
  - [artefacto 1: qué es, dónde vive]
  - [artefacto 2: qué es, dónde vive]

Verificación:
  [comando concreto que confirma que la tarea terminó bien]

Riesgos:
  - [qué podría romperse si se improvisa]
```

---

## Ejemplo — implementación

```
Objetivo:
  Agregar el operador "notContains" al motor de reglas YAML.

Contexto:
  Stack: .NET 10, xUnit.
  Operadores existentes: equals, contains, startsWith, endsWith.
  El motor usa el patrón Strategy con IYamlRuleOperator.
  Ver: src/NetworkSentinel.Analyzers/Rules/Operators/

Restricciones:
  - No modificar IYamlRuleOperator ni OperatorRegistry.Register() — contratos estables.
  - No tocar los operadores existentes.
  - No agregar dependencias externas.

Archivos relevantes:
  - src/.../Operators/ContainsOperator.cs — modelo a seguir
  - src/.../Operators/OperatorRegistry.cs — donde se registra el operador nuevo
  - tests/.../Operators/ContainsOperatorTests.cs — modelo de test a seguir

Salida esperada:
  - src/.../Operators/NotContainsOperator.cs
  - tests/.../Operators/NotContainsOperatorTests.cs (mínimo 2 tests: match y no-match)
  - OperatorRegistry actualizado con "notContains"

Verificación:
  dotnet test --filter "FullyQualifiedName~NotContainsOperator"
  → todos los tests verdes

Riesgos:
  - El nombre del operador en el YAML debe ser exactamente "notContains" (case-sensitive).
```

---

## Ejemplo — exploración

```
Objetivo:
  Mapear todos los puntos donde WafEvent.HttpMethod se usa en el pipeline.

Contexto:
  WafEvent está en src/NetworkSentinel.Core/Models/WafEvent.cs.
  HttpMethod actualmente siempre es null (sin enricher).

Restricciones:
  - Solo lectura — no modificar nada.
  - No proponer soluciones todavía.

Archivos relevantes:
  - src/ completo (lectura)
  - tests/ completo (lectura)

Salida esperada:
  - Lista de archivos que leen o escriben HttpMethod con línea aproximada.
  - Nota sobre qué fallaría si HttpMethod sigue null.
  - Incertidumbres marcadas explícitamente.

Verificación:
  La lista debe ser verificable con grep — no puede ser una lista de suposiciones.
```

---

## Escala del brief según la tarea

No todas las tareas necesitan todos los campos. La escala mínima:

| Tipo | Campos obligatorios |
|---|---|
| Exploración | Objetivo + Archivos + Restricciones (solo lectura) |
| Corrección puntual | Objetivo + Archivos + Verificación |
| Implementación | Todos los campos |
| Documentación | Objetivo + Archivos + Salida esperada |
