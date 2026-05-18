# Biblioteca de Gaps — DevOps

Gaps comunes en pipelines de CI/CD, automatización, releases y procesos de entrega.

---

## CI — Continuous Integration

| # | Gap | Cuándo aparece |
|---|---|---|
| DO-01 | CI no corre en PRs — solo en main | Al mergear un PR roto |
| DO-02 | CI corre tests pero no build de release | Al tener tests verdes y binario roto |
| DO-03 | CI sin caché de dependencias → builds lentos | Al esperar 10 minutos por `npm install` o `dotnet restore` |
| DO-04 | CI sin separación de etapas (lint / test / build / deploy) | Al querer saber exactamente qué falló |
| DO-05 | Tests de integración en CI sin aislamiento | Al tener tests que se pisan entre corridas paralelas |
| DO-06 | CI sin reporte de cobertura | Al no saber qué porcentaje del código se testea |
| DO-07 | Sin gate de calidad mínimo (tests N%, lint clean) | Al mergear código que baja la calidad sin saberlo |

---

## CD — Continuous Delivery / Deployment

| # | Gap | Cuándo aparece |
|---|---|---|
| DO-08 | Deploy manual sin script reproducible | Al no poder reproducir un deploy anterior |
| DO-09 | Sin ambientes separados (dev / staging / prod) | Al testear en producción sin querer |
| DO-10 | Sin smoke test post-deploy | Al saber que el deploy fue bien recién cuando el usuario reporta |
| DO-11 | Sin rollback automático o documentado | Al tener un deploy roto en producción |
| DO-12 | Deploy sin notificación al equipo | Al enterarse del deploy por otro canal |

---

## Releases y versionado

| # | Gap | Cuándo aparece |
|---|---|---|
| DO-13 | Sin política de versionado definida | Al tener `v1.0.0`, `v1.0.1-fix`, `release-final2` en el mismo repo |
| DO-14 | Tags sin release notes | Al querer saber qué cambió entre dos versiones |
| DO-15 | CHANGELOG no automatizado o no mantenido | Al querer comunicar cambios a stakeholders |
| DO-16 | Sin tag de "última versión estable" | Al querer referenciar la versión productiva |

---

## Seguridad en el pipeline

| # | Gap | Cuándo aparece |
|---|---|---|
| DO-17 | Secretos hardcodeados en los workflows de CI | Al hacer un security audit |
| DO-18 | Sin escaneo de dependencias vulnerables (SAST/SCA) | Al recibir una CVE que ya estaba en el repo |
| DO-19 | Permisos de CI demasiado amplios (write access innecesario) | Al revisar el principio de mínimo privilegio |
| DO-20 | Sin firma de artefactos de release | Al necesitar verificar integridad de un binario |

---

## Documentación del pipeline

| # | Gap | Cuándo aparece |
|---|---|---|
| DO-21 | Sin diagrama del flujo de CI/CD | Al incorporar una persona nueva al equipo |
| DO-22 | Workflows con nombres genéricos (ci.yml, build.yml) | Al tener 5 workflows y no saber qué hace cada uno |
| DO-23 | Sin runbook de "¿qué hago si el CI falla?" | Al tener el CI roto y no saber por dónde empezar |
| DO-24 | Pipeline sin owner definido | Al necesitar actualizar un workflow y no saber a quién consultar |
