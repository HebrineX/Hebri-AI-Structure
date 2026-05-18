# Vol 06 · Gap Tracking

> Anterior: [Vol 05 · Prompts](./vol-05-prompts.md) · Siguiente: [Vol 07 · Harness](./vol-07-harness.md)

## Qué es un Gap

Un gap es cualquier cosa que sabés que falta, está incompleta, o decidiste
no hacer todavía — pero que quedó registrada explícitamente para no perder
el hilo.

**La diferencia entre gap y tarea:**

| | Gap | Tarea |
|---|---|---|
| Qué es | Algo que falta, aún no listo para especificar | Algo concreto con spec aprobada |
| Cuándo aparece | Durante análisis o cierre de fase | Cuando el gap tiene spec aprobada |
| Tiene fecha/owner | No necesariamente | Sí |

Un gap nunca desaparece en silencio. Si se resuelve, se marca con referencia
a qué lo cerró. Si se descarta, se marca con motivo.

---

## Estructura de un Gap

```markdown
## Gap #[N] — [título corto]

**Estado:** [Identificado | Diferido a Fase X | En análisis | Resuelto | Descartado]
**Capa:** [Dev | Infra | DevOps | Docs | Testing | Security]

**Descripción:** [Qué falta o está incompleto]

**Contexto:** [Por qué existe este gap]

**Motivo de diferimiento:** [Por qué no se resuelve ahora]

**Destino:** Fase [N] / Sin asignar

**Resuelto por:** [PR / Commit / Tarea — si aplica]
```

Los gaps del proyecto viven en `PROGRESS.md` o en `docs/gaps.md` para
proyectos con muchos gaps. Esta biblia tiene la biblioteca de gaps comunes
por capa como referencia al arrancar.

---

## Biblioteca de Gaps — Dev

| # | Gap | Cuándo aparece |
|---|---|---|
| D-01 | Interfaces públicas no estabilizadas | Al intentar testear por primera vez |
| D-02 | Contratos entre módulos no definidos | Al conectar dos módulos |
| D-03 | Lógica de negocio en el handler/controller | Al querer reusar la lógica |
| N-01 | Options classes sin validación en startup (.NET) | Al deployar con config incompleta |
| N-02 | Build de release da warnings no vistos en debug (.NET) | Al correr `dotnet build -c Release` |
| P-01 | Type annotations incompletas en interfaces públicas (Python) | Al integrar con otro módulo |
| P-02 | requirements.txt sin versiones pinneadas (Python) | Al instalar en un entorno nuevo |
| Y-01 | Operadores faltantes en motor YAML (regex, notContains) | Al escribir la primera regla que los necesita |
| Y-02 | Sin validación de schema al cargar reglas YAML | Al agregar un campo nuevo y romper reglas viejas |
| Y-03 | Sin hot-reload de reglas sin reinicio | Al actualizar reglas en producción |

---

## Biblioteca de Gaps — Testing

| # | Gap | Cuándo aparece |
|---|---|---|
| T-01 | Tests que dependen de archivos en disco en lugar de fixtures | Al correr en CI sin archivos |
| T-02 | No hay tests de casos borde (null, vacío, límite) | Al producir la primera regresión |
| T-03 | Suite sin naming convention — difícil filtrar por área | Al querer correr solo tests de una feature |
| T-04 | Sin tests de regresión para bugs corregidos | Al volver a reportarse el mismo bug |
| T-05 | Tests lentos sin separación unit/integration | Al esperar 15 min por feedback en cada PR |
| T-06 | Cobertura no medida o medida sin criterio | Al no saber qué quedó sin tocar |
| T-07 | Tests flaky tolerados | Al no confiar en el verde del CI |
| T-08 | Sin tests de contrato entre servicios | Al romper un consumidor sin saberlo |
| T-09 | Mocks que mienten (no representan el comportamiento real) | Al pasar tests pero fallar en integración |

---

## Biblioteca de Gaps — Docs

| # | Gap | Cuándo aparece |
|---|---|---|
| Doc-01 | AGENTS.md sin comandos concretos de verificación | Al retomar el trabajo con un agente |
| Doc-02 | Decisiones de arquitectura no documentadas (sin ADR) | Al querer cambiar algo y no recordar por qué está así |
| Doc-03 | README desactualizado respecto al código real | Al onboardear a alguien |
| Doc-04 | Sin documentación de troubleshooting / runbook | Al recibir una alerta sin saber qué hacer |
| Doc-05 | Sin diagrama de arquitectura de alto nivel | Al explicar el sistema a alguien externo |
| Doc-06 | Specs aprobadas pero no archivadas en `specs/done/` | Al perder rastro de qué cambió y por qué |
| Doc-07 | Changelog inexistente o ad-hoc | Al no poder citar una versión |

---

## Biblioteca de Gaps — Security/Compliance

| # | Gap | Cuándo aparece |
|---|---|---|
| S-01 | Secretos en repo (incluso en archivos viejos) | Al hacer auditoría o scan |
| S-02 | Sin rotación de credenciales documentada | Al irse alguien del equipo |
| S-03 | Permisos de tokens/CI demasiado amplios | Al revisar mínimo privilegio |
| S-04 | Dependencias sin escaneo (CVE) | Al recibir el primer aviso de vulnerabilidad |
| S-05 | Sin validación de entrada en endpoints públicos | Al recibir el primer payload malformado en prod |
| S-06 | Logs con datos sensibles (tokens, PII) | Al revisar logs para debug |
| S-07 | Sin política de retención de datos | Al recibir un pedido de borrado bajo GDPR |
| S-08 | Modelo de amenazas no escrito | Al diseñar una feature nueva sin saber qué proteger |
| S-09 | Sin SBOM (software bill of materials) | Al tener que reportar componentes ante un cliente |

---

## Biblioteca de Gaps — Infra

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

## Biblioteca de Gaps — DevOps

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

## PROGRESS.md — Template

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
