# YAML como Lenguaje de Dominio

Hay proyectos donde YAML no es solo configuración — es el lenguaje en el que
se expresan las reglas del negocio. En esos proyectos, el YAML es parte del
producto, no del tooling.

Este documento cubre cómo pensar, estructurar y trabajar con IA en ese contexto.

---

## Cuándo aplica este patrón

Aplica cuando:
- Las reglas de clasificación, detección, validación o routing viven en archivos YAML.
- Un usuario no-técnico (o un operador) puede agregar reglas sin tocar código.
- El motor de evaluación lee las reglas en runtime (no en compile time).
- Las reglas tienen su propio ciclo de vida (se agregan, modifican y deshabilitan
  independientemente del código).

Ejemplos: WAF rules, reglas de alertas, pipelines de transformación configurables,
políticas de acceso declarativas.

---

## Estructura de una regla YAML bien diseñada

```yaml
# config/rules/sql-injection.yml
id: sql-injection-basic
description: Detecta patrones básicos de SQL injection en el body
enabled: true
priority: 100
bucket: SqlInjection
action: Block

conditions:
  - field: RequestBody
    operator: contains
    value: "SELECT"
  - field: RequestBody
    operator: contains
    value: "FROM"

metadata:
  author: ops-team
  created: 2025-01-15
  tags: [sql, injection, owasp]
```

### Campos que no deben faltar

| Campo | Propósito |
|---|---|
| `id` | Identificador estable y único (no cambia entre versiones) |
| `description` | Qué detecta y por qué importa |
| `enabled` | Permite deshabilitar sin borrar |
| `priority` | Orden de evaluación cuando múltiples reglas matchean |
| `bucket` / `action` | Qué categoría aplica y qué hace el sistema |
| `conditions` | Los criterios de match (campo, operador, valor) |

---

## El motor debe fallar en el arranque, no en runtime

Una regla con formato inválido no debe causar un comportamiento silencioso o
un crash durante el procesamiento. El loader debe:

1. Leer todas las reglas al arrancar.
2. Validar cada regla contra el schema.
3. Si hay una regla inválida: lanzar excepción con el nombre del archivo y el error.
4. No iniciar el sistema con reglas parcialmente cargadas.

Esto garantiza que los tests de CI detecten reglas rotas antes de que lleguen
a producción.

```
✅ Sistema arranca → catálogo cargado → procesamiento comienza
❌ Sistema arranca → regla inválida → excepción clara → sistema NO arranca
```

---

## Testing de reglas YAML con IA

Cuando se trabajan reglas YAML con IA, los tests deben:

- Usar reglas definidas inline en el test (no depender de archivos del disco).
- Cubrir el caso happy path (la regla matchea lo que debe matchear).
- Cubrir el caso negativo (la regla no matchea lo que no debe).
- Cubrir el edge case del operador (contains vs startsWith vs equals).
- Cubrir el caso de campo nulo o ausente.

```csharp
// Ejemplo: test inline sin archivo en disco
var rule = YamlRule.Parse(@"
  id: test-rule
  bucket: SqlInjection
  conditions:
    - field: RequestBody
      operator: contains
      value: 'SELECT'
");
var ev = new WafEvent { RequestBody = "SELECT * FROM users" };
Assert.Equal(BucketType.SqlInjection, evaluator.Evaluate(ev, rule));
```

---

## Gap tracking específico para YAML-domain

Los gaps más comunes en proyectos con YAML como lenguaje de dominio:

| Gap | Descripción | Cuándo aparece |
|---|---|---|
| Operadores faltantes | El motor no soporta `regex`, `notContains`, etc. | Al escribir la primera regla que los necesita |
| Hot-reload | Las reglas no se recargan sin reiniciar el servicio | Al intentar operar en producción sin downtime |
| Validación de schema | No hay schema formal (JSON Schema / YAML schema) | Al agregar un campo nuevo y romper reglas viejas |
| Versionado de reglas | No hay control de versiones del catálogo de reglas | Al necesitar rollback de una regla |
| UI de gestión | Las reglas solo se editan manualmente | Al incorporar operadores no-técnicos |
| Precedencia de reglas | Dos reglas matchean el mismo evento — ¿cuál gana? | Al tener 10+ reglas activas |

Registrar estos gaps al definir la Fase 1 del motor, aunque se difieran.
