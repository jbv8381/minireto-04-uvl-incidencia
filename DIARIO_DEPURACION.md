# Diario de depuración

## 1. Comprensión del informe
- Comportamiento esperado: El catálogo es válido.
- Comportamiento observado: La aplicación busca `weather.uvl` dentro de `models/` e informa de que el fichero no existe.
- Información del entorno relevante: CATALOG_FILE, UVL_MODELS_DIR
- Información que falta o que pediríamos: sistema operativo, directorio desde el que se ejecuta `validate.py`, valores exactos de las variables de entorno y salida completa del comando.

## 2. Reproducción
- Comandos ejecutados: export CATALOG_FILE=external/catalog.csv
export UVL_MODELS_DIR=external/models
python3 validate.py
- Evidencia obtenida: El catálogo no es válido:
- Línea 2: no existe models\weather.uvl
- ¿Se ha reproducido de forma consistente?: si

## 3. Hipótesis y diagnóstico
- Primera hipótesis: La primera hipótesis razonable es que la aplicación lee CATALOG_FILE, pero ignora UVL_MODELS_DIR.
- Compzrobación realizada: mirar las funciones en catalog.py
- Causa raíz: el catálogo respeta la variable de entorno, pero el directorio de modelos ignora UVL_MODELS_DIR. Por eso busca models/weather.uvl aunque se haya configurado.

## 4. Reparación y validación
- Prueba de regresión añadida: def test_models_directory_can_be_configured(monkeypatch, tmp_path: Path):
    monkeypatch.setenv("UVL_MODELS_DIR", str(tmp_path))
    assert get_models_dir() == tmp_path

- Cambio realizado: def get_models_dir() -> Path:
    return Path(os.environ.get("UVL_MODELS_DIR", "models"))
- Comandos de validación: python -m pytest -q
python validate.py
- Resultado: El catálogo es válido.

## 5. Trazabilidad
- Número o URL de la incidencia: <número-de-incidencia>
- Commit que la corrige: Respeta UVL_MODELS_DIR al localizar los modelos
Fixes #<número-de-incidencia>
