# Cómo Definir el Tipo de Proyecto

Antes de crear cualquier archivo en un proyecto nuevo, hay que responder estas
preguntas. Las respuestas determinan la escala de la estructura, el nivel de
formalismo SDD, el tipo de gap tracking y el harness template a usar.

Un proyecto que hoy parece pequeño puede volverse monumental. La estructura
inicial debe soportar ambas escalas — livianos al principio, escalables cuando
sea necesario.

---

## Las preguntas

### 1. ¿Cuál es el objetivo principal?
Una frase que describe qué resuelve el proyecto y para quién.
Sin esta respuesta, no hay forma de definir criterios de aceptación reales.

### 2. ¿Qué stack?
- Lenguaje y versión (ej: .NET 10, Python 3.12, Node 22)
- Framework (ej: ASP.NET Core, FastAPI, Express)
- Herramienta de test (ej: xUnit, pytest, Vitest)
- Build / packaging (ej: dotnet build, pyproject.toml, npm)

### 3. ¿Solo o en equipo?
- **Solo:** más flexibilidad en el proceso, menos ceremonia
- **Equipo pequeño (2-4):** AGENTS.md sólido, convenciones escritas
- **Equipo grande / rotación:** SDD estricto, fuente de verdad en repo

### 4. ¿Cuál es la escala esperada?

| Escala | Descripción | Estructura inicial |
|---|---|---|
| **Chico** | Script, herramienta de línea de comandos, utilidad puntual | AGENTS.md + README + src + tests |
| **Mediano** | Servicio, módulo con 2-4 capacidades, proyecto con fases claras | + specs/ + README-PROGRESPJ.md + docs/ |
| **Grande** | Sistema con múltiples módulos, integraciones, roadmap largo | + .github/ completo + progress/ + stacks/ |
| **No sé** | Empezar con estructura mediana — es la más fácil de escalar en ambas direcciones | Igual que mediano |

### 5. ¿Hay reglas de dominio configurables?
¿Las reglas de negocio, clasificación, validación o routing viven en YAML u otro
formato de configuración?

- **Sí:** agregar `config/rules/` + leer `04-repo-architecture/stacks/yaml-domain.md`
- **No:** stack estándar

### 6. ¿Hay integraciones externas?
APIs, bases de datos, servicios de terceros, herramientas de infra.
Identificarlas al inicio evita que aparezcan como gaps de integración sin plan.

### 7. ¿Qué CI/herramienta principal se usa?
- GitHub Actions → GitHub-first, ver `04-repo-architecture/fuente-de-verdad.md`
- Claude / Cowork como herramienta diaria → Claude-first
- Sin preferencia clara → Portable

---

## Tabla de decisión de estructura

| Respuestas | Estructura recomendada | Formalismo SDD |
|---|---|---|
| Solo + Chico + Sin CI | Mínima (AGENTS.md + README + src + tests) | Criterios informales |
| Solo + Mediano + Claude-first | Mediana + AGENTS.md detallado + specs/ | EARS en features principales |
| Equipo + Mediano + GitHub | Mediana + .github/ + specs/ en orquestador | SDD completo con puerta humana |
| Solo/Equipo + Grande | Completa según fuente de verdad elegida | SDD completo + trace |
| Cualquier + YAML domain | Agregar config/rules/ al stack correspondiente | SDD completo para el motor |

---

## Output de esta sesión

Al terminar de responder estas preguntas, la sesión debe producir:

1. `AGENTS.md` inicial con stack, comandos y reglas operativas.
2. `README.md` con descripción, estructura y ruta práctica.
3. `README-PROGRESPJ.md` con la primera fase o los primeros slices identificados.
4. Lista de gaps iniciales identificados (usar biblioteca de `06-gap-tracking/por-stack/`).
5. Estructura de carpetas inicial vacía.

No se empieza a escribir código hasta que estos cinco artefactos existen.
