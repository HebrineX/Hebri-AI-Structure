# Anti-patrones de Subagentes

Los anti-patrones suelen verse como productividad al principio y como deuda
operativa después. Cada uno tiene una señal característica que permite detectarlo
antes de que el daño sea difícil de revertir.

---

## 1. Delegación vaga

**Qué es:** Pedirle algo demasiado amplio a un agente.

**Señal:** La respuesta suena prolija pero no aterriza en archivos ni decisiones
concretas. El agente describe cosas en lugar de producirlas.

**Causa raíz:** La tarea no tenía criterio de salida verificable.

**Corrección:** Antes de delegar, responder: ¿qué archivo concreto produce?
¿Con qué contenido exacto? ¿Cómo se verifica?

---

## 2. Context dumping

**Qué es:** Pasarle todo el repo, todo el chat, todas las notas al agente.

**Señal:** Respuestas largas con fragmentos relevantes mezclados con ruido.
El agente mezcla contexto de sesiones anteriores con el pedido actual.

**Causa raíz:** No se definió la unidad mínima de contexto para esta tarea.

**Corrección:** Ver `../01-modelo-de-trabajo/unidad-minima-de-contexto.md`.
El contexto debe ser la porción relevante para esta tarea, no todo lo disponible.

---

## 3. Ownership difuso

**Qué es:** Nadie sabe con precisión qué puede tocar qué agente.

**Señal:** Aparecen ediciones cruzadas, cambios en archivos que nadie autorizó,
o conflictos entre dos workers que "pensaron que era libre".

**Causa raíz:** No se definió ownership antes de ejecutar.

**Corrección:** Ver `ownership.md`. El ownership se define antes de ejecutar,
no se negocia durante la ejecución.

---

## 4. El agente que resuelve diseño

**Qué es:** Un Worker empieza a proponer arquitectura cuando solo tenía que
ejecutar una tarea acotada.

**Señal:** Propone reestructurar todo antes de terminar el cambio pedido.
O produce una solución "más elegante" que cambia contratos no incluidos en el scope.

**Causa raíz:** La tarea tenía huecos que el agente rellenó con criterio propio.

**Corrección:** Las decisiones de diseño son del humano. Si el agente encuentra
un problema de diseño durante la ejecución, lo reporta — no lo resuelve por su cuenta.

---

## 5. Paralelismo falso

**Qué es:** Lanzar tareas "en paralelo" que en realidad compiten por los mismos archivos.

**Señal:** Conflicto de archivos, resultados inconsistentes, necesidad de rehacer trabajo.

**Causa raíz:** No se verificó que los ownerships fueran disjuntos antes de paralelizar.

**Corrección:** Paralelizar solo cuando los archivos no se solapan. Si hay duda,
serializar y paralelizar después.

---

## 6. Cierre sin evidencia

**Qué es:** El agente dice que terminó pero no muestra cómo se valida.

**Señal:** "Listo, ya lo hice" sin comando de verificación, sin archivos tocados
listados, sin output de test.

**Causa raíz:** No se definió el criterio de cierre en la tarea.

**Corrección:** Una tarea no cierra sin evidencia. El formato mínimo de cierre:
```
Resultado: [completado / bloqueado]
Archivos tocados: [lista con rutas]
Verificación ejecutada: [comando + output]
Riesgos residuales: [si hay alguno]
```

---

## 7. Mezclar exploración con edición

**Qué es:** Un agente explora y modifica en el mismo paso, sin punto de control.

**Señal:** Cambios colaterales no pedidos. "Ya que estaba, también ajusté..."

**Causa raíz:** No se separó el rol de Explorer del de Worker.

**Corrección:** Explorer primero, reporte, confirmación, Worker después.
Ver `explorer-worker.md`.

---

## 8. Teléfono descompuesto

**Qué es:** Cada agente resume el output del anterior en lugar de leer el artefacto original.

**Señal:** Las respuestas se vuelven cada vez más abstractas. Nadie puede citar
el archivo fuente. El contexto se degrada por capas.

**Causa raíz:** Los agentes devuelven texto en lugar de escribir archivos y
referenciarlos.

**Corrección:** Los agentes escriben resultados en archivos y devuelven solo
la referencia. El siguiente agente lee el archivo, no el resumen.
