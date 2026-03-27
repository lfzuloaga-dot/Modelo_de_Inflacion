# Pronóstico de Inflación Venezuela 2026: Modelo Híbrido OLS-HAC

## Propósito
Modelar y proyectar la trayectoria de la inflación mensual y acumulada en Venezuela para el año 2026. El algoritmo utiliza un enfoque econométrico híbrido que combina metas de consenso de expertos con una regresión de **Mínimos Cuadrados Ordinarios (OLS)** ajustada por **Errores Estándar Robustos (HAC/Newey-West)** para capturar la volatilidad de la liquidez monetaria y el tipo de cambio.

## Índice
* [Visión General]
* [Fundamentos Matemáticos]
* [Variables del Modelo]
* [Validación Estadística]
* [Reproducibilidad]
* [Extensiones Sugeridas]

---

## Visión General
El modelo procesa datos provenientes de fuentes oficiales y proyecciones de consultoras externas para ejecutar una simulación mensual que incorpora:
- **Efecto Pass-Through:** Transmisión de la variación del tipo de cambio a precios.
- **Rezago Monetario:** Impacto de la liquidez (M2) con un decalaje de 1 mes ($t-1$).
- **Estacionalidad Histórica:** Ajuste por picos de consumo en el último trimestre (Oct-Dic).

**Salidas principales:**
* **Variación Mensual (%):** Pronóstico ajustado por regresión.
* **Inflación Acumulada:** Cálculo mediante factor compuesto (geométrico).
* **Significancia Estadística:** Reporte de P-values y $R^2$ ajustado para evitar el overfitting.

---

## Fundamentos Matemáticos

### 1. Ecuación de Regresión (Log-Diferencias)
Para asegurar la estacionariedad de las series y mejorar la significancia, el modelo opera sobre las variaciones porcentuales:

$$\Delta \ln(\text{Inf}_t) = \beta_0 + \beta_1 \Delta \ln(\text{TC}_t) + \beta_2 \Delta \ln(\text{M2}_{t-1}) + \beta_3 \text{Peso}_t + \epsilon_t$$

### 2. Factor de Acumulación
La inflación acumulada se calcula como una composición de tasas:
$$\text{Inflación Acum}_n = \left( \prod_{i=1}^{n} (1 + \pi_i) \right) - 1$$
*Donde $\pi_i$ es la tasa de inflación mensual.*

### 3. Ajuste de Errores Robustos (HAC)
Dado que las series de tiempo económicas presentan autocorrelación, el modelo utiliza la matriz de covarianza de **Newey-West** para corregir los errores estándar y obtener P-values confiables:
$$\text{Var}(\hat{\beta}) = (X'X)^{-1} \hat{\Omega} (X'X)^{-1}$$

---

## Variables del Modelo

| Variable | Fuente | Descripción |
| :--- | :--- | :--- |
| **Meta Expertos** | Excel (`inflacion 2`) | Ancla de expectativas basada en proyecciones de mercado. |
| **Var. Dólar** | Excel (`tipo_cambio`) | Variación mensual del tipo de cambio oficial/paralelo. |
| **M2 Lag 1** | Excel (`m2_bcv`) | Crecimiento de la liquidez monetaria con rezago de 30 días. |
| **Peso Estacional** | Parámetro Interno | Coeficientes de ponderación mensual (Ene: 1.0 ... Dic: 2.0). |

---

## Validación y Calidad de Resultados

Para garantizar la robustez del modelo, se monitorean las siguientes métricas en cada corrida:
* **$R^2$ Ajustado:** Se busca un rango óptimo entre **0.88 y 0.95**. Valores de 1.00 son rechazados por indicar identidad matemática o error de lógica.
* **P-Value (Significancia):** Se exige un $p < 0.05$ (95% de confianza) para las variables explicativas clave.
* **Durbin-Watson:** Validación de la independencia de los residuos (objetivo $\approx 2.0$).
* **Ruido Blanco:** Inclusión de perturbaciones aleatorias para simular volatilidad real de mercado.

---

## Reproducibilidad

### Requisitos Técnicos
```python
import pandas as pd
import numpy as np
import statsmodels.api as sm
import matplotlib.pyplot as plt
