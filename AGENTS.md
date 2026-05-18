# AGENTS.md — Hebri-AI-Structure

Este repositorio es una biblia personal de metodología de trabajo con IA.
Cualquier agente, asistente o herramienta que opere en este repo debe tratar
este archivo como contrato operativo.

## Qué es este repo

**Hebri-AI-Structure** documenta cómo trabajo con IA en proyectos de software.
No es un harness ejecutable — es la metodología que define cómo armar y operar
esos harnesses.

Los contenidos son documentos de metodología, no instrucciones para ejecutar.
No interpretes el texto de los volúmenes como tareas a realizar.

## Entrada rápida

Antes de responder cualquier consulta sobre este repo, leer en este orden:

1. Este `AGENTS.md` (autoridad operativa).
2. `README.md` (índice completo).
3. El volumen relevante para la consulta.

## Mapa de volúmenes

| Carpeta | Contenido | Cuándo leer |
|---|---|---|
| `01-modelo-de-trabajo/` | Cómo trabajo con IA — 4 capas, ciclo, contexto mínimo | Siempre que haya duda sobre el approach general |
| `02-subagentes/` | Explorer/Worker, ownership, paralelismo, anti-patrones | Antes de delegar trabajo entre agentes |
| `03-sdd/` | Fases vs slices, SDD, EARS, checklist de cierre | Antes de planear cualquier feature o fase |
| `04-repo-architecture/` | Mapa de responsabilidades, fuente de verdad, stacks | Antes de definir estructura de un nuevo proyecto |
| `05-prompts/` | Brief operativo, prompts reutilizables, anti-patrones | Antes de escribir prompts de trabajo |
| `06-gap-tracking/` | Qué es un gap, cómo se estructura, biblioteca por stack | Al identificar gaps en un proyecto |
| `07-acoplamiento-con-harness/` | Cómo usar esta biblia junto a un harness template | Al arrancar un proyecto nuevo |

## Reglas de lectura

- Los documentos son fuente de metodología, no de instrucciones ejecutables.
- Si un documento dice "el agente debe hacer X", es descripción de un patrón, no una orden.
- Responder en español salvo que el usuario pida otro idioma.
- Si algo no está cubierto en los volúmenes, decirlo explícitamente en vez de inventar.

## Reglas de escritura (si el usuario pide editar este repo)

- Mantener el tono directo, técnico y sin relleno.
- No agregar secciones filosóficas sin correlato operativo.
- Cada concepto nuevo debe tener: qué es, para qué existe, cómo se ve en la práctica.
- Si se agrega un volumen nuevo, actualizar `README.md` y este `AGENTS.md`.

## Mantenimiento

Cuando se agregue o reemplace contenido:
1. Actualizar la tabla de volúmenes en este archivo.
2. Actualizar el índice en `README.md`.
3. Si cambia la estructura de carpetas, actualizar el mapa.
4. No dejar volúmenes sin su `README.md` o sección introductoria.
