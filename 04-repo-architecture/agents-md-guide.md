# Cómo Escribir un Buen AGENTS.md

AGENTS.md es el mapa operativo para agentes. No es una landing page ni un manual
para humanos. Su trabajo es decirle a un agente: qué leer, en qué orden, y qué
reglas no puede romper.

Un AGENTS.md largo puede sonar completo y aun así fallar porque el agente no sabe
qué hacer primero. Un mapa corto con rutas correctas es más efectivo que un
documento exhaustivo sin prioridades.

---

## Qué debe responder

1. Cuál es el stack (lenguaje, framework, herramientas de test).
2. Dónde está cada cosa importante (rutas, no descripciones vagas).
3. Qué comandos se usan para instalar, probar, validar y correr.
4. Qué archivos o carpetas son delicados o están fuera de alcance.
5. Qué estilo de cambios se espera.
6. Qué permisos o acciones están prohibidas.
7. Cómo cerrar una tarea (qué evidencia se espera).

---

## Template base

```markdown
# AGENTS.md

## Stack
- Lenguaje: [...]
- Framework: [...]
- Tests: [...]
- Build: [...]

## Comandos
- Instalar: `[comando]`
- Tests: `[comando]`
- Build: `[comando]`
- Validar todo: `[comando]`

## Mapa del repo
| Ruta | Contenido | Cuándo leer |
|---|---|---|
| `specs/` | Requirements, design, tasks | Antes de implementar |
| `README-PROGRESPJ.md` | Estado de fases y gaps | Siempre al arrancar |
| `docs/` | Arquitectura y convenciones | Antes de cambios estructurales |
| `progress/current.md` | Estado vivo de sesión | Siempre |

## Reglas operativas
- Mantener cambios acotados al pedido.
- No tocar código antes de que haya spec aprobada.
- No modificar [archivos delicados] sin permiso explícito.
- No declarar done sin tests verdes y build limpio.
- Si un comando falla, reportar el error exacto antes de continuar.

## Cierre de tarea
Antes de declarar una tarea completada, reportar:
- Archivos modificados (con rutas).
- Comando de verificación ejecutado + resultado.
- Tests ejecutados y resultado.
- Gaps nuevos identificados, si los hay.
- Riesgos residuales, si los hay.
```

---

## El error más común

Escribir un AGENTS.md filosófico. Frases como "usar buenas prácticas" o
"considerar el impacto en el sistema" no son reglas — son ruido. Un agente
necesita comandos, rutas, límites y criterios concretos.

**Malo:**
```markdown
## Principios
- Escribir código limpio y mantenible.
- Siempre considerar el impacto en el resto del sistema.
- Usar buenas prácticas de testing.
```

**Bueno:**
```markdown
## Reglas operativas
- Ownership exclusivo: no tocar archivos fuera del scope definido en la tarea.
- No modificar interfaces públicas sin spec aprobada.
- Cada nuevo comportamiento tiene al menos un test.
- Comando de verificación: `dotnet test` — todos los tests deben pasar.
```

---

## Jerarquía de instrucciones

Las instrucciones tienen precedencia. En orden de mayor a menor autoridad:

1. Instrucciones del sistema / plataforma.
2. Pedido explícito del usuario en la conversación.
3. `AGENTS.md` más cercano al archivo que se está tocando.
4. `AGENTS.md` raíz del repo.
5. Documentación auxiliar (`docs/`, `specs/`).

En monorepos, un `AGENTS.md` dentro de un submódulo puede especializar
(no contradecir) las reglas del `AGENTS.md` raíz.

---

## Cuándo actualizar AGENTS.md

- Cuando cambia el stack o los comandos de verificación.
- Cuando se agrega una carpeta nueva que los agentes deben conocer.
- Cuando aparece una regla nueva que se repitió más de una vez en conversaciones.
- Cuando una sesión terminó con un malentendido que hubiera evitado una regla clara.
