# Unidad Mínima de Contexto

El contexto no es información — es información relevante para esta tarea específica.
La diferencia importa: más contexto no significa mejor resultado. Significa más ruido,
más costo, y más probabilidad de que el agente mezcle señales que no corresponden.

---

## Los campos

| Campo | Para qué sirve | Qué poner |
|---|---|---|
| **Objetivo** | Evita que el agente optimice otra cosa | Una frase verificable |
| **Estado actual** | Le da el punto de partida real | Qué existe hoy, no qué debería existir |
| **Restricciones** | Acota las soluciones | Qué no se puede tocar, cambiar o asumir |
| **Archivos relevantes** | Reduce exploración ciega | Rutas concretas, no carpetas enteras |
| **Criterios de aceptación** | Define el cierre | Qué tiene que ser verdad al terminar |
| **Verificación** | Obliga a probar | El comando o test que confirma |
| **Riesgos** | Hace visible lo delicado | Lo que podría romperse si se improvisa |

---

## Formato

```
Objetivo:
  [una frase]

Estado actual:
  [qué existe ahora en el código/docs]

Restricciones:
  - [no X]
  - [no modificar Y]
  - [no asumir Z]

Archivos relevantes:
  - src/...
  - tests/...
  - docs/...

Criterios de aceptación:
  - [condición verificable 1]
  - [condición verificable 2]

Verificación:
  [comando concreto]

Riesgos:
  - [qué podría romperse]
```

---

## Qué incluir y qué no incluir

**Incluir:**
- El problema exacto que se está resolviendo.
- El formato esperado de la salida.
- Los archivos que marcan ownership.
- Las decisiones anteriores que afectan este trabajo.
- Los criterios de aceptación sin ambigüedad.

**No incluir:**
- Historia del proyecto que no afecta esta tarea.
- Opiniones contradictorias sobre el diseño.
- Pistas de sesiones anteriores que ya no aplican.
- Instrucciones abiertas como "mejoralo bastante" o "revisá si hay algo más".

---

## Ejemplo

```
Objetivo:
  Implementar EnricherHttpMethod que lea el log de acceso y enriquezca
  WafEvent con el verbo HTTP real.

Estado actual:
  WafEvent.HttpMethod existe como campo pero siempre es null.
  YamlRuleEvaluator ya evalúa ese campo cuando no es null.
  Los logs de acceso están en /var/log/nginx/access.log con formato combinado.

Restricciones:
  - No modificar IWafEnricher ni WafEvent (contratos estables).
  - No leer el log completo en memoria — usar streaming.
  - No tocar el pipeline de clasificación existente.

Archivos relevantes:
  - src/NetworkSentinel.Enrichers/HttpMethodEnricher.cs (a crear)
  - src/NetworkSentinel.Core/Models/WafEvent.cs (solo lectura)
  - tests/NetworkSentinel.Enrichers.Tests/HttpMethodEnricherTests.cs (a crear)

Criterios de aceptación:
  - Para un WafEvent con RequestId conocido, HttpMethod queda poblado.
  - Si el log no tiene la línea correspondiente, HttpMethod queda null sin error.
  - El enricher no modifica ningún otro campo de WafEvent.

Verificación:
  dotnet test --filter "FullyQualifiedName~HttpMethodEnricher"

Riesgos:
  - El formato del log puede variar entre versiones de nginx.
  - Si el RequestId no está en el log, no debe bloquear el pipeline.
```

---

## Escala de contexto según el tipo de tarea

No todas las tareas necesitan los 7 campos completos.

| Tipo de tarea | Campos mínimos |
|---|---|
| Exploración (explorer) | Objetivo + Archivos relevantes + Restricciones |
| Implementación acotada | Todos los campos |
| Corrección de bug | Objetivo + Estado actual + Verificación |
| Documentación | Objetivo + Archivos relevantes + Criterios de aceptación |
| Revisión (reviewer) | Objetivo + Archivos relevantes + Criterios de aceptación |
