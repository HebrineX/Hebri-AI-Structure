---
description: "Cerrar formalmente una fase con evidencia"
---

Voy a cerrar la Fase ${input:numero:Número de fase}.

Verificar y reportar:
1. ¿Todos los tests pasan? → correr el comando de verificación y mostrar resultado.
2. ¿El build de release corre limpio?
3. ¿Todos los requirements tienen al menos un test?
4. ¿Los gaps diferidos están registrados en PROGRESS.md con motivo?
5. ¿La documentación refleja el estado actual?
6. ¿El tag de versión está creado (si corresponde)?
7. Si el proyecto usa Hebri-AI-Harness 0.8.8:
   - Validar `session-pin.md`, `memory-registry.yaml`, `memory-routing.yaml`
     y `context-budget.yaml`.
   - `PROJECT_BINDING.yaml` está en `bound` y coincide con el proyecto.
   - `state.yaml` y `registry.yaml` están coherentes.
   - Existe `gate-log.yaml`.
   - Existe `verification-matrix.yaml`.
   - Existe `final-report.md`.
   - Existe cierre de memoria (`memory-closure-checklist.md` o equivalente).
   - Existe cierre de agentes (`agent-closure.md` o equivalente).
   - `G5I_memory_consistency_complete` está en `pass`.
   - `G6_agent_closure_complete` está en `pass`.
   - No quedan locks ni agentes abiertos.

Producir:
- Resumen de cierre con evidencia concreta (N tests passed, build OK).
- Lista de gaps diferidos a la siguiente fase.
- Próximos pasos sugeridos.
- En Harness 0.8.8, no declarar `done` si falta binding válido, presupuesto
  respetado o cualquier artefacto P0.
