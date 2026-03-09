# Pronóstico de Inflación Venezuela 2026 — Modelo Híbrido (ARIMA + OLS)

## Propósito

Estimar la inflación mensual de **Venezuela durante 2026** usando un modelo híbrido que combina **inercia estadística (ARIMA)** y **regresión OLS** basada en variables macroeconómicas (variación del tipo de cambio y M2). El script limpia automáticamente los archivos de entrada, desagrega la meta anual con estacionalidad y genera un **reporte mensual + gráfico comparativo**.

## Índice

* Visión General  
* Fundamentos Matemáticos  
* Parámetros del Modelo  
* Suposiciones y Alcances  
* Validación y Calidad de Resultados  
* Extensiones Sugeridas  

---

## Visión General

El algoritmo integra tres componentes clave:

- Meta de inflación anual obtenida del promedio de pronósticos de expertos.
- Desagregación mensual mediante **estacionalidad venezolana**.
- Variables macroeconómicas: **variación del tipo de cambio** y **crecimiento del M2**.

El modelo final utiliza:
- **ARIMA(1,1,0)** → captura inercia inflacionaria.
- **Regresión OLS** → ajusta la relación entre inflación mensual, FX, M2 y el componente ARIMA.

**Salidas principales:**
- Tabla de inflación mensual y acumulada (enero–diciembre 2026).  
- Indicador de ajuste: **R²** del modelo OLS.  
- Gráfico: *Meta Expertos vs Modelo Híbrido*.

---

## Fundamentos Matemáticos

### Desagregación de meta anual

\[
\text{tasa}_m = (1 + \text{meta\_anual})^{1/12} - 1
\]

\[
\text{inflación\_objetivo}_m = \text{tasa}_m \cdot 12 \cdot \frac{w_m}{\sum w}
\]

donde \( w_m \) son los pesos estacionales.

### Componente ARIMA

Modelo ARIMA(1,1,0):

\[
y_t = y_{t-1} + \phi (y_{t-1} - y_{t-2}) + \varepsilon_t
\]

### Regresión OLS (modelo final)

\[
\text{Inflación}_t = \beta_0 + 
\beta_1(\Delta TC_t) +
\beta_2(\Delta M2_t) +
\beta_3(\text{pred\_arima}_t)
\]

Pronóstico final:

\[
\hat{y}_t = X_t \beta
\]

---

## Parámetros del Modelo

### Entradas requeridas (Excel)
- `inflacion*.xlsx` → columnas numéricas con pronósticos.  
- `tipo_cambio*.xlsx` → columna `Variacion`.  
- `m2_bcv*.xlsx` → columna `M2`.

### Estacionalidad

```python
pesos = [1.0, 0.8, 0.9, 1.1, 1.0, 0.9, 1.0, 1.2, 1.3, 1.5, 1.8, 2.0]
