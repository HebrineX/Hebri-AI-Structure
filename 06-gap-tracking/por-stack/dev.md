# Biblioteca de Gaps — Dev

Gaps comunes en proyectos de desarrollo de software. Usar como punto de partida
al iniciar un proyecto nuevo — identificar cuáles aplican y registrarlos desde
el principio en lugar de descubrirlos durante el trabajo.

---

## Arquitectura y diseño

| # | Gap | Cuándo aparece |
|---|---|---|
| D-01 | Interfaces públicas no estabilizadas | Al intentar testear por primera vez |
| D-02 | Contratos entre módulos no definidos | Al conectar dos módulos |
| D-03 | Separación Core/Domain/Infrastructure no respetada | Al agregar una segunda feature |
| D-04 | Lógica de negocio en el handler/controller | Al querer reusar la lógica |
| D-05 | Modelo de datos demasiado acoplado a la persistencia | Al cambiar el repositorio |

---

## Testing

| # | Gap | Cuándo aparece |
|---|---|---|
| T-01 | Tests que dependen de archivos en disco en lugar de fixtures | Al correr en CI sin archivos |
| T-02 | Tests que mockean todo excepto lo que se quiere probar | Al buscar el bug y no encontrarlo |
| T-03 | No hay tests de casos borde (null, vacío, límite) | Al producir la primera regresión |
| T-04 | Tests de integración que dependen de servicios externos reales | Al correr en CI aislado |
| T-05 | Suite de tests sin naming convention → difícil filtrar por área | Al querer correr solo los tests de una feature |
| T-06 | No hay tests de pipeline end-to-end | Al no saber si los módulos se conectan bien |

---

## .NET específico

| # | Gap | Cuándo aparece |
|---|---|---|
| N-01 | Interfaces registradas en DI pero no testeadas via DI | Al tener tests verdes pero runtime roto |
| N-02 | Options classes sin validación en startup | Al deployar con config incompleta |
| N-03 | Worker hospedado sin graceful shutdown | Al deployar en contenedor |
| N-04 | Logs estructurados no configurados | Al intentar debuggear en producción |
| N-05 | Build de release da warnings no vistos en debug | Al correr `dotnet build -c Release` |

---

## Python específico

| # | Gap | Cuándo aparece |
|---|---|---|
| P-01 | Type annotations incompletas en interfaces públicas | Al integrar con otro módulo |
| P-02 | Imports circulares entre módulos de dominio | Al crecer el proyecto |
| P-03 | requirements.txt sin versiones pinneadas | Al instalar en un entorno nuevo |
| P-04 | Fixtures de pytest no centralizadas en conftest.py | Al duplicar setup en múltiples test files |
| P-05 | Scripts que asumen cwd = raíz del proyecto | Al correr desde otro directorio |

---

## YAML como lenguaje de dominio

| # | Gap | Cuándo aparece |
|---|---|---|
| Y-01 | Operadores faltantes (regex, notContains, etc.) | Al escribir la primera regla que los necesita |
| Y-02 | Sin validación de schema al cargar reglas | Al agregar un campo nuevo y romper reglas viejas |
| Y-03 | Hot-reload de reglas sin reinicio del servicio | Al necesitar actualizar reglas en producción |
| Y-04 | Sin versionado del catálogo de reglas | Al necesitar rollback de una regla problemática |
| Y-05 | Precedencia de reglas no definida cuando varias matchean | Al tener 10+ reglas activas |
| Y-06 | Sin UI de gestión para operadores no-técnicos | Al incorporar operadores que editan YAML |

---

## Documentación

| # | Gap | Cuándo aparece |
|---|---|---|
| Doc-01 | AGENTS.md sin comandos concretos de verificación | Al intentar retomar el trabajo con un agente |
| Doc-02 | README sin ejemplo de uso rápido (quick start) | Al incorporar una persona nueva |
| Doc-03 | Decisiones de arquitectura no documentadas (sin ADR) | Al querer cambiar algo y no recordar por qué está así |
| Doc-04 | Guía de autoría de reglas/configuración no escrita | Al delegar la escritura de reglas a otra persona |
| Doc-05 | Changelog o historial de fases sin cerrar formalmente | Al intentar reconstruir el estado del sistema |
