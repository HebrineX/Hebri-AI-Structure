# Apéndice · Ejemplo End-to-End

> Anterior: [Vol 09 · Roles cerrados](./vol-09-roles-cerrados.md)

Recorrido completo de un slice ficticio aplicando los 9 volúmenes. El
proyecto es **TaskNotifier**: un servicio .NET 8 que envía recordatorios de
tareas vencidas a un canal de Slack.

Estado inicial: la Fase 1 (modelo de datos + persistencia + endpoint REST)
está cerrada con 42 tests verdes. Queremos arrancar la Fase 2.

---

## 1 · Intención

> Cuando una tarea pasa su fecha de vencimiento, el sistema debe notificar
> al canal de Slack del owner dentro de los 5 minutos siguientes.

Frase verificable: existe un comando (`dotnet test`) que valida el
comportamiento y un test de integración que dispara la condición.

---

## 2 · Contexto

**Stack:** C# / .NET 8 · xUnit · FluentAssertions · MediatR · EF Core
(SQLite en tests).

**Estado actual:**

- `src/TaskNotifier.Core/Models/Task.cs` ya existe con campo `DueAt`.
- `src/TaskNotifier.Api/` expone CRUD de tareas.
- Tests: 42 passed.

**Decisiones previas:** ADR-001 (xUnit como framework), ADR-002 (MediatR
para handlers de comandos).

**Restricciones:**

- No tocar `Task.cs` ni los handlers existentes (estables, Fase 1).
- No introducir base de datos nueva — usar SQLite ya configurado.

---

## 3 · Tipo de Proyecto y Estructura

Aplicando [Vol 07](./vol-07-harness.md): escala mediana, GitHub-first, ya
tiene `specs/` y `PROGRESS.md`. No hace falta crear estructura nueva.

---

## 4 · Fase 2 y Slices

Aplicando [Vol 03 · Fases vs Slices](./vol-03-sdd.md):

```text
Fase 2 — Notificaciones de tareas vencidas
  ├── Slice 2.1 — Scheduler que detecta tareas vencidas
  ├── Slice 2.2 — Cliente de Slack y mensaje formateado
  ├── Slice 2.3 — Wire-up scheduler → cliente + idempotencia
  └── Slice 2.4 — Configuración del intervalo y feature flag
```

Vamos a hacer **Slice 2.1** completo.

---

## 5 · Spec del Slice 2.1

`specs/2.1-scheduler/requirements.md`:

```text
R1: MIENTRAS el servicio está corriendo, el sistema DEBE evaluar cada N
    segundos la lista de tareas con DueAt < now y NotifiedAt = null.
R2: CUANDO el scheduler encuentra una tarea vencida no notificada, DEBE
    emitir un evento `TaskOverdueDetected(taskId)` en el bus interno.
R3: SI la base de datos no responde al consultar tareas, ENTONCES el
    scheduler DEBE registrar el error y reintentar en la próxima iteración
    sin frenar el host.

N: por defecto 60 segundos, configurable vía appsettings.
```

`specs/2.1-scheduler/design.md`:

```text
Archivos nuevos:
  src/TaskNotifier.Worker/Scheduling/OverdueScanner.cs
  src/TaskNotifier.Worker/Scheduling/OverdueScannerOptions.cs
  tests/TaskNotifier.Worker.Tests/Scheduling/OverdueScannerTests.cs

Decisión 1: Usar IHostedService (BackgroundService) — está bien soportado
            por .NET 8 y compatible con el host existente.

Decisión 2: Inyectar IClock (envuelve DateTime.UtcNow) para tests.

Decisión 3: El scanner NO envía mensajes — solo publica eventos al bus.
            La separación facilita el slice 2.2 y los tests.

Alternativas descartadas:
  - Cron tipo Quartz: agrega dependencia para un caso simple.
  - Polling desde fuera del host: complica el deploy.

Fuera de alcance: el envío de Slack y la idempotencia van en slices 2.2 y 2.3.
```

`specs/2.1-scheduler/tasks.md`:

```text
- [ ] T1 — Crear OverdueScannerOptions con SecondsInterval (default 60).
      Cubre: R1.
- [ ] T2 — Crear OverdueScanner : BackgroundService que lea TaskRepository,
      filtre vencidas no notificadas, y publique TaskOverdueDetected.
      Cubre: R1, R2.
- [ ] T3 — Test: con 3 tareas (1 vencida no notif, 1 vencida notif, 1 futura),
      el scanner publica 1 solo evento con el id correcto. Cubre: R1, R2.
- [ ] T4 — Test: si TaskRepository.Get lanza, el scanner loguea y la próxima
      iteración corre normal. Cubre: R3.
- [ ] T5 — Registrar OverdueScanner en Program.cs como hosted service.
```

Aprobación humana:

```text
Estado: aprobado
Aprobado por: Lucas
Fecha: 2026-05-18
Alcance aprobado: R1, R2, R3
Condición de cierre: tests T3 y T4 verdes; build de release limpio.
```

---

## 6 · Brief Operativo para el Worker

Aplicando [Vol 05](./vol-05-prompts.md) y [Vol 08](./vol-08-mcps-y-autonomia.md):

```text
Objetivo:
  Implementar Slice 2.1 según specs/2.1-scheduler/.

Contexto:
  Stack: .NET 8, xUnit, MediatR, EF Core SQLite en tests.
  Estado: Fase 1 cerrada (42 tests). Spec aprobada.

Restricciones:
  - Ownership exclusivo: src/TaskNotifier.Worker/Scheduling/ y
    tests/TaskNotifier.Worker.Tests/Scheduling/. Más Program.cs solo para T5.
  - No modificar Task.cs ni los handlers existentes.
  - No agregar dependencias nuevas a .csproj.

Archivos relevantes:
  - specs/2.1-scheduler/{requirements,design,tasks}.md
  - src/TaskNotifier.Core/Models/Task.cs (solo lectura)
  - src/TaskNotifier.Worker/Program.cs (editar al final, T5)

Autonomía: Nivel 2 (escritura local). Para correr tests: Nivel 3 acordado
para el cierre.

Salida esperada:
  - Archivos creados según tasks.md.
  - Tests T3 y T4 escritos y verdes.
  - Diff del Program.cs registrando el hosted service.

Verificación:
  dotnet test --filter "FullyQualifiedName~Scheduling"

Riesgos:
  - Inyección de IClock — si se olvida, los tests dependen de tiempo real
    y son flaky.
  - Excepción no capturada en el loop del BackgroundService puede tirar el
    host abajo.
```

---

## 6.1 · Controles Harness 0.8.8

Aplicando [Vol 09](./vol-09-roles-cerrados.md) y el
[Apéndice Harness 0.8](./apendice-harness-0-5-operacion.md), antes de que el
Worker escriba se declara preflight:

```text
Preflight:
- Approval ID: APR-2.1-001
- Acción propuesta: implementar Slice 2.1 según spec aprobada
- CWD: repo TaskNotifier
- Project root: repo TaskNotifier
- Harness path: repo TaskNotifier/.hebrinex
- Binding status: bound
- Read-set:
  - specs/2.1-scheduler/
  - src/TaskNotifier.Core/Models/Task.cs
  - src/TaskNotifier.Worker/Program.cs
- Write-set:
  - src/TaskNotifier.Worker/Scheduling/
  - tests/TaskNotifier.Worker.Tests/Scheduling/
  - src/TaskNotifier.Worker/Program.cs
- Comando/tool:
  - dotnet test --filter "FullyQualifiedName~Scheduling"
- Red/git/externo: no
- External write scope: none
- Riesgo: medio
- Verificación: tests T3/T4 verdes y build limpio
- Evidencia esperada:
  - progress/cycles/C-021/gate-log.yaml
  - progress/cycles/C-021/slice-2.1/verification-matrix.yaml
  - progress/cycles/C-021/slice-2.1/final-report.md
  - progress/cycles/C-021/slice-2.1/agent-closure.md
- Requiere SI: sí
```

El `SI` aprueba solo este envelope. Si cambia el write-set o el comando, el
leader pide un nuevo `SI`.

Artefactos de estado esperados:

```text
progress/state.yaml
progress/registry.yaml
progress/cycles/C-021/audit.jsonl
progress/cycles/C-021/gate-log.yaml
progress/cycles/C-021/slice-2.1/verification-matrix.yaml
progress/cycles/C-021/slice-2.1/final-report.md
progress/cycles/C-021/slice-2.1/agent-closure.md
```

---

## 7 · Ejecución

El Worker abre las specs, lee el modelo `Task`, escribe los archivos
listados en `tasks.md`. Para cada archivo: crea, ejecuta `dotnet build` del
proyecto Worker, ajusta hasta build limpio. Después escribe los tests T3 y
T4, corre la suite filtrada.

Output del Worker al cerrar:

```text
Archivos creados:
  src/TaskNotifier.Worker/Scheduling/OverdueScannerOptions.cs (12 líneas)
  src/TaskNotifier.Worker/Scheduling/OverdueScanner.cs (58 líneas)
  tests/TaskNotifier.Worker.Tests/Scheduling/OverdueScannerTests.cs (97 líneas)

Archivos modificados:
  src/TaskNotifier.Worker/Program.cs (+3 líneas, registración hosted service)

Comando: dotnet test --filter "FullyQualifiedName~Scheduling"
Resultado: 5 tests passed (2 nuevos + 3 sanity adyacentes).

Decisiones de implementación:
  - IClock se inyectó vía IClock interface en TaskNotifier.Core/Time/.
    No estaba en el design.md — registrar como decisión nueva.

Gaps identificados:
  - Gap nuevo: el TaskRepository no tiene método GetOverdue(); usé Where
    inline en el scanner. Aceptable para el slice, pero deja un D-03
    (lógica en handler) que conviene refactorizar.

P0:
  Approval ID: APR-2.1-001
  Write-set declarado: src/TaskNotifier.Worker/Scheduling/,
    tests/TaskNotifier.Worker.Tests/Scheduling/, Program.cs
  Write-set real: coincide
  Evidence ref: progress/cycles/C-021/audit.jsonl
```

---

## 8 · Review

Aplicando [Vol 03 · checklist de cierre](./vol-03-sdd.md):

- [x] R1, R2, R3 con al menos un test cada uno (T3 cubre R1+R2, T4 cubre R3).
- [x] Sin contradicciones spec ↔ código.
- [x] Decisión nueva sobre `IClock` agregada al design.md como apéndice.
- [x] Gap nuevo registrado.

---

## 9 · Registro

`PROGRESS.md` actualizado:

```markdown
| Fase 2 | Notificaciones de tareas vencidas | 🔄 En progreso | — |

### Slices completos
- Slice 2.1 — Scheduler de tareas vencidas. Tests: 5. Cerrado: 2026-05-18.

### Gaps activos
| Gap #7 | Repository sin método GetOverdue (lógica inline) | Identificado | Slice 2.3 |
```

---

## 10 · Cierre del Slice

Comando final ejecutado:

```bash
dotnet test                       # 47 passed (42 + 5)
dotnet build -c Release           # build limpio
```

Controles P0:

```text
G0_session_contract: pass
G1_context_ready: pass
G2_dispatch_ready: pass
G3_locks_acquired: pass
G4_execution_complete: pass
G5_review_or_validation: pass
G6_agent_closure_complete: pass
G7_handoff_complete: pass
```

Archivos de cierre:

```text
progress/cycles/C-021/gate-log.yaml
progress/cycles/C-021/slice-2.1/verification-matrix.yaml
progress/cycles/C-021/slice-2.1/final-report.md
progress/cycles/C-021/slice-2.1/agent-closure.md
```

Si corresponde versionar el cambio, el leader pide un preflight separado para
`git add`, `git commit` o `git push`.

Listo. Siguiente iteración: Slice 2.2.

---

## Lecciones del Recorrido

1. **El paso de aprobación humana cortó el ímpetu** — y eso es lo que hace.
   Sin ese corte, el Worker hubiera empezado a implementar antes de que
   alguien viera la decisión del IClock.

2. **El gap del repository apareció durante la implementación**, no durante
   la spec. Está bien: no toda decisión se ve antes. Se registra y se
   procesa después.

3. **El ownership disjunto evitó pisar Fase 1.** Worker tocó solo lo
   declarado.

4. **El brief operativo cabe en una pantalla.** No hay context dumping. Lo
   que el agente necesita está, lo que no necesita no entra.

5. **El comando de verificación se eligió desde la spec, no después.** Eso
   es lo que la convierte en una spec en vez de una descripción.
