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

## Independencia Técnica y Anti-confirmation Bias

Un agente útil no debe confirmar una decisión solo porque la dijo el operador.
El operador puede equivocarse, los agentes pueden equivocarse y una aprobación
humana puede estar basada en información incompleta.

Principio:

```text
El sistema no valida una decisión por autoridad.
La valida por evidencia, contrato, contexto y riesgo.
```

Todo rol debe separar:

| Campo | Pregunta |
|---|---|
| Pedido | ¿Qué pidió el operador o el rol anterior? |
| Hecho observado | ¿Qué evidencia existe en archivos, comandos o estado? |
| Inferencia | ¿Qué conclusión se está deduciendo? |
| Riesgo | ¿Qué podría estar mal o incompleto? |
| Decisión | ¿Qué se puede aprobar, bloquear o pedir aclarar? |

Reglas:

- No convertir preferencias del usuario en verdad técnica.
- Marcar supuestos y evidencia faltante.
- Desafiar instrucciones ambiguas, riesgosas o contradictorias.
- Decir "no hay evidencia suficiente" cuando corresponda.
- Escalar al leader si una instrucción humana rompe contrato, seguridad,
  evidencia o trazabilidad.

Modo automático no significa obediencia automática. Modo manual no significa
que cada `SI` sea técnicamente correcto.

---

## Detractor Pass

El detractor pass es una revisión adversarial controlada. Existe para detectar
errores de los propios agentes antes de cerrar decisiones importantes.

Se modela como:

```yaml
role: auditor
profile: detractor
mode: read_only
objective: buscar fallas, supuestos débiles, sesgos, riesgos omitidos y conclusiones no demostradas
```

Se activa en:

- cierre de fase o slice crítico;
- planes P0;
- cambios de arquitectura;
- auditorías de cumplimiento;
- decisiones con riesgo alto;
- cierres donde la evidencia es débil o contradictoria.

Salida mínima:

```text
Tesis evaluada:
Objeciones:
Evidencia:
Severidad:
Qué falsaría la objeción:
Recomendación: aceptar | ajustar | bloquear | pedir evidencia
```

Regla anti-ruido: el detractor no objeta por gusto. Cada objeción debe estar
apoyada por evidencia o por una hipótesis verificable.

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

### Memoria por capas

El harness no debe depender de la memoria de Codex, Claude, Gemini, Qwen,
DeepSeek ni ninguna otra herramienta. Cada una puede resumir, compactar o
perder contexto de forma distinta.

El patrón correcto es:

```text
memoria conversacional = accidental
memoria del harness = contractual
orquestador = decide capas activas
```

Capas recomendadas:

| Capa | Propósito | Cuándo cargar |
|---|---|---|
| local | session pin, contrato activo y foco | siempre al iniciar o reentrar |
| daily | trabajo fresco del día | por defecto si coincide el día operativo |
| cycle | fase/slice, roles, locks, approvals y gates | si hay ciclo activo |
| project | arquitectura y decisiones estables | por perfil de contexto |
| complete | historia profunda y auditoría global | sólo con motivo y aprobación |

Esto reduce tokens porque el agente no carga todo el harness para cada error,
y reduce drift porque el re-entry se vuelve una lectura corta y verificable.

En `Hebri-AI-Harness 0.12.0`, el ahorro de contexto se valida como contrato:
`context-budget.yaml` define límites por ruta (`memory_bootstrap`,
`first_message`, `debug_log_intake`, `leader_light`) y el validador local
registra warning cuando una ruta supera el presupuesto recomendado. Solo
bloquea si supera 2x el límite declarado. Si un agente necesita `leader_full`
o memoria `complete/`, debe declarar motivo, scope y pedir aprobación.

La documentacion personal/local queda fuera de la operacion: puede existir en
el proyecto o en el entorno del usuario, pero no se carga, no se copia a
`.hebrinex/` consumidor, no entra al manifest y no se usa como evidencia.

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

## Controles P0 de Tool Use

En `Hebri-AI-Harness 0.12.0`, las acciones con efecto no dependen solo de una
frase en el chat. Deben pasar por controles estructurados:

| Control | Para qué existe |
|---|---|
| `tool-policy.yaml` | Clasifica por rol qué acciones son `allow`, `ask` o `deny` |
| `command-taxonomy.md` | Distingue lectura segura, verificación local, escritura, red, git, destructivo y secretos |
| `write-set-policy.md` | Obliga a declarar qué archivos se van a escribir y comparar contra el diff real |
| `secret-denylist.md` | Bloquea por defecto `.env`, llaves, tokens, certificados, backups y logs sensibles |
| `preflight-template.md` | Estandariza qué se explica antes de ejecutar una acción con efecto |
| `approval-envelope.md` | Convierte el `SI` humano en aprobación concreta por acción, scope y riesgo |
| `PROJECT_BINDING.yaml` | Confirma que el harness pertenece al proyecto activo |
| `reentry-checklist.md` | Reconstruye contrato después de compactación, cambio de cwd o cambio de proyecto |
| `memory-registry.yaml` | Declara qué capas de memoria están activas |
| `memory-routing.yaml` | Define qué memoria carga cada entrypoint |
| `session-pin.md` | Contrato mínimo para rehidratar sin leer todo |
| `context-budget.yaml` | Define presupuestos por entrypoint y bloquea cargas excesivas |
| `entrypoints/` | Primer mensaje, reentry light/full, debug logs y compactación |
| `adapters/` | Traduce el contrato a Codex, Claude, Gemini, Qwen, DeepSeek y otras IAs |
| `scripts/validate-harness.ps1` | Valida manifest, schemas, presupuestos, copia `bound` simulada y pruebas negativas |
| `memory-closure-checklist.md` | Cierra consistencia de memoria antes de declarar `done` |
| `clarification-checklist.md` | Bloquea planes prematuros cuando falta información mínima |
| `analysis-checklist.md` | Fuerza requisitos, constraints, riesgos y evidencia antes de implementar |
| `blast-radius.md` | Declara módulos, archivos, comandos, red, git, rollback y riesgos |
| `task-graph.yaml` | Ordena dependencias, waves y paralelismo dentro del límite de agentes |
| `detractor-pass.md` | Revisa conclusiones de agentes buscando errores y sesgos |
| `changelog-policy.md` + `release-history-matrix.yaml` | Evita changelogs/release notes escritos sin reconstrucción histórica |
| `deploy-migration-policy.md` | Exige entorno, comando, evidencia, versión/ciclo y rollback en deploys o migraciones |
| `reference-drift-policy.md` | Detecta drift entre versión, binding, README, prompts, changelog e init |
| `ci-pipeline-policy.md` | Reconstruye iteraciones de CI/pipeline sin colapsar fallos relevantes |
| `backlog-policy.md` | Ordena P0/P1/P2 por bloqueo, impacto, dependencia y criterio de cierre |
| `final-report-evidence-policy.md` | Obliga a cerrar con links a gates, evidencia, closures, locks y gaps |
| `ai-preset-policy.md` | Verifica que presets/adapters de IA no salteen contrato, memoria ni approvals |
| `harness-manifest.txt` | Centraliza la estructura que `init.sh` valida para reducir drift |

Desde 0.9.0, estos controles se vuelven más trazables porque el orquestador
incorpora registries explícitos:

| Registry | Para qué existe |
|---|---|
| `prompt-registry.yaml` | Declara prompts oficiales, categoría y uso permitido |
| `registry-index.yaml` | Lista registries canónicos y evita fuentes duplicadas |
| `adapter-registry.yaml` | Hace auditables los adapters por IA |
| `context-profile-registry.yaml` | Vincula perfiles de contexto con rutas cargables |
| `gate-registry.yaml` | Declara gates/subgates y evita cierres por texto informal |
| `policy-registry.yaml` | Ordena políticas operativas y de permisos |
| `template-registry.yaml` | Declara templates SDD/progreso como contrato verificable |

Regla: un prompt puede guiar una acción, pero no crear autoridad nueva. La
autoridad sale del binding, registries, policies, state, registry, gates,
locks y evidencia.

En 0.12.0, la seguridad informática se expresa como registries, policies y
enforcement ejecutable. El objetivo no es solo evitar que la IA "se pase de
rol", sino reducir superficie de ataque real del harness:

| Control ejecutable | Qué bloquea |
|---|---|
| `hebrinex approve` | Acciones sin envelope vigente, sin hash exacto o fuera de TTL |
| `command-gateway.ps1 -ApprovalId` | Approval inexistente, vencido, no aprobado o usado con otro comando |
| Apply sin symlinks | Rutas contenidas en apariencia pero redirigidas por junction/symlink |
| Timeout con kill de árbol | Procesos hijo o nietos sobreviviendo a un timeout |
| Claude `PreToolUse` | Comandos Bash/PowerShell bloqueados por policy antes de ejecutar |
| `status` con locks | Trabajo activo o vencido invisible para el operador |

La base 0.10.0 ya expresaba seguridad como registries y policies verificables:

| Registro/política | Control |
|---|---|
| `permission-registry.yaml` | Permisos por rol, denies explícitos y bloqueo si falta capability |
| `command-risk-registry.yaml` | Clasifica lectura, validación local, escritura, destructivo, red, git remoto, secretos y privilegios |
| `write-scope-registry.yaml` | Limita escrituras a scope aprobado y bloquea path traversal/symlink escape |
| `network-policy.yaml` | Red deny-by-default, con endpoint, propósito, evidencia y expiración |
| `secrets-policy.yaml` | Deny-by-default para secretos, patrones de detección y redacción obligatoria |
| `supply-chain-policy.yaml` | Instalaciones con preflight, lockfile/checksum cuando exista y prohibición de ejecutar código remoto |
| `logging-policy.yaml` | Reportes sin secretos y backups auditables |
| `escalation-policy.yaml` | Elevaciones explícitas, temporales y con evidencia |
| `threat-model.yaml` | Amenazas esperadas: autoridad por prompt, command injection, path traversal, secretos, red, git remoto y supply chain |

Defaults de 0.12.0:

- permiso desconocido: bloqueado;
- comando desconocido: bloqueado;
- red: denegada salvo allowlist/aprobación;
- secretos: denegados y redactados antes de chat/reporte;
- dependencia nueva: preflight, `SI`, propósito y procedencia;
- código remoto descargado y ejecutado: denegado;
- salida con secreto: bloqueo y redacción.

**Regla:** en modo automático el leader puede decidir micro-pasos, pero no
puede editar, ejecutar comandos, usar red, hacer git, cambiar estado SDD ni
abrir subagentes con escritura/verificación sin preflight y `SI`.

Un `SI` no aprueba una sesión entera. Aprueba el action envelope declarado.
Si cambia comando, cwd, project root, harness path, binding, write-set, red,
git, scope externo o riesgo, se pide otro `SI`.

Después de una compactación, resumen de sesión, cambio de cwd o cambio de
proyecto, los approvals anteriores expiran. El leader debe hacer re-entry:

```text
1. Confirmar project_root.
2. Confirmar harness_path.
3. Validar PROJECT_BINDING.yaml.
4. Leer session-pin.md, memory-registry.yaml, memory-routing.yaml,
   context-budget.yaml, state.yaml, registry.yaml y PROGRESS.md.
5. Reconstruir ciclo, locks, agentes abiertos y handoffs.
6. Pedir un nuevo SI antes de cualquier acción con efecto.
```

**Regla:** ninguna autonomía sobrevive a un binding `missing` o `mismatch`.
Si el harness no pertenece al proyecto activo, el flujo se bloquea.

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
4. Para harness P0: `audit.jsonl`, `gate-log.yaml`, `verification-matrix.yaml`,
   `final-report.md` y `agent-closure.md` por ciclo o slice relevante.

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
- [ ] Regla anti-confirmation bias activa.
- [ ] Detractor pass definido para cierres o decisiones de riesgo.

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

---

## Autonomia con Runtime 0.10.0

Los comandos `/harness status`, `/harness reentry`, `/harness budget`, `/harness manual`, `/harness automatico` y `/harness audit` son controles de bajo costo. No reemplazan aprobación humana ni evidencia.

El runtime reduce tokens porque consulta primero estado compacto. Si necesita contexto completo, debe declarar read-set, budget y pedir `SI`.

## Autonomía con Runtime 0.12.0

`0.12.0` agrega una capa práctica sobre el runtime: la autonomía no avanza sólo
porque el chat diga "sí". El `SI` se puede materializar como approval envelope
y el gateway lo valida contra la acción exacta.

Flujo sano:

```text
1. El agente propone preflight.
2. El operador aprueba con SI.
3. `hebrinex approve -Apply -CommandText <accion exacta>` crea el envelope.
4. `command-gateway.ps1 -ApprovalId <APR>` valida hash, TTL y estado.
5. El comando se permite, pregunta o bloquea según policy.
```

Con hooks de Claude Code, el host puede hacer cumplir parte del contrato antes
del tool call:

- `SessionStart` inyecta el re-entry brief para no depender de memoria del chat.
- `PreToolUse` permite read-only seguro, fuerza pregunta ante patrones
  bloqueados y delega casos ambiguos al flujo normal de permisos.

Regla metodológica: los hooks ayudan, pero no son evidencia. La evidencia sigue
siendo el estado, registry, approval envelope, gate log, salida de comando y
validadores.
