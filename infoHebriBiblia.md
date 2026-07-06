# infoHebriBiblia

Resumen de la actualizacion de Hebri-AI-Structure para acompañar
Hebri-AI-Harness 0.16.0.

## Version

- Version de la biblia: 3.8.0.
- Referencia operativa: Hebri-AI-Harness 0.16.0.
- Repo hermano: https://github.com/HebrineX/Hebri-AI-Harness

## Cambios principales documentados

- La referencia activa del repo pasa de Harness 0.15.0 a 0.16.0.
- README, indices, AGENTS, BIBLIA, prompts y HARNESS_VERSION quedan alineados.
- Vol 07 incorpora integraciones host, backends MCP de agentes, matriz de
  adapters con madurez y ruta de migracion 0.15.0 -> 0.16.0.
- Vol 08 aclara que role agents nativos y tools MCP no amplian permisos.
- Vol 09 aclara que Claude/Cursor/Copilot reciben derivados o adaptadores, no
  autoridad para definir agentes.
- El apendice operativo pasa a Harness 0.16.0 y suma checklist de 0.15.0 y
  0.16.0.

## Relacion con el harness

Hebri-AI-Structure explica la metodologia. Hebri-AI-Harness implementa el
contrato operativo. Para 0.16.0, la biblia documenta estos puntos como regla:

- el harness define agentes;
- los hosts pueden ejecutar o adaptar agentes, pero no inventarlos;
- `agent_audit` y `agent_review` requieren backend, capability y evidencia;
- `.claude/agents/`, reglas Cursor e instrucciones Copilot son derivados;
- si hay drift entre host y registry, gana el harness.

## Verificacion esperada

- Indices coherentes entre README.md, biblia/README.md y BIBLIA.md.
- CHANGELOG.md con entrada 3.8.0.
- HARNESS_VERSION en 0.16.0.
- Prompts operativos nombran Harness 0.16.0.
- Sin referencias activas pendientes a 0.15.0 como version actual.

## Nota

Este archivo es informativo. No reemplaza el contenido canonico de los
volumenes ni el contrato operativo del harness.
