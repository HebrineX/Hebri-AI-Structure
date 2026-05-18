# EARS — Requirements Verificables

EARS (Easy Approach to Requirements Syntax) es un formato para escribir
requirements que pueden verificarse. No es una ceremonia — es una forma de
eliminar ambigüedad antes de que cueste código.

La regla: si un requirement no puede convertirse en un test, no está terminado.

---

## Patrones base

| Patrón | Forma | Cuándo usarlo |
|---|---|---|
| **Ubicuo** | El sistema DEBE `[acción]`. | Comportamiento siempre presente |
| **Evento** | CUANDO `[evento]`, el sistema DEBE `[acción]`. | Comportamiento disparado por algo |
| **Estado** | MIENTRAS `[condición]`, el sistema DEBE `[acción]`. | Comportamiento durante un estado |
| **Opcional** | DONDE `[feature activa]`, el sistema DEBE `[acción]`. | Comportamiento condicional a config |
| **No deseado** | SI `[situación no deseada]` ENTONCES el sistema DEBE `[acción]`. | Manejo de errores y edge cases |

---

## Reglas de escritura

1. Cada requirement tiene un ID estable: `R1`, `R2`, `R3...`
2. Cada requirement debe ser verificable con un test concreto.
3. Un requirement no mezcla varios `DEBE` — uno por requirement.
4. Sin verbos blandos: nada de "podría", "soporta", "intenta", "debería considerar".
5. Cada `R` mapea a al menos un test en `tasks.md`.

---

## Ejemplos

### Malo vs Bueno

**Malo:**
```
El sistema debería permitir ver eventos recientes rápido,
con límite configurable y buen manejo de errores.
```
→ No verificable. Mezcla tres behaviors. "Rápido" no tiene criterio.

**Bueno:**
```
R1: CUANDO el worker procesa un WafEvent, el sistema DEBE clasificarlo
    contra todas las reglas YAML activas.

R2: DONDE el directorio de reglas está vacío, el sistema DEBE clasificar
    el evento como Unclassified sin lanzar excepción.

R3: SI una regla YAML tiene formato inválido ENTONCES el sistema DEBE
    lanzar YamlRuleCatalogException al arrancar, antes de procesar eventos.
```

### Ejemplo completo — Feature de clasificación por reglas

```
R1: CUANDO el sistema arranca, DEBE cargar todas las reglas YAML
    del directorio configurado en YamlRules__Directory.

R2: CUANDO un WafEvent llega al analyzer, el sistema DEBE evaluar
    todas las reglas cargadas en orden de prioridad descendente.

R3: CUANDO una regla matchea, el sistema DEBE aplicar la acción
    definida (Block, Allow, Log) y detener la evaluación de esa cubeta.

R4: MIENTRAS no hay ninguna regla que matchee, el sistema DEBE
    clasificar el evento como Unclassified.

R5: SI YamlRules__RequiredAtBoot es true y el directorio no existe
    ENTONCES el sistema DEBE fallar al arrancar con mensaje de error claro.

R6: SI una regla tiene un operador desconocido ENTONCES el sistema
    DEBE ignorar esa regla y registrar un warning, sin detener el pipeline.
```

---

## Escala de formalismo

No todos los proyectos necesitan EARS completo desde el primer día.

| Escala del proyecto | Uso recomendado |
|---|---|
| Script o herramienta simple | Criterios de aceptación en lenguaje natural |
| Feature con 2-4 behaviors | EARS para los casos no triviales |
| Módulo con múltiples casos borde | EARS completo |
| Sistema crítico o con contrato externo | EARS + ADR para cada decisión |

La decisión de escala se toma al definir el tipo de proyecto.
Ver `../07-acoplamiento-con-harness/como-definir-tipo-de-proyecto.md`.

---

## Trazabilidad

Cada requirement debe aparecer referenciado en `tasks.md`:

```markdown
- [ ] T3 — Agregar test WhenNoRuleMatches_ClassifiesAsUnclassified. Cubre: R4.
- [ ] T4 — Agregar test WhenRequiredAtBootAndDirMissing_ThrowsOnStartup. Cubre: R5.
```

Y el reviewer verifica que no quede ningún `R` sin test antes de aprobar el cierre.
