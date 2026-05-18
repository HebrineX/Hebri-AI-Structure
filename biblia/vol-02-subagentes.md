# Vol 02 · Subagentes

> Anterior: [Vol 01 · Modelo de trabajo](./vol-01-modelo-de-trabajo.md) · Siguiente: [Vol 03 · SDD](./vol-03-sdd.md)

## Explorer/Worker

La separación más útil cuando se trabaja con subagentes. No es una
separación técnica — es una separación de responsabilidades.

> **Cuándo escalar a roles cerrados:** cuando el proyecto suma SDD, equipo
> y reviewers externos, Explorer/Worker queda corto. Pasar a los 4 roles
> cerrados de [Vol 09](./vol-09-roles-cerrados.md): leader, spec_author,
> implementer, reviewer.

**Explorer** sirve para entender. Solo lectura. Devuelve hallazgos con
evidencia. No edita archivos, no propone soluciones mientras explora, no
completa huecos con supuestos.

Prompt base:

```text
Rol: explorer
Alcance: solo lectura sobre [carpeta o archivos]
Objetivo: [pregunta concreta]
Entrega: [lista de archivos, resumen del flujo, riesgos identificados]
Restricción: no hagas cambios. Si algo no es claro, marcalo como incertidumbre.
```

**Worker** sirve para hacer. Recibe objetivo acotado, ownership claro y
criterio de salida verificable. Toca solo los archivos autorizados, explica
qué cambió, valida con el comando acordado.

Prompt base:

```text
Rol: worker
Ownership exclusivo: [archivo o carpeta]
Objetivo: [tarea concreta]
Restricciones: [qué no tocar, qué no cambiar]
Verificación: [comando]
Salida esperada: [descripción del resultado]
```

**Regla:** Explorer primero, Worker después. Si no podés describir el
ownership del Worker en una frase, el Explorer todavía no terminó.

---

## Ownership de Archivos

Ownership es la definición explícita de qué puede tocar cada agente.

```text
Worker A:
  Ownership: src/NetworkSentinel.Analyzers/Rules/
  Permiso: edición
  Límite: no modificar IYamlRuleOperator ni YamlRuleCatalog.Load()
  Invasivo si: toca archivos en src/NetworkSentinel.Core/

Worker B:
  Ownership: tests/NetworkSentinel.Analyzers.Tests/Rules/
  Permiso: creación + edición
  Invasivo si: modifica lógica de producción para hacer pasar un test
```

En trabajo paralelo: los ownerships no pueden solaparse en ningún archivo.
Si dos workers necesitan el mismo archivo, se serializa.

---

## Anti-patrones de Subagentes

1. **Delegación vaga** — La respuesta suena prolija pero no aterriza en
   archivos ni decisiones concretas. Causa: la tarea no tenía criterio de
   salida verificable.

2. **Context dumping** — Se pasa todo el repo como contexto. El agente
   mezcla señales irrelevantes. Causa: no se definió la unidad mínima de
   contexto.

3. **Ownership difuso** — Aparecen cambios en archivos que nadie autorizó.
   Causa: no se definió ownership antes de ejecutar.

4. **El agente que resuelve diseño** — El Worker propone reestructurar todo
   antes de terminar el cambio pedido. Causa: la tarea tenía huecos que el
   agente rellenó con criterio propio.

5. **Paralelismo falso** — Tareas "paralelas" que compiten por los mismos
   archivos. Causa: no se verificó que los ownerships fueran disjuntos.

6. **Cierre sin evidencia** — El agente dice que terminó sin mostrar cómo se
   valida. Formato mínimo de cierre: archivos tocados + comando ejecutado +
   resultado.

7. **Mezclar exploración con edición** — "Ya que estaba, también ajusté...".
   Causa: no se separó el rol Explorer del Worker.

8. **Teléfono descompuesto** — Cada agente resume el output del anterior en
   lugar de leer el artefacto original. Los agentes escriben resultados en
   archivos y devuelven solo la referencia.
