# Clase 2.5: Convolución y Correlación

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué es la convolución y cómo se calcula
- Entender la correlación y sus aplicaciones
- Conocer las propiedades de la convolución
- Aplicar convolución en el procesamiento de audio
- Entender la relación entre convolución y filtros

## Introducción a la Convolución

### ¿Qué es la Convolución?

La **convolución** es una operación matemática que combina dos señales para producir una tercera señal. Es fundamental en el procesamiento de señales y el análisis de sistemas.

### Definición Matemática

**Convolución continua**:
```
(f * g)(t) = ∫[-∞ to ∞] f(τ)g(t-τ) dτ
```

**Convolución discreta**:
```
(f * g)[n] = Σ[k=-∞ to ∞] f[k]g[n-k]
```

### Interpretación Física

**Concepto**: La convolución representa cómo una señal se "dispersa" o "filtra" a través de un sistema.

**Ejemplo**: Si f(t) es una señal de entrada y g(t) es la respuesta al impulso de un sistema, entonces (f * g)(t) es la salida del sistema.

## Propiedades de la Convolución

### 1. Conmutatividad
```
f * g = g * f
```

### 2. Asociatividad
```
(f * g) * h = f * (g * h)
```

### 3. Distributividad
```
f * (g + h) = f * g + f * h
```

### 4. Elemento Identidad
```
f * δ = f
```

Donde δ es el impulso unitario.

### 5. Escalamiento
```
(af) * g = a(f * g)
```

## Cálculo de Convolución

### Método Gráfico

**Pasos**:
1. Reflejar una de las señales
2. Desplazar la señal reflejada
3. Multiplicar las señales superpuestas
4. Integrar (o sumar) el resultado
5. Repetir para todos los desplazamientos

### Ejemplo Práctico

**Señales**:
```
f[n] = [1, 2, 3]
g[n] = [1, 1]
```

**Cálculo paso a paso**:

**n = 0**:
```
f[0]g[0] = 1 × 1 = 1
(f * g)[0] = 1
```

**n = 1**:
```
f[0]g[1] + f[1]g[0] = 1 × 1 + 2 × 1 = 3
(f * g)[1] = 3
```

**n = 2**:
```
f[1]g[1] + f[2]g[0] = 2 × 1 + 3 × 1 = 5
(f * g)[2] = 5
```

**n = 3**:
```
f[2]g[1] = 3 × 1 = 3
(f * g)[3] = 3
```

**Resultado**: (f * g)[n] = [1, 3, 5, 3]

### Método de la Matriz

**Matriz de convolución**:
```
[f[0] 0    0  ]
[f[1] f[0] 0  ]
[f[2] f[1] f[0]]
[0    f[2] f[1]]
[0    0    f[2]]
```

**Multiplicación por g**:
```
[1]   [1]
[2]   [3]
[3] × [1] = [5]
[0]   [3]
[0]   [0]
```

## Convolución en el Dominio de la Frecuencia

### Teorema de Convolución

**En el dominio temporal**:
```
(f * g)(t) ↔ F(ω)G(ω)
```

**En el dominio frecuencial**:
```
f(t)g(t) ↔ (1/2π)(F * G)(ω)
```

### Ventajas del Dominio de la Frecuencia

1. **Eficiencia**: FFT es más rápida que convolución directa
2. **Implementación**: Más fácil en procesadores digitales
3. **Análisis**: Mejor comprensión del comportamiento

### Ejemplo: Filtrado en Frecuencia

**Proceso**:
```
1. FFT de la señal de entrada
2. FFT de la respuesta al impulso
3. Multiplicación en el dominio de la frecuencia
4. IFFT del resultado
```

## Correlación

### ¿Qué es la Correlación?

La **correlación** mide la similitud entre dos señales. Es útil para detectar patrones, sincronización y análisis de señales.

### Definición Matemática

**Correlación cruzada**:
```
(f ⋆ g)(t) = ∫[-∞ to ∞] f*(τ)g(t+τ) dτ
```

**Correlación cruzada discreta**:
```
(f ⋆ g)[n] = Σ[k=-∞ to ∞] f*[k]g[n+k]
```

**Autocorrelación**:
```
(f ⋆ f)(t) = ∫[-∞ to ∞] f*(τ)f(t+τ) dτ
```

### Propiedades de la Correlación

#### 1. No es Conmutativa
```
f ⋆ g ≠ g ⋆ f
```

#### 2. Relación con Convolución
```
f ⋆ g = f* * g(-t)
```

#### 3. Autocorrelación
```
(f ⋆ f)(0) = ∫|f(t)|² dt = Energía de la señal
```

## Aplicaciones en Audio

### 1. Filtrado de Audio

**Convolución con respuesta al impulso**:
```
y[n] = x[n] * h[n]
```

Donde:
- **x[n]** = señal de audio
- **h[n]** = respuesta al impulso del filtro
- **y[n]** = señal filtrada

### 2. Reverb Digital

**Simulación de espacios acústicos**:
```
y[n] = x[n] * r[n]
```

Donde **r[n]** es la respuesta al impulso del espacio.

**Características**:
- Múltiples reflexiones
- Decaimiento exponencial
- Efectos de difusión

### 3. Ecualización

**Filtros de ecualización**:
```
y[n] = x[n] * eq[n]
```

Donde **eq[n]** es la respuesta al impulso del ecualizador.

### 4. Detección de Pitch

**Autocorrelación**:
```
R[n] = Σ[k=0 to N-1] x[k]x[k+n]
```

**Detección**:
- Encontrar picos en R[n]
- El primer pico indica el período
- Frecuencia = fs/período

### 5. Sincronización de Audio

**Correlación cruzada**:
```
R[n] = Σ[k=0 to N-1] x[k]y[k+n]
```

**Aplicación**:
- Alineación temporal de señales
- Detección de retrasos
- Sincronización de múltiples fuentes

## Implementación Práctica

### Convolución Rápida

**Algoritmo**:
```
1. Calcular FFT de ambas señales
2. Multiplicar en el dominio de la frecuencia
3. Calcular IFFT del resultado
```

**Ventajas**:
- Complejidad O(N log N) vs O(N²)
- Eficiente para señales largas
- Implementación en hardware

### Convolución Circular

**Problema**: Aliasing en el dominio de la frecuencia
**Solución**: Zero-padding para evitar solapamiento

**Proceso**:
```
1. Añadir ceros a ambas señales
2. Calcular FFT
3. Multiplicar
4. Calcular IFFT
5. Extraer la parte válida
```

### Ejemplo de Implementación

**Pseudocódigo**:
```
function convolucion_rapida(x, h):
    N = length(x) + length(h) - 1
    N = next_power_of_2(N)
    
    x_padded = zero_pad(x, N)
    h_padded = zero_pad(h, N)
    
    X = fft(x_padded)
    H = fft(h_padded)
    
    Y = X * H
    
    y = ifft(Y)
    
    return y[0:length(x)+length(h)-1]
```

## Análisis de Sistemas

### Respuesta al Impulso

**Definición**: La respuesta al impulso h(t) es la salida de un sistema cuando la entrada es un impulso unitario δ(t).

**Relación con convolución**:
```
y(t) = x(t) * h(t)
```

### Caracterización de Sistemas

#### 1. Sistemas Lineales e Invariantes en el Tiempo (LTI)
- **Linealidad**: Superposición
- **Invarianza**: Comportamiento independiente del tiempo
- **Caracterización**: Completamente descrito por h(t)

#### 2. Sistemas Causal
```
h(t) = 0 para t < 0
```

#### 3. Sistemas Estables
```
∫|h(t)| dt < ∞
```

### Ejemplo: Filtro Pasa-Bajo

**Respuesta al impulso**:
```
h(t) = (1/RC)e^(-t/RC)u(t)
```

**Características**:
- Causal (h(t) = 0 para t < 0)
- Estable (integral finita)
- Filtro pasa-bajo

## Ejercicios Prácticos

### Ejercicio 1: Convolución Básica

```
Dadas las señales:
x[n] = [1, 2, 3]
h[n] = [1, 1, 1]

Pregunta: Calcular x[n] * h[n]

Solución:
n = 0: x[0]h[0] = 1 × 1 = 1
n = 1: x[0]h[1] + x[1]h[0] = 1 × 1 + 2 × 1 = 3
n = 2: x[0]h[2] + x[1]h[1] + x[2]h[0] = 1 × 1 + 2 × 1 + 3 × 1 = 6
n = 3: x[1]h[2] + x[2]h[1] = 2 × 1 + 3 × 1 = 5
n = 4: x[2]h[2] = 3 × 1 = 3

Resultado: [1, 3, 6, 5, 3]
```

### Ejercicio 2: Autocorrelación

```
Dada la señal: x[n] = [1, 2, 3, 2, 1]

Pregunta: Calcular la autocorrelación R[0]

Solución:
R[0] = Σ[k=0 to 4] x[k]x[k] = 1² + 2² + 3² + 2² + 1²
R[0] = 1 + 4 + 9 + 4 + 1 = 19
```

### Ejercicio 3: Filtrado

```
Señal de entrada: x[n] = [1, 0, 1, 0, 1]
Filtro: h[n] = [0.5, 0.5]

Pregunta: Calcular la salida filtrada

Solución:
y[n] = x[n] * h[n]

n = 0: x[0]h[0] = 1 × 0.5 = 0.5
n = 1: x[0]h[1] + x[1]h[0] = 1 × 0.5 + 0 × 0.5 = 0.5
n = 2: x[1]h[1] + x[2]h[0] = 0 × 0.5 + 1 × 0.5 = 0.5
n = 3: x[2]h[1] + x[3]h[0] = 1 × 0.5 + 0 × 0.5 = 0.5
n = 4: x[3]h[1] + x[4]h[0] = 0 × 0.5 + 1 × 0.5 = 0.5
n = 5: x[4]h[1] = 1 × 0.5 = 0.5

Resultado: [0.5, 0.5, 0.5, 0.5, 0.5, 0.5]
```

## Relación con el Audio Digital

### Latencia en Convolución

**Problema**: La convolución introduce retraso
**Solución**: 
- Convolución circular con solapamiento
- Procesamiento en bloques
- Optimización de algoritmos

### Eficiencia Computacional

**Consideraciones**:
- Tamaño de la respuesta al impulso
- Longitud de la señal de entrada
- Recursos de procesamiento disponibles

### Implementación en Tiempo Real

**Desafíos**:
- Latencia mínima
- Eficiencia computacional
- Calidad de audio

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Convolución**: Operación fundamental en procesamiento de señales
2. **Propiedades**: Conmutatividad, asociatividad, distributividad
3. **Cálculo**: Métodos gráfico, matricial y en frecuencia
4. **Correlación**: Medida de similitud entre señales
5. **Aplicaciones en Audio**: Filtrado, reverb, ecualización, detección de pitch

## Próxima Clase

En la siguiente clase estudiaremos la **estadística y análisis de ruido**, fundamentales para entender la calidad del audio y el diseño de sistemas de alta fidelidad.

---

## Navegación
- **Anterior**: [Clase 2.4: Transformada Z y Sistemas Discretos](clase-2-4-transformada-z-sistemas-discretos.md)
- **Siguiente**: [Clase 2.6: Estadística y Análisis de Ruido](clase-2-6-estadistica-analisis-ruido.md)
- **Volver al**: [Índice Principal](../../README.md)
