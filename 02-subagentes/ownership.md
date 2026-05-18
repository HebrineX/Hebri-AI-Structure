# Ownership de Archivos

Ownership es la definición explícita de qué puede tocar cada agente. Sin ownership
claro, el trabajo paralelo se pisa, las revisiones son inciertas y los cambios
sorpresa aparecen cuando menos se esperan.

---

## Qué define un ownership bien asignado

- Archivo o carpeta autorizada (rutas concretas).
- Tipo de permiso: solo lectura, edición, o creación.
- Límite de impacto: qué no puede modificar aunque esté en la carpeta.
- Criterio de invasión: qué cambio estaría fuera de scope.
- Confirmación de no reversión: no puede deshacer trabajo de otros agentes.

---

## Formato

```
Worker [nombre/id]:
  Ownership: [ruta]
  Permiso: [lectura | edición | creación]
  Límite: [qué no puede tocar dentro de ese scope]
  Invasivo si: [descripción de cambio que estaría fuera de scope]
```

---

## Ejemplo bien asignado

```
Worker A:
  Ownership: src/NetworkSentinel.Analyzers/Rules/
  Permiso: edición
  Límite: no modificar IYamlRuleOperator ni YamlRuleCatalog.Load() (contrato estable)
  Invasivo si: toca archivos en src/NetworkSentinel.Core/

Worker B:
  Ownership: tests/NetworkSentinel.Analyzers.Tests/Rules/
  Permiso: creación + edición
  Límite: solo archivos de test — no tocar src/
  Invasivo si: modifica lógica de producción para hacer pasar un test

Explorer C:
  Ownership: src/ completo
  Permiso: solo lectura
  Invasivo si: edita cualquier archivo
```

---

## Ejemplo mal asignado

```
Worker:
  "Editá lo que haga falta para que funcione"
  → No hay límite. No se puede revisar. No se puede paralelizar.

Worker:
  "Acomodá todo el módulo de analyzers"
  → No hay criterio de invasión. Scope indeterminado.
```

---

## Ownership en trabajo paralelo

Cuando dos workers operan al mismo tiempo:
- Sus ownerships no pueden solaparse en ningún archivo.
- Si necesitan leer el mismo archivo, ambos pueden hacerlo en modo lectura.
- Si uno necesita editar un archivo que el otro también toca, se serializa: uno termina, el otro empieza.

Un solapamiento de ownership no es un problema técnico de merge — es un problema
de diseño de la tarea. La solución correcta es redefinir el scope antes de ejecutar.

---

## Cuándo el ownership cambia durante la sesión

Si el Worker necesita tocar algo fuera de su scope para cumplir el objetivo:
1. Detener el trabajo.
2. Reportar el bloqueo con el archivo específico que necesita.
3. Esperar confirmación del scope extendido o redefinición de la tarea.

No asumir que "como ya estaba ahí" está autorizado.
