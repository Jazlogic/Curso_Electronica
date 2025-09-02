# Clase 2.1: Números Complejos y Fasores

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué son los números complejos y cómo se representan
- Entender las operaciones básicas con números complejos
- Conocer el concepto de fasores y su aplicación en audio
- Aplicar números complejos en el análisis de circuitos AC
- Entender la relación entre fasores y señales de audio

## Introducción a los Números Complejos

### ¿Qué es un Número Complejo?

Un **número complejo** es un número que puede expresarse en la forma:
```
z = a + bi
```

Donde:
- **a** = parte real
- **b** = parte imaginaria  
- **i** = unidad imaginaria (i² = -1)

### Representación de Números Complejos

#### 1. Forma Rectangular (Cartesiana)
```
z = a + bi
```

**Ejemplo**:
```
z = 3 + 4i
- Parte real: 3
- Parte imaginaria: 4
```

#### 2. Forma Polar
```
z = r∠θ = r(cos θ + i sin θ)
```

Donde:
- **r** = magnitud (módulo)
- **θ** = ángulo (argumento)

**Ejemplo**:
```
z = 5∠53.13°
- Magnitud: 5
- Ángulo: 53.13°
```

#### 3. Forma Exponencial
```
z = re^(iθ)
```

### Conversión entre Formas

#### De Rectangular a Polar
```
r = √(a² + b²)
θ = arctan(b/a)
```

#### De Polar a Rectangular
```
a = r cos θ
b = r sin θ
```

### Ejemplo Práctico
```
Número complejo: z = 3 + 4i

Conversión a polar:
r = √(3² + 4²) = √(9 + 16) = √25 = 5
θ = arctan(4/3) = 53.13°

Forma polar: z = 5∠53.13°
```

## Operaciones con Números Complejos

### 1. Suma y Resta

**Forma Rectangular**:
```
z₁ + z₂ = (a₁ + a₂) + i(b₁ + b₂)
z₁ - z₂ = (a₁ - a₂) + i(b₁ - b₂)
```

**Ejemplo**:
```
z₁ = 3 + 4i
z₂ = 1 + 2i

z₁ + z₂ = (3 + 1) + i(4 + 2) = 4 + 6i
z₁ - z₂ = (3 - 1) + i(4 - 2) = 2 + 2i
```

### 2. Multiplicación

**Forma Rectangular**:
```
z₁ × z₂ = (a₁a₂ - b₁b₂) + i(a₁b₂ + a₂b₁)
```

**Forma Polar**:
```
z₁ × z₂ = r₁r₂∠(θ₁ + θ₂)
```

**Ejemplo**:
```
z₁ = 3 + 4i = 5∠53.13°
z₂ = 1 + 2i = √5∠63.43°

Forma polar:
z₁ × z₂ = 5√5∠(53.13° + 63.43°) = 5√5∠116.56°
```

### 3. División

**Forma Rectangular**:
```
z₁/z₂ = (a₁a₂ + b₁b₂)/(a₂² + b₂²) + i(a₂b₁ - a₁b₂)/(a₂² + b₂²)
```

**Forma Polar**:
```
z₁/z₂ = (r₁/r₂)∠(θ₁ - θ₂)
```

**Ejemplo**:
```
z₁ = 3 + 4i = 5∠53.13°
z₂ = 1 + 2i = √5∠63.43°

Forma polar:
z₁/z₂ = (5/√5)∠(53.13° - 63.43°) = √5∠(-10.3°)
```

### 4. Conjugado Complejo

**Definición**:
```
z* = a - bi
```

**Propiedades**:
```
z × z* = a² + b² = r²
|z| = √(z × z*)
```

## Fasores

### ¿Qué es un Fasor?

Un **fasor** es una representación compleja de una señal sinusoidal que facilita el análisis de circuitos de corriente alterna.

### Representación Fasorial

**Señal sinusoidal**:
```
v(t) = Vm cos(ωt + φ)
```

**Fasor correspondiente**:
```
V = Vm∠φ
```

Donde:
- **Vm** = amplitud máxima
- **φ** = ángulo de fase
- **ω** = frecuencia angular

### Ejemplo de Conversión

**Señal**:
```
v(t) = 10 cos(377t + 30°)
```

**Fasor**:
```
V = 10∠30°
```

### Operaciones con Fasores

#### 1. Suma de Fasores
```
V₁ = 5∠0°
V₂ = 3∠90°

V₁ + V₂ = 5 + 3i = √34∠30.96°
```

#### 2. Multiplicación por Impedancia
```
V = I × Z
```

Donde:
- **V** = fasor de voltaje
- **I** = fasor de corriente
- **Z** = impedancia compleja

## Impedancia Compleja

### Definición

La **impedancia** es la oposición que presenta un circuito al flujo de corriente alterna.

```
Z = R + jX
```

Donde:
- **R** = resistencia (parte real)
- **X** = reactancia (parte imaginaria)
- **j** = unidad imaginaria en ingeniería

### Tipos de Impedancia

#### 1. Resistencia Pura
```
Z = R
```

#### 2. Inductancia Pura
```
Z = jωL = jXL
```

Donde:
- **XL** = reactancia inductiva = ωL

#### 3. Capacitancia Pura
```
Z = 1/(jωC) = -j/(ωC) = -jXC
```

Donde:
- **XC** = reactancia capacitiva = 1/(ωC)

### Ejemplo Práctico

**Circuito RLC en serie**:
```
Z = R + jωL + 1/(jωC)
Z = R + j(ωL - 1/(ωC))
```

**Datos**:
- R = 10Ω
- L = 0.1H
- C = 100μF
- f = 60Hz

**Cálculo**:
```
ω = 2πf = 2π × 60 = 377 rad/s
XL = ωL = 377 × 0.1 = 37.7Ω
XC = 1/(ωC) = 1/(377 × 0.0001) = 26.5Ω

Z = 10 + j(37.7 - 26.5) = 10 + j11.2
|Z| = √(10² + 11.2²) = √(100 + 125.44) = 15Ω
θ = arctan(11.2/10) = 48.2°
Z = 15∠48.2°Ω
```

## Aplicaciones en Audio

### 1. Análisis de Filtros

**Filtro Pasa-Bajo RC**:
```
H(jω) = 1/(1 + jωRC)
```

**Magnitud**:
```
|H(jω)| = 1/√(1 + (ωRC)²)
```

**Fase**:
```
∠H(jω) = -arctan(ωRC)
```

### 2. Análisis de Amplificadores

**Amplificador con retroalimentación**:
```
A(jω) = A₀/(1 + A₀β(jω))
```

Donde:
- **A₀** = ganancia de lazo abierto
- **β** = factor de retroalimentación

### 3. Análisis de Resonancia

**Frecuencia de resonancia**:
```
ω₀ = 1/√(LC)
```

**Factor de calidad**:
```
Q = ω₀L/R = 1/(ω₀CR)
```

## Ejercicios Prácticos

### Ejercicio 1: Conversión de Formas

```
Dado: z = 4 + 3i

Pregunta: Convertir a forma polar

Solución:
r = √(4² + 3²) = √(16 + 9) = √25 = 5
θ = arctan(3/4) = 36.87°

Respuesta: z = 5∠36.87°
```

### Ejercicio 2: Operaciones con Fasores

```
Dados:
V₁ = 10∠0° V
V₂ = 5∠90° V

Pregunta: Calcular V₁ + V₂

Solución:
V₁ = 10 + 0i = 10
V₂ = 0 + 5i = 5i
V₁ + V₂ = 10 + 5i = √125∠26.57° = 11.18∠26.57° V
```

### Ejercicio 3: Análisis de Impedancia

```
Datos:
R = 5Ω
L = 0.02H
C = 100μF
f = 1000Hz

Pregunta: Calcular la impedancia total

Solución:
ω = 2π × 1000 = 6283 rad/s
XL = ωL = 6283 × 0.02 = 125.7Ω
XC = 1/(ωC) = 1/(6283 × 0.0001) = 1.59Ω

Z = 5 + j(125.7 - 1.59) = 5 + j124.1
|Z| = √(5² + 124.1²) = 124.2Ω
θ = arctan(124.1/5) = 87.7°
Z = 124.2∠87.7°Ω
```

## Relación con el Audio

### ¿Por qué son importantes los números complejos en audio?

1. **Análisis de Frecuencia**: Los números complejos permiten analizar cómo responden los circuitos a diferentes frecuencias
2. **Filtros**: El diseño de filtros de audio requiere el uso de números complejos
3. **Fase**: La fase de las señales de audio es crucial para la calidad del sonido
4. **Resonancia**: Los circuitos resonantes se analizan usando números complejos

### Ejemplo Práctico: Filtro de Audio

**Filtro pasa-bajo de primer orden**:
```
H(jω) = 1/(1 + jω/ωc)
```

Donde:
- **ωc** = frecuencia de corte

**Para audio a 1kHz con fc = 500Hz**:
```
ω = 2π × 1000 = 6283 rad/s
ωc = 2π × 500 = 3142 rad/s

H(j6283) = 1/(1 + j6283/3142) = 1/(1 + j2)
|H| = 1/√(1 + 4) = 1/√5 = 0.447
∠H = -arctan(2) = -63.4°
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Números Complejos**: Representación rectangular, polar y exponencial
2. **Operaciones**: Suma, resta, multiplicación, división
3. **Fasores**: Representación compleja de señales sinusoidales
4. **Impedancia**: Análisis de circuitos AC con números complejos
5. **Aplicaciones en Audio**: Filtros, amplificadores, resonancia

## Próxima Clase

En la siguiente clase estudiaremos las **funciones trigonométricas y ondas**, fundamentales para entender las señales de audio.

---

## Navegación
- **Anterior**: [Módulo 1 - Índice](../modulo-01-fundamentos-electronica/README.md)
- **Siguiente**: [Clase 2.2: Funciones Trigonométricas y Ondas](clase-2-2-funciones-trigonometricas-ondas.md)
- **Volver al**: [Índice Principal](../../README.md)
