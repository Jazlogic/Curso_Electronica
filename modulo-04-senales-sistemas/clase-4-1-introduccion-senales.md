# Clase 4.1: Introducción a las Señales

## 🎯 Objetivo de la Clase
Comprender qué son las señales, sus tipos fundamentales y cómo se representan en el dominio temporal y frecuencial, estableciendo las bases para el análisis de sistemas de audio.

## 📋 Contenido de la Clase

### 1. ¿Qué es una Señal?

Una **señal** es cualquier cantidad física que varía con el tiempo y que puede transportar información. En el contexto del audio, las señales representan variaciones de presión sonora, voltaje eléctrico o datos digitales.

#### Definición Matemática
Una señal se puede representar como una función:
```
x(t) = f(t)
```
Donde:
- `x(t)` es la señal en función del tiempo
- `t` es la variable tiempo
- `f(t)` es la función que describe la variación

#### Ejemplo Práctico: Señal de Audio
```python
import numpy as np
import matplotlib.pyplot as plt

# Generar una señal sinusoidal (tono puro)
frecuencia = 440  # Hz (nota La)
amplitud = 1.0
tiempo = np.linspace(0, 0.01, 1000)  # 10ms
señal_audio = amplitud * np.sin(2 * np.pi * frecuencia * tiempo)

plt.figure(figsize=(10, 4))
plt.plot(tiempo * 1000, señal_audio)  # Convertir a ms
plt.title('Señal de Audio: Tono de 440 Hz')
plt.xlabel('Tiempo (ms)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 2. Tipos de Señales

#### 2.1 Señales Analógicas
Las señales analógicas son **continuas** en el tiempo y pueden tomar cualquier valor dentro de un rango.

**Características:**
- Continuas en el tiempo
- Valores continuos (infinitos)
- Representan fenómenos físicos reales
- Ejemplo: voltaje de un micrófono

```python
# Señal analógica continua
tiempo_analogico = np.linspace(0, 1, 10000)  # 10,000 puntos
señal_analogica = np.sin(2 * np.pi * 5 * tiempo_analogico) + 0.5 * np.sin(2 * np.pi * 15 * tiempo_analogico)

plt.figure(figsize=(12, 4))
plt.plot(tiempo_analogico, señal_analogica)
plt.title('Señal Analógica Continua')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

#### 2.2 Señales Digitales
Las señales digitales son **discretas** en el tiempo y toman valores específicos (cuantizados).

**Características:**
- Discretas en el tiempo
- Valores cuantizados (finitos)
- Representan datos procesados
- Ejemplo: archivo de audio MP3

```python
# Señal digital discreta
tiempo_digital = np.arange(0, 1, 0.1)  # Muestreo cada 0.1s
señal_digital = np.sin(2 * np.pi * 5 * tiempo_digital) + 0.5 * np.sin(2 * np.pi * 15 * tiempo_digital)

plt.figure(figsize=(12, 4))
plt.stem(tiempo_digital, señal_digital, basefmt=' ')
plt.title('Señal Digital Discreta')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 3. Dominio Temporal vs Dominio Frecuencial

#### 3.1 Dominio Temporal
Representa cómo varía la señal en función del tiempo.

**Información que proporciona:**
- Amplitud instantánea
- Duración
- Forma de onda
- Transientes

```python
# Análisis en dominio temporal
def analizar_temporal(señal, tiempo, titulo):
    plt.figure(figsize=(12, 6))
    
    # Señal completa
    plt.subplot(2, 1, 1)
    plt.plot(tiempo, señal)
    plt.title(f'{titulo} - Dominio Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Detalle de un ciclo
    plt.subplot(2, 1, 2)
    plt.plot(tiempo[:100], señal[:100])
    plt.title('Detalle de un Ciclo')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

# Ejemplo con señal compleja
tiempo = np.linspace(0, 0.1, 1000)
señal_compleja = (np.sin(2 * np.pi * 100 * tiempo) + 
                 0.5 * np.sin(2 * np.pi * 300 * tiempo) + 
                 0.3 * np.sin(2 * np.pi * 500 * tiempo))

analizar_temporal(señal_compleja, tiempo, 'Señal Compleja')
```

#### 3.2 Dominio Frecuencial
Representa qué frecuencias están presentes en la señal y con qué intensidad.

**Información que proporciona:**
- Contenido espectral
- Armónicos
- Ruido
- Distorsión

```python
# Análisis en dominio frecuencial
def analizar_frecuencial(señal, tiempo, titulo):
    # Calcular FFT
    fft = np.fft.fft(señal)
    frecuencias = np.fft.fftfreq(len(señal), tiempo[1] - tiempo[0])
    
    # Solo frecuencias positivas
    frec_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    
    plt.figure(figsize=(12, 6))
    
    # Espectro de magnitud
    plt.subplot(2, 1, 1)
    plt.plot(frec_positivas, magnitud)
    plt.title(f'{titulo} - Dominio Frecuencial')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud')
    plt.grid(True)
    plt.xlim(0, 1000)  # Limitar a 1 kHz para mejor visualización
    
    # Espectro en dB
    plt.subplot(2, 1, 2)
    magnitud_db = 20 * np.log10(magnitud + 1e-10)  # Evitar log(0)
    plt.plot(frec_positivas, magnitud_db)
    plt.title('Espectro en dB')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    plt.xlim(0, 1000)
    
    plt.tight_layout()
    plt.show()

analizar_frecuencial(señal_compleja, tiempo, 'Señal Compleja')
```

### 4. Propiedades Fundamentales de las Señales

#### 4.1 Amplitud
La amplitud es la magnitud máxima de la señal.

```python
def calcular_amplitud(señal):
    amplitud_pico = np.max(np.abs(señal))
    amplitud_rms = np.sqrt(np.mean(señal**2))
    return amplitud_pico, amplitud_rms

# Ejemplo
amplitud_pico, amplitud_rms = calcular_amplitud(señal_compleja)
print(f"Amplitud pico: {amplitud_pico:.3f}")
print(f"Amplitud RMS: {amplitud_rms:.3f}")
```

#### 4.2 Frecuencia
La frecuencia es el número de ciclos por segundo.

```python
def encontrar_frecuencias_dominantes(señal, tiempo, num_frecuencias=5):
    fft = np.fft.fft(señal)
    frecuencias = np.fft.fftfreq(len(señal), tiempo[1] - tiempo[0])
    
    # Solo frecuencias positivas
    frec_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    
    # Encontrar picos
    indices_picos = np.argsort(magnitud)[-num_frecuencias:]
    frecuencias_dominantes = frec_positivas[indices_picos]
    magnitudes = magnitud[indices_picos]
    
    return frecuencias_dominantes, magnitudes

frecuencias, magnitudes = encontrar_frecuencias_dominantes(señal_compleja, tiempo)
print("Frecuencias dominantes:")
for f, m in zip(frecuencias, magnitudes):
    print(f"  {f:.1f} Hz: {m:.3f}")
```

#### 4.3 Fase
La fase describe el desplazamiento temporal de la señal.

```python
def analizar_fase(señal, tiempo):
    fft = np.fft.fft(señal)
    frecuencias = np.fft.fftfreq(len(señal), tiempo[1] - tiempo[0])
    
    frec_positivas = frecuencias[:len(frecuencias)//2]
    fase = np.angle(fft[:len(fft)//2])
    
    plt.figure(figsize=(10, 4))
    plt.plot(frec_positivas, fase * 180 / np.pi)  # Convertir a grados
    plt.title('Espectro de Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.grid(True)
    plt.xlim(0, 1000)
    plt.show()

analizar_fase(señal_compleja, tiempo)
```

### 5. Señales de Audio Específicas

#### 5.1 Señales Musicales
Las señales musicales son complejas y contienen múltiples frecuencias.

```python
# Simular una nota musical (La4 = 440 Hz)
def generar_nota_musical(frecuencia_fundamental, duracion, fs=44100):
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Frecuencia fundamental + armónicos
    señal = (np.sin(2 * np.pi * frecuencia_fundamental * tiempo) +
             0.5 * np.sin(2 * np.pi * 2 * frecuencia_fundamental * tiempo) +
             0.25 * np.sin(2 * np.pi * 3 * frecuencia_fundamental * tiempo) +
             0.125 * np.sin(2 * np.pi * 4 * frecuencia_fundamental * tiempo))
    
    # Envolvente ADSR (Attack, Decay, Sustain, Release)
    ataque = 0.1
    decaimiento = 0.2
    sostenido = 0.5
    liberacion = 0.2
    
    envolvente = np.ones_like(tiempo)
    
    # Attack
    idx_ataque = int(ataque * len(tiempo))
    envolvente[:idx_ataque] = np.linspace(0, 1, idx_ataque)
    
    # Decay
    idx_decaimiento = int((ataque + decaimiento) * len(tiempo))
    envolvente[idx_ataque:idx_decaimiento] = np.linspace(1, 0.7, idx_decaimiento - idx_ataque)
    
    # Sustain
    idx_sostenido = int((ataque + decaimiento + sostenido) * len(tiempo))
    envolvente[idx_decaimiento:idx_sostenido] = 0.7
    
    # Release
    envolvente[idx_sostenido:] = np.linspace(0.7, 0, len(tiempo) - idx_sostenido)
    
    return tiempo, señal * envolvente

tiempo_nota, nota_la = generar_nota_musical(440, 1.0)

plt.figure(figsize=(12, 6))
plt.subplot(2, 1, 1)
plt.plot(tiempo_nota, nota_la)
plt.title('Nota Musical La4 (440 Hz) con Envolvente ADSR')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 1, 2)
analizar_frecuencial(nota_la, tiempo_nota, 'Nota Musical')
```

#### 5.2 Señales de Voz
Las señales de voz tienen características específicas.

```python
# Simular una señal de voz (formantes)
def simular_voz(frecuencia_fundamental, duracion, fs=44100):
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Frecuencia fundamental (tono de voz)
    f0 = frecuencia_fundamental
    
    # Formantes típicos de vocal "A"
    formante1 = 730  # Hz
    formante2 = 1090  # Hz
    formante3 = 2440  # Hz
    
    # Generar señal con formantes
    señal = (np.sin(2 * np.pi * f0 * tiempo) +
             0.3 * np.sin(2 * np.pi * formante1 * tiempo) +
             0.2 * np.sin(2 * np.pi * formante2 * tiempo) +
             0.1 * np.sin(2 * np.pi * formante3 * tiempo))
    
    # Modulación de amplitud (vibrato)
    vibrato = 1 + 0.1 * np.sin(2 * np.pi * 5 * tiempo)
    señal *= vibrato
    
    return tiempo, señal

tiempo_voz, voz = simular_voz(150, 0.5)  # Voz masculina típica

plt.figure(figsize=(12, 6))
plt.subplot(2, 1, 1)
plt.plot(tiempo_voz, voz)
plt.title('Señal de Voz Simulada')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 1, 2)
analizar_frecuencial(voz, tiempo_voz, 'Señal de Voz')
```

### 6. Aplicaciones en Sistemas de Audio

#### 6.1 Procesamiento de Señales
```python
# Ejemplo: Filtro pasa-bajos simple
def filtro_pasa_bajos(señal, frecuencia_corte, fs):
    # Filtro RC de primer orden
    alpha = 2 * np.pi * frecuencia_corte / fs
    señal_filtrada = np.zeros_like(señal)
    
    for i in range(1, len(señal)):
        señal_filtrada[i] = señal_filtrada[i-1] + alpha * (señal[i] - señal_filtrada[i-1])
    
    return señal_filtrada

# Aplicar filtro a la señal compleja
señal_filtrada = filtro_pasa_bajos(señal_compleja, 200, 10000)  # 200 Hz de corte

plt.figure(figsize=(12, 8))

plt.subplot(3, 1, 1)
plt.plot(tiempo, señal_compleja)
plt.title('Señal Original')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(tiempo, señal_filtrada)
plt.title('Señal Filtrada (Pasa-bajos 200 Hz)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 3)
analizar_frecuencial(señal_filtrada, tiempo, 'Señal Filtrada')

plt.tight_layout()
plt.show()
```

#### 6.2 Análisis de Calidad de Audio
```python
def analizar_calidad_audio(señal, tiempo):
    # Calcular métricas de calidad
    snr = 20 * np.log10(np.std(señal) / (np.std(señal) * 0.01))  # SNR aproximado
    thd = np.std(señal - np.sin(2 * np.pi * 440 * tiempo)) / np.std(señal) * 100  # THD aproximado
    
    print(f"Análisis de Calidad de Audio:")
    print(f"  SNR: {snr:.1f} dB")
    print(f"  THD: {thd:.2f}%")
    print(f"  Rango dinámico: {20 * np.log10(np.max(np.abs(señal)) / np.min(np.abs(señal))):.1f} dB")

analizar_calidad_audio(señal_compleja, tiempo)
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Análisis de Señal Compleja
```python
# Crear una señal con múltiples componentes
def ejercicio_1():
    tiempo = np.linspace(0, 1, 10000)
    
    # Señal con 3 componentes
    señal = (2 * np.sin(2 * np.pi * 50 * tiempo) +
             1 * np.sin(2 * np.pi * 150 * tiempo) +
             0.5 * np.sin(2 * np.pi * 300 * tiempo))
    
    # Agregar ruido
    ruido = 0.1 * np.random.randn(len(tiempo))
    señal_con_ruido = señal + ruido
    
    # Analizar en ambos dominios
    plt.figure(figsize=(15, 10))
    
    # Dominio temporal
    plt.subplot(3, 2, 1)
    plt.plot(tiempo, señal)
    plt.title('Señal Original')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    plt.subplot(3, 2, 2)
    plt.plot(tiempo, señal_con_ruido)
    plt.title('Señal con Ruido')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Dominio frecuencial
    plt.subplot(3, 2, 3)
    analizar_frecuencial(señal, tiempo, 'Señal Original')
    
    plt.subplot(3, 2, 4)
    analizar_frecuencial(señal_con_ruido, tiempo, 'Señal con Ruido')
    
    # Comparación
    plt.subplot(3, 2, 5)
    plt.plot(tiempo, señal, label='Original', alpha=0.7)
    plt.plot(tiempo, señal_con_ruido, label='Con Ruido', alpha=0.7)
    plt.title('Comparación')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_1()
```

### Ejercicio 2: Generador de Señales
```python
def generador_señales(tipo, frecuencia, duracion, fs=44100):
    """
    Generador de diferentes tipos de señales
    """
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    if tipo == 'sinusoidal':
        señal = np.sin(2 * np.pi * frecuencia * tiempo)
    elif tipo == 'cuadrada':
        señal = np.sign(np.sin(2 * np.pi * frecuencia * tiempo))
    elif tipo == 'triangular':
        señal = 2 * np.abs(2 * (frecuencia * tiempo - np.floor(frecuencia * tiempo + 0.5))) - 1
    elif tipo == 'diente_sierra':
        señal = 2 * (frecuencia * tiempo - np.floor(frecuencia * tiempo + 0.5))
    else:
        raise ValueError("Tipo de señal no válido")
    
    return tiempo, señal

# Probar diferentes tipos de señales
tipos = ['sinusoidal', 'cuadrada', 'triangular', 'diente_sierra']
frecuencia = 100  # Hz
duracion = 0.1  # s

plt.figure(figsize=(15, 10))
for i, tipo in enumerate(tipos):
    tiempo, señal = generador_señales(tipo, frecuencia, duracion)
    
    plt.subplot(2, 2, i+1)
    plt.plot(tiempo, señal)
    plt.title(f'Señal {tipo.capitalize()}')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)

plt.tight_layout()
plt.show()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **Señal**: Función que varía con el tiempo y transporta información
2. **Señales Analógicas**: Continuas en tiempo y amplitud
3. **Señales Digitales**: Discretas en tiempo y cuantizadas en amplitud
4. **Dominio Temporal**: Representación de la señal vs tiempo
5. **Dominio Frecuencial**: Representación del contenido espectral
6. **Propiedades**: Amplitud, frecuencia, fase

### Aplicaciones en Audio
- Análisis de señales musicales
- Procesamiento de voz
- Filtrado de señales
- Medición de calidad de audio

### Herramientas Utilizadas
- Python con NumPy y Matplotlib
- Análisis FFT
- Generación de señales
- Visualización de datos

## 🎯 Objetivos Alcanzados
- ✅ Comprender qué son las señales
- ✅ Diferenciar señales analógicas y digitales
- ✅ Analizar señales en dominio temporal y frecuencial
- ✅ Identificar propiedades fundamentales
- ✅ Aplicar conceptos a señales de audio

## 🚀 Navegación

**← Anterior**: [Módulo 3: Programación Básica](../modulo-03-programacion-basica/README.md)
**Siguiente →**: [Clase 4.2: Características de las Señales de Audio](clase-4-2-caracteristicas-audio.md)

---

*Duración de la clase: 3-4 horas*
