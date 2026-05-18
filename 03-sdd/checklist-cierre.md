# Checklist de Cierre

Una fase o slice no cierra cuando "está listo" — cierra cuando la evidencia
está en el repositorio. Este checklist es el contrato entre la intención y el merge.

---

## Checklist de cierre de slice

```
[ ] El requirement está escrito y tiene ID estable.
[ ] La spec define comportamiento verificable (no ambigüedades).
[ ] El alcance excluye explícitamente lo que no entra.
[ ] Las decisiones relevantes están en design.md.
[ ] Las tasks están partidas en pasos ejecutables con trazabilidad a requirements.
[ ] Los criterios de aceptación son concretos y verificables.
[ ] Cada requirement tiene al menos un test.
[ ] Los tests pasan (comando de verificación ejecutado).
[ ] No hay contradiciones entre spec, código y tests.
[ ] Los gaps nuevos identificados durante el trabajo están registrados.
[ ] El repositorio puede reconstruir el contexto sin depender de memoria oral.
```

---

## Checklist de cierre de fase

Todo lo anterior, más:

```
[ ] Todos los slices de la fase tienen cierre documentado.
[ ] El build corre limpio (sin warnings críticos).
[ ] Los tests de regresión de fases anteriores siguen verdes.
[ ] Los gaps diferidos a la siguiente fase están registrados con motivo.
[ ] El README del proyecto (o README-PROGRESPJ.md) refleja el nuevo estado.
[ ] Si corresponde: tag de versión creado.
[ ] Si corresponde: release notes escritas.
[ ] La siguiente fase tiene al menos un gap o item de roadmap identificado.
```

---

## Formato de evidencia de cierre

Al cerrar una fase, el registro en el repo debe incluir:

```markdown
## Cierre Fase [N] — [nombre]

Fecha: [fecha]
Estado: completa

### Tests
- Suite completa: [N] tests passed, 0 failed
- Comando: `[dotnet test / pytest / etc]`

### Build
- `[dotnet build -c Release / npm run build / etc]` sin errores

### Gaps diferidos
| # | Descripción | Destino |
|---|---|---|
| Gap #X | [descripción] | Fase [N+1] |

### Tag
`v[versión]` — [si corresponde]
```

---

## Señales de que el cierre no está listo

- "Funciona pero no corre los tests todavía."
- "Hay un test roto pero es por otra cosa."
- "El doc lo actualizo después."
- "El gap está en mi cabeza, no lo escribí."
- "El build da warnings pero no errores."

Ninguna de esas frases es evidencia de cierre. Son deuda disfrazada de progreso.
