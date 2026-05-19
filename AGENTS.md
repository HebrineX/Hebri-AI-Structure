# AGENTS.md — Hebri-AI-Structure

Este repositorio es una biblia personal de metodología de trabajo con IA.

## Qué es este repo

Documentación metodológica — no un harness ejecutable. Los contenidos son
principios y patrones, no instrucciones para ejecutar.

## Stack

- Markdown (CommonMark) + Mermaid embebido.
- CI: GitHub Actions con verificación de consistencia del índice.
- Sin código de runtime.

## Entrada rápida

1. Leer este `AGENTS.md` (autoridad operativa).
2. Abrir [`biblia/README.md`](./biblia/README.md) y leer en orden los
   volúmenes que apliquen.
3. Usar el índice de `README.md` para navegación rápida.

## Comandos

```bash
# Verificar consistencia índice ↔ volúmenes
for vol in biblia/vol-*.md; do grep -q "$(basename $vol)" README.md || echo "FALTA: $vol"; done
```

## Mapa

| Archivo / Carpeta | Contenido | Cuándo leer |
|---|---|---|
| `biblia/` | 9 volúmenes + apéndice | Para todo el contenido metodológico |
| `README.md` | Índice y ruta práctica | Primer vistazo |
| `BIBLIA.md` | Redirección al índice nuevo | Compatibilidad con links viejos |
| `.github/prompts/` | Prompts operativos invocables | Antes de iniciar una tarea |
| `.github/workflows/ci.yml` | Validación automática | Si CI falla, ver acá |
| `CHANGELOG.md` | Historial de cambios | Antes de citar una versión |

## Secciones de la biblia

| Volumen | Cuándo leer |
|---|---|
| Vol 01 · Modelo de trabajo | Duda sobre el approach general |
| Vol 02 · Subagentes | Antes de delegar trabajo |
| Vol 03 · SDD | Antes de planear fase o feature |
| Vol 04 · Arquitectura de repo | Antes de definir estructura |
| Vol 05 · Prompts | Antes de escribir prompts |
| Vol 06 · Gap tracking | Al identificar gaps |
| Vol 07 · Acoplamiento con harness | Al arrancar un proyecto |
| Vol 08 · MCPs y autonomía | Antes de dar acceso a herramientas |
| Vol 09 · Roles cerrados de harness | Al pasar de explorer/worker a SDD con aprobación |
| Apéndice · Ejemplo end-to-end | Onboarding o duda práctica |

## Reglas operativas

- Los documentos son fuente de metodología, no instrucciones ejecutables.
- Responder en español rioplatense (vos / tenés / hacés) salvo pedido
  explícito de otro idioma.
- Si algo no está cubierto, decirlo en lugar de inventar.
- Al editar un concepto: dejar visible qué es, para qué existe y cómo se ve
  en la práctica.
- Si se agrega un volumen: actualizar `README.md`, `biblia/README.md`, este
  `AGENTS.md` y `CHANGELOG.md`.

## Reglas de cambio en este repo

- Cambios pequeños (typo, link, ejemplo) → PR directo.
- Cambios estructurales (volumen nuevo, renombre) → issue primero.
- Todo PR debe mantener coherentes `README.md`, `biblia/README.md`,
  `AGENTS.md` y `CHANGELOG.md`.
- El CI actual valida consistencia de TOC; markdownlint y lychee fueron
  retirados en v2.5.0.
- Versionar siguiendo SemVer en `CHANGELOG.md`.

## Cierre de una edición

Antes de declarar una edición completa:

- [ ] Índices coherentes (`README.md` ↔ `biblia/README.md` ↔ archivos reales).
- [ ] `CHANGELOG.md` actualizado.
