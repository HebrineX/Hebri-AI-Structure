# Vol 02 · Subagentes

> Anterior: [Vol 01 · Modelo de trabajo](./vol-01-modelo-de-trabajo.md) · Siguiente: [Vol 03 · SDD](./vol-03-sdd.md)

## Explorer/Worker

La separación más útil para empezar a trabajar con subagentes. No es una
separación técnica — es una separación de responsabilidades. En versiones
anteriores de esta biblia, Explorer/Worker funcionaba como la división central.
Desde `3.0.0`, se entiende como **familia base**, no como límite conceptual.

> **Cuándo escalar a roles cerrados:** cuando el proyecto suma SDD, equipo
> y reviewers externos, Explorer/Worker queda corto. Pasar a los 4 roles
> cerrados de [Vol 09](./vol-09-roles-cerrados.md): leader, spec_author,
> implementer, reviewer.

La regla de fondo no cambia: no se crean agentes por capricho. Se agregan
capacidades solo cuando reducen ambigüedad, mejoran evidencia o protegen el
flujo.

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

## Roles Mínimos y Perfiles Parametrizados

El objetivo futuro no es tener más agentes. Es tener pocos roles estables y
especializarlos por perfil cuando la tarea lo justifica.

```text
Rol estructural = responsabilidad estable.
Perfil = especialización temporal.
Tarea = objetivo concreto de ese ciclo.
```

Roles mínimos recomendados:

| Rol | Responsabilidad |
|---|---|
| `interpreter` | Comunica con el operador, traduce estado y pide `SI` |
| `leader` | Coordina, decide flujo, mantiene trazabilidad y gates |
| `executor` | Produce cambios dentro del scope aprobado |
| `reviewer` | Revisa producción contra spec, diff y evidencia |
| `auditor` | Audita contrato, proceso, riesgos, sesgos y cumplimiento |
| `reporter` | Comunica resultados de forma clara, humana y accionable |

Perfiles posibles:

```yaml
role: auditor
profile: harness_compliance | cost | security | architecture | release | detractor
```

```yaml
role: reporter
profile: operator | technical | executive
```

**Regla anti-explosión:** no se crea un rol nuevo si la necesidad puede
expresarse como perfil de un rol existente.

Ejemplos:

| Necesidad | Forma correcta |
|---|---|
| Auditar consumo de tokens | `auditor(profile: cost)` |
| Buscar riesgos de seguridad | `auditor(profile: security)` |
| Cuestionar un cierre | `auditor(profile: detractor)` |
| Explicar resultados al operador | `reporter(profile: operator)` |
| Preparar informe técnico | `reporter(profile: technical)` |

Esto mantiene la esencia del sistema: mínima cantidad de agentes activos,
máxima claridad de intención.

---

## Auditor, Reporter y Detractor

Estos perfiles aparecen porque Explorer/Worker es demasiado general para tres
necesidades distintas:

1. verificar cumplimiento;
2. comunicar resultados;
3. cuestionar conclusiones propias del sistema.

**Auditor** es read-only. Su tarea es revisar contrato, evidencia, gates,
roles, riesgos, costos, seguridad o arquitectura. No implementa y no aprueba.

**Reporter** transforma resultados técnicos en una salida legible para el
operador. No inventa evidencia, no cambia el veredicto y no aprueba.

**Detractor** es un perfil del auditor. Busca errores de los agentes, sesgos,
supuestos débiles, conclusiones no demostradas y riesgos omitidos. No bloquea
por gusto: cada objeción debe traer evidencia o una hipótesis verificable.

```text
Anti-confirmation bias = no asumir que el usuario tiene razón.
Detractor pass = no asumir que los agentes tienen razón.
```

El sistema mejora cuando sus propias conclusiones pueden ser atacadas sin
romper el flujo.

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
