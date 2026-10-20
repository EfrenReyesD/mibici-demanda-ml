# Predicción de la demanda de salidas en el sistema MiBici (Guadalajara)

Implementación de un modelo de Machine Learning para estimar el número de salidas por estación y por hora del sistema de bicicletas públicas MiBici del Área Metropolitana de Guadalajara, a partir de sus datos abiertos de 2024 y 2025.

Proyecto de la materia **Inteligencia artificial y machine learning**, Maestría en Ingeniería y Desarrollo de Software (IUV).
Autor: **Efren Reyes Dimas**

## Datos

Los datos provienen del portal de datos abiertos de MiBici: https://mibici.net/es/datos-abiertos/

No se incluyen en el repositorio. Para reproducir el análisis, descarga los archivos CSV mensuales y colócalos así:

```
datos/
├── 2024/   datos_abiertos_2024_01.csv ... datos_abiertos_2024_12.csv
├── 2025/   datos_abiertos_2025_01.csv ... datos_abiertos_2025_12.csv
└── nomenclatura_2026_09.csv
```

## Cómo ejecutar

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Ejecuta los notebooks en orden:

| Notebook | Contenido |
|---|---|
| `01_revision.ipynb` | Revisión inicial de los 25 archivos: columnas, codificación, formatos y valores nulos |
| `02_preparacion.ipynb` | Carga unificada, análisis de duraciones, reglas de limpieza y selección de estaciones |
| `02b_revision_usuarios.ipynb` | Verificación de que los movimientos de balanceo no están registrados como viajes |
| `03_tabla_modelo.ipynb` | Tabla estación × hora con intervalos de cero salidas, variables de calendario y festivos |
| `04_modelos.ipynb` | Modelo base, regresión lineal, árbol de decisión, Random Forest, validación cruzada temporal, Grid Search, selección de características y Gradient Boosting |

## Resultados (conjunto de prueba, 2025)

| Modelo | MAE | RMSE | R² |
|---|---|---|---|
| **Gradient Boosting (Poisson), sin mes (modelo final)** | **2.123** | **3.287** | **0.638** |
| Base (promedio histórico) | 2.169 | 3.363 | 0.621 |
| Random Forest optimizado | 2.174 | 3.354 | 0.623 |
| Random Forest | 2.315 | 3.548 | 0.578 |
| Árbol de decisión | 2.322 | 3.559 | 0.576 |
| Regresión lineal | 2.805 | 4.247 | 0.396 |

El modelo se entrena con 2024 y se evalúa con 2025, sobre las 30 estaciones con más salidas en 2024 que operaron los 24 meses, dentro del horario de servicio (5:00 a 00:59).

Las figuras se encuentran en la carpeta `figuras/`.

## Alcance

El modelo predice **salidas observadas** por estación y hora. No estima el riesgo de desabasto, ya que eso requeriría además las llegadas, el inventario de bicicletas y la capacidad disponible en cada momento.