# Explorer y Worker

La separación más útil cuando se trabaja con subagentes es la de Explorer y Worker.
No es una separación técnica — es una separación de responsabilidades que evita
que un agente explore y modifique al mismo tiempo sin control.

---

## Explorer

El Explorer sirve para entender. Su trabajo es leer, mapear, comparar y devolver
información condensada. No hace cambios.

**Cuándo usarlo:**
- Antes de implementar cualquier cosa.
- Cuando no sabés exactamente qué superficie afecta un cambio.
- Para identificar tests, dependencias o patrones existentes.
- Para comparar alternativas sin tocar código.

**Qué pedirle:**
- Que cite archivos concretos con rutas.
- Que resuma con bullets cortos y verificables.
- Que marque explícitamente lo que no pudo confirmar.
- Que no proponga soluciones — solo mapee el estado actual.

**Qué no debe hacer:**
- Editar archivos.
- Proponer refactors mientras explora.
- Completar huecos con supuestos no verificados.

**Prompt base:**
```
Rol: explorer
Alcance: solo lectura sobre [carpeta o archivos]
Objetivo: [pregunta concreta]
Entrega: [lista de archivos, resumen del flujo, riesgos identificados]
Restricción: no hagas cambios. Si algo no es claro, marcalo como incertidumbre.
```

---

## Worker

El Worker sirve para hacer. Recibe un objetivo acotado, archivos con ownership
claro, y criterios de salida verificables. Puede editar, pero solo dentro del
scope autorizado.

**Cuándo usarlo:**
- Cuando el Explorer ya mapeó el terreno.
- Cuando hay una spec aprobada.
- Cuando el ownership está definido con precisión.

**Qué pedirle:**
- Que toque solo los archivos autorizados.
- Que explique qué cambió y por qué.
- Que valide con el comando de verificación acordado.
- Que deje el trabajo listo para revisión, no para merge automático.

**Qué no debe hacer:**
- Tocar archivos fuera del ownership.
- Revertir cambios de otras sesiones.
- Tomar decisiones de diseño que no estén en la spec.
- Declarar "done" sin evidencia.

**Prompt base:**
```
Rol: worker
Ownership exclusivo: [archivo o carpeta]
Objetivo: [tarea concreta]
Restricciones: [qué no tocar, qué no cambiar]
Verificación: [comando]
Salida esperada: [descripción del resultado]
```

---

## Regla de uso

**Explorer primero, Worker después.**

El Explorer reduce incertidumbre. El Worker ejecuta sobre terreno conocido.
Invertir el orden es lo que genera cambios sorpresa, scope creep y trabajo
que hay que deshacer.

Si no podés describir el ownership del Worker en una frase, el Explorer
todavía no terminó su trabajo.

---

## Cuándo un agente hace los dos roles

En tareas pequeñas y bien entendidas, el mismo agente puede explorar brevemente
y después ejecutar — pero debe hacerlo de forma explícita y secuencial:
1. Explorar → reportar hallazgos.
2. Esperar confirmación.
3. Ejecutar con ownership claro.

No explorar y modificar en el mismo paso sin control.
