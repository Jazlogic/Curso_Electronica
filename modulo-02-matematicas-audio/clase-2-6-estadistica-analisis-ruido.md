# Clase 2.6: Estadística y Análisis de Ruido

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender los conceptos básicos de estadística aplicada al audio
- Entender los diferentes tipos de ruido en sistemas de audio
- Conocer las medidas de calidad de audio
- Aplicar análisis estadístico en el diseño de sistemas de audio
- Entender la relación entre ruido y calidad de audio

## Introducción a la Estadística en Audio

### ¿Por qué es importante la estadística en audio?

La **estadística** es fundamental en el análisis y diseño de sistemas de audio porque:

1. **Ruido**: Todas las señales de audio contienen ruido
2. **Variabilidad**: Las señales de audio son inherentemente variables
3. **Calidad**: La calidad del audio se mide estadísticamente
4. **Optimización**: Los sistemas se optimizan basándose en análisis estadístico

### Conceptos Básicos

#### 1. Media (Promedio)
```
μ = (1/N) Σ[i=1 to N] x[i]
```

#### 2. Varianza
```
σ² = (1/N) Σ[i=1 to N] (x[i] - μ)²
```

#### 3. Desviación Estándar
```
σ = √σ²
```

#### 4. Valor RMS (Root Mean Square)
```
RMS = √[(1/N) Σ[i=1 to N] x[i]²]
```

## Tipos de Ruido en Audio

### 1. Ruido Térmico

**Origen**: Movimiento aleatorio de electrones en conductores
**Características**:
- Distribución gaussiana
- Independiente de la frecuencia
- Proporcional a la temperatura

**Fórmula**:
```
Vn = √(4kTRB)
```

Donde:
- **k** = constante de Boltzmann
- **T** = temperatura absoluta
- **R** = resistencia
- **B** = ancho de banda

### 2. Ruido de Cuantización

**Origen**: Error en la conversión analógica-digital
**Características**:
- Distribución uniforme
- Proporcional al paso de cuantización
- Dependiente de la resolución

**Fórmula**:
```
SNR = 6.02N + 1.76 dB
```

Donde **N** es el número de bits.

### 3. Ruido de Fase (Jitter)

**Origen**: Variaciones en el reloj de muestreo
**Características**:
- Afecta la calidad temporal
- Genera distorsión armónica
- Crítico en audio de alta calidad

### 4. Ruido de Intermodulación

**Origen**: No linealidades en el sistema
**Características**:
- Genera productos de intermodulación
- Afecta la fidelidad
- Dependiente de la amplitud

### 5. Ruido de Fondo

**Origen**: Múltiples fuentes (alimentación, interferencias)
**Características**:
- Amplio espectro
- Variable en el tiempo
- Difícil de caracterizar

## Medidas de Calidad de Audio

### 1. Relación Señal a Ruido (SNR)

**Definición**:
```
SNR = 20 log₁₀(Ps/Pn) dB
```

Donde:
- **Ps** = potencia de la señal
- **Pn** = potencia del ruido

**Valores típicos**:
- Audio CD: 96 dB
- Audio profesional: > 100 dB
- Audio de consumo: 80-90 dB

### 2. Distorsión Armónica Total (THD)

**Definición**:
```
THD = √(Σ[n=2 to ∞] Hn²)/H1 × 100%
```

Donde:
- **Hn** = amplitud del armónico n
- **H1** = amplitud del fundamental

**Valores típicos**:
- Audio de alta calidad: < 0.1%
- Audio de consumo: < 1%
- Audio de baja calidad: > 5%

### 3. Distorsión de Intermodulación (IMD)

**Definición**:
```
IMD = √(Σ[productos de intermodulación])/frecuencia fundamental × 100%
```

**Aplicación**: Medida de no linealidades en amplificadores

### 4. Rango Dinámico

**Definición**:
```
Rango Dinámico = 20 log₁₀(Vmax/Vmin) dB
```

Donde:
- **Vmax** = voltaje máximo sin distorsión
- **Vmin** = voltaje mínimo detectable

### 5. Factor de Ruido

**Definición**:
```
NF = 10 log₁₀(SNRin/SNRout) dB
```

**Aplicación**: Medida de ruido añadido por el sistema

## Análisis Estadístico de Señales

### 1. Función de Densidad de Probabilidad (PDF)

**Definición**: Probabilidad de que una variable aleatoria tome un valor específico

**Para ruido gaussiano**:
```
p(x) = (1/√(2πσ²)) e^(-(x-μ)²/(2σ²))
```

### 2. Función de Distribución Acumulativa (CDF)

**Definición**: Probabilidad de que una variable aleatoria sea menor o igual a un valor

```
F(x) = P(X ≤ x) = ∫[-∞ to x] p(t) dt
```

### 3. Función de Autocorrelación

**Definición**:
```
R(τ) = E[x(t)x(t+τ)]
```

**Aplicación**: Análisis de periodicidad y ruido

### 4. Densidad Espectral de Potencia (PSD)

**Definición**:
```
S(f) = |X(f)|²
```

Donde **X(f)** es la transformada de Fourier de x(t).

## Análisis de Ruido en Sistemas de Audio

### 1. Ruido en Amplificadores

**Fuentes principales**:
- Ruido térmico de resistencias
- Ruido de transistores
- Ruido de alimentación

**Análisis**:
```
Vn_total = √(Vn1² + Vn2² + Vn3² + ...)
```

### 2. Ruido en Convertidores ADC/DAC

**Fuentes principales**:
- Ruido de cuantización
- Ruido de referencia
- Ruido de reloj

**Análisis**:
```
SNR = 6.02N + 1.76 - 10 log₁₀(fs/2B) dB
```

### 3. Ruido en Filtros

**Fuentes principales**:
- Ruido de componentes pasivos
- Ruido de amplificadores operacionales
- Ruido de alimentación

**Análisis**:
```
Vn_filter = √(Σ[Vn_component²])
```

## Optimización de Sistemas de Audio

### 1. Minimización de Ruido

**Estrategias**:
- Selección de componentes de bajo ruido
- Diseño de circuitos balanceados
- Filtrado de alimentación
- Blindaje electromagnético

### 2. Maximización de SNR

**Técnicas**:
- Amplificación de señal
- Reducción de ruido
- Filtrado de ruido
- Compresión de rango dinámico

### 3. Análisis de Sensibilidad

**Método**:
- Variación de parámetros
- Análisis de Monte Carlo
- Optimización estadística

## Aplicaciones en Audio Digital

### 1. Análisis de Calidad

**Métricas**:
- SNR en diferentes frecuencias
- THD vs frecuencia
- IMD vs amplitud
- Rango dinámico

### 2. Optimización de Algoritmos

**Técnicas**:
- Análisis de ruido de cuantización
- Optimización de filtros
- Compensación de no linealidades

### 3. Calibración de Sistemas

**Procesos**:
- Medición de ruido de fondo
- Calibración de niveles
- Compensación de distorsión

## Ejercicios Prácticos

### Ejercicio 1: Cálculo de SNR

```
Datos:
- Señal: 1V RMS
- Ruido: 0.01V RMS

Pregunta: Calcular la SNR

Solución:
SNR = 20 log₁₀(1/0.01) = 20 log₁₀(100) = 20 × 2 = 40 dB
```

### Ejercicio 2: Análisis de THD

```
Datos:
- Fundamental: 1V
- 2do armónico: 0.01V
- 3er armónico: 0.005V
- 4to armónico: 0.002V

Pregunta: Calcular la THD

Solución:
THD = √(0.01² + 0.005² + 0.002²)/1 × 100%
THD = √(0.0001 + 0.000025 + 0.000004)/1 × 100%
THD = √0.000129/1 × 100% = 0.0114 × 100% = 1.14%
```

### Ejercicio 3: Análisis de Ruido

```
Datos:
- Ruido térmico: 0.001V
- Ruido de cuantización: 0.002V
- Ruido de alimentación: 0.0005V

Pregunta: Calcular el ruido total

Solución:
Vn_total = √(0.001² + 0.002² + 0.0005²)
Vn_total = √(0.000001 + 0.000004 + 0.00000025)
Vn_total = √0.00000525 = 0.00229V
```

## Relación con el Audio de Ultra Baja Latencia

### 1. Análisis de Latencia

**Fuentes de latencia**:
- Procesamiento de señales
- Buffers de audio
- Algoritmos de filtrado
- Compensación de retraso

### 2. Optimización de Rendimiento

**Técnicas**:
- Análisis de ruido en tiempo real
- Optimización de algoritmos
- Compensación de distorsión
- Minimización de latencia

### 3. Calidad vs Latencia

**Compromisos**:
- Filtros de alta calidad vs latencia
- Análisis espectral vs tiempo de procesamiento
- Compensación de ruido vs retraso

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Estadística Básica**: Media, varianza, desviación estándar, RMS
2. **Tipos de Ruido**: Térmico, cuantización, fase, intermodulación
3. **Medidas de Calidad**: SNR, THD, IMD, rango dinámico
4. **Análisis Estadístico**: PDF, CDF, autocorrelación, PSD
5. **Aplicaciones en Audio**: Análisis de calidad, optimización, calibración

## Próxima Clase

En la siguiente clase del Módulo 3 estudiaremos los **fundamentos de programación**, comenzando con C/C++ para el procesamiento de audio de ultra baja latencia.

---

## Navegación
- **Anterior**: [Clase 2.5: Convolución y Correlación](clase-2-5-convolucion-correlacion.md)
- **Siguiente**: [Módulo 3 - Índice](../modulo-03-programacion-basica/README.md)
- **Volver al**: [Índice Principal](../../README.md)
