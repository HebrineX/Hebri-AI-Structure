# Stack — .NET

Convenciones específicas para proyectos .NET trabajados con IA.

---

## Comandos estándar

```bash
# Restaurar dependencias
dotnet restore

# Build
dotnet build
dotnet build -c Release          # para cierre de fase

# Tests
dotnet test
dotnet test --filter "FullyQualifiedName~[NombreClase]"   # subset
dotnet test --filter "FullyQualifiedName~[Fase]"          # por fase

# Ver resultados con detalle
dotnet test --logger "console;verbosity=detailed"
```

---

## Estructura de proyectos recomendada

```
SolucionName/
├── src/
│   ├── SolucionName.Core/           ← modelos, interfaces, contratos
│   ├── SolucionName.Domain/         ← lógica de dominio
│   ├── SolucionName.Analyzers/      ← componentes de análisis
│   ├── SolucionName.Reporters/      ← salida y reportes
│   ├── SolucionName.Sources/        ← fuentes de datos
│   ├── SolucionName.Enrichers/      ← enriquecimiento de datos
│   └── SolucionName.Worker/         ← punto de entrada / host
├── tests/
│   ├── SolucionName.Core.Tests/
│   ├── SolucionName.Analyzers.Tests/
│   ├── SolucionName.Integration.Tests/
│   └── ...
├── config/                          ← configuración (YAML rules, etc.)
├── docs/
└── SolucionName.sln
```

---

## Convenciones de naming

| Tipo | Convención | Ejemplo |
|---|---|---|
| Proyectos | `Solución.Módulo` | `NetworkSentinel.Analyzers` |
| Clases de test | `[Clase]Tests` | `YamlRuleCatalogTests` |
| Métodos de test | `[Cuando]_[Resultado]` | `WhenRuleMissing_ClassifiesAsUnclassified` |
| Interfaces | `I[Nombre]` | `IWafEnricher`, `IYamlRuleOperator` |
| Options | `[Feature]Options` | `FakeWafSourceOptions`, `YamlRulesOptions` |

---

## xUnit — patrones de test

### Test básico
```csharp
[Fact]
public void WhenValidRule_MatchesExpectedBucket()
{
    // Arrange
    var rule = new YamlRule { ... };
    var wafEvent = new WafEvent { ... };

    // Act
    var result = _evaluator.Evaluate(wafEvent, rule);

    // Assert
    Assert.Equal(BucketType.SqlInjection, result);
}
```

### Test parametrizado
```csharp
[Theory]
[InlineData("SELECT * FROM", BucketType.SqlInjection)]
[InlineData("../../../etc/passwd", BucketType.PathTraversal)]
[InlineData("normal request", BucketType.Unclassified)]
public void WhenPayloadMatches_ReturnsCorrectBucket(
    string payload, BucketType expected)
{
    // ...
}
```

### Test de integración con FakeSource
```csharp
// Usar FakeWafSource para tests de pipeline end-to-end
// No mockear el pipeline — usar la fuente fake
services.AddFakeWafSource(opts => opts.Events = testEvents);
```

---

## YAML como lenguaje de dominio en .NET

Cuando las reglas de negocio viven en YAML (no en código), aplican convenciones
adicionales. Ver `yaml-domain.md` para el detalle completo.

Puntos clave para .NET:
- El loader de YAML debe fallar al arrancar si hay reglas inválidas
  (`YamlRules__RequiredAtBoot = true`).
- Los tests del catálogo de reglas deben correr sin necesidad de archivos reales
  (usar reglas inline en el test).
- Los operadores (`equals`, `contains`, `startsWith`, etc.) deben ser extensibles
  sin modificar el motor.

---

## Cierre de fase en .NET

Antes de tagear y pushear una fase:

```powershell
# 1. Tests completos
dotnet test

# 2. Build de release
dotnet build -c Release

# 3. Tag
git add .
git commit -m "Fase [N]: [descripción]"
git tag -a v[versión] -m "Fase [N] — [descripción]"
git push origin main v[versión]
```

El cierre no se considera completo hasta que `dotnet test` y `dotnet build -c Release`
corren sin errores. Los warnings críticos también bloquean el cierre.

---

## Gaps comunes en proyectos .NET

Ver `../../06-gap-tracking/por-stack/dev.md` para la lista de gaps comunes
por tipo de proyecto .NET.
