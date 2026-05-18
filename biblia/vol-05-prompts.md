# Vol 05 · Prompts

> Anterior: [Vol 04 · Arquitectura de repo](./vol-04-arquitectura-repo.md) · Siguiente: [Vol 06 · Gap tracking](./vol-06-gap-tracking.md)

## Brief Operativo

El brief operativo es el formato estándar para pedirle trabajo a un agente.
Es un contrato de tarea, no una pregunta abierta.

```text
Objetivo:
  [una frase verificable]

Contexto:
  Stack: [lenguaje, framework, versión]
  Estado actual: [qué existe hoy]
  Decisiones relevantes: [referencias si aplica]

Restricciones:
  - No tocar [archivo o carpeta].
  - No modificar [interfaz o contrato].

Archivos relevantes:
  - [ruta] — [para qué sirve en esta tarea]

Salida esperada:
  - [artefacto: qué es, dónde vive]

Verificación:
  [comando concreto]

Riesgos:
  - [qué podría romperse si se improvisa]
```

---

## Anti-patrones de Prompts

1. **El prompt filosófico** — "Ayudame a mejorar la arquitectura". Sin
   criterio de salida, el agente elige qué "mejorar" significa.

2. **Sin restricciones** — Define qué hacer pero no qué no hacer. El agente
   toca lo que necesite "para que funcione".

3. **Sin verificación** — Define qué producir pero no cómo saber si está
   correcto. Los tests pueden pasar sin cubrir los casos relevantes.

4. **El prompt de "arreglá todo"** — Combina exploración con implementación
   sin punto de control humano. Separar siempre en Explorer primero, Worker
   después.

5. **Sin contexto de stack** — El agente produce una solución genérica que
   no respeta las convenciones del proyecto.

6. **El prompt acumulado** — Pegar todo el historial de conversaciones
   anteriores como contexto. El agente mezcla decisiones ya revertidas con
   el pedido actual. El contexto se prepara desde los archivos del repo, no
   desde el chat.

---

## Portabilidad entre Herramientas

Los prompts pre-cargados de este repo usan la sintaxis `${input:nombre:hint}`
de GitHub Copilot / VS Code. Para otras herramientas:

| Herramienta | Equivalente |
|---|---|
| GitHub Copilot | `${input:nombre:hint}` |
| Claude Code (slash) | Reemplazar por placeholders `<nombre>` y pedir al user |
| Cursor / Cline | Texto plano con marcas `[NOMBRE]` |
| Modelo directo (API) | Variables del template |

**Regla:** El contenido semántico del prompt no cambia. Solo cambia el
mecanismo de interpolación. Si tu equipo usa varias herramientas, mantener
el contenido y duplicar el envoltorio.
