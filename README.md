# 📈 Sistema de Proyección de Inflación Híbrido (Data-Driven)

Este repositorio contiene un modelo de **Forecasting Económico** de alta precisión diseñado para entornos de volatilidad macroeconómica (basado en el contexto de Venezuela). El sistema utiliza un enfoque híbrido que combina la inercia de series de tiempo, fundamentos monetarios y simulaciones de Monte Carlo para la gestión de incertidumbre.

## 🚀 Características Principales

* **Modelo Híbrido Estocástico:** Combina la capacidad predictiva de **ARIMA (Inercia)** con la explicativa de **Regresión Lineal (OLS)**.
* **Desagregación Estacional Proxy:** Transforma metas anuales de expertos (FMI/BCV) en una serie mensual ponderada por picos históricos (Oct-Nov-Dic).
* **Análisis de Fundamentos:** Evalúa el impacto directo de la Liquidez Monetaria (**M2**) y la variación del **Tipo de Cambio**.
* **Cuantificación de Riesgo:** Genera 10,000 escenarios mediante **Monte Carlo** para determinar intervalos de confianza y percentiles de probabilidad.

---

## 🧠 Arquitectura del Modelo

El flujo de datos se divide en tres etapas críticas:

1.  **Capa Técnica (ARIMA 1,1,1):** Extrae el componente de autocorrelación de la inflación proxy.
2.  **Capa Fundamental (Regresión OLS):** Ajusta la proyección según variables exógenas:
    * $\Delta \% \text{ Tipo de Cambio}$
    * $\Delta \% \text{ M2 (Lag de 1 mes)}$
    * $\text{Predicciones ARIMA (Inercia)}$
3.  **Capa de Simulación:** Utiliza el error estándar de los residuos ($\sigma$) para proyectar la distribución de probabilidad del siguiente periodo.



---

## 📊 Fundamentos Matemáticos

### 1. Estimación de Inflación Proxy
Para distribuir la meta anual de forma realista, aplicamos una tasa base compuesta ajustada por pesos estacionales ($w_i$):
$$\pi_{proxy} = ((1 + \text{meta\_anual})^{1/12} - 1) \times 12 \times w_i$$

### 2. Especificación de la Regresión
$$\text{Inflación}_t = \beta_0 + \beta_1 (\text{VarDolar}_t) + \beta_2 (\text{VarM2}_{t-1}) + \beta_3 (\text{PredARIMA}_t) + \epsilon$$

---

## 💻 Implementación y Resultados

El sistema genera un reporte detallado de calidad estadística y proyecciones:

```text
============================================================
       SISTEMA DE PROYECCIÓN DE INFLACIÓN DATA-DRIVEN 2026
============================================================
CALIDAD DEL AJUSTE (R-Cuadrado): 0.9450
INCERTIDUMBRE (Sigma del Modelo): 1.50%
------------------------------------------------------------
PREDICCIÓN PARA EL PERIODO SIGUIENTE:
Valor Central (Más probable):
