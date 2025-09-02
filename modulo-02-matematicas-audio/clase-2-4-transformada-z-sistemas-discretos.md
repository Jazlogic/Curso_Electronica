# Clase 2.4: Transformada Z y Sistemas Discretos

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué es la transformada Z
- Entender la relación entre transformada Z y sistemas discretos
- Conocer las propiedades de la transformada Z
- Aplicar la transformada Z en el análisis de filtros digitales
- Entender la estabilidad de sistemas discretos

## Introducción a la Transformada Z

### ¿Qué es la Transformada Z?

La **transformada Z** es una herramienta matemática para analizar señales y sistemas discretos en el dominio de la frecuencia compleja. Es análoga a la transformada de Laplace para sistemas continuos.

### Definición

**Transformada Z bilateral**:
```
X(z) = Σ[n=-∞ to ∞] x[n]z^(-n)
```

**Transformada Z unilateral**:
```
X(z) = Σ[n=0 to ∞] x[n]z^(-n)
```

Donde:
- **z** = variable compleja
- **x[n]** = señal discreta
- **n** = índice de tiempo

### Región de Convergencia (ROC)

La **ROC** es el conjunto de valores de z para los cuales la transformada Z converge.

**Ejemplo**:
```
x[n] = a^n u[n]  (exponencial causal)

X(z) = Σ[n=0 to ∞] a^n z^(-n) = Σ[n=0 to ∞] (a/z)^n

Converge si |a/z| < 1, es decir, |z| > |a|
ROC: |z| > |a|
```

## Transformadas Z Básicas

### 1. Impulso Unitario
```
δ[n] ↔ 1, ROC: todo el plano z
```

### 2. Escalón Unitario
```
u[n] ↔ z/(z-1), ROC: |z| > 1
```

### 3. Exponencial Causal
```
a^n u[n] ↔ z/(z-a), ROC: |z| > |a|
```

### 4. Exponencial Anticausal
```
-a^n u[-n-1] ↔ z/(z-a), ROC: |z| < |a|
```

### 5. Sinusoidal
```
sin(ω₀n) u[n] ↔ (z sin(ω₀))/(z² - 2z cos(ω₀) + 1)
```

### 6. Cosenoidal
```
cos(ω₀n) u[n] ↔ (z² - z cos(ω₀))/(z² - 2z cos(ω₀) + 1)
```

## Propiedades de la Transformada Z

### 1. Linealidad
```
ax[n] + by[n] ↔ aX(z) + bY(z)
```

### 2. Desplazamiento en Tiempo
```
x[n-k] ↔ z^(-k)X(z)
```

### 3. Escalamiento en Frecuencia
```
a^n x[n] ↔ X(z/a)
```

### 4. Convolución
```
x[n] * y[n] ↔ X(z)Y(z)
```

### 5. Diferenciación
```
nx[n] ↔ -z dX(z)/dz
```

### 6. Teorema del Valor Inicial
```
x[0] = lim[z→∞] X(z)
```

### 7. Teorema del Valor Final
```
lim[n→∞] x[n] = lim[z→1] (z-1)X(z)
```

## Sistemas Discretos

### Ecuación en Diferencias

Un sistema discreto se describe mediante:

```
y[n] = Σ[k=0 to M] b[k]x[n-k] - Σ[k=1 to N] a[k]y[n-k]
```

### Función de Transferencia

**Transformada Z de la ecuación en diferencias**:
```
H(z) = Y(z)/X(z) = (Σ[k=0 to M] b[k]z^(-k))/(1 + Σ[k=1 to N] a[k]z^(-k))
```

**Forma factorizada**:
```
H(z) = K × (Π[k=1 to M] (1 - z[k]z^(-1)))/(Π[k=1 to N] (1 - p[k]z^(-1)))
```

Donde:
- **z[k]** = ceros
- **p[k]** = polos
- **K** = ganancia

### Ejemplo: Filtro de Primer Orden

**Ecuación en diferencias**:
```
y[n] = x[n] + 0.5y[n-1]
```

**Función de transferencia**:
```
H(z) = 1/(1 - 0.5z^(-1)) = z/(z - 0.5)
```

**Polo**: z = 0.5
**ROC**: |z| > 0.5

## Estabilidad de Sistemas

### Criterio de Estabilidad

Un sistema es **estable** si y solo si todos sus polos están dentro del círculo unitario (|z| < 1).

### Análisis de Estabilidad

#### 1. Sistema de Primer Orden
```
H(z) = 1/(1 - az^(-1))
```

**Polo**: z = a
**Estable si**: |a| < 1

#### 2. Sistema de Segundo Orden
```
H(z) = 1/(1 + a₁z^(-1) + a₂z^(-2))
```

**Polos**: Soluciones de z² + a₁z + a₂ = 0
**Estable si**: |p₁| < 1 y |p₂| < 1

### Ejemplo Práctico

**Sistema**:
```
y[n] = x[n] + 0.8y[n-1] - 0.2y[n-2]
```

**Función de transferencia**:
```
H(z) = 1/(1 - 0.8z^(-1) + 0.2z^(-2))
```

**Polos**: z² - 0.8z + 0.2 = 0
```
z = (0.8 ± √(0.64 - 0.8))/2 = (0.8 ± √(-0.16))/2
z = 0.4 ± j0.2
```

**Magnitud de polos**:
```
|z| = √(0.4² + 0.2²) = √(0.16 + 0.04) = √0.2 = 0.447
```

**Conclusión**: Sistema estable (|z| < 1)

## Filtros Digitales

### Clasificación de Filtros

#### 1. Filtros FIR (Finite Impulse Response)
```
y[n] = Σ[k=0 to M] b[k]x[n-k]
```

**Características**:
- Respuesta al impulso finita
- Siempre estables
- Fase lineal posible
- Mayor complejidad computacional

#### 2. Filtros IIR (Infinite Impulse Response)
```
y[n] = Σ[k=0 to M] b[k]x[n-k] - Σ[k=1 to N] a[k]y[n-k]
```

**Características**:
- Respuesta al impulso infinita
- Pueden ser inestables
- Fase no lineal
- Menor complejidad computacional

### Diseño de Filtros

#### 1. Filtro Pasa-Bajo

**Ecuación en diferencias**:
```
y[n] = 0.1x[n] + 0.9y[n-1]
```

**Función de transferencia**:
```
H(z) = 0.1/(1 - 0.9z^(-1))
```

**Frecuencia de corte**: fc = fs × arccos(1.8 - 0.81)/(2π)

#### 2. Filtro Pasa-Alto

**Ecuación en diferencias**:
```
y[n] = 0.5(x[n] - x[n-1]) + 0.8y[n-1]
```

**Función de transferencia**:
```
H(z) = 0.5(1 - z^(-1))/(1 - 0.8z^(-1))
```

## Análisis de Frecuencia

### Respuesta en Frecuencia

**Evaluación en el círculo unitario**:
```
H(e^(jω)) = H(z)|[z=e^(jω)]
```

### Ejemplo: Filtro de Primer Orden

**Función de transferencia**:
```
H(z) = 1/(1 - 0.5z^(-1))
```

**Respuesta en frecuencia**:
```
H(e^(jω)) = 1/(1 - 0.5e^(-jω))
```

**Magnitud**:
```
|H(e^(jω))| = 1/√(1.25 - cos(ω))
```

**Fase**:
```
∠H(e^(jω)) = -arctan(0.5 sin(ω)/(1 - 0.5 cos(ω)))
```

## Aplicaciones en Audio

### 1. Filtros de Audio

**Ecualizador gráfico**:
- Múltiples filtros en paralelo
- Cada filtro sintonizado a una frecuencia específica
- Control independiente de ganancia

### 2. Efectos de Audio

**Eco digital**:
```
y[n] = x[n] + αx[n-D]
```

Donde:
- **D** = retardo en muestras
- **α** = factor de atenuación

**Función de transferencia**:
```
H(z) = 1 + αz^(-D)
```

### 3. Compresión de Audio

**Compresor dinámico**:
- Control automático de ganancia
- Reducción del rango dinámico
- Implementación en tiempo real

### 4. Análisis de Espectro

**Analizador de espectro en tiempo real**:
- FFT de ventanas deslizantes
- Visualización del espectro
- Detección de picos y valles

## Ejercicios Prácticos

### Ejercicio 1: Transformada Z Básica

```
Dada la señal: x[n] = 2^n u[n]

Pregunta: Calcular la transformada Z

Solución:
X(z) = Σ[n=0 to ∞] 2^n z^(-n) = Σ[n=0 to ∞] (2/z)^n

Converge si |2/z| < 1, es decir, |z| > 2

X(z) = 1/(1 - 2/z) = z/(z - 2)

ROC: |z| > 2
```

### Ejercicio 2: Análisis de Sistema

```
Sistema: y[n] = x[n] + 0.5x[n-1] + 0.25y[n-1]

Pregunta: Calcular la función de transferencia

Solución:
Y(z) = X(z) + 0.5z^(-1)X(z) + 0.25z^(-1)Y(z)

Y(z)(1 - 0.25z^(-1)) = X(z)(1 + 0.5z^(-1))

H(z) = Y(z)/X(z) = (1 + 0.5z^(-1))/(1 - 0.25z^(-1))
H(z) = (z + 0.5)/(z - 0.25)
```

### Ejercicio 3: Estabilidad

```
Sistema: y[n] = x[n] + 1.2y[n-1] - 0.35y[n-2]

Pregunta: ¿Es estable el sistema?

Solución:
H(z) = 1/(1 - 1.2z^(-1) + 0.35z^(-2))

Polos: z² - 1.2z + 0.35 = 0
z = (1.2 ± √(1.44 - 1.4))/2 = (1.2 ± √0.04)/2
z = (1.2 ± 0.2)/2

z₁ = 0.7, z₂ = 0.5

|z₁| = 0.7 < 1, |z₂| = 0.5 < 1

Conclusión: Sistema estable
```

## Relación con el Audio Digital

### Muestreo y Cuantización

**Efectos de la cuantización**:
- Ruido de cuantización
- Distorsión no lineal
- Limitación de rango dinámico

### Filtros Anti-Aliasing

**Implementación digital**:
- Filtro pasa-bajo antes del muestreo
- Frecuencia de corte < fs/2
- Transición suave para evitar aliasing

### Procesamiento en Tiempo Real

**Consideraciones**:
- Latencia mínima
- Eficiencia computacional
- Estabilidad numérica

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Transformada Z**: Herramienta para análisis de sistemas discretos
2. **Propiedades**: Linealidad, desplazamiento, convolución
3. **Sistemas Discretos**: Ecuaciones en diferencias y funciones de transferencia
4. **Estabilidad**: Criterio del círculo unitario
5. **Aplicaciones en Audio**: Filtros, efectos, análisis espectral

## Próxima Clase

En la siguiente clase estudiaremos la **convolución y correlación**, fundamentales para el procesamiento de señales y el análisis de sistemas.

---

## Navegación
- **Anterior**: [Clase 2.3: Análisis de Fourier Básico](clase-2-3-analisis-fourier-basico.md)
- **Siguiente**: [Clase 2.5: Convolución y Correlación](clase-2-5-convolucion-correlacion.md)
- **Volver al**: [Índice Principal](../../README.md)
