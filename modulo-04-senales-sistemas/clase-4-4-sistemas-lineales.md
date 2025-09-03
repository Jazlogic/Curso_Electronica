# Clase 4.4: Sistemas Lineales

## 🎯 Objetivo de la Clase
Analizar sistemas lineales e invariantes en el tiempo, incluyendo linealidad, invariancia temporal, respuesta al impulso y convolución, fundamentales para el diseño de sistemas de audio.

## 📋 Contenido de la Clase

### 1. Propiedades de los Sistemas Lineales

#### 1.1 Linealidad
Un sistema es lineal si cumple el principio de superposición:
- **Aditividad**: T[x1(t) + x2(t)] = T[x1(t)] + T[x2(t)]
- **Homogeneidad**: T[ax(t)] = aT[x(t)]

```python
import numpy as np
import matplotlib.pyplot as plt

def demostrar_linealidad():
    """
    Demostrar las propiedades de linealidad
    """
    # Definir sistema lineal (filtro pasa-bajos)
    def sistema_lineal(señal, alpha=0.1):
        salida = np.zeros_like(señal)
        for i in range(1, len(señal)):
            salida[i] = salida[i-1] + alpha * (señal[i] - salida[i-1])
        return salida
    
    # Generar señales de entrada
    tiempo = np.linspace(0, 1, 1000)
    x1 = np.sin(2 * np.pi * 5 * tiempo)
    x2 = np.sin(2 * np.pi * 10 * tiempo)
    a = 2.0
    
    # Aplicar sistema
    y1 = sistema_lineal(x1)
    y2 = sistema_lineal(x2)
    y1_plus_y2 = sistema_lineal(x1 + x2)
    y1_scaled = sistema_lineal(a * x1)
    
    # Verificar aditividad
    aditividad = y1_plus_y2 - (y1 + y2)
    
    # Verificar homogeneidad
    homogeneidad = y1_scaled - (a * y1)
    
    plt.figure(figsize=(15, 10))
    
    # Aditividad
    plt.subplot(2, 3, 1)
    plt.plot(tiempo, x1, label='x1(t)')
    plt.plot(tiempo, x2, label='x2(t)')
    plt.title('Señales de Entrada')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 3, 2)
    plt.plot(tiempo, y1, label='y1(t)')
    plt.plot(tiempo, y2, label='y2(t)')
    plt.plot(tiempo, y1 + y2, '--', label='y1 + y2')
    plt.title('Salidas Individuales')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 3, 3)
    plt.plot(tiempo, aditividad)
    plt.title('Error de Aditividad')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Error')
    plt.grid(True)
    
    # Homogeneidad
    plt.subplot(2, 3, 4)
    plt.plot(tiempo, a * x1, label='a * x1(t)')
    plt.title('Entrada Escalada')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 3, 5)
    plt.plot(tiempo, y1_scaled, label='T[a * x1]')
    plt.plot(tiempo, a * y1, '--', label='a * T[x1]')
    plt.title('Salidas Escaladas')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 3, 6)
    plt.plot(tiempo, homogeneidad)
    plt.title('Error de Homogeneidad')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Error')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    print(f"Error máximo de aditividad: {np.max(np.abs(aditividad)):.2e}")
    print(f"Error máximo de homogeneidad: {np.max(np.abs(homogeneidad)):.2e}")

demostrar_linealidad()
```

#### 1.2 Invariancia Temporal
Un sistema es invariante en el tiempo si un desplazamiento temporal en la entrada produce el mismo desplazamiento en la salida.

```python
def demostrar_invariancia_temporal():
    """
    Demostrar invariancia temporal
    """
    # Sistema invariante (filtro pasa-bajos)
    def sistema_invariante(señal, alpha=0.1):
        salida = np.zeros_like(señal)
        for i in range(1, len(señal)):
            salida[i] = salida[i-1] + alpha * (señal[i] - salida[i-1])
        return salida
    
    # Generar señal de entrada
    tiempo = np.linspace(0, 2, 2000)
    x = np.sin(2 * np.pi * 5 * tiempo) * np.exp(-tiempo)
    
    # Aplicar sistema
    y = sistema_invariante(x)
    
    # Desplazar entrada
    desplazamiento = 0.5  # segundos
    x_desplazada = np.roll(x, int(desplazamiento * 1000))
    y_desplazada = sistema_invariante(x_desplazada)
    
    # Desplazar salida original
    y_desplazada_teorica = np.roll(y, int(desplazamiento * 1000))
    
    plt.figure(figsize=(15, 8))
    
    plt.subplot(2, 2, 1)
    plt.plot(tiempo, x, label='x(t)')
    plt.plot(tiempo, x_desplazada, '--', label=f'x(t-{desplazamiento})')
    plt.title('Entradas')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 2, 2)
    plt.plot(tiempo, y, label='y(t)')
    plt.plot(tiempo, y_desplazada, '--', label=f'y(t-{desplazamiento})')
    plt.title('Salidas')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 2, 3)
    plt.plot(tiempo, y_desplazada, label='T[x(t-τ)]')
    plt.plot(tiempo, y_desplazada_teorica, '--', label='y(t-τ)')
    plt.title('Comparación')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 2, 4)
    error = y_desplazada - y_desplazada_teorica
    plt.plot(tiempo, error)
    plt.title('Error de Invariancia Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Error')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    print(f"Error máximo de invariancia temporal: {np.max(np.abs(error)):.2e}")

demostrar_invariancia_temporal()
```

### 2. Respuesta al Impulso

#### 2.1 Función Delta de Dirac
```python
def generar_impulso_discreto(n, n0):
    """
    Generar impulso discreto
    """
    impulso = np.zeros(n)
    impulso[n0] = 1
    return impulso

def generar_impulso_continuo(tiempo, t0):
    """
    Generar aproximación de impulso continuo
    """
    dt = tiempo[1] - tiempo[0]
    impulso = np.zeros_like(tiempo)
    idx = np.argmin(np.abs(tiempo - t0))
    impulso[idx] = 1 / dt
    return impulso

# Generar impulsos
n = 100
n0 = 50
impulso_discreto = generar_impulso_discreto(n, n0)

tiempo = np.linspace(0, 1, 1000)
t0 = 0.5
impulso_continuo = generar_impulso_continuo(tiempo, t0)

plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)
plt.stem(range(n), impulso_discreto, basefmt=' ')
plt.title('Impulso Discreto δ[n-n₀]')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(1, 2, 2)
plt.plot(tiempo, impulso_continuo)
plt.title('Impulso Continuo δ(t-t₀)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.tight_layout()
plt.show()
```

#### 2.2 Respuesta al Impulso de Sistemas
```python
def sistema_rc(señal, alpha=0.1):
    """
    Sistema RC (filtro pasa-bajos)
    """
    salida = np.zeros_like(señal)
    for i in range(1, len(señal)):
        salida[i] = salida[i-1] + alpha * (señal[i] - salida[i-1])
    return salida

def sistema_derivador(señal, dt):
    """
    Sistema derivador
    """
    salida = np.zeros_like(señal)
    for i in range(1, len(señal)):
        salida[i] = (señal[i] - señal[i-1]) / dt
    return salida

# Generar respuesta al impulso
n = 100
impulso = generar_impulso_discreto(n, 10)

# Aplicar sistemas
respuesta_rc = sistema_rc(impulso, 0.2)
respuesta_derivador = sistema_derivador(impulso, 1.0)

plt.figure(figsize=(15, 8))

plt.subplot(2, 2, 1)
plt.stem(range(n), impulso, basefmt=' ')
plt.title('Impulso de Entrada')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 2)
plt.stem(range(n), respuesta_rc, basefmt=' ')
plt.title('Respuesta al Impulso - Sistema RC')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 3)
plt.stem(range(n), respuesta_derivador, basefmt=' ')
plt.title('Respuesta al Impulso - Derivador')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 4)
# Comparar respuestas
plt.stem(range(n), respuesta_rc, basefmt=' ', label='RC')
plt.stem(range(n), respuesta_derivador, basefmt=' ', label='Derivador')
plt.title('Comparación de Respuestas')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
```

### 3. Convolución

#### 3.1 Convolución Discreta
```python
def convolucion_discreta(x, h):
    """
    Implementar convolución discreta
    """
    n = len(x)
    m = len(h)
    y = np.zeros(n + m - 1)
    
    for i in range(n + m - 1):
        for j in range(max(0, i - m + 1), min(i + 1, n)):
            if i - j < m:
                y[i] += x[j] * h[i - j]
    
    return y

def convolucion_numpy(x, h):
    """
    Convolución usando NumPy
    """
    return np.convolve(x, h, mode='full')

# Ejemplo de convolución
x = np.array([1, 2, 3, 4, 5])
h = np.array([0.5, 0.3, 0.2])

# Convolución manual
y_manual = convolucion_discreta(x, h)

# Convolución con NumPy
y_numpy = convolucion_numpy(x, h)

plt.figure(figsize=(15, 8))

plt.subplot(2, 2, 1)
plt.stem(range(len(x)), x, basefmt=' ')
plt.title('Señal x[n]')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 2)
plt.stem(range(len(h)), h, basefmt=' ')
plt.title('Respuesta al Impulso h[n]')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 3)
plt.stem(range(len(y_manual)), y_manual, basefmt=' ')
plt.title('Convolución Manual')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 4)
plt.stem(range(len(y_numpy)), y_numpy, basefmt=' ')
plt.title('Convolución NumPy')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.tight_layout()
plt.show()

print(f"Convolución manual: {y_manual}")
print(f"Convolución NumPy: {y_numpy}")
print(f"Error: {np.max(np.abs(y_manual - y_numpy)):.2e}")
```

#### 3.2 Convolución en Audio
```python
def aplicar_reverb(señal, respuesta_impulso):
    """
    Aplicar reverberación usando convolución
    """
    return np.convolve(señal, respuesta_impulso, mode='full')

# Generar señal de audio
fs = 44100
tiempo = np.linspace(0, 0.5, int(fs * 0.5))
señal_audio = np.sin(2 * np.pi * 440 * tiempo) * np.exp(-tiempo * 2)

# Generar respuesta al impulso de reverb
tiempo_reverb = np.linspace(0, 1, int(fs * 1))
respuesta_reverb = np.exp(-tiempo_reverb * 3) * np.sin(2 * np.pi * 1000 * tiempo_reverb)

# Aplicar reverb
señal_con_reverb = aplicar_reverb(señal_audio, respuesta_reverb)

plt.figure(figsize=(15, 10))

plt.subplot(3, 1, 1)
plt.plot(tiempo, señal_audio)
plt.title('Señal de Audio Original')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(tiempo_reverb, respuesta_reverb)
plt.title('Respuesta al Impulso de Reverb')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 3)
tiempo_salida = np.linspace(0, len(señal_con_reverb)/fs, len(señal_con_reverb))
plt.plot(tiempo_salida, señal_con_reverb)
plt.title('Señal con Reverb')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.tight_layout()
plt.show()
```

### 4. Sistemas LTI en Audio

#### 4.1 Filtros LTI
```python
def filtro_pasa_bajos(señal, frecuencia_corte, fs):
    """
    Filtro pasa-bajos LTI
    """
    alpha = 2 * np.pi * frecuencia_corte / fs
    salida = np.zeros_like(señal)
    
    for i in range(1, len(señal)):
        salida[i] = salida[i-1] + alpha * (señal[i] - salida[i-1])
    
    return salida

def filtro_pasa_altos(señal, frecuencia_corte, fs):
    """
    Filtro pasa-altos LTI
    """
    alpha = 2 * np.pi * frecuencia_corte / fs
    salida = np.zeros_like(señal)
    
    for i in range(1, len(señal)):
        salida[i] = alpha * (señal[i] - señal[i-1]) + (1 - alpha) * salida[i-1]
    
    return salida

# Aplicar filtros
fs = 44100
tiempo = np.linspace(0, 0.1, int(fs * 0.1))
señal_original = (np.sin(2 * np.pi * 100 * tiempo) + 
                 0.5 * np.sin(2 * np.pi * 1000 * tiempo) + 
                 0.3 * np.sin(2 * np.pi * 5000 * tiempo))

señal_pasa_bajos = filtro_pasa_bajos(señal_original, 500, fs)
señal_pasa_altos = filtro_pasa_altos(señal_original, 500, fs)

plt.figure(figsize=(15, 10))

plt.subplot(3, 1, 1)
plt.plot(tiempo, señal_original)
plt.title('Señal Original')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(tiempo, señal_pasa_bajos)
plt.title('Filtro Pasa-Bajos (500 Hz)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(tiempo, señal_pasa_altos)
plt.title('Filtro Pasa-Altos (500 Hz)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.tight_layout()
plt.show()
```

#### 4.2 Análisis de Estabilidad
```python
def analizar_estabilidad(señal, respuesta_impulso):
    """
    Analizar estabilidad del sistema
    """
    # Calcular suma de la respuesta al impulso
    suma_impulso = np.sum(np.abs(respuesta_impulso))
    
    # Verificar estabilidad BIBO
    es_estable = suma_impulso < np.inf
    
    return es_estable, suma_impulso

# Generar respuestas al impulso
n = 100
impulso = generar_impulso_discreto(n, 10)

# Sistema estable (RC)
respuesta_estable = sistema_rc(impulso, 0.1)

# Sistema inestable (simulado)
respuesta_inestable = np.zeros(n)
for i in range(1, n):
    respuesta_inestable[i] = 1.1 * respuesta_inestable[i-1] + impulso[i]

# Analizar estabilidad
estable, suma_estable = analizar_estabilidad(impulso, respuesta_estable)
inestable, suma_inestable = analizar_estabilidad(impulso, respuesta_inestable)

plt.figure(figsize=(15, 8))

plt.subplot(2, 2, 1)
plt.stem(range(n), respuesta_estable, basefmt=' ')
plt.title(f'Sistema Estable\nSuma: {suma_estable:.2f}')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 2)
plt.stem(range(n), respuesta_inestable, basefmt=' ')
plt.title(f'Sistema Inestable\nSuma: {suma_inestable:.2f}')
plt.xlabel('n')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 3)
plt.plot(range(n), np.cumsum(np.abs(respuesta_estable)))
plt.title('Suma Acumulativa - Estable')
plt.xlabel('n')
plt.ylabel('Suma Acumulativa')
plt.grid(True)

plt.subplot(2, 2, 4)
plt.plot(range(n), np.cumsum(np.abs(respuesta_inestable)))
plt.title('Suma Acumulativa - Inestable')
plt.xlabel('n')
plt.ylabel('Suma Acumulativa')
plt.grid(True)

plt.tight_layout()
plt.show()

print(f"Sistema estable: {estable}")
print(f"Sistema inestable: {inestable}")
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Identificación de Sistemas
```python
def ejercicio_identificacion():
    """
    Identificar si un sistema es LTI
    """
    # Sistema 1: LTI (filtro pasa-bajos)
    def sistema1(señal, alpha=0.1):
        salida = np.zeros_like(señal)
        for i in range(1, len(señal)):
            salida[i] = salida[i-1] + alpha * (señal[i] - salida[i-1])
        return salida
    
    # Sistema 2: No lineal (compresor)
    def sistema2(señal, threshold=0.5):
        salida = np.zeros_like(señal)
        for i in range(len(señal)):
            if np.abs(señal[i]) > threshold:
                salida[i] = np.sign(señal[i]) * threshold + (señal[i] - np.sign(señal[i]) * threshold) * 0.5
            else:
                salida[i] = señal[i]
        return salida
    
    # Sistema 3: Variante en el tiempo
    def sistema3(señal, alpha_base=0.1):
        salida = np.zeros_like(señal)
        for i in range(1, len(señal)):
            alpha = alpha_base * (1 + 0.1 * i / len(señal))  # Alpha cambia con el tiempo
            salida[i] = salida[i-1] + alpha * (señal[i] - salida[i-1])
        return salida
    
    # Generar señal de prueba
    tiempo = np.linspace(0, 1, 1000)
    señal = np.sin(2 * np.pi * 5 * tiempo)
    
    # Aplicar sistemas
    salida1 = sistema1(señal)
    salida2 = sistema2(señal)
    salida3 = sistema3(señal)
    
    plt.figure(figsize=(15, 10))
    
    plt.subplot(2, 2, 1)
    plt.plot(tiempo, señal, label='Entrada')
    plt.plot(tiempo, salida1, label='Salida')
    plt.title('Sistema 1: LTI (Filtro Pasa-Bajos)')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 2, 2)
    plt.plot(tiempo, señal, label='Entrada')
    plt.plot(tiempo, salida2, label='Salida')
    plt.title('Sistema 2: No Lineal (Compresor)')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 2, 3)
    plt.plot(tiempo, señal, label='Entrada')
    plt.plot(tiempo, salida3, label='Salida')
    plt.title('Sistema 3: Variante en el Tiempo')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 2, 4)
    # Comparar respuestas
    plt.plot(tiempo, salida1, label='LTI')
    plt.plot(tiempo, salida2, label='No Lineal')
    plt.plot(tiempo, salida3, label='Variante')
    plt.title('Comparación de Sistemas')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_identificacion()
```

### Ejercicio 2: Diseño de Filtros LTI
```python
def ejercicio_diseño_filtros():
    """
    Diseñar y comparar diferentes filtros LTI
    """
    # Generar señal de prueba
    fs = 44100
    tiempo = np.linspace(0, 0.1, int(fs * 0.1))
    señal = (np.sin(2 * np.pi * 100 * tiempo) + 
             0.5 * np.sin(2 * np.pi * 1000 * tiempo) + 
             0.3 * np.sin(2 * np.pi * 5000 * tiempo))
    
    # Diferentes filtros
    filtros = {
        'Pasa-Bajos': lambda s: filtro_pasa_bajos(s, 500, fs),
        'Pasa-Altos': lambda s: filtro_pasa_altos(s, 500, fs),
        'Pasa-Banda': lambda s: filtro_pasa_bajos(filtro_pasa_altos(s, 200, fs), 2000, fs),
        'Rechaza-Banda': lambda s: s - filtro_pasa_bajos(filtro_pasa_altos(s, 200, fs), 2000, fs)
    }
    
    plt.figure(figsize=(15, 12))
    
    # Señal original
    plt.subplot(3, 2, 1)
    plt.plot(tiempo, señal)
    plt.title('Señal Original')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Aplicar filtros
    for i, (nombre, filtro) in enumerate(filtros.items()):
        señal_filtrada = filtro(señal)
        
        plt.subplot(3, 2, i+2)
        plt.plot(tiempo, señal_filtrada)
        plt.title(f'Filtro {nombre}')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_diseño_filtros()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **Linealidad**: Principio de superposición
2. **Invariancia Temporal**: Comportamiento independiente del tiempo
3. **Respuesta al Impulso**: Caracterización completa del sistema
4. **Convolución**: Operación fundamental en sistemas LTI
5. **Estabilidad**: Suma finita de la respuesta al impulso

### Aplicaciones en Audio
- Diseño de filtros
- Procesamiento de señales
- Análisis de sistemas
- Implementación de efectos

### Herramientas Utilizadas
- Análisis de linealidad
- Verificación de invariancia temporal
- Cálculo de convolución
- Análisis de estabilidad

## 🎯 Objetivos Alcanzados
- ✅ Comprender propiedades de sistemas lineales
- ✅ Analizar invariancia temporal
- ✅ Calcular respuesta al impulso
- ✅ Implementar convolución
- ✅ Diseñar filtros LTI

## 🚀 Navegación

**← Anterior**: [Clase 4.3: Muestreo y Cuantización](clase-4-3-muestreo-cuantizacion.md)
**Siguiente →**: [Clase 4.5: Respuesta en Frecuencia](clase-4-5-respuesta-frecuencia.md)

---

*Duración de la clase: 3-4 horas*
