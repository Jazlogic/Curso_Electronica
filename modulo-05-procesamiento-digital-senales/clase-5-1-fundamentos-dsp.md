# Clase 5.1: Fundamentos del DSP

## 🎯 Objetivo de la Clase
Comprender los conceptos fundamentales del procesamiento digital de señales aplicados al audio, incluyendo señales discretas, transformada Z y sistemas lineales invariantes en el tiempo.

## 📚 Contenido

### 1. Introducción al DSP

#### ¿Qué es el Procesamiento Digital de Señales?
El **Procesamiento Digital de Señales (DSP)** es la manipulación matemática de señales digitales para extraer información, mejorar la calidad o transformar las características de la señal.

**Características principales:**
- Procesamiento de señales discretas en el tiempo
- Manipulación matemática precisa
- Reproducibilidad y estabilidad
- Flexibilidad en el diseño de algoritmos

#### Ventajas del DSP sobre el procesamiento analógico:
1. **Precisión**: No hay deriva de componentes
2. **Reproducibilidad**: Mismos resultados siempre
3. **Flexibilidad**: Cambios de software sin hardware
4. **Complejidad**: Algoritmos complejos factibles
5. **Integración**: Múltiples funciones en un chip

### 2. Señales Discretas

#### Definición de Señal Discreta
Una **señal discreta** es una secuencia de números que representa valores de una señal en instantes de tiempo específicos.

**Notación matemática:**
```
x[n] = {x[0], x[1], x[2], ..., x[N-1]}
```

Donde:
- `n` es el índice de tiempo discreto
- `x[n]` es el valor de la señal en el instante n
- `N` es la longitud de la señal

#### Tipos de Señales Discretas

**1. Señal Impulso Unitario (Delta de Kronecker):**
```python
def impulso_unitario(n, n0=0):
    """
    Genera un impulso unitario en n0
    δ[n-n0] = 1 si n = n0, 0 en caso contrario
    """
    return 1 if n == n0 else 0

# Ejemplo
import numpy as np
import matplotlib.pyplot as plt

n = np.arange(-5, 6)
impulso = [impulso_unitario(i) for i in n]

plt.figure(figsize=(10, 4))
plt.stem(n, impulso)
plt.title('Impulso Unitario δ[n]')
plt.xlabel('n')
plt.ylabel('δ[n]')
plt.grid(True)
plt.show()
```

**2. Señal Escalón Unitario:**
```python
def escalon_unitario(n):
    """
    Genera un escalón unitario
    u[n] = 1 si n ≥ 0, 0 si n < 0
    """
    return 1 if n >= 0 else 0

# Ejemplo
n = np.arange(-5, 6)
escalon = [escalon_unitario(i) for i in n]

plt.figure(figsize=(10, 4))
plt.stem(n, escalon)
plt.title('Escalón Unitario u[n]')
plt.xlabel('n')
plt.ylabel('u[n]')
plt.grid(True)
plt.show()
```

**3. Señal Exponencial:**
```python
def exponencial_discreta(n, a):
    """
    Genera señal exponencial x[n] = a^n
    """
    return a**n

# Ejemplo: exponencial decreciente
n = np.arange(0, 10)
a = 0.8
exp_signal = [exponencial_discreta(i, a) for i in n]

plt.figure(figsize=(10, 4))
plt.stem(n, exp_signal)
plt.title(f'Señal Exponencial x[n] = {a}^n')
plt.xlabel('n')
plt.ylabel('x[n]')
plt.grid(True)
plt.show()
```

### 3. Transformada Z

#### Definición
La **transformada Z** es una herramienta matemática fundamental en DSP que convierte una señal discreta en el dominio del tiempo al dominio de la frecuencia compleja.

**Definición matemática:**
```
X(z) = Σ(n=-∞ to ∞) x[n] * z^(-n)
```

Donde:
- `X(z)` es la transformada Z de x[n]
- `z` es una variable compleja
- `x[n]` es la señal discreta

#### Región de Convergencia (ROC)
La **Región de Convergencia** es el conjunto de valores de z para los cuales la transformada Z converge.

**Ejemplo de transformada Z:**
```python
import sympy as sp

# Definir variables
z = sp.symbols('z')
n = sp.symbols('n', integer=True)

# Transformada Z del escalón unitario
# u[n] ↔ z/(z-1) para |z| > 1
escalon_z = z/(z-1)
print(f"Transformada Z del escalón unitario: {escalon_z}")

# Transformada Z del impulso unitario
# δ[n] ↔ 1
impulso_z = 1
print(f"Transformada Z del impulso unitario: {impulso_z}")
```

#### Propiedades de la Transformada Z

**1. Linealidad:**
```
a₁x₁[n] + a₂x₂[n] ↔ a₁X₁(z) + a₂X₂(z)
```

**2. Desplazamiento en el tiempo:**
```
x[n-k] ↔ z^(-k)X(z)
```

**3. Convolución:**
```
x₁[n] * x₂[n] ↔ X₁(z)X₂(z)
```

### 4. Sistemas Lineales Invariantes en el Tiempo (LTI)

#### Definición de Sistema LTI
Un **sistema LTI** es aquel que cumple:
- **Linealidad**: T{ax₁[n] + bx₂[n]} = aT{x₁[n]} + bT{x₂[n]}
- **Invarianza temporal**: Si y[n] = T{x[n]}, entonces y[n-k] = T{x[n-k]}

#### Respuesta al Impulso
La **respuesta al impulso** h[n] de un sistema LTI es la salida cuando la entrada es un impulso unitario δ[n].

**Propiedad fundamental:**
```
y[n] = x[n] * h[n] = Σ(k=-∞ to ∞) x[k]h[n-k]
```

#### Ejemplo de Sistema LTI:
```python
def sistema_lti_ejemplo(x, h):
    """
    Implementa un sistema LTI mediante convolución
    y[n] = x[n] * h[n]
    """
    return np.convolve(x, h, mode='full')

# Ejemplo: Filtro promedio móvil
def filtro_promedio_movil(x, N):
    """
    Filtro promedio móvil de N puntos
    h[n] = 1/N para n = 0,1,...,N-1
    """
    h = np.ones(N) / N
    return sistema_lti_ejemplo(x, h)

# Señal de entrada: suma de sinusoides
fs = 1000  # Frecuencia de muestreo
t = np.arange(0, 1, 1/fs)
f1, f2 = 50, 150  # Frecuencias en Hz
x = np.sin(2*np.pi*f1*t) + 0.5*np.sin(2*np.pi*f2*t)

# Aplicar filtro promedio móvil
N = 10
y = filtro_promedio_movil(x, N)

# Visualizar resultados
plt.figure(figsize=(12, 8))

plt.subplot(3, 1, 1)
plt.plot(t, x)
plt.title('Señal de Entrada')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(np.arange(N)/fs, np.ones(N)/N, 'ro-')
plt.title('Respuesta al Impulso del Filtro')
plt.xlabel('Tiempo (s)')
plt.ylabel('h[n]')
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(t, y[:len(t)])
plt.title('Señal Filtrada')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.tight_layout()
plt.show()
```

### 5. Convolución Discreta

#### Definición Matemática
La **convolución discreta** entre dos secuencias x[n] y h[n] se define como:

```
y[n] = x[n] * h[n] = Σ(k=-∞ to ∞) x[k]h[n-k]
```

#### Implementación de Convolución:
```python
def convolucion_discreta(x, h):
    """
    Implementa convolución discreta
    """
    N = len(x)
    M = len(h)
    L = N + M - 1
    y = np.zeros(L)
    
    for n in range(L):
        for k in range(max(0, n-M+1), min(n+1, N)):
            if 0 <= n-k < M:
                y[n] += x[k] * h[n-k]
    
    return y

# Ejemplo de convolución
x = np.array([1, 2, 3, 4])
h = np.array([0.5, 0.5])

y = convolucion_discreta(x, h)
print(f"x[n] = {x}")
print(f"h[n] = {h}")
print(f"y[n] = x[n] * h[n] = {y}")

# Verificar con numpy
y_numpy = np.convolve(x, h)
print(f"Verificación con numpy: {y_numpy}")
```

### 6. Estabilidad de Sistemas

#### Criterio de Estabilidad
Un sistema LTI es **estable** si y solo si su respuesta al impulso es absolutamente sumable:

```
Σ(n=-∞ to ∞) |h[n]| < ∞
```

#### Función de Transferencia
La **función de transferencia** H(z) de un sistema LTI es la transformada Z de su respuesta al impulso:

```
H(z) = Y(z)/X(z) = Σ(n=-∞ to ∞) h[n]z^(-n)
```

#### Ejemplo de Análisis de Estabilidad:
```python
def analizar_estabilidad(h):
    """
    Analiza la estabilidad de un sistema basado en su respuesta al impulso
    """
    suma_absoluta = np.sum(np.abs(h))
    es_estable = suma_absoluta < np.inf
    
    print(f"Respuesta al impulso: {h}")
    print(f"Suma absoluta: {suma_absoluta}")
    print(f"Sistema estable: {es_estable}")
    
    return es_estable

# Ejemplo 1: Sistema estable (filtro promedio móvil)
h_estable = np.ones(5) / 5
analizar_estabilidad(h_estable)

# Ejemplo 2: Sistema inestable (exponencial creciente)
n = np.arange(0, 10)
h_inestable = 2**n
analizar_estabilidad(h_inestable)
```

### 7. Aplicaciones en Audio

#### Procesamiento de Audio Digital
En audio, el DSP se aplica para:

1. **Filtrado**: Eliminar ruido, ecualización
2. **Efectos**: Reverb, delay, chorus
3. **Compresión**: Reducir rango dinámico
4. **Análisis**: Espectro, pitch detection
5. **Síntesis**: Generación de sonidos

#### Ejemplo: Filtro Pasa-Bajas para Audio
```python
def filtro_pasa_bajas_audio(senal, fc, fs):
    """
    Filtro pasa-bajas simple para audio
    fc: frecuencia de corte
    fs: frecuencia de muestreo
    """
    # Coeficientes del filtro (promedio móvil)
    N = int(fs / (2 * fc))
    h = np.ones(N) / N
    
    return np.convolve(senal, h, mode='same')

# Generar señal de audio de prueba
fs = 44100  # Frecuencia de muestreo estándar
t = np.arange(0, 1, 1/fs)
f1, f2 = 1000, 5000  # Frecuencias en Hz

# Señal con dos componentes de frecuencia
audio = np.sin(2*np.pi*f1*t) + 0.3*np.sin(2*np.pi*f2*t)

# Aplicar filtro pasa-bajas
fc = 2000  # Frecuencia de corte
audio_filtrado = filtro_pasa_bajas_audio(audio, fc, fs)

# Análisis espectral
from scipy.fft import fft, fftfreq

# FFT de la señal original
fft_original = fft(audio)
freqs = fftfreq(len(audio), 1/fs)

# FFT de la señal filtrada
fft_filtrado = fft(audio_filtrado)

# Visualizar espectros
plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)
plt.plot(freqs[:len(freqs)//2], np.abs(fft_original[:len(fft_original)//2]))
plt.title('Espectro - Señal Original')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud')
plt.grid(True)

plt.subplot(1, 2, 2)
plt.plot(freqs[:len(freqs)//2], np.abs(fft_filtrado[:len(fft_filtrado)//2]))
plt.title('Espectro - Señal Filtrada')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud')
plt.grid(True)

plt.tight_layout()
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Análisis de Sistema LTI
```python
# Implementar y analizar un sistema LTI con respuesta al impulso:
# h[n] = 0.5^n * u[n]

def ejercicio_sistema_lti():
    n = np.arange(0, 20)
    h = 0.5**n  # Respuesta al impulso
    
    # Verificar estabilidad
    suma_absoluta = np.sum(np.abs(h))
    print(f"Suma absoluta de h[n]: {suma_absoluta}")
    print(f"Sistema estable: {suma_absoluta < np.inf}")
    
    # Calcular respuesta a escalón unitario
    u = np.ones(20)
    y = np.convolve(u, h, mode='full')[:20]
    
    # Visualizar
    plt.figure(figsize=(12, 4))
    
    plt.subplot(1, 3, 1)
    plt.stem(n, h)
    plt.title('Respuesta al Impulso h[n]')
    plt.xlabel('n')
    plt.ylabel('h[n]')
    
    plt.subplot(1, 3, 2)
    plt.stem(n, u)
    plt.title('Entrada: Escalón Unitario')
    plt.xlabel('n')
    plt.ylabel('u[n]')
    
    plt.subplot(1, 3, 3)
    plt.stem(n, y)
    plt.title('Respuesta al Escalón')
    plt.xlabel('n')
    plt.ylabel('y[n]')
    
    plt.tight_layout()
    plt.show()

ejercicio_sistema_lti()
```

### Ejercicio 2: Transformada Z
```python
# Calcular la transformada Z de x[n] = a^n * u[n]

def ejercicio_transformada_z():
    import sympy as sp
    
    z, a = sp.symbols('z a')
    n = sp.symbols('n', integer=True)
    
    # Transformada Z de a^n * u[n]
    # X(z) = z/(z-a) para |z| > |a|
    X_z = z/(z-a)
    
    print(f"Transformada Z de a^n * u[n]: {X_z}")
    print(f"Región de convergencia: |z| > |a|")
    
    # Casos especiales
    print(f"\nPara a = 1 (escalón unitario): {X_z.subs(a, 1)}")
    print(f"Para a = 0.5: {X_z.subs(a, 0.5)}")

ejercicio_transformada_z()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 4.8: Medición de Señales](../modulo-04-senales-sistemas/clase-4-8-medicion-senales.md)
- **Clase Siguiente**: [Clase 5.2: Filtros Digitales FIR](clase-5-2-filtros-digitales-fir.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Oppenheim, A. V., & Schafer, R. W. (2010). *Discrete-Time Signal Processing*
- Proakis, J. G., & Manolakis, D. G. (2006). *Digital Signal Processing*
- Smith, J. O. (2007). *Introduction to Digital Filters*

---
*Duración estimada: 4-5 horas*
