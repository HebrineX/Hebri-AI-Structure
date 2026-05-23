# Copilot Instructions — Hebri-AI-Structure

Este repositorio es una biblia de metodología de trabajo con IA, no un
proyecto de software con código ejecutable.

## Qué es esto

Documentación metodológica organizada en 9 volúmenes más apéndices, en
la carpeta `biblia/`. Los archivos son Markdown con diagramas Mermaid
embebidos. No hay código fuente, tests ni builds.

## Cómo responder consultas sobre este repo

- Leer primero `AGENTS.md` raíz para entender el propósito.
- Usar `biblia/README.md` o el índice de `README.md` para navegar.
- Responder en español rioplatense salvo pedido explícito de otro idioma.
- Citar el archivo y sección específica al hacer referencia a un concepto.
  Ejemplo: "según `biblia/vol-03-sdd.md` § Fases vs Slices".

## Estilo de respuesta

- Directo y técnico — sin relleno.
- Si algo no está cubierto en los documentos, decirlo explícitamente.
- No inventar convenciones no documentadas en este repo.

## Reglas al editar

- Cada concepto: qué es, para qué existe, cómo se ve en la práctica.
- Sin verbos blandos ("intentar", "considerar", "tratar de").
- Si se agrega un volumen, actualizar `README.md`, `biblia/README.md`,
  `AGENTS.md` y `CHANGELOG.md` en el mismo PR.
- El CI actual valida consistencia de índices. Markdownlint y link checker
  fueron retirados en v2.5.0.
