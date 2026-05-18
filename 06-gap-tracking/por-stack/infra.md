# Biblioteca de Gaps — Infra

Gaps comunes en proyectos de infraestructura: servidores, redes, contenedores,
storage, observabilidad y seguridad operativa.

---

## Contenedores y orquestación

| # | Gap | Cuándo aparece |
|---|---|---|
| I-01 | Sin health check en el contenedor | Al deployar en Kubernetes o ECS |
| I-02 | Sin graceful shutdown — el proceso corta conexiones abruptamente | Al actualizar la imagen en producción |
| I-03 | Imagen sin multi-stage build → imagen demasiado pesada | Al medir el tiempo de pull en CI |
| I-04 | Variables de entorno hardcodeadas en Dockerfile | Al intentar usar la misma imagen en staging y prod |
| I-05 | Sin límites de recursos (CPU/memoria) en el contenedor | Al ver consumo descontrolado en producción |
| I-06 | Sin política de restart definida | Al ver el contenedor crasheando en silencio |

---

## Configuración y secretos

| # | Gap | Cuándo aparece |
|---|---|---|
| I-07 | Secretos en variables de entorno sin rotación | Al tener una fuga de credenciales |
| I-08 | Config sin validación al arrancar | Al deployar con config incompleta y no saberlo |
| I-09 | Sin separación entre config de dev y prod | Al deployar config de desarrollo en producción |
| I-10 | Config hardcodeada en el código fuente | Al querer cambiar algo sin recompilar |

---

## Observabilidad

| # | Gap | Cuándo aparece |
|---|---|---|
| I-11 | Sin logs estructurados (solo texto plano) | Al intentar hacer queries sobre los logs |
| I-12 | Sin trazas distribuidas entre servicios | Al debuggear un problema que cruza dos servicios |
| I-13 | Sin métricas de negocio (solo métricas de infraestructura) | Al querer saber cuántos eventos se clasifican por minuto |
| I-14 | Alertas configuradas sobre síntomas, no sobre causas | Al recibir alertas ruidosas que no llevan a acción |
| I-15 | Sin runbook para las alertas más comunes | Al recibir una alerta y no saber qué hacer |

---

## Redes y acceso

| # | Gap | Cuándo aparece |
|---|---|---|
| I-16 | Sin segmentación de red entre servicios internos y externos | Al revisar el modelo de seguridad |
| I-17 | Puertos innecesarios expuestos en el firewall | Al hacer una auditoría de superficie de ataque |
| I-18 | Sin control de acceso basado en roles (RBAC) para infra | Al incorporar una persona nueva al equipo |
| I-19 | Certificados TLS sin rotación automática | Al tener un corte por cert expirado |

---

## Backup y recuperación

| # | Gap | Cuándo aparece |
|---|---|---|
| I-20 | Sin estrategia de backup definida | Al tener pérdida de datos |
| I-21 | Backups no testeados (no se sabe si restauran) | Al necesitar restaurar por primera vez |
| I-22 | Sin RTO/RPO definidos | Al discutir cuánto downtime es aceptable |
| I-23 | Sin runbook de disaster recovery | Al enfrentar un incidente serio |
