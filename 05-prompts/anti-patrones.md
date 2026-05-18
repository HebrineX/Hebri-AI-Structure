# Anti-patrones de Prompts

Los anti-patrones de prompts producen trabajo que parece correcto pero requiere
corrección o descarte. Reconocerlos evita ciclos de retrabajo.

---

## 1. El prompt filosófico

**Qué es:** Un prompt que describe la intención en términos abstractos sin criterio de salida.

**Ejemplo malo:**
```
Ayudame a mejorar la arquitectura del sistema de clasificación.
```

**Por qué falla:** No hay criterio de aceptación. El agente elige qué "mejorar"
significa y produce algo que puede ser correcto en abstracto pero incorrecto para
este contexto.

**Corrección:**
```
Objetivo: Refactorizar YamlRuleEvaluator para que los operadores sean extensibles
sin modificar el motor.
Ownership: src/NetworkSentinel.Analyzers/Rules/YamlRuleEvaluator.cs
Restricción: no cambiar la firma pública de Evaluate().
Verificación: dotnet test --filter "FullyQualifiedName~YamlRuleEvaluator"
```

---

## 2. El prompt sin restricciones

**Qué es:** Un prompt que define qué hacer pero no qué no hacer.

**Ejemplo malo:**
```
Implementá el enricher de HttpMethod.
```

**Por qué falla:** El agente no sabe qué puede tocar. Puede modificar WafEvent,
cambiar IWafEnricher, o agregar dependencias — todo "para que funcione".

**Corrección:** Siempre agregar las restricciones de ownership y contrato.

---

## 3. El prompt sin verificación

**Qué es:** Un prompt que define qué producir pero no cómo saber si está correcto.

**Ejemplo malo:**
```
Escribí los tests para el operador Contains.
```

**Por qué falla:** El agente puede escribir tests que pasan pero no cubren
los casos relevantes. Sin criterio de verificación, no hay forma de saber
si los tests son suficientes.

**Corrección:**
```
Escribí tests para ContainsOperator.
Casos requeridos:
- Match exacto en el campo correcto.
- No-match cuando el valor no está presente.
- Campo nulo — no debe lanzar excepción.
Verificación: dotnet test --filter "FullyQualifiedName~ContainsOperatorTests"
→ 3 tests verdes.
```

---

## 4. El prompt de "arreglá todo"

**Qué es:** Delegar el análisis completo más la implementación en un solo paso.

**Ejemplo malo:**
```
Hay algo roto en el pipeline de clasificación, encontralo y arreglalo.
```

**Por qué falla:** Combina exploración (entender qué está roto) con implementación
(arreglarlo) sin un punto de control humano en el medio. El agente puede
"arreglar" algo que no era el problema real.

**Corrección:** Separar en dos pasos.
```
Paso 1 (Explorer): Encontrá por qué la clasificación devuelve siempre Unclassified
cuando el body contiene "SELECT". Reportá archivos y líneas, no arregles nada.

[Revisar el reporte → confirmar el problema → autorizar la corrección]

Paso 2 (Worker): Corregir [el problema específico identificado en paso 1].
```

---

## 5. El prompt sin contexto de stack

**Qué es:** Un prompt que describe la tarea pero no dice en qué stack o proyecto.

**Por qué falla:** El agente produce una solución genérica que no respeta las
convenciones del proyecto, los nombres de clases existentes, ni los patrones
de test vigentes.

**Corrección:** Incluir siempre stack, rutas relevantes, y un ejemplo del patrón
que se sigue (ej: "ver ContainsOperator como modelo").

---

## 6. El prompt acumulado de sesiones anteriores

**Qué es:** Pegar todo el historial de la conversación como contexto para
la siguiente tarea.

**Por qué falla:** El agente mezcla decisiones ya revertidas, opciones descartadas
y razonamientos obsoletos con el contexto actual. El resultado es inconsistente.

**Corrección:** El contexto de cada sesión se prepara desde los archivos del repo,
no desde el historial de chat. Ver `../01-modelo-de-trabajo/unidad-minima-de-contexto.md`.
