# Clase 2.3: Análisis de Fourier Básico

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué es la transformada de Fourier
- Entender la serie de Fourier para señales periódicas
- Conocer la transformada de Fourier para señales no periódicas
- Aplicar el análisis de Fourier en el procesamiento de audio
- Entender la relación entre dominio temporal y frecuencial

## Introducción al Análisis de Fourier

### ¿Qué es la Transformada de Fourier?

La **transformada de Fourier** es una herramienta matemática que permite descomponer una señal en sus componentes de frecuencia. Es fundamental para el análisis y procesamiento de señales de audio.

### Concepto Fundamental

**Idea básica**: Cualquier señal puede expresarse como una suma de sinusoides de diferentes frecuencias, amplitudes y fases.

```
Señal en tiempo → Análisis de Fourier → Espectro de frecuencias
```

## Serie de Fourier

### Para Señales Periódicas

Una señal periódica puede expresarse como:

```
f(t) = a₀ + Σ[n=1 to ∞] [aₙcos(nω₀t) + bₙsin(nω₀t)]
```

Donde:
- **a₀** = componente DC
- **aₙ, bₙ** = coeficientes de Fourier
- **ω₀** = frecuencia fundamental
- **n** = número de armónico

### Forma Compleja

```
f(t) = Σ[n=-∞ to ∞] cₙe^(jnω₀t)
```

Donde:
```
cₙ = (1/T) ∫[0 to T] f(t)e^(-jnω₀t) dt
```

### Ejemplo: Onda Cuadrada

**Onda cuadrada**:
```
f(t) = {  A, 0 < t < T/2
        { -A, T/2 < t < T
```

**Serie de Fourier**:
```
f(t) = (4A/π) × [sin(ω₀t) + (1/3)sin(3ω₀t) + (1/5)sin(5ω₀t) + ...]
```

**Coeficientes**:
- c₁ = 4A/π
- c₃ = 4A/(3π)
- c₅ = 4A/(5π)
- cₙ = 0 para n par

### Ejemplo: Onda Triangular

**Onda triangular**:
```
f(t) = (8A/π²) × [sin(ω₀t) - (1/9)sin(3ω₀t) + (1/25)sin(5ω₀t) - ...]
```

**Características**:
- Solo armónicos impares
- Amplitudes decrecen como 1/n²
- Mejor convergencia que onda cuadrada

## Transformada de Fourier

### Para Señales No Periódicas

**Transformada de Fourier**:
```
F(ω) = ∫[-∞ to ∞] f(t)e^(-jωt) dt
```

**Transformada Inversa**:
```
f(t) = (1/2π) ∫[-∞ to ∞] F(ω)e^(jωt) dω
```

### Propiedades de la Transformada de Fourier

#### 1. Linealidad
```
F{af(t) + bg(t)} = aF(ω) + bG(ω)
```

#### 2. Desplazamiento en Tiempo
```
F{f(t - t₀)} = F(ω)e^(-jωt₀)
```

#### 3. Desplazamiento en Frecuencia
```
F{f(t)e^(jω₀t)} = F(ω - ω₀)
```

#### 4. Escalamiento
```
F{f(at)} = (1/|a|)F(ω/a)
```

#### 5. Convolución
```
F{f(t) * g(t)} = F(ω)G(ω)
```

### Ejemplos de Transformadas

#### 1. Impulso (Delta de Dirac)
```
δ(t) ↔ 1
```

#### 2. Exponencial Decayente
```
e^(-at)u(t) ↔ 1/(a + jω)
```

#### 3. Pulso Rectangular
```
rect(t/T) ↔ T sinc(ωT/2)
```

Donde:
```
sinc(x) = sin(x)/x
```

#### 4. Función Sinc
```
sinc(t) ↔ rect(ω/2π)
```

## Transformada Discreta de Fourier (DFT)

### Para Señales Digitales

**DFT**:
```
X[k] = Σ[n=0 to N-1] x[n]e^(-j2πkn/N)
```

**IDFT**:
```
x[n] = (1/N) Σ[k=0 to N-1] X[k]e^(j2πkn/N)
```

Donde:
- **N** = número de muestras
- **k** = índice de frecuencia
- **n** = índice de tiempo

### Ejemplo Práctico

**Señal**: x[n] = [1, 0, -1, 0] (N = 4)

**Cálculo de X[0]**:
```
X[0] = 1×1 + 0×1 + (-1)×1 + 0×1 = 0
```

**Cálculo de X[1]**:
```
X[1] = 1×1 + 0×(-j) + (-1)×(-1) + 0×j = 1 + 0 + 1 + 0 = 2
```

**Cálculo de X[2]**:
```
X[2] = 1×1 + 0×(-1) + (-1)×1 + 0×(-1) = 1 + 0 - 1 + 0 = 0
```

**Cálculo de X[3]**:
```
X[3] = 1×1 + 0×j + (-1)×(-1) + 0×(-j) = 1 + 0 + 1 + 0 = 2
```

**Resultado**: X[k] = [0, 2, 0, 2]

## Transformada Rápida de Fourier (FFT)

### Algoritmo Eficiente

La **FFT** es un algoritmo eficiente para calcular la DFT.

**Complejidad**:
- DFT directa: O(N²)
- FFT: O(N log N)

### Ventajas de la FFT

1. **Velocidad**: Mucho más rápida que DFT directa
2. **Eficiencia**: Algoritmo optimizado
3. **Aplicaciones**: Análisis en tiempo real

### Ejemplo de Uso

**Análisis de audio en tiempo real**:
```
1. Capturar N muestras de audio
2. Aplicar ventana (Hamming, Hanning)
3. Calcular FFT
4. Analizar espectro de frecuencias
5. Procesar según aplicación
```

## Aplicaciones en Audio

### 1. Análisis Espectral

**Espectrograma**:
- Representación visual del espectro en el tiempo
- Eje X: tiempo
- Eje Y: frecuencia
- Color: amplitud

### 2. Filtrado en Frecuencia

**Proceso**:
```
1. FFT de la señal
2. Modificar coeficientes de frecuencia
3. IFFT para obtener señal filtrada
```

### 3. Análisis de Armónicos

**Identificación de instrumentos**:
- Cada instrumento tiene un patrón característico de armónicos
- Análisis de Fourier revela estos patrones

### 4. Detección de Pitch

**Algoritmo básico**:
```
1. Calcular FFT
2. Encontrar picos en el espectro
3. Determinar frecuencia fundamental
```

### 5. Compresión de Audio

**Principio**:
- Eliminar frecuencias no audibles
- Reducir resolución de frecuencias menos importantes

## Ventanas para Análisis

### Problema del Leakage

**Causa**: Señales no periódicas en ventana finita
**Efecto**: Dispersión de energía en frecuencias adyacentes

### Tipos de Ventanas

#### 1. Rectangular
```
w[n] = 1, 0 ≤ n ≤ N-1
```

**Características**:
- Lobe principal estrecho
- Lobes laterales altos
- Mejor resolución, más leakage

#### 2. Hamming
```
w[n] = 0.54 - 0.46 cos(2πn/(N-1))
```

**Características**:
- Lobes laterales bajos
- Lobe principal más ancho
- Menos leakage, menor resolución

#### 3. Hanning
```
w[n] = 0.5 - 0.5 cos(2πn/(N-1))
```

**Características**:
- Balance entre resolución y leakage
- Muy usada en audio

#### 4. Blackman
```
w[n] = 0.42 - 0.5 cos(2πn/(N-1)) + 0.08 cos(4πn/(N-1))
```

**Características**:
- Lobes laterales muy bajos
- Lobe principal muy ancho
- Mínimo leakage, menor resolución

## Ejercicios Prácticos

### Ejercicio 1: Serie de Fourier

```
Dada la onda cuadrada con A = 1 y T = 1s

Pregunta: Calcular los primeros 3 armónicos

Solución:
ω₀ = 2π/T = 2π rad/s

c₁ = 4A/π = 4/π = 1.27
c₃ = 4A/(3π) = 4/(3π) = 0.42
c₅ = 4A/(5π) = 4/(5π) = 0.25

f(t) ≈ 1.27 sin(2πt) + 0.42 sin(6πt) + 0.25 sin(10πt)
```

### Ejercicio 2: Transformada de Pulso

```
Dado el pulso rectangular: f(t) = {1, -1 < t < 1
                                  {0, |t| > 1

Pregunta: Calcular la transformada de Fourier

Solución:
F(ω) = ∫[-1 to 1] 1×e^(-jωt) dt
F(ω) = [e^(-jωt)/(-jω)]|[-1 to 1]
F(ω) = (e^(-jω) - e^(jω))/(-jω)
F(ω) = 2 sin(ω)/ω = 2 sinc(ω)
```

### Ejercicio 3: Análisis de FFT

```
Dada la señal: x[n] = [1, 1, 1, 1] (N = 4)

Pregunta: Calcular la FFT

Solución:
X[0] = 1 + 1 + 1 + 1 = 4
X[1] = 1 + 1×(-j) + 1×(-1) + 1×j = 1 - j - 1 + j = 0
X[2] = 1 + 1×(-1) + 1×1 + 1×(-1) = 1 - 1 + 1 - 1 = 0
X[3] = 1 + 1×j + 1×(-1) + 1×(-j) = 1 + j - 1 - j = 0

X[k] = [4, 0, 0, 0]
```

## Relación con el Audio Digital

### Muestreo y Aliasing

**Teorema de Nyquist**:
```
fs ≥ 2fmax
```

**Efecto del Aliasing**:
- Frecuencias > fs/2 se "doblan" hacia frecuencias más bajas
- Distorsión irreversible de la señal

### Filtro Anti-Aliasing

**Función**: Eliminar frecuencias > fs/2 antes del muestreo
**Implementación**: Filtro pasa-bajo analógico

### Resolución de Frecuencia

**DFT**:
```
Δf = fs/N
```

Donde:
- **Δf** = resolución de frecuencia
- **fs** = frecuencia de muestreo
- **N** = número de muestras

**Ejemplo**:
- fs = 44.1 kHz
- N = 1024
- Δf = 44100/1024 = 43.07 Hz

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Serie de Fourier**: Para señales periódicas
2. **Transformada de Fourier**: Para señales no periódicas
3. **DFT y FFT**: Para señales digitales
4. **Aplicaciones en Audio**: Análisis espectral, filtrado, detección de pitch
5. **Ventanas**: Para reducir leakage en análisis

## Próxima Clase

En la siguiente clase estudiaremos la **transformada Z y sistemas discretos**, fundamentales para el procesamiento digital de señales.

---

## Navegación
- **Anterior**: [Clase 2.2: Funciones Trigonométricas y Ondas](clase-2-2-funciones-trigonometricas-ondas.md)
- **Siguiente**: [Clase 2.4: Transformada Z y Sistemas Discretos](clase-2-4-transformada-z-sistemas-discretos.md)
- **Volver al**: [Índice Principal](../../README.md)
