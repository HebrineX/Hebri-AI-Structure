# Stack — Python

Convenciones específicas para proyectos Python trabajados con IA.

---

## Comandos estándar

```bash
# Entorno virtual
python -m venv .venv
source .venv/bin/activate       # Linux/macOS
.venv\Scripts\activate          # Windows

# Dependencias
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Tests
pytest
pytest -k "test_nombre"                    # subset por nombre
pytest -k "fase1 or clasificacion"         # subset por tag/keyword
pytest --tb=short                          # traceback resumido
pytest -v                                  # verbose

# Build / distribución
python -m build
```

---

## Estructura de proyectos recomendada

```
proyecto/
├── src/
│   └── proyecto/
│       ├── __init__.py
│       ├── core/             ← modelos, interfaces, tipos
│       ├── domain/           ← lógica de negocio
│       ├── analyzers/        ← componentes de análisis
│       ├── reporters/        ← salida y reportes
│       └── worker.py         ← punto de entrada
├── tests/
│   ├── unit/
│   ├── integration/
│   └── conftest.py
├── config/                   ← YAML rules, settings
├── docs/
├── requirements.txt
├── requirements-dev.txt
└── pyproject.toml
```

---

## Convenciones de naming

| Tipo | Convención | Ejemplo |
|---|---|---|
| Módulos | `snake_case` | `yaml_rule_catalog.py` |
| Clases | `PascalCase` | `YamlRuleCatalog`, `WafEvent` |
| Tests | `test_[cuando]_[resultado]` | `test_when_rule_missing_classifies_as_unclassified` |
| Fixtures (pytest) | `snake_case` en `conftest.py` | `sample_waf_event`, `yaml_rule_catalog` |

---

## pytest — patrones de test

### Test básico
```python
def test_when_valid_rule_matches_expected_bucket(evaluator, sample_event):
    rule = YamlRule(
        id="test-rule",
        bucket=BucketType.SQL_INJECTION,
        conditions=[{"field": "body", "operator": "contains", "value": "SELECT"}]
    )
    result = evaluator.evaluate(sample_event, rule)
    assert result == BucketType.SQL_INJECTION
```

### Test parametrizado
```python
@pytest.mark.parametrize("payload,expected", [
    ("SELECT * FROM users", BucketType.SQL_INJECTION),
    ("../../../etc/passwd", BucketType.PATH_TRAVERSAL),
    ("normal request", BucketType.UNCLASSIFIED),
])
def test_when_payload_matches_returns_correct_bucket(evaluator, payload, expected):
    event = WafEvent(body=payload)
    assert evaluator.evaluate(event) == expected
```

### Fixture en conftest.py
```python
@pytest.fixture
def yaml_rule_catalog(tmp_path):
    rules_dir = tmp_path / "rules"
    rules_dir.mkdir()
    (rules_dir / "test-rule.yml").write_text(SAMPLE_RULE_YAML)
    return YamlRuleCatalog.load(rules_dir)
```

---

## Cierre de fase en Python

Antes de tagear y pushear una fase:

```bash
# 1. Tests completos
pytest

# 2. Type checking (si usa mypy o pyright)
mypy src/
# o
pyright src/

# 3. Lint
ruff check src/ tests/

# 4. Tag
git add .
git commit -m "Fase [N]: [descripción]"
git tag -a v[versión] -m "Fase [N] — [descripción]"
git push origin main v[versión]
```

---

## Gaps comunes en proyectos Python

Ver `../../06-gap-tracking/por-stack/dev.md` para la lista completa.

Gaps específicos de Python frecuentes:
- Type annotations incompletas (especialmente en interfaces públicas).
- Tests que dependen del filesystem en lugar de usar fixtures/mocks.
- Imports circulares entre módulos de dominio.
- `requirements.txt` sin versiones pinneadas.
