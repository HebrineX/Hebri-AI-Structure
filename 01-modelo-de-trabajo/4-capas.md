# Las 4 Capas de Trabajo con IA

La diferencia entre usar IA como chat y usarla como sistema de trabajo no está
en escribir prompts más largos. Está en que cada interacción tenga estructura.

Esa estructura tiene cuatro capas. Cuando las cuatro existen, la IA puede ayudar
en trabajo serio. Cuando falta alguna, la IA improvisa — y cuando improvisa en
silencio, rompe cosas.

---

## Las capas

### Capa 1 — Contexto
*Lo que la IA debe saber antes de actuar.*

El contexto no es un dump de información. Es la porción del proyecto que el agente
necesita para actuar sin suponer. Incluye: stack, arquitectura relevante, restricciones
activas, decisiones anteriores que afectan el trabajo actual.

Sin contexto, el agente optimiza para la respuesta más razonable en abstracto —
que no necesariamente es la correcta para este proyecto.

### Capa 2 — Tarea
*Lo que debe producir ahora.*

La tarea tiene alcance acotado, criterios de salida verificables y un propietario claro
(qué archivos puede tocar, qué no). Una tarea que dice "mejorá lo que veas" no es
una tarea — es una delegación ciega.

Una tarea bien definida responde: ¿qué produce?, ¿sobre qué archivos?, ¿cómo
se sabe que terminó?

### Capa 3 — Verificación
*Cómo se sabe que el resultado sirve.*

La verificación no es opcional. Un resultado que no se puede probar no es un resultado
cerrado — es un supuesto que se integra con confianza falsa.

La verificación puede ser: comando que corre, test que pasa, output que matchea un
criterio, revisión de un archivo contra una spec.

### Capa 4 — Memoria
*Dónde queda registrado para no repetir el mismo razonamiento.*

La memoria no vive en el chat — vive en archivos. Decisiones, gaps identificados,
specs aprobadas, resultados de fases: todo lo que importa debe sobrevivir al cierre
de la conversación.

Sin esta capa, cada sesión empieza de cero. Con esta capa, el contexto se acumula
y el trabajo mejora con el tiempo.

---

## Cómo se ven las 4 capas en la práctica

```
Contexto:
  Stack: .NET 10, xUnit, YAML rules engine
  Fase activa: Fase 2 — motor de reglas
  Restricción: no tocar el pipeline de Fase 1

Tarea:
  Implementar YamlRuleCatalog.Load()
  Ownership: src/NetworkSentinel.Analyzers/Rules/YamlRuleCatalog.cs
  Salida: clase que lee archivos .yml desde un directorio y construye lista de reglas

Verificación:
  dotnet test --filter "FullyQualifiedName~YamlRuleCatalogTests"
  Todos los tests definidos en tasks.md deben pasar

Memoria:
  Decisión registrada en design.md: se eligió YAML sobre JSON por legibilidad
  Gap #3 actualizado: soporte de hot-reload diferido a Fase 3
```

---

## La regla

Si alguna de las cuatro capas falta, la sesión va a producir trabajo que parece
correcto pero requiere corrección posterior. El costo de armar las cuatro capas
al inicio es siempre menor que el costo de corregir output sin base.
