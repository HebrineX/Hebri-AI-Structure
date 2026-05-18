# Fuente de Verdad

El problema de trabajar con múltiples herramientas (GitHub, Claude, Copilot, CI)
es que cada una tiene su propia capa de configuración e instrucciones. Si no se
define explícitamente dónde vive la fuente de verdad, el contexto se duplica
y las capas entran en contradicción.

---

## El principio

> La fuente de verdad es **una sola carpeta**. Las demás capas son conectores
> que apuntan a esa fuente — nunca la reemplazan ni la duplican.

```
FUENTE DE VERDAD
   specs/              ← una sola, siempre
      ├── fase-1/
      │     requirements.md
      │     design.md
      │     tasks.md
      └── fase-2/

CONECTORES (apuntan a la fuente, no la duplican)
   .github/
      workflows/ci.yml       → ejecuta dotnet test / pytest
      prompts/plan.prompt.md → dice "leer specs/<feature>/"
   AGENTS.md                 → dice "antes de implementar: leer specs/"
   .claude/settings.json     → configuración de herramienta, no de specs
```

---

## Qué significa en la práctica

**El CI no copia specs.** Lee el mismo `specs/` que el agente.

**El agente no tiene su propia copia de requirements.** Lee el mismo `specs/`
que el equipo humano.

**Cambiar de herramienta no mueve la verdad.** Si mañana se cambia de GitHub
Actions a otro CI, el cambio afecta el conector (el yml) — no las specs.
Si se cambia de Claude a Copilot, el cambio afecta las instrucciones del agente
— no la fuente de verdad.

---

## Cómo elegir la carpeta fuente

La carpeta fuente de verdad es la que el equipo usa como referencia al discutir
el trabajo. Usualmente:

- `specs/` para proyectos que arrancan portable o Claude-first.
- `.github/orquestador/sdd/` para proyectos GitHub-first con equipo que vive en PRs.

Lo que no se recomienda es tener las dos carpetas con contenido diferente.
Si se migra de una a otra, se hace la migración completa y se deja una nota de
transición en la carpeta vieja.

---

## GitHub-first vs Claude-first vs Portable

Tres configuraciones comunes. La diferencia está en dónde vive el conector principal,
no en dónde viven las specs.

### Portable
```
specs/          ← fuente de verdad
AGENTS.md       ← conector para agentes
.github/workflows/ci.yml   ← conector para CI
```
Cuándo: equipo pequeño, herramienta de IA variable, sin dependencia fuerte de GitHub.

### GitHub-first
```
.github/orquestador/sdd/   ← fuente de verdad (dentro de GitHub)
.github/prompts/           ← conector para Copilot
.github/workflows/         ← conector para CI
AGENTS.md                  ← apunta a .github/orquestador/sdd/
```
Cuándo: el equipo vive en issues, PRs y code reviews. GitHub es la superficie central.

### Claude-first
```
specs/          ← fuente de verdad
AGENTS.md       ← conector principal
.claude/        ← configuración de herramienta (no duplica specs)
.github/workflows/ci.yml   ← conector para CI
```
Cuándo: Claude es la herramienta de trabajo diario. GitHub es source control + CI.

---

## Árbol de decisión

```
¿El trabajo se revisa y bloquea en PRs con equipo?
├── Sí → GitHub-first
└── No → ¿La herramienta diaria es Claude / Copilot / agente local?
    ├── Claude → Claude-first (specs/ + AGENTS.md + .claude/)
    ├── Copilot → GitHub-first con .github/copilot-instructions.md
    └── Indiferente → Portable (specs/ + AGENTS.md)
```

---

## Regla de migración

Si en algún momento la fuente de verdad cambia de carpeta:
1. Mover el contenido completo.
2. Dejar un archivo `MOVED.md` en la carpeta vieja con la nueva ruta.
3. Actualizar `AGENTS.md` para que apunte a la nueva ubicación.
4. No mantener dos carpetas activas con contenido diferente.
