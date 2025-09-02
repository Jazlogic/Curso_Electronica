# Clase 5.1: Fundamentos del DSP

## 🎯 Objetivo de la Clase
Entender los conceptos básicos del procesamiento digital de señales (DSP) y cómo se aplican al procesamiento de audio en tiempo real.

## 📚 Contenido de la Clase

### 1. ¿Qué es el Procesamiento Digital de Señales?

El DSP es el procesamiento de señales analógicas usando técnicas digitales. En audio, esto significa convertir señales de audio analógicas a digitales, procesarlas matemáticamente, y convertirlas de vuelta a analógicas.

#### Ventajas del DSP:
- **Precisión**: No hay deriva de componentes
- **Reproducibilidad**: Mismos resultados siempre
- **Flexibilidad**: Fácil modificación de algoritmos
- **Costo**: Más económico en producción masiva

### 2. Sistemas Discretos

Un sistema discreto procesa señales que están definidas solo en instantes específicos de tiempo.

#### Representación Matemática:
```
x[n] = señal de entrada discreta
y[n] = señal de salida discreta
h[n] = respuesta al impulso del sistema
```

#### Propiedades de Sistemas Discretos:

**Linealidad**
```python
# Un sistema es lineal si:
# T[a·x1[n] + b·x2[n]] = a·T[x1[n]] + b·T[x2[n]]

def sistema_lineal(x):
    """Ejemplo de sistema lineal: y[n] = 2x[n] + 3"""
    return 2 * x + 3

def verificar_linealidad():
    x1 = np.array([1, 2, 3])
    x2 = np.array([4, 5, 6])
    a, b = 2, 3
    
    # Lado izquierdo: T[a·x1 + b·x2]
    lado_izq = sistema_lineal(a * x1 + b * x2)
    
    # Lado derecho: a·T[x1] + b·T[x2]
    lado_der = a * sistema_lineal(x1) + b * sistema_lineal(x2)
    
    print(f"Lado izquierdo: {lado_izq}")
    print(f"Lado derecho: {lado_der}")
    print(f"¿Es lineal? {np.allclose(lado_izq, lado_der)}")

verificar_linealidad()
```

**Invariancia Temporal**
```python
# Un sistema es invariante en el tiempo si:
# y[n] = T[x[n]] entonces y[n-k] = T[x[n-k]]

def sistema_invariante(x):
    """Ejemplo de sistema invariante: y[n] = x[n-1]"""
    y = np.zeros_like(x)
    y[1:] = x[:-1]  # Desplazamiento de 1 muestra
    return y

def verificar_invariancia():
    x = np.array([1, 2, 3, 4, 5])
    k = 2  # Desplazamiento
    
    # Aplicar sistema y luego desplazar
    y1 = sistema_invariante(x)
    y1_desplazado = np.zeros_like(x)
    y1_desplazado[k:] = y1[:-k]
    
    # Desplazar entrada y luego aplicar sistema
    x_desplazado = np.zeros_like(x)
    x_desplazado[k:] = x[:-k]
    y2 = sistema_invariante(x_desplazado)
    
    print(f"¿Es invariante? {np.allclose(y1_desplazado, y2)}")

verificar_invariancia()
```

### 3. Convolución Discreta

La convolución es la operación fundamental en DSP. Para sistemas lineales e invariantes (LTI):

```
y[n] = x[n] * h[n] = Σ x[k]·h[n-k]
```

```python
def convolucion_discreta(x, h):
    """
    Implementa convolución discreta
    
    Args:
        x: señal de entrada
        h: respuesta al impulso
    
    Returns:
        y: señal de salida
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

# Ejemplo de uso
x = np.array([1, 2, 3, 4])  # Señal de entrada
h = np.array([0.5, 0.3, 0.2])  # Respuesta al impulso

y = convolucion_discreta(x, h)
print(f"Señal de entrada: {x}")
print(f"Respuesta al impulso: {h}")
print(f"Señal de salida: {y}")

# Verificación con NumPy
y_numpy = np.convolve(x, h)
print(f"Verificación NumPy: {y_numpy}")
```

### 4. Respuesta al Impulso

La respuesta al impulso h[n] caracteriza completamente un sistema LTI.

```python
def respuesta_impulso():
    """Genera y analiza respuesta al impulso"""
    # Impulso unitario
    impulso = np.zeros(10)
    impulso[0] = 1
    
    # Sistema: filtro pasa-bajas simple
    # y[n] = 0.5*x[n] + 0.3*x[n-1] + 0.2*x[n-2]
    h = np.array([0.5, 0.3, 0.2])
    
    # Aplicar sistema al impulso
    respuesta = np.convolve(impulso, h)
    
    # Visualizar
    plt.figure(figsize=(12, 4))
    
    plt.subplot(1, 3, 1)
    plt.stem(impulso)
    plt.title('Impulso Unitario')
    plt.xlabel('n')
    plt.ylabel('Amplitud')
    
    plt.subplot(1, 3, 2)
    plt.stem(h)
    plt.title('Respuesta al Impulso')
    plt.xlabel('n')
    plt.ylabel('Amplitud')
    
    plt.subplot(1, 3, 3)
    plt.stem(respuesta)
    plt.title('Respuesta del Sistema')
    plt.xlabel('n')
    plt.ylabel('Amplitud')
    
    plt.tight_layout()
    plt.show()
    
    return h, respuesta

h, respuesta = respuesta_impulso()
```

### 5. Estabilidad de Sistemas

Un sistema es estable si toda entrada acotada produce una salida acotada.

#### Criterio de Estabilidad:
Un sistema LTI es estable si y solo si:
```
Σ |h[n]| < ∞
```

```python
def verificar_estabilidad(h):
    """
    Verifica la estabilidad de un sistema
    
    Args:
        h: respuesta al impulso
    
    Returns:
        bool: True si es estable
    """
    suma_absoluta = np.sum(np.abs(h))
    return suma_absoluta < np.inf

# Ejemplos
h_estable = np.array([0.5, 0.3, 0.2])  # Decae
h_inestable = np.array([1.5, 1.2, 1.1])  # Crece

print(f"Sistema estable: {verificar_estabilidad(h_estable)}")
print(f"Sistema inestable: {verificar_estabilidad(h_inestable)}")
```

### 6. Implementación en Tiempo Real

Para audio en tiempo real, necesitamos procesar muestras una por una:

```python
class ProcesadorAudioTiempoReal:
    def __init__(self, buffer_size=1024):
        self.buffer_size = buffer_size
        self.buffer = np.zeros(buffer_size)
        self.index = 0
    
    def procesar_muestra(self, muestra):
        """
        Procesa una muestra de audio en tiempo real
        
        Args:
            muestra: muestra de entrada
        
        Returns:
            muestra procesada
        """
        # Ejemplo: filtro pasa-bajas simple
        # y[n] = 0.5*x[n] + 0.3*x[n-1] + 0.2*x[n-2]
        
        # Actualizar buffer
        self.buffer[self.index] = muestra
        
        # Calcular salida
        y = (0.5 * self.buffer[self.index] + 
             0.3 * self.buffer[(self.index-1) % self.buffer_size] + 
             0.2 * self.buffer[(self.index-2) % self.buffer_size])
        
        # Actualizar índice
        self.index = (self.index + 1) % self.buffer_size
        
        return y
    
    def procesar_buffer(self, buffer_entrada):
        """
        Procesa un buffer completo de audio
        
        Args:
            buffer_entrada: array de muestras
        
        Returns:
            buffer procesado
        """
        buffer_salida = np.zeros_like(buffer_entrada)
        
        for i, muestra in enumerate(buffer_entrada):
            buffer_salida[i] = self.procesar_muestra(muestra)
        
        return buffer_salida

# Ejemplo de uso
procesador = ProcesadorAudioTiempoReal()

# Generar señal de prueba
t = np.linspace(0, 1, 44100)  # 1 segundo a 44.1 kHz
frecuencia = 1000  # 1 kHz
senal_entrada = np.sin(2 * np.pi * frecuencia * t)

# Procesar
senal_salida = procesador.procesar_buffer(senal_entrada)

# Visualizar resultado
plt.figure(figsize=(12, 6))
plt.subplot(2, 1, 1)
plt.plot(t[:1000], senal_entrada[:1000])
plt.title('Señal de Entrada')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')

plt.subplot(2, 1, 2)
plt.plot(t[:1000], senal_salida[:1000])
plt.title('Señal Procesada')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')

plt.tight_layout()
plt.show()
```

## 🛠️ Ejercicios Prácticos

### Ejercicio 1: Implementar Sistema LTI
```python
def ejercicio_1():
    """Implementa un sistema LTI personalizado"""
    
    class SistemaLTI:
        def __init__(self, coeficientes):
            """
            Args:
                coeficientes: array con los coeficientes del sistema
            """
            self.coefs = coeficientes
            self.buffer = np.zeros(len(coeficientes))
            self.index = 0
        
        def procesar(self, x):
            """Procesa una muestra"""
            # Actualizar buffer
            self.buffer[self.index] = x
            
            # Calcular salida
            y = 0
            for i, coef in enumerate(self.coefs):
                idx = (self.index - i) % len(self.buffer)
                y += coef * self.buffer[idx]
            
            # Actualizar índice
            self.index = (self.index + 1) % len(self.buffer)
            
            return y
    
    # Crear sistema: y[n] = 0.8*x[n] - 0.2*x[n-1] + 0.1*x[n-2]
    sistema = SistemaLTI([0.8, -0.2, 0.1])
    
    # Probar con impulso
    impulso = np.zeros(10)
    impulso[0] = 1
    
    respuesta = []
    for muestra in impulso:
        respuesta.append(sistema.procesar(muestra))
    
    print(f"Respuesta al impulso: {respuesta}")
    
    return sistema

sistema = ejercicio_1()
```

### Ejercicio 2: Análisis de Estabilidad
```python
def ejercicio_2():
    """Analiza la estabilidad de diferentes sistemas"""
    
    sistemas = {
        'Estable': [0.5, 0.3, 0.2],
        'Inestable': [1.5, 1.2, 1.1],
        'Marginal': [1.0, 0.0, 0.0]
    }
    
    for nombre, h in sistemas.items():
        suma_abs = np.sum(np.abs(h))
        es_estable = suma_abs < np.inf and suma_abs < 10  # Límite práctico
        
        print(f"{nombre}:")
        print(f"  Coeficientes: {h}")
        print(f"  Suma absoluta: {suma_abs:.3f}")
        print(f"  ¿Estable? {es_estable}")
        print()

ejercicio_2()
```

## 📖 Conceptos Clave para Recordar

1. **DSP** = procesamiento digital de señales
2. **Sistema LTI** = lineal e invariante en el tiempo
3. **Convolución** = operación fundamental en DSP
4. **Respuesta al impulso** = caracteriza el sistema
5. **Estabilidad** = entrada acotada → salida acotada
6. **Tiempo real** = procesamiento muestra por muestra

## 🎯 Objetivos Alcanzados

Al final de esta clase, deberías poder:
- [ ] Explicar qué es el DSP y sus ventajas
- [ ] Identificar sistemas lineales e invariantes
- [ ] Implementar convolución discreta
- [ ] Analizar la estabilidad de sistemas
- [ ] Procesar audio en tiempo real

## 📚 Recursos Adicionales

- [Fundamentos de DSP](recursos/fundamentos-dsp.md)
- [Implementación en C](recursos/implementacion-c.md)
- [Optimización de rendimiento](recursos/optimizacion-rendimiento.md)

## 🚀 Navegación

**← Anterior**: [Módulo 5: Procesamiento Digital de Señales](README.md)
**Siguiente →**: [Clase 5.2: Filtros Digitales FIR](clase-5-2-filtros-fir.md)

---

*Duración estimada: 3-4 horas*
*Dificultad: Intermedio*
