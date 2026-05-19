# Vol 08 · MCPs, Tool Use y Niveles de Autonomía

> Anterior: [Vol 07 · Harness](./vol-07-harness.md) · Siguiente: [Vol 09 · Roles cerrados](./vol-09-roles-cerrados.md)

Los volúmenes 01 a 07 tratan al agente como una entidad que lee, piensa y
escribe texto. En la práctica, un agente moderno también **ejecuta**: corre
comandos, llama APIs, escribe a disco, abre un browser. Este volumen cubre
ese lado: qué herramientas tiene, qué puede hacer con cada una, cuándo se
pasa de raya, y cómo recuperar cuando algo sale mal.

---

## Tool Use: lo que el agente puede tocar

Un agente tiene tres tipos de capacidades:

| Tipo | Ejemplos | Riesgo |
|---|---|---|
| **Lectura** | Read, Glob, Grep, list_dir | Bajo — no muta estado |
| **Escritura local** | Write, Edit, Bash (con efectos) | Medio — muta archivos del proyecto |
| **Efecto externo** | API calls, git push, deploy, send_email, pago | Alto — afecta cosas fuera del repo |

**Regla:** Antes de dar acceso, definir explícitamente qué tipo de capacidad
necesita la tarea. Una tarea de exploración no debería tener acceso a
escritura. Una tarea de implementación no debería tener acceso a efectos
externos sin aprobación humana.

---

## MCPs (Model Context Protocol)

Un MCP es un servidor que expone un conjunto de tools al agente. Trata los
MCPs como **dependencias del proyecto**: cada uno suma superficie y suma
riesgo.

**Antes de instalar un MCP, responder:**

1. ¿Qué tools agrega exactamente? Listarlos.
2. ¿Qué tipo de capacidad son (lectura / escritura / efecto externo)?
3. ¿Qué credenciales o secretos necesita?
4. ¿Quién mantiene el MCP? (oficial del vendor, comunidad, propio)
5. ¿Se puede desconectar fácilmente?

**Documentar los MCPs activos en AGENTS.md:**

```markdown
## MCPs activos

| MCP | Tools que aporta | Capacidad | Credenciales |
|---|---|---|---|
| github | issues, prs, files | escritura externa | GH_TOKEN |
| slack | send_message, list_channels | efecto externo | SLACK_BOT_TOKEN |
| filesystem | read, write, list | escritura local | — |
```

**Anti-patrón:** Conectar MCPs "por si acaso". Cada tool extra es ruido en
el razonamiento del agente y vector de error.

---

## Niveles de Autonomía

No todos los agentes deben ejecutar todo. Definir explícitamente la
autonomía evita sorpresas. Cinco niveles, de más restrictivo a más libre:

| Nivel | Nombre | Qué puede | Cuándo aplicarlo |
|---|---|---|---|
| 0 | Read-only | Solo lectura del repo | Exploración, auditoría |
| 1 | Suggest | Lectura + propuesta de cambios (diff, no aplica) | Reviews, pair programming |
| 2 | Local-write | Escribe en disco, NO ejecuta efectos externos | Implementación de slice |
| 3 | Validated-execute | Ejecuta tests/builds locales, NO push ni deploy | Cierre con verificación |
| 4 | Full | Push, deploy, notificaciones externas | Solo con humano supervisando |

**Regla:** Subir un nivel se hace explícito, no implícito. Si el agente está
en N2 y necesita N3 para validar, lo declara y espera autorización (o el
operador lo concede para la tarea).

**Por defecto:** N2 para Workers, N0 para Explorers, N3 solo durante cierre
de slice/fase.

---

## Modos de Operación

Los niveles de autonomía definen qué puede hacer un agente. El modo define
cuánta aprobación humana necesita antes de avanzar.

| Modo | Uso | Regla |
|---|---|---|
| `manual` | Control fino, tareas riesgosas, primera vez de un flujo | El operador aprueba cada cambio, comando, slice y handoff |
| `automático` | Flujo conocido con scope aprobado | El leader decide pasos seguros, pero pide `SI` antes de editar, correr comandos, llamar APIs/modelos o cambiar estado |

En modo automático el agente no tiene permiso para sorprender. Antes de
mutar estado debe explicar: acción propuesta, archivos o tools involucradas,
riesgo, verificación y resultado esperado. Después espera un `SI`.

En modo manual, si una fase tiene cinco slices, se presenta cada slice por
separado y se espera aprobación antes de continuar.

---

## Runtime LLM como Adaptador

En proyectos que conectan modelos por API, el LLM no debe ser dueño de la
arquitectura. Debe vivir como adaptador detrás de contratos explícitos.

```text
domain/          reglas puras: roles, ownership, SDD, gaps
application/     casos de uso
workflows/       estados, gates y aprobaciones
orchestration/   leader, dispatch y ciclos multiagente
prompts/         templates versionados y renderer
llm/             cliente de modelo, retries, fallbacks, usage
tools/           registry, policy y auditoría
infrastructure/  filesystem, shell, git, artifact store
interfaces/      CLI, HTTP, desktop, MCP
```

**Reglas:**

- `domain/` no importa `llm/`, `tools/`, `prompts/` ni infraestructura.
- `llm/` no conoce SDD, roles ni ownership.
- `prompts/` renderiza texto; no ejecuta tools.
- `tools/` siempre pasa por una policy de permisos.
- `workflows/` controla gates, estados y aprobación humana.

---

## Resiliencia de Llamadas a Modelos

Para llamadas LLM productivas:

- timeout por request;
- máximo 3 intentos;
- backoff exponencial con jitter;
- reintentos solo en errores transitorios (timeout, 429, 5xx, conexión);
- fallback de modelo por tipo de tarea;
- circuit breaker por proveedor si hay fallas repetidas;
- idempotency key por `traceId + promptId + inputHash`;
- validación estricta de salida antes de avanzar gates.

Las salidas que alimentan workflow deben ser JSON con schema o Markdown
validable. Respuestas vacías, genéricas, con rutas inexistentes no marcadas
como `to_create`, requirements sin tests o evidencia sin comando bloquean el
gate.

---

## Costos, Cache y Contexto

Optimizar costo no es usar siempre el modelo más barato; es reducir contexto
irrelevante y elegir capacidad por tarea.

- Usar context slicing: cargar solo archivos del brief.
- Versionar prompts con `id`, `version`, `schema_version`, `role`,
  `source` y `last_reviewed`.
- Cachear respuestas determinísticas con
  `promptId + promptVersion + inputHash + model + schemaVersion`.
- Cachear embeddings de docs/specs por hash de archivo.
- Registrar token usage por `traceId`, rol y prompt.
- Evitar prompts gigantes que mezclan spec, template, checklist y runtime.

### Perfiles de contexto

Un harness operativo debería definir perfiles de lectura por rol:

| Perfil | Lee | No lee salvo necesidad |
|---|---|---|
| `leader` | estado, modo, registry, bloqueos | spec completa de todos los slices |
| `spec_author` | contexto producto/arquitectura, template SDD | prompts de implementación |
| `implementer` | spec activa, lock, ownership, verificación | biblioteca completa de gaps |
| `reviewer` | spec, diff/artefacto impl, gate log | docs no relacionadas |
| `bootstrap` | prompt bootstrap + spec larga | runtime LLM completo |

El objetivo no es ahorrar por ahorrar: es evitar que el agente mezcle señales
irrelevantes con el contrato activo.

---

## Permisos por archivo y por comando

Ownership de archivos ([Vol 02](./vol-02-subagentes.md)) y autonomía
(este volumen) se combinan en una matriz:

```text
Worker A — Nivel 2 — Ownership: src/Analyzers/
  Puede: Read, Edit, Write dentro de src/Analyzers/
  Puede: Bash para `dotnet test --filter Analyzers`
  No puede: git push, modificar src/Core/, llamar APIs externas
```

En herramientas que soportan allowlists (Claude Code `allowed_tools`,
políticas de MCP, etc.) esto se materializa como configuración. En el resto:
queda escrito en el AGENTS.md del proyecto y se confía en disciplina del
operador.

---

## Recuperación de Errores

Los agentes fallan. La pregunta no es si, sino cómo se detecta y se sale.

**Síntomas de agente en problemas:**

1. **Loop** — Repite la misma acción con pequeñas variaciones sin
   converger. Tres iteraciones idénticas → cortar.
2. **Alucinación de archivos** — Edita o cita archivos que no existen.
   Verificar con `ls` antes de seguir.
3. **"Termina" sin terminar** — Declara done sin evidencia (sin comando
   corrido, sin test). Forzar el formato de cierre.
4. **Cambio de scope sin permiso** — Toca archivos fuera del ownership.
   Revertir y rearmar la tarea.
5. **Cierre cosmético** — Hace que el test pase modificando el test, no la
   lógica. Pedir diff del test y de la prod simultáneamente.

**Cómo salir:**

```text
1. Parar el agente (no seguir dándole vueltas).
2. Capturar último estado: archivos tocados, último output, último comando.
3. Revertir lo que no esté validado.
4. Escribir el estado en PROGRESS.md como gap.
5. Rearmar la tarea con contexto más acotado o autonomía más baja.
```

**Anti-patrón:** Pedirle al mismo agente que "se arregle solo". Si está en
un loop, la nueva instrucción la procesa con el mismo contexto roto. Cortar
sesión y rearmar.

---

## Economía de Contexto

Cada token cuesta dinero y aumenta la probabilidad de que el agente pierda
foco. Optimizar contexto NO es minimizar — es no cargar lo irrelevante.

**Heurísticas:**

| Situación | Cómo manejar |
|---|---|
| Repo grande | El Explorer devuelve solo rutas + resumen, no archivos completos |
| Decisiones históricas | Vivien en `specs/done/` o ADRs, no se re-leen en cada sesión |
| Logs de errores | Solo el último error relevante, no el output completo |
| Documentación externa | Linkearla, no copiarla |
| Conversaciones previas | Resumen estructurado, no transcripción |

**Regla del 80/20:** Si el 80% del contexto cargado es irrelevante para la
tarea concreta, la unidad mínima de contexto está mal definida. Volver al
[Vol 01](./vol-01-modelo-de-trabajo.md).

---

## Elección de Modelo

Diferentes modelos para diferentes tareas. Pensarlo como herramientas, no
como una sola pieza universal.

| Tarea | Modelo apropiado |
|---|---|
| Exploración, resumen, clasificación | Modelo rápido y barato (Haiku, Mini) |
| Implementación de slice acotado | Modelo intermedio (Sonnet, 4-class) |
| Diseño de arquitectura, debugging difícil | Modelo top (Opus, top-tier) |
| Embeddings, búsqueda semántica | Modelo de embeddings dedicado |

**Anti-patrón:** Usar siempre el modelo más potente "por las dudas". Cuesta
más, no necesariamente acierta más, y a veces es más lento.

---

## Observabilidad de Acciones del Agente

Si el agente puede ejecutar, hay que poder auditar lo que ejecutó.

**Mínimo aceptable:**

1. Log de cada tool call: timestamp, tool, argumentos, resultado.
2. Diff por sesión: qué archivos cambiaron, qué comandos corrieron.
3. Estado declarado al cerrar: "tasks T1, T2 completas; verificación con
   `[comando]`; resultado `[N] passed`".

**Para tareas con autonomía N3+:** logs persistidos fuera del chat (archivo
o sistema central). El chat es efímero.

---

## Checklist al Configurar un Agente Nuevo

- [ ] Nivel de autonomía declarado (N0 a N4).
- [ ] Tools permitidos enumerados (allowlist, no denylist).
- [ ] MCPs activos documentados en AGENTS.md.
- [ ] Ownership de archivos definido si va a escribir.
- [ ] Credenciales mínimas (no compartir tokens de admin con un agente que
      solo necesita lectura).
- [ ] Plan de salida si falla (qué se revierte, dónde se registra).
- [ ] Modelo elegido por costo/capacidad de la tarea típica.

---

## Anti-patrones de Tool Use

1. **El agente con todo activado** — MCPs y herramientas conectadas "por
   conveniencia". El razonamiento se diluye entre demasiadas opciones.

2. **Autonomía implícita** — No se declara nivel; el agente decide hasta
   dónde llegar. Termina haciendo push sin que nadie lo aprobara.

3. **Credenciales sobre-amplias** — Token con permisos de admin para un
   agente que solo lee. Cuando algo sale mal, el blast radius es enorme.

4. **Tool call sin verificación de resultado** — El agente ejecuta `dotnet
   test`, el comando falla, y el agente reporta "tests OK" porque no leyó
   el exit code.

5. **Sin presupuesto de iteraciones** — El agente reintenta indefinidamente.
   Definir N intentos máximo, después cortar y reportar.

6. **Mezclar tool use con razonamiento** — El agente ejecuta y razona en la
   misma frase, sin pausa para verificar. Patrón sano: ejecutar → leer
   resultado → razonar → siguiente acción.
