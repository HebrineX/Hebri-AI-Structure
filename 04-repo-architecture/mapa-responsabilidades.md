# Mapa de Responsabilidades

Cada archivo en un repositorio tiene una audiencia y una función. Cuando esas
funciones se mezclan, los agentes trabajan con contexto incompleto y el equipo
corrige los mismos errores una y otra vez.

Este mapa define qué archivo hace qué — y qué no hace.

---

## Mapa completo

| Archivo / Carpeta | Audiencia principal | Responsabilidad | NO es para |
|---|---|---|---|
| `README.md` | Personas nuevas | Presentar el proyecto, instalación y uso rápido | Detalle de arquitectura, reglas de agente |
| `AGENTS.md` | Agentes de código | Reglas operativas, mapa de rutas, comandos, permisos, cierre | Landing page, visión de producto |
| `README-PROGRESPJ.md` | Equipo + agentes | Estado de fases/slices, gaps activos, roadmap | Documentación técnica del sistema |
| `.github/copilot-instructions.md` | GitHub Copilot | Instrucciones generales persistentes de estilo y contexto | Repetir todo lo que ya está en AGENTS.md |
| `.github/instructions/*.instructions.md` | Copilot por zona | Reglas específicas aplicadas a paths concretos | Reglas globales |
| `.github/prompts/*.prompt.md` | Equipo y asistentes | Comandos reutilizables para tareas que se repiten | Documentación del sistema |
| `.github/workflows/*.yml` | CI/CD | Automatización ejecutable: tests, build, validaciones | Documentar cosas — solo ejecutar |
| `specs/<feature>/` | Equipo + agentes | Requirements, design, tasks de una feature/fase | Código, documentación de usuario |
| `docs/` | Equipo | Arquitectura, convenciones, guías técnicas | Estado del proyecto, specs activas |
| `progress/current.md` | Agentes | Estado vivo de la sesión activa | Historial permanente |
| `progress/history/` | Equipo | Historial de sesiones cerradas | Estado vivo |

---

## La regla de oro

> Un archivo de contexto no finge que ejecuta. Un workflow ejecuta.
> Un catálogo documenta. Un prompt orienta. Un AGENTS.md gobierna.

Mantener esa separación evita sistemas confusos donde nadie sabe qué es fuente
de verdad y qué es documentación.

---

## AGENTS.md vs README.md — la diferencia concreta

| Pregunta | README.md | AGENTS.md |
|---|---|---|
| ¿Qué hace este proyecto? | ✅ | ✗ |
| ¿Cómo se instala? | ✅ | Solo si el agente lo necesita para verificar |
| ¿Qué archivos puede tocar el agente? | ✗ | ✅ |
| ¿Qué comandos corren los tests? | ✗ | ✅ |
| ¿Qué está prohibido tocar? | ✗ | ✅ |
| ¿Cómo se cierra una tarea? | ✗ | ✅ |
| ¿Cuál es el roadmap? | ✅ (resumen) | ✗ |

---

## Estructura mínima por tipo de proyecto

### Proyecto simple (script / herramienta pequeña)
```
repo/
├── AGENTS.md
├── README.md
├── src/
├── tests/
└── .github/
    └── workflows/ci.yml
```

### Proyecto mediano (servicio / módulo con fases)
```
repo/
├── AGENTS.md
├── README.md
├── README-PROGRESPJ.md
├── src/
├── tests/
├── docs/
│   └── architecture.md
├── specs/
│   └── <feature>/
│       ├── requirements.md
│       ├── design.md
│       └── tasks.md
└── .github/
    ├── copilot-instructions.md
    ├── prompts/
    └── workflows/
```

### Proyecto grande (sistema con múltiples módulos y roadmap largo)
```
repo/
├── AGENTS.md
├── README.md
├── README-PROGRESPJ.md
├── src/
│   ├── Module.Core/
│   ├── Module.Domain/
│   └── Module.Worker/
├── tests/
├── docs/
│   ├── architecture.md
│   ├── conventions.md
│   └── stacks/
├── specs/
│   ├── fase-1/
│   └── fase-2/
├── progress/
│   ├── current.md
│   └── history/
└── .github/
    ├── copilot-instructions.md
    ├── instructions/
    ├── prompts/
    └── workflows/
```

La escala se elige al arrancar el proyecto.
Ver `../07-acoplamiento-con-harness/como-definir-tipo-de-proyecto.md`.
