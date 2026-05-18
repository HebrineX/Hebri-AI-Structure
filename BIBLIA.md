# BIBLIA — Hebri-AI-Structure

Metodología personal de trabajo con inteligencia artificial aplicada a proyectos de software.

> **Ruta práctica:**
> - Proyecto nuevo → [Vol 07 · Arrancar proyecto](#vol-07--acoplamiento-con-harness)
> - Planear fase o slice → [Vol 03 · Fases vs slices](#fases-vs-slices)
> - Delegar a subagentes → [Vol 02 · Explorer/Worker](#explorerworker)
> - Definir estructura de repo → [Vol 04 · Mapa de responsabilidades](#mapa-de-responsabilidades)
> - Registrar algo que falta → [Vol 06 · Gap tracking](#vol-06--gap-tracking)

---

## Vol 01 · Modelo de Trabajo

### Las 4 Capas

La diferencia entre usar IA como chat y usarla como sistema de trabajo no está en escribir prompts más largos. Está en que cada interacción tenga estructura.

**Capa 1 — Contexto.** Lo que la IA debe saber antes de actuar. No es un dump de información — es la porción del proyecto que el agente necesita para actuar sin suponer. Sin contexto, el agente optimiza para la respuesta más razonable en abstracto, que no es necesariamente la correcta para este proyecto.

**Capa 2 — Tarea.** Lo que debe producir ahora. La tarea tiene alcance acotado, criterios de salida verificables y un propietario claro. Una tarea que dice "mejorá lo que veas" no es una tarea — es una delegación ciega.

**Capa 3 — Verificación.** Cómo se sabe que el resultado sirve. Un resultado que no se puede probar no es un resultado cerrado — es un supuesto que se integra con confianza falsa. La verificación es ejecutable: un comando, un test, una revisión contra una spec.

**Capa 4 — Memoria.** Dónde queda registrado para no repetir el mismo razonamiento. La memoria no vive en el chat — vive en archivos. Sin esta capa, cada sesión empieza de cero.

---

### Ciclo de Trabajo

```
Intención → Contexto → Plan → Ejecución → Verificación → Registro → Siguiente iteración
```

**Intención:** El resultado deseado en una frase verificable. Si al terminar no podés contrastarla contra lo producido, la intención estaba mal definida.

**Contexto:** La porción del proyecto que el agente necesita para este trabajo. Cada sesión nueva empieza desde lo que está escrito, no desde lo que se recordó.

**Plan:** Lista de 3 a 7 pasos concretos con archivos afectados y criterio de salida por paso. Evita trabajo impulsivo.

**Ejecución:** Un cambio a la vez, ownership explícito, sin saltear pasos.

**Verificación:** Comando que corre, test que pasa, output que matchea un criterio.

**Registro:** Mover el conocimiento del chat al repositorio — decisiones, gaps, estado actualizado. Es la capa que más se saltea y la que hace que el trabajo sea acumulable.

**Siguiente iteración:** Con el registro hecho, la próxima sesión avanza desde el último estado conocido.

---

### Unidad Mínima de Contexto

| Campo | Para qué sirve | Nota |
|---|---|---|
| Objetivo | Evita que el agente optimice otra cosa | Una frase verificable |
| Estado actual | Da el punto de partida real | Qué existe hoy, no qué debería existir |
| Restricciones | Acota las soluciones | Qué no se puede tocar, cambiar o asumir |
| Archivos relevantes | Reduce exploración ciega | Rutas concretas, no carpetas enteras |
| Criterios de aceptación | Define el cierre | Qué tiene que ser verdad al terminar |
| Verificación | Obliga a probar | El comando o test que confirma |
| Riesgos | Hace visible lo delicado | Lo que podría romperse si se improvisa |

**Escala mínima por tipo de tarea:**

| Tipo | Campos obligatorios |
|---|---|
| Exploración | Objetivo + Archivos + Restricciones (solo lectura) |
| Corrección puntual | Objetivo + Archivos + Verificación |
| Implementación | Todos los campos |
| Documentación | Objetivo + Archivos + Salida esperada |

---

## Vol 02 · Subagentes

### Explorer/Worker

La separación más útil cuando se trabaja con subagentes. No es una separación técnica — es una separación de responsabilidades.

**Explorer** sirve para entender. Solo lectura. Devuelve hallazgos con evidencia. No edita archivos, no propone soluciones mientras explora, no completa huecos con supuestos.

Prompt base:
```
Rol: explorer
Alcance: solo lectura sobre [carpeta o archivos]
Objetivo: [pregunta concreta]
Entrega: [lista de archivos, resumen del flujo, riesgos identificados]
Restricción: no hagas cambios. Si algo no es claro, marcalo como incertidumbre.
```

**Worker** sirve para hacer. Recibe objetivo acotado, ownership claro y criterio de salida verificable. Toca solo los archivos autorizados, explica qué cambió, valida con el comando acordado.

Prompt base:
```
Rol: worker
Ownership exclusivo: [archivo o carpeta]
Objetivo: [tarea concreta]
Restricciones: [qué no tocar, qué no cambiar]
Verificación: [comando]
Salida esperada: [descripción del resultado]
```

**Regla:** Explorer primero, Worker después. Si no podés describir el ownership del Worker en una frase, el Explorer todavía no terminó.

---

### Ownership de Archivos

Ownership es la definición explícita de qué puede tocar cada agente.

```
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

En trabajo paralelo: los ownerships no pueden solaparse en ningún archivo. Si dos workers necesitan el mismo archivo, se serializa.

---

### Anti-patrones de Subagentes

1. **Delegación vaga** — La respuesta suena prolija pero no aterriza en archivos ni decisiones concretas. Causa: la tarea no tenía criterio de salida verificable.

2. **Context dumping** — Se pasa todo el repo como contexto. El agente mezcla señales irrelevantes. Causa: no se definió la unidad mínima de contexto.

3. **Ownership difuso** — Aparecen cambios en archivos que nadie autorizó. Causa: no se definió ownership antes de ejecutar.

4. **El agente que resuelve diseño** — El Worker propone reestructurar todo antes de terminar el cambio pedido. Causa: la tarea tenía huecos que el agente rellenó con criterio propio.

5. **Paralelismo falso** — Tareas "paralelas" que compiten por los mismos archivos. Causa: no se verificó que los ownerships fueran disjuntos.

6. **Cierre sin evidencia** — El agente dice que terminó sin mostrar cómo se valida. Formato mínimo de cierre: archivos tocados + comando ejecutado + resultado.

7. **Mezclar exploración con edición** — "Ya que estaba, también ajusté...". Causa: no se separó el rol Explorer del Worker.

8. **Teléfono descompuesto** — Cada agente resume el output del anterior en lugar de leer el artefacto original. Los agentes escriben resultados en archivos y devuelven solo la referencia.

---

## Vol 03 · SDD

### Flujo SDD

SDD (Specification-Driven Development) garantiza que la IA ejecuta lo que el humano aprobó — no lo que interpretó.

```
pending
  └── spec_author escribe requirements.md, design.md, tasks.md
        └── spec_ready
              └── ── HUMANO APRUEBA (puerta obligatoria) ──
                    └── in_progress
                          └── implementer ejecuta tasks aprobadas
                                └── reviewer verifica contra spec
                                      └── done (con evidencia)
```

**requirements.md** — Qué necesidad existe y cómo debe comportarse el sistema. Usa formato EARS.

**design.md** — Qué archivos se tocan, qué decisiones se toman, qué alternativas se descartan y por qué, qué queda fuera del alcance.

**tasks.md** — Lista ejecutable con trazabilidad a requirements:
```
- [ ] T1 — [descripción]. Cubre: R1.
- [ ] T2 — [descripción]. Cubre: R1, R2.
```

**Formato de aprobación:**
```
Estado: aprobado
Aprobado por: [nombre]
Fecha: [fecha]
Alcance aprobado: R1, R2, R3
Condición de cierre: todos los R con test verde y build limpio
```

Si cambia el alcance después de aprobar, el estado vuelve a `spec_ready`.

---

### Fases vs Slices

Dos escalas de planificación simultáneas.

**Fase** — escala global. Agrupa una capacidad completa que lleva el sistema a un estado cualitativamente distinto. Tiene estado visible, cierra con evidencia (build + tests + tag opcional).

**Slice** — escala granular. La parte más pequeña implementable, verificable y mergeable de forma independiente. Cubre uno o más requirements.

```
Fase 2 — Motor de reglas
  ├── Slice 2.1 — YAML rule catalog
  ├── Slice 2.2 — Rule evaluator
  ├── Slice 2.3 — Clasificación guiada por reglas
  └── Slice 2.4 — Reporter agrupado por regla

Fase 2.5 — Enrichment (diferida de Fase 2)
  └── Slice 2.5.1 — HttpMethod enricher
```

**Estado visible de fases** (en README o README-PROGRESPJ.md):
```markdown
| Fase | Descripción | Estado | Tests |
|---|---|---|---|
| Fase 1 | Capacidad base | ✅ Completa | 87 passed |
| Fase 2 | Motor de reglas YAML | ✅ Completa | 270 passed |
| Fase 2.5 | Enrichment HTTP | 🔄 En progreso | — |
| Fase 3 | Threat Intelligence | ⏳ Pendiente | — |
```

**Versionado** — posibilidad, no norma. Cuando se usa: `v[major].[fase].[patch]` por fase, o semántico si hay API pública. Lo importante: el tag representa un estado real (build + tests verdes).

**Gaps entre fases** — al cerrar una fase, los ítems diferidos quedan registrados explícitamente con motivo. Ver Vol 06.

---

### EARS — Requirements Verificables

| Patrón | Forma |
|---|---|
| Ubicuo | El sistema DEBE `[acción]`. |
| Evento | CUANDO `[evento]`, el sistema DEBE `[acción]`. |
| Estado | MIENTRAS `[condición]`, el sistema DEBE `[acción]`. |
| Opcional | DONDE `[feature activa]`, el sistema DEBE `[acción]`. |
| No deseado | SI `[situación no deseada]` ENTONCES el sistema DEBE `[acción]`. |

**Reglas:** ID estable por requirement (R1, R2...). Verificable con un test. Un solo DEBE por requirement. Sin verbos blandos. Cada R mapea a al menos un test en tasks.md.

**Ejemplo:**
```
R1: CUANDO el sistema arranca, DEBE cargar todas las reglas YAML del directorio configurado.
R2: CUANDO un WafEvent llega al analyzer, el sistema DEBE evaluar todas las reglas en orden de prioridad.
R3: SI una regla tiene formato inválido ENTONCES el sistema DEBE lanzar YamlRuleCatalogException al arrancar.
```

---

### Checklist de Cierre

**Slice:**
```
[ ] Requirement escrito con ID estable.
[ ] Spec define comportamiento verificable.
[ ] Alcance excluye explícitamente lo que no entra.
[ ] Decisiones relevantes en design.md.
[ ] Tasks con trazabilidad a requirements.
[ ] Criterios de aceptación concretos y verificables.
[ ] Cada requirement tiene al menos un test.
[ ] Tests pasan (comando ejecutado).
[ ] Sin contradicciones entre spec, código y tests.
[ ] Gaps nuevos registrados.
```

**Fase (todo lo anterior más):**
```
[ ] Build de release limpio.
[ ] Tests de regresión de fases anteriores verdes.
[ ] Gaps diferidos registrados con motivo.
[ ] README-PROGRESPJ.md actualizado.
[ ] Tag creado (si corresponde).
[ ] Release notes escritas (si corresponde).
```

Señales de que el cierre NO está listo: "funciona pero no corrí los tests", "el doc lo actualizo después", "el gap está en mi cabeza".

---

## Vol 04 · Arquitectura de Repo

### Mapa de Responsabilidades

| Archivo / Carpeta | Audiencia | Responsabilidad | NO es para |
|---|---|---|---|
| `README.md` | Personas nuevas | Presentar el proyecto, instalación y uso rápido | Detalle de arquitectura, reglas de agente |
| `AGENTS.md` | Agentes de código | Reglas operativas, mapa de rutas, comandos, permisos, cierre | Landing page, visión de producto |
| `README-PROGRESPJ.md` | Equipo + agentes | Estado de fases/slices, gaps activos, roadmap | Documentación técnica |
| `.github/copilot-instructions.md` | GitHub Copilot | Instrucciones generales de estilo y contexto | Repetir lo que ya está en AGENTS.md |
| `.github/prompts/*.prompt.md` | Equipo y asistentes | Comandos reutilizables para tareas que se repiten | Documentación del sistema |
| `.github/workflows/*.yml` | CI/CD | Automatización ejecutable | Documentar cosas |
| `specs/<feature>/` | Equipo + agentes | Requirements, design, tasks | Código, documentación de usuario |
| `progress/current.md` | Agentes | Estado vivo de la sesión activa | Historial permanente |

**Regla de oro:** Un archivo de contexto no finge que ejecuta. Un workflow ejecuta. Un catálogo documenta. Un prompt orienta. Un AGENTS.md gobierna. Mantener esa separación evita sistemas confusos.

**Estructura mínima por escala:**

Chico (script / herramienta):
```
repo/ → AGENTS.md · README.md · src/ · tests/ · .github/workflows/ci.yml
```

Mediano (servicio con fases):
```
repo/ → + README-PROGRESPJ.md · docs/ · specs/<feature>/ · .github/prompts/
```

Grande (sistema multi-módulo):
```
repo/ → + .github/instructions/ · progress/current.md · progress/history/
```

---

### Fuente de Verdad

El problema de trabajar con múltiples herramientas (GitHub, Claude, Copilot, CI) es que cada una tiene su capa de configuración. Si no se define dónde vive la fuente de verdad, el contexto se duplica y las capas entran en contradicción.

**El principio:** La fuente de verdad es una sola carpeta. Las demás capas son conectores que apuntan a esa fuente — nunca la reemplazan ni la duplican.

```
FUENTE DE VERDAD
   specs/              ← una sola, siempre

CONECTORES (apuntan a la fuente, no la duplican)
   .github/workflows/ci.yml       → ejecuta dotnet test
   .github/prompts/plan.prompt.md → dice "leer specs/<feature>/"
   AGENTS.md                      → dice "antes de implementar: leer specs/"
```

Cambiar de herramienta no mueve la verdad — solo reemplaza el conector.

**Árbol de decisión:**
```
¿El trabajo se revisa y bloquea en PRs con equipo?
├── Sí → GitHub-first (.github/orquestador/sdd/ como fuente)
└── No → ¿La herramienta diaria es Claude?
    ├── Sí → Claude-first (specs/ + AGENTS.md + .claude/)
    └── No → Portable (specs/ + AGENTS.md)
```

**Regla de migración:** Al cambiar la fuente de verdad, mover el contenido completo, dejar un `MOVED.md` en la carpeta vieja, actualizar AGENTS.md. No mantener dos carpetas activas con contenido diferente.

---

### Cómo Escribir un Buen AGENTS.md

AGENTS.md es el mapa operativo para agentes. Debe funcionar como mapa, no como enciclopedia.

**Debe responder:**
1. Cuál es el stack.
2. Dónde está cada cosa importante (rutas, no descripciones vagas).
3. Qué comandos se usan para instalar, probar, validar y correr.
4. Qué archivos o carpetas son delicados.
5. Qué estilo de cambios se espera.
6. Qué está prohibido tocar.
7. Cómo cerrar una tarea (qué evidencia se espera).

**Error más común:** AGENTS.md filosófico. "Usar buenas prácticas" no es una regla. Un agente necesita comandos, rutas, límites y criterios concretos.

**Template base:**
```markdown
# AGENTS.md

## Stack
- Lenguaje: [...] · Framework: [...] · Tests: [...]

## Comandos
- Tests: `[comando]`
- Build: `[comando]`
- Validar todo: `[comando]`

## Mapa
| Ruta | Contenido | Cuándo leer |
|---|---|---|
| `specs/` | Requirements, design, tasks | Antes de implementar |
| `README-PROGRESPJ.md` | Estado de fases y gaps | Siempre al arrancar |

## Reglas
- No tocar código antes de spec aprobada.
- No declarar done sin tests verdes y build limpio.
- Si un comando falla, reportar el error exacto antes de continuar.

## Cierre
Antes de declarar done: archivos modificados · comando ejecutado + resultado · gaps nuevos.
```

---

### Stacks

#### .NET

Comandos estándar:
```powershell
dotnet restore
dotnet build
dotnet build -c Release                                    # cierre de fase
dotnet test
dotnet test --filter "FullyQualifiedName~[NombreClase]"   # subset
```

Estructura recomendada:
```
src/
  Solucion.Core/        ← modelos, interfaces, contratos
  Solucion.Domain/      ← lógica de dominio
  Solucion.Analyzers/   ← componentes de análisis
  Solucion.Worker/      ← punto de entrada / host
tests/
  Solucion.Core.Tests/
  Solucion.Analyzers.Tests/
  Solucion.Integration.Tests/
```

Naming de tests: `[Cuando]_[Resultado]` → `WhenRuleMissing_ClassifiesAsUnclassified`

Cierre de fase:
```powershell
dotnet test && dotnet build -c Release
git tag -a v[versión] -m "Fase [N] — [descripción]"
git push origin main v[versión]
```

#### Python

Comandos estándar:
```bash
pip install -r requirements.txt
pytest
pytest -k "test_nombre"
mypy src/
ruff check src/ tests/
```

Naming de tests: `test_when_[condicion]_[resultado]`

#### YAML como lenguaje de dominio

Aplica cuando las reglas de negocio viven en archivos YAML y tienen su propio ciclo de vida (se agregan, modifican y deshabilitan independientemente del código).

**El motor debe fallar al arrancar, no en runtime.** Una regla inválida debe lanzar excepción con nombre de archivo y error antes de procesar eventos.

Estructura mínima de una regla:
```yaml
id: sql-injection-basic
description: Detecta patrones básicos de SQL injection en el body
enabled: true
priority: 100
bucket: SqlInjection
action: Block
conditions:
  - field: RequestBody
    operator: contains
    value: "SELECT"
```

Tests: usar reglas inline en el test, no depender de archivos en disco. Cubrir match, no-match, campo nulo.

Gaps comunes: operadores faltantes (regex, notContains), sin validación de schema, sin hot-reload, sin versionado del catálogo, precedencia no definida cuando varias reglas matchean.

---

## Vol 05 · Prompts

### Brief Operativo

El brief operativo es el formato estándar para pedirle trabajo a un agente. Es un contrato de tarea, no una pregunta abierta.

```
Objetivo:
  [una frase verificable]

Contexto:
  Stack: [lenguaje, framework, versión]
  Estado actual: [qué existe hoy]
  Decisiones relevantes: [referencias si aplica]

Restricciones:
  - No tocar [archivo o carpeta].
  - No modificar [interfaz o contrato].

Archivos relevantes:
  - [ruta] — [para qué sirve en esta tarea]

Salida esperada:
  - [artefacto: qué es, dónde vive]

Verificación:
  [comando concreto]

Riesgos:
  - [qué podría romperse si se improvisa]
```

---

### Anti-patrones de Prompts

1. **El prompt filosófico** — "Ayudame a mejorar la arquitectura". Sin criterio de salida, el agente elige qué "mejorar" significa.

2. **Sin restricciones** — Define qué hacer pero no qué no hacer. El agente toca lo que necesite "para que funcione".

3. **Sin verificación** — Define qué producir pero no cómo saber si está correcto. Los tests pueden pasar sin cubrir los casos relevantes.

4. **El prompt de "arreglá todo"** — Combina exploración con implementación sin punto de control humano. Separar siempre en Explorer primero, Worker después.

5. **Sin contexto de stack** — El agente produce una solución genérica que no respeta las convenciones del proyecto.

6. **El prompt acumulado** — Pegar todo el historial de conversaciones anteriores como contexto. El agente mezcla decisiones ya revertidas con el pedido actual. El contexto se prepara desde los archivos del repo, no desde el chat.

---

## Vol 06 · Gap Tracking

### Qué es un Gap

Un gap es cualquier cosa que sabés que falta, está incompleta, o decidiste no hacer todavía — pero que quedó registrada explícitamente para no perder el hilo.

**La diferencia entre gap y tarea:**

| | Gap | Tarea |
|---|---|---|
| Qué es | Algo que falta, aún no listo para especificar | Algo concreto con spec aprobada |
| Cuándo aparece | Durante análisis o cierre de fase | Cuando el gap tiene spec aprobada |
| Tiene fecha/owner | No necesariamente | Sí |

Un gap nunca desaparece en silencio. Si se resuelve, se marca con referencia a qué lo cerró. Si se descarta, se marca con motivo.

---

### Estructura de un Gap

```markdown
## Gap #[N] — [título corto]

**Estado:** [Identificado | Diferido a Fase X | En análisis | Resuelto | Descartado]
**Capa:** [Dev | Infra | DevOps | Docs | Testing]

**Descripción:** [Qué falta o está incompleto]

**Contexto:** [Por qué existe este gap]

**Motivo de diferimiento:** [Por qué no se resuelve ahora]

**Destino:** Fase [N] / Sin asignar

**Resuelto por:** [PR / Commit / Tarea — si aplica]
```

Los gaps del proyecto viven en `README-PROGRESPJ.md` o en `docs/gaps.md` para proyectos con muchos gaps. Esta biblia tiene la biblioteca de gaps comunes por stack como referencia al arrancar.

---

### Biblioteca de Gaps — Dev

| # | Gap | Cuándo aparece |
|---|---|---|
| D-01 | Interfaces públicas no estabilizadas | Al intentar testear por primera vez |
| D-02 | Contratos entre módulos no definidos | Al conectar dos módulos |
| D-03 | Lógica de negocio en el handler/controller | Al querer reusar la lógica |
| T-01 | Tests que dependen de archivos en disco en lugar de fixtures | Al correr en CI sin archivos |
| T-02 | No hay tests de casos borde (null, vacío, límite) | Al producir la primera regresión |
| T-03 | Suite sin naming convention — difícil filtrar por área | Al querer correr solo tests de una feature |
| N-01 | Options classes sin validación en startup (.NET) | Al deployar con config incompleta |
| N-02 | Build de release da warnings no vistos en debug (.NET) | Al correr `dotnet build -c Release` |
| P-01 | Type annotations incompletas en interfaces públicas (Python) | Al integrar con otro módulo |
| P-02 | requirements.txt sin versiones pinneadas (Python) | Al instalar en un entorno nuevo |
| Y-01 | Operadores faltantes en motor YAML (regex, notContains) | Al escribir la primera regla que los necesita |
| Y-02 | Sin validación de schema al cargar reglas YAML | Al agregar un campo nuevo y romper reglas viejas |
| Y-03 | Sin hot-reload de reglas sin reinicio | Al actualizar reglas en producción |
| Doc-01 | AGENTS.md sin comandos concretos de verificación | Al retomar el trabajo con un agente |
| Doc-02 | Decisiones de arquitectura no documentadas (sin ADR) | Al querer cambiar algo y no recordar por qué está así |

---

### Biblioteca de Gaps — Infra

| # | Gap | Cuándo aparece |
|---|---|---|
| I-01 | Sin health check en el contenedor | Al deployar en Kubernetes o ECS |
| I-02 | Sin graceful shutdown | Al actualizar la imagen en producción |
| I-03 | Variables de entorno hardcodeadas en Dockerfile | Al usar la misma imagen en staging y prod |
| I-04 | Sin límites de recursos (CPU/memoria) | Al ver consumo descontrolado en producción |
| I-05 | Sin logs estructurados (solo texto plano) | Al intentar hacer queries sobre los logs |
| I-06 | Sin métricas de negocio | Al querer saber cuántos eventos se procesan por minuto |
| I-07 | Sin runbook para las alertas más comunes | Al recibir una alerta y no saber qué hacer |
| I-08 | Certificados TLS sin rotación automática | Al tener un corte por cert expirado |
| I-09 | Backups no testeados (no se sabe si restauran) | Al necesitar restaurar por primera vez |

---

### Biblioteca de Gaps — DevOps

| # | Gap | Cuándo aparece |
|---|---|---|
| DO-01 | CI no corre en PRs — solo en main | Al mergear un PR roto |
| DO-02 | CI corre tests pero no build de release | Al tener tests verdes y binario roto |
| DO-03 | CI sin separación de etapas (lint/test/build/deploy) | Al querer saber exactamente qué falló |
| DO-04 | Sin smoke test post-deploy | Al saber que el deploy fue bien recién cuando el usuario reporta |
| DO-05 | Sin rollback documentado | Al tener un deploy roto en producción |
| DO-06 | Sin política de versionado definida | Al tener tags inconsistentes |
| DO-07 | Secretos hardcodeados en workflows de CI | Al hacer un security audit |
| DO-08 | Permisos de CI demasiado amplios | Al revisar el principio de mínimo privilegio |

---

### README-PROGRESPJ — Template

```markdown
# [Nombre del Proyecto] — Progreso

**Stack:** [lenguaje · framework · tests]

## Estado

| Fase | Descripción | Estado | Tests |
|---|---|---|---|
| Fase 1 | [descripción] | ✅ Completa | [N] passed |
| Fase 2 | [descripción] | 🔄 En progreso | — |
| Fase 3 | [descripción] | ⏳ Pendiente | — |

## Gaps conocidos

| # | Descripción | Estado | Destino |
|---|---|---|---|
| Gap #1 | [descripción] | Diferido | Fase X |
| Gap #2 | [descripción] | Identificado | Sin asignar |

## Criterios de cierre — Fase actual

- [ ] [N] tests pasando
- [ ] Build de release limpio
- [ ] Gaps diferidos documentados
- [ ] Tag creado: `v[versión]`

## Historial

### Fase 1 ✅
- Fecha: [fecha] · Tests: [N] passed · Tag: `v[versión]`
- Gaps diferidos: Gap #1, Gap #2
```

---

## Vol 07 · Acoplamiento con Harness

### Cómo Definir el Tipo de Proyecto

Antes de crear cualquier archivo, responder estas preguntas. Un proyecto que hoy parece pequeño puede volverse monumental — la estructura inicial debe soportar ambas escalas.

**1. ¿Cuál es el objetivo principal?** Una frase que describe qué resuelve y para quién.

**2. ¿Qué stack?** Lenguaje + versión, framework, herramienta de test, build/packaging.

**3. ¿Solo o en equipo?** Solo → más flexibilidad. Equipo → SDD estricto, fuente de verdad en repo.

**4. ¿Cuál es la escala esperada?**

| Escala | Descripción | Estructura inicial |
|---|---|---|
| Chico | Script, herramienta simple | AGENTS.md + README + src + tests |
| Mediano | Servicio con 2-4 capacidades | + specs/ + README-PROGRESPJ.md + docs/ |
| Grande | Sistema con múltiples módulos | + .github/ completo + progress/ |
| No sé | Empezar con mediano | Igual que mediano |

**5. ¿Hay reglas de dominio configurables?** YAML rules, políticas declarativas → agregar `config/rules/` + leer Vol 04 · YAML domain.

**6. ¿Hay integraciones externas?** APIs, bases de datos, servicios externos. Identificarlas al inicio.

**7. ¿Qué CI/herramienta principal?** GitHub Actions → GitHub-first. Claude diario → Claude-first. Sin preferencia → Portable.

**Output de esta sesión antes de escribir código:**
1. `AGENTS.md` inicial con stack, comandos y reglas.
2. `README.md` con descripción y estructura.
3. `README-PROGRESPJ.md` con primera fase o slices.
4. Lista de gaps iniciales (de la biblioteca Vol 06).
5. Estructura de carpetas inicial.

---

### La Biblia y el Harness

**Esta biblia aporta:** el modelo mental, el proceso, las convenciones por stack, la biblioteca de gaps, los formatos.

**El harness template aporta:** la estructura de carpetas lista para copiar, AGENTS.md base pre-cargado, templates de specs, prompts pre-cargados, scripts de setup.

El template implementa la biblia. Si hay conflicto, la biblia gana.

**Flujo de arranque:**
```
1. Responder preguntas de tipo de proyecto (este volumen)
2. Elegir harness template que corresponde al tipo
3. Copiar el template como base del repo
4. Personalizar AGENTS.md con stack, comandos y reglas específicas
5. Completar README-PROGRESPJ.md con fases/slices y gaps iniciales
6. Empezar el trabajo con el ciclo definido en Vol 01
```

En el AGENTS.md del proyecto, agregar referencia:
```markdown
## Metodología
Este proyecto sigue Hebri-AI-Structure: [link al repo]
```

No copiar el contenido de la biblia al proyecto — referenciarla.

---

## Créditos

Construido sobre **[The AI Biblia](https://github.com/Nicolas-Melluso/The-AI-Biblia)** de [Nicolás Ezequiel Melluso](https://www.linkedin.com/in/nicolas-ezequiel-melluso/).

**Base de The AI Biblia:** modelo de 4 capas · ciclo de trabajo · Explorer/Worker · SDD con EARS · AGENTS.md como contrato operativo · mapa de responsabilidades · prompts reutilizables versionados.

**Adaptaciones y aportes propios:** fases vs slices como dos escalas simultáneas · YAML como lenguaje de dominio · sistema de gap tracking con biblioteca por stack · README-PROGRESPJ.md separado del README principal · stacks específicos (.NET / Python) · resolución de la tensión fuente de verdad única · preguntas de arranque por tipo de proyecto.
