# Clase 2.2: Funciones Trigonométricas y Ondas

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender las funciones trigonométricas básicas
- Entender las propiedades de las ondas sinusoidales
- Conocer los parámetros que definen una onda
- Aplicar las funciones trigonométricas en el análisis de audio
- Entender la relación entre frecuencia, período y fase

## Introducción a las Funciones Trigonométricas

### Funciones Básicas

#### 1. Seno (sin)
```
y = sin(x)
```

**Características**:
- Período: 2π
- Rango: [-1, 1]
- Puntos clave: sin(0) = 0, sin(π/2) = 1, sin(π) = 0, sin(3π/2) = -1

#### 2. Coseno (cos)
```
y = cos(x)
```

**Características**:
- Período: 2π
- Rango: [-1, 1]
- Puntos clave: cos(0) = 1, cos(π/2) = 0, cos(π) = -1, cos(3π/2) = 0

#### 3. Tangente (tan)
```
y = tan(x) = sin(x)/cos(x)
```

**Características**:
- Período: π
- Rango: (-∞, ∞)
- Asíntotas verticales en x = π/2 + nπ

### Relaciones Fundamentales

#### Identidad Pitagórica
```
sin²(x) + cos²(x) = 1
```

#### Identidades de Ángulo Doble
```
sin(2x) = 2sin(x)cos(x)
cos(2x) = cos²(x) - sin²(x)
```

#### Identidades de Suma
```
sin(a + b) = sin(a)cos(b) + cos(a)sin(b)
cos(a + b) = cos(a)cos(b) - sin(a)sin(b)
```

## Ondas Sinusoidales

### Definición de Onda Sinusoidal

Una **onda sinusoidal** es una función que describe una oscilación periódica suave.

**Forma general**:
```
y(t) = A sin(ωt + φ) + C
```

Donde:
- **A** = amplitud (máximo desplazamiento)
- **ω** = frecuencia angular (rad/s)
- **φ** = ángulo de fase (rad)
- **C** = desplazamiento vertical (offset DC)

### Parámetros de la Onda

#### 1. Amplitud (A)
- **Definición**: Valor máximo de la onda
- **Unidades**: Voltios (V), Pascal (Pa), etc.
- **Efecto**: Controla el volumen en audio

#### 2. Frecuencia (f)
- **Definición**: Número de ciclos por segundo
- **Unidades**: Hertz (Hz)
- **Relación**: ω = 2πf

#### 3. Período (T)
- **Definición**: Tiempo para completar un ciclo
- **Unidades**: Segundos (s)
- **Relación**: T = 1/f = 2π/ω

#### 4. Fase (φ)
- **Definición**: Desplazamiento horizontal de la onda
- **Unidades**: Radianes (rad) o grados (°)
- **Conversión**: 2π rad = 360°

### Ejemplo Práctico

**Onda de audio a 440 Hz (La musical)**:
```
y(t) = 0.5 sin(2π × 440 × t + 0)
```

**Parámetros**:
- Amplitud: 0.5 V
- Frecuencia: 440 Hz
- Fase: 0 rad
- Período: 1/440 = 2.27 ms

## Análisis de Ondas Complejas

### Suma de Ondas Sinusoidales

**Principio de Superposición**:
```
y(t) = A₁sin(ω₁t + φ₁) + A₂sin(ω₂t + φ₂) + ...
```

### Ejemplo: Acordes Musicales

**Acorde de Do Mayor (C-E-G)**:
```
y(t) = sin(2π × 261.63t) + sin(2π × 329.63t) + sin(2π × 392.00t)
```

**Frecuencias**:
- Do (C4): 261.63 Hz
- Mi (E4): 329.63 Hz  
- Sol (G4): 392.00 Hz

### Análisis de Fase

#### Diferencia de Fase
```
Δφ = φ₂ - φ₁
```

#### Efectos de la Fase

**Fase en fase (0°)**:
```
y(t) = sin(ωt) + sin(ωt) = 2sin(ωt)
```
- Amplitud resultante: 2A

**Fase opuesta (180°)**:
```
y(t) = sin(ωt) + sin(ωt + π) = 0
```
- Amplitud resultante: 0 (cancelación)

**Fase cuadratura (90°)**:
```
y(t) = sin(ωt) + sin(ωt + π/2) = sin(ωt) + cos(ωt)
```

## Transformaciones de Ondas

### 1. Cambio de Amplitud
```
y(t) = A × sin(ωt + φ)
```

### 2. Cambio de Frecuencia
```
y(t) = sin(kωt + φ)
```

### 3. Cambio de Fase
```
y(t) = sin(ωt + φ + Δφ)
```

### 4. Desplazamiento Vertical
```
y(t) = sin(ωt + φ) + C
```

## Aplicaciones en Audio

### 1. Generación de Tones

**Tono puro**:
```
y(t) = A sin(2πft)
```

**Tono con armónicos**:
```
y(t) = A₁sin(2πft) + A₂sin(4πft) + A₃sin(6πft) + ...
```

### 2. Modulación de Amplitud (AM)

**Señal modulada**:
```
y(t) = [1 + m sin(2πfmt)] × A sin(2πfct)
```

Donde:
- **m** = índice de modulación
- **fm** = frecuencia de modulación
- **fc** = frecuencia de portadora

### 3. Modulación de Frecuencia (FM)

**Señal modulada**:
```
y(t) = A sin(2πfct + β sin(2πfmt))
```

Donde:
- **β** = índice de modulación FM

### 4. Análisis de Distorsión

**Distorsión armónica**:
```
y(t) = A sin(ωt) + B sin(2ωt) + C sin(3ωt) + ...
```

**THD (Total Harmonic Distortion)**:
```
THD = √(B² + C² + D² + ...)/A × 100%
```

## Análisis Espectral

### Espectro de Frecuencias

Una señal compleja puede descomponerse en sus componentes de frecuencia:

```
y(t) = A₀ + A₁sin(ωt + φ₁) + A₂sin(2ωt + φ₂) + A₃sin(3ωt + φ₃) + ...
```

### Ejemplo: Onda Cuadrada

**Serie de Fourier**:
```
y(t) = (4A/π) × [sin(ωt) + (1/3)sin(3ωt) + (1/5)sin(5ωt) + ...]
```

**Componentes**:
- Fundamental: A sin(ωt)
- 3er armónico: (A/3) sin(3ωt)
- 5to armónico: (A/5) sin(5ωt)

## Ejercicios Prácticos

### Ejercicio 1: Parámetros de Onda

```
Dada la onda: y(t) = 2 sin(2π × 1000t + π/4)

Preguntas:
1. ¿Cuál es la amplitud?
2. ¿Cuál es la frecuencia?
3. ¿Cuál es el período?
4. ¿Cuál es la fase en grados?

Solución:
1. Amplitud: A = 2
2. Frecuencia: f = 1000 Hz
3. Período: T = 1/1000 = 0.001 s = 1 ms
4. Fase: φ = π/4 = 45°
```

### Ejercicio 2: Suma de Ondas

```
Dadas las ondas:
y₁(t) = 3 sin(2π × 100t)
y₂(t) = 2 sin(2π × 100t + π/2)

Pregunta: ¿Cuál es la amplitud resultante?

Solución:
y₁(t) = 3 sin(2π × 100t)
y₂(t) = 2 cos(2π × 100t)  [sin(x + π/2) = cos(x)]

Amplitud resultante:
A = √(3² + 2²) = √(9 + 4) = √13 = 3.61
```

### Ejercicio 3: Análisis de Fase

```
Dadas las ondas:
y₁(t) = sin(2π × 50t)
y₂(t) = sin(2π × 50t + π)

Pregunta: ¿Cuál es la amplitud resultante?

Solución:
y₁(t) = sin(2π × 50t)
y₂(t) = -sin(2π × 50t)  [sin(x + π) = -sin(x)]

y(t) = sin(2π × 50t) - sin(2π × 50t) = 0

Amplitud resultante: 0 (cancelación total)
```

## Relación con el Audio Digital

### Muestreo de Señales

**Teorema de Nyquist**:
```
fs ≥ 2fmax
```

Donde:
- **fs** = frecuencia de muestreo
- **fmax** = frecuencia máxima de la señal

### Ejemplo: Audio CD

**Especificaciones**:
- Frecuencia de muestreo: 44.1 kHz
- Frecuencia máxima: 22.05 kHz
- Resolución: 16 bits

### Aliasing

**Problema**: Si fs < 2fmax, se produce aliasing

**Solución**: Filtro anti-aliasing antes del muestreo

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Funciones Trigonométricas**: sin, cos, tan y sus propiedades
2. **Ondas Sinusoidales**: Parámetros y características
3. **Análisis de Ondas**: Suma, fase, transformaciones
4. **Aplicaciones en Audio**: Generación, modulación, distorsión
5. **Análisis Espectral**: Descomposición en frecuencias

## Próxima Clase

En la siguiente clase estudiaremos el **análisis de Fourier básico**, fundamental para entender el procesamiento de señales de audio.

---

## Navegación
- **Anterior**: [Clase 2.1: Números Complejos y Fasores](clase-2-1-numeros-complejos-fasores.md)
- **Siguiente**: [Clase 2.3: Análisis de Fourier Básico](clase-2-3-analisis-fourier-basico.md)
- **Volver al**: [Índice Principal](../../README.md)
