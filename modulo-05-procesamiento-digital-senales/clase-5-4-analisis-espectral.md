# Clase 5.4: Análisis Espectral

## 🎯 Objetivo de la Clase
Implementar análisis espectral en tiempo real para audio, incluyendo FFT, ventanas espectrales, análisis de frecuencia y espectrogramas para aplicaciones de ultra baja latencia.

## 📚 Contenido

### 1. Introducción al Análisis Espectral

#### ¿Qué es el Análisis Espectral?
El **análisis espectral** es el proceso de descomponer una señal en sus componentes de frecuencia, permitiendo analizar el contenido espectral de señales de audio en tiempo real.

**Aplicaciones en audio:**
- Análisis de frecuencia en tiempo real
- Detección de pitch
- Análisis de armónicos
- Ecualización automática
- Detección de instrumentos
- Análisis de ruido

#### Transformada de Fourier Discreta (DFT)
La DFT convierte una señal del dominio del tiempo al dominio de la frecuencia:

```
X[k] = Σ(n=0 to N-1) x[n] * e^(-j2πkn/N)
```

Donde:
- `X[k]` es el coeficiente de frecuencia k
- `x[n]` es la muestra en el tiempo n
- `N` es el tamaño de la ventana

### 2. Transformada Rápida de Fourier (FFT)

#### Algoritmo FFT
La FFT es una implementación eficiente de la DFT que reduce la complejidad computacional de O(N²) a O(N log N).

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.fft import fft, fftfreq, ifft
from scipy import signal

def analisis_fft_basico(señal, fs):
    """
    Análisis FFT básico de una señal
    """
    N = len(señal)
    
    # Calcular FFT
    X = fft(señal)
    freqs = fftfreq(N, 1/fs)
    
    # Solo frecuencias positivas
    freqs_pos = freqs[:N//2]
    X_pos = X[:N//2]
    
    # Magnitud y fase
    magnitud = np.abs(X_pos)
    fase = np.angle(X_pos)
    
    return freqs_pos, magnitud, fase

# Ejemplo: Análisis de señal compuesta
fs = 44100
t = np.arange(0, 1, 1/fs)

# Señal con múltiples frecuencias
f1, f2, f3 = 440, 880, 1320  # Hz
señal = (np.sin(2*np.pi*f1*t) + 
         0.5*np.sin(2*np.pi*f2*t) + 
         0.3*np.sin(2*np.pi*f3*t))

# Análisis FFT
freqs, magnitud, fase = analisis_fft_basico(señal, fs)

# Visualizar
plt.figure(figsize=(12, 8))

plt.subplot(3, 1, 1)
plt.plot(t, señal)
plt.title('Señal en el Dominio del Tiempo')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(freqs, magnitud)
plt.title('Espectro de Magnitud')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud')
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(freqs, fase)
plt.title('Espectro de Fase')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Fase (rad)')
plt.grid(True)

plt.tight_layout()
plt.show()
```

#### Implementación de FFT Optimizada
```python
class AnalizadorEspectral:
    def __init__(self, tamaño_ventana, fs):
        """
        Analizador espectral optimizado para tiempo real
        
        tamaño_ventana: tamaño de la ventana FFT
        fs: frecuencia de muestreo
        """
        self.N = tamaño_ventana
        self.fs = fs
        self.buffer = np.zeros(self.N, dtype=np.float32)
        self.indice = 0
        
        # Pre-calcular ventana de Hamming
        self.ventana = np.hamming(self.N).astype(np.float32)
        
        # Pre-calcular frecuencias
        self.freqs = np.fft.fftfreq(self.N, 1/fs)[:self.N//2]
    
    def procesar_muestra(self, muestra):
        """
        Procesa una muestra y actualiza el buffer
        """
        self.buffer[self.indice] = muestra
        self.indice = (self.indice + 1) % self.N
    
    def obtener_espectro(self):
        """
        Calcula el espectro de la ventana actual
        """
        # Aplicar ventana
        señal_ventaneada = self.buffer * self.ventana
        
        # Calcular FFT
        X = np.fft.fft(señal_ventaneada)
        X_pos = X[:self.N//2]
        
        # Magnitud en dB
        magnitud_db = 20 * np.log10(np.abs(X_pos) + 1e-10)
        
        return self.freqs, magnitud_db
    
    def obtener_espectro_completo(self):
        """
        Obtiene espectro completo (magnitud y fase)
        """
        señal_ventaneada = self.buffer * self.ventana
        X = np.fft.fft(señal_ventaneada)
        X_pos = X[:self.N//2]
        
        magnitud = np.abs(X_pos)
        fase = np.angle(X_pos)
        
        return self.freqs, magnitud, fase

# Ejemplo de uso
analizador = AnalizadorEspectral(1024, 44100)

# Procesar señal
t = np.arange(0, 2, 1/44100)
f1, f2 = 1000, 2000
señal = np.sin(2*np.pi*f1*t) + 0.5*np.sin(2*np.pi*f2*t)

# Llenar buffer
for muestra in señal:
    analizador.procesar_muestra(muestra)

# Obtener espectro
freqs, magnitud_db = analizador.obtener_espectro()

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(freqs, magnitud_db)
plt.title('Espectro de Audio en Tiempo Real')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.grid(True)
plt.axvline(f1, color='r', linestyle='--', label=f'f1 = {f1} Hz')
plt.axvline(f2, color='g', linestyle='--', label=f'f2 = {f2} Hz')
plt.legend()
plt.show()
```

### 3. Ventanas Espectrales

#### Tipos de Ventanas
Las ventanas espectrales se usan para reducir el efecto de leakage en el análisis FFT.

```python
def comparar_ventanas_espectrales():
    """
    Compara diferentes tipos de ventanas espectrales
    """
    N = 1024
    ventanas = {
        'Rectangular': np.ones(N),
        'Hamming': np.hamming(N),
        'Hanning': np.hanning(N),
        'Blackman': np.blackman(N),
        'Kaiser': np.kaiser(N, 8.6)
    }
    
    # Señal de prueba: dos sinusoides
    t = np.arange(N) / 44100
    f1, f2 = 1000, 1001  # Frecuencias muy cercanas
    señal = np.sin(2*np.pi*f1*t) + 0.5*np.sin(2*np.pi*f2*t)
    
    plt.figure(figsize=(12, 10))
    
    for i, (nombre, ventana) in enumerate(ventanas.items()):
        # Aplicar ventana
        señal_ventaneada = señal * ventana
        
        # FFT
        X = np.fft.fft(señal_ventaneada)
        freqs = np.fft.fftfreq(N, 1/44100)[:N//2]
        magnitud = np.abs(X[:N//2])
        
        plt.subplot(3, 2, i+1)
        plt.plot(freqs, 20*np.log10(magnitud + 1e-10))
        plt.title(f'Ventana {nombre}')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.grid(True)
        plt.xlim([900, 1100])
        plt.ylim([-100, 20])
    
    plt.tight_layout()
    plt.show()

comparar_ventanas_espectrales()
```

#### Selección de Ventana para Audio
```python
def seleccionar_ventana_audio(tipo_analisis, N):
    """
    Selecciona la ventana óptima según el tipo de análisis
    
    tipo_analisis: 'frecuencia', 'armonicos', 'ruido', 'tiempo_real'
    N: tamaño de la ventana
    """
    if tipo_analisis == 'frecuencia':
        # Para análisis de frecuencia precisa
        return np.kaiser(N, 8.6)  # Kaiser con β=8.6
    elif tipo_analisis == 'armonicos':
        # Para análisis de armónicos
        return np.hanning(N)  # Hanning para buena resolución
    elif tipo_analisis == 'ruido':
        # Para análisis de ruido
        return np.blackman(N)  # Blackman para mejor supresión de sidelobes
    elif tipo_analisis == 'tiempo_real':
        # Para procesamiento en tiempo real
        return np.hamming(N)  # Hamming para balance entre resolución y eficiencia
    else:
        return np.hamming(N)  # Por defecto

# Ejemplo de uso
N = 1024
ventana = seleccionar_ventana_audio('tiempo_real', N)

plt.figure(figsize=(10, 6))
plt.plot(ventana)
plt.title('Ventana Seleccionada para Tiempo Real')
plt.xlabel('Muestra')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 4. Análisis de Frecuencia en Tiempo Real

#### Detector de Frecuencia Fundamental
```python
class DetectorFrecuencia:
    def __init__(self, tamaño_ventana, fs, rango_frecuencias=(80, 2000)):
        """
        Detector de frecuencia fundamental en tiempo real
        
        tamaño_ventana: tamaño de la ventana FFT
        fs: frecuencia de muestreo
        rango_frecuencias: rango de frecuencias a analizar (Hz)
        """
        self.N = tamaño_ventana
        self.fs = fs
        self.fmin, self.fmax = rango_frecuencias
        
        # Buffer circular
        self.buffer = np.zeros(self.N, dtype=np.float32)
        self.indice = 0
        
        # Ventana
        self.ventana = np.hamming(self.N).astype(np.float32)
        
        # Frecuencias
        self.freqs = np.fft.fftfreq(self.N, 1/fs)[:self.N//2]
        
        # Índices del rango de frecuencias
        self.idx_min = np.argmin(np.abs(self.freqs - self.fmin))
        self.idx_max = np.argmin(np.abs(self.freqs - self.fmax))
    
    def procesar_muestra(self, muestra):
        """
        Procesa una muestra de audio
        """
        self.buffer[self.indice] = muestra
        self.indice = (self.indice + 1) % self.N
    
    def detectar_frecuencia(self):
        """
        Detecta la frecuencia fundamental
        """
        # Aplicar ventana
        señal_ventaneada = self.buffer * self.ventana
        
        # FFT
        X = np.fft.fft(señal_ventaneada)
        X_pos = X[:self.N//2]
        
        # Magnitud en el rango de interés
        magnitud = np.abs(X_pos[self.idx_min:self.idx_max])
        freqs_rango = self.freqs[self.idx_min:self.idx_max]
        
        # Encontrar pico máximo
        idx_pico = np.argmax(magnitud)
        frecuencia_detectada = freqs_rango[idx_pico]
        
        return frecuencia_detectada, magnitud, freqs_rango

# Ejemplo de uso
detector = DetectorFrecuencia(2048, 44100, (80, 2000))

# Señal de prueba (nota musical)
t = np.arange(0, 2, 1/44100)
f0 = 440  # La4
# Agregar armónicos
señal = (np.sin(2*np.pi*f0*t) + 
         0.5*np.sin(2*np.pi*2*f0*t) + 
         0.3*np.sin(2*np.pi*3*f0*t) + 
         0.1*np.sin(2*np.pi*4*f0*t))

# Procesar señal
frecuencias_detectadas = []
for muestra in señal:
    detector.procesar_muestra(muestra)
    if detector.indice == 0:  # Ventana completa
        freq, magnitud, freqs = detector.detectar_frecuencia()
        frecuencias_detectadas.append(freq)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(frecuencias_detectadas)
plt.axhline(y=f0, color='r', linestyle='--', label=f'Frecuencia real: {f0} Hz')
plt.title('Detección de Frecuencia Fundamental')
plt.xlabel('Ventana')
plt.ylabel('Frecuencia Detectada (Hz)')
plt.legend()
plt.grid(True)
plt.show()
```

#### Análisis de Armónicos
```python
class AnalizadorArmonicos:
    def __init__(self, tamaño_ventana, fs, num_armonicos=10):
        """
        Analizador de armónicos en tiempo real
        """
        self.N = tamaño_ventana
        self.fs = fs
        self.num_armonicos = num_armonicos
        
        self.buffer = np.zeros(self.N, dtype=np.float32)
        self.indice = 0
        self.ventana = np.hamming(self.N).astype(np.float32)
        self.freqs = np.fft.fftfreq(self.N, 1/fs)[:self.N//2]
    
    def procesar_muestra(self, muestra):
        """
        Procesa una muestra
        """
        self.buffer[self.indice] = muestra
        self.indice = (self.indice + 1) % self.N
    
    def analizar_armonicos(self, frecuencia_fundamental):
        """
        Analiza los armónicos de una frecuencia fundamental
        """
        # Aplicar ventana
        señal_ventaneada = self.buffer * self.ventana
        
        # FFT
        X = np.fft.fft(señal_ventaneada)
        X_pos = X[:self.N//2]
        magnitud = np.abs(X_pos)
        
        # Encontrar armónicos
        armonicos = []
        amplitudes = []
        
        for i in range(1, self.num_armonicos + 1):
            freq_armonico = i * frecuencia_fundamental
            
            # Encontrar índice más cercano
            idx = np.argmin(np.abs(self.freqs - freq_armonico))
            
            if idx < len(magnitud):
                armonicos.append(freq_armonico)
                amplitudes.append(magnitud[idx])
        
        return armonicos, amplitudes

# Ejemplo de uso
analizador = AnalizadorArmonicos(2048, 44100, 8)

# Señal con armónicos
t = np.arange(0, 1, 1/44100)
f0 = 220  # A3
señal = np.zeros_like(t)
for i in range(1, 9):
    señal += (1/i) * np.sin(2*np.pi*i*f0*t)

# Procesar
for muestra in señal:
    analizador.procesar_muestra(muestra)

# Analizar armónicos
armonicos, amplitudes = analizador.analizar_armonicos(f0)

# Visualizar
plt.figure(figsize=(12, 6))
plt.bar(armonicos, amplitudes)
plt.title('Análisis de Armónicos')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Amplitud')
plt.grid(True, axis='y')
plt.show()
```

### 5. Espectrogramas

#### Espectrograma en Tiempo Real
```python
class EspectrogramaTiempoReal:
    def __init__(self, tamaño_ventana, fs, solapamiento=0.5):
        """
        Generador de espectrogramas en tiempo real
        
        tamaño_ventana: tamaño de la ventana FFT
        fs: frecuencia de muestreo
        solapamiento: fracción de solapamiento entre ventanas
        """
        self.N = tamaño_ventana
        self.fs = fs
        self.solapamiento = solapamiento
        self.paso = int(self.N * (1 - solapamiento))
        
        self.buffer = np.zeros(self.N, dtype=np.float32)
        self.indice = 0
        self.ventana = np.hamming(self.N).astype(np.float32)
        
        # Matriz de espectrograma
        self.espectrograma = []
        self.tiempos = []
        
        # Frecuencias
        self.freqs = np.fft.fftfreq(self.N, 1/fs)[:self.N//2]
    
    def procesar_muestra(self, muestra):
        """
        Procesa una muestra y actualiza el espectrograma
        """
        self.buffer[self.indice] = muestra
        self.indice = (self.indice + 1) % self.N
        
        # Si hemos llenado una ventana
        if self.indice == 0:
            self._actualizar_espectrograma()
    
    def _actualizar_espectrograma(self):
        """
        Actualiza el espectrograma con la ventana actual
        """
        # Aplicar ventana
        señal_ventaneada = self.buffer * self.ventana
        
        # FFT
        X = np.fft.fft(señal_ventaneada)
        X_pos = X[:self.N//2]
        magnitud_db = 20 * np.log10(np.abs(X_pos) + 1e-10)
        
        # Agregar al espectrograma
        self.espectrograma.append(magnitud_db)
        self.tiempos.append(len(self.espectrograma) * self.paso / self.fs)
    
    def obtener_espectrograma(self):
        """
        Obtiene el espectrograma completo
        """
        if not self.espectrograma:
            return None, None, None
        
        espectrograma_array = np.array(self.espectrograma).T
        return self.freqs, self.tiempos, espectrograma_array
    
    def visualizar(self):
        """
        Visualiza el espectrograma
        """
        freqs, tiempos, espectro = self.obtener_espectrograma()
        
        if espectro is None:
            print("No hay datos para visualizar")
            return
        
        plt.figure(figsize=(12, 8))
        plt.imshow(espectro, aspect='auto', origin='lower', 
                  extent=[tiempos[0], tiempos[-1], freqs[0], freqs[-1]])
        plt.colorbar(label='Magnitud (dB)')
        plt.title('Espectrograma en Tiempo Real')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Frecuencia (Hz)')
        plt.show()

# Ejemplo de uso
espectrograma = EspectrogramaTiempoReal(1024, 44100, 0.75)

# Señal con frecuencia variable
t = np.arange(0, 2, 1/44100)
f_inicial, f_final = 1000, 2000
frecuencia = f_inicial + (f_final - f_inicial) * t / t[-1]
señal = np.sin(2*np.pi*frecuencia*t)

# Procesar señal
for muestra in señal:
    espectrograma.procesar_muestra(muestra)

# Visualizar
espectrograma.visualizar()
```

### 6. Aplicaciones en Audio

#### Analizador de Espectro en Tiempo Real
```python
class AnalizadorEspectroAudio:
    def __init__(self, tamaño_ventana, fs):
        """
        Analizador de espectro completo para audio
        """
        self.N = tamaño_ventana
        self.fs = fs
        
        self.buffer = np.zeros(self.N, dtype=np.float32)
        self.indice = 0
        self.ventana = np.hamming(self.N).astype(np.float32)
        self.freqs = np.fft.fftfreq(self.N, 1/fs)[:self.N//2]
        
        # Promedio móvil para suavizar
        self.alpha = 0.1
        self.espectro_promediado = np.zeros(self.N//2, dtype=np.float32)
    
    def procesar_muestra(self, muestra):
        """
        Procesa una muestra
        """
        self.buffer[self.indice] = muestra
        self.indice = (self.indice + 1) % self.N
    
    def obtener_espectro_suavizado(self):
        """
        Obtiene el espectro suavizado
        """
        if self.indice == 0:  # Ventana completa
            # Aplicar ventana
            señal_ventaneada = self.buffer * self.ventana
            
            # FFT
            X = np.fft.fft(señal_ventaneada)
            X_pos = X[:self.N//2]
            magnitud_db = 20 * np.log10(np.abs(X_pos) + 1e-10)
            
            # Promedio móvil
            self.espectro_promediado = (self.alpha * magnitud_db + 
                                      (1 - self.alpha) * self.espectro_promediado)
        
        return self.freqs, self.espectro_promediado
    
    def detectar_picos(self, umbral_db=-40):
        """
        Detecta picos en el espectro
        """
        freqs, espectro = self.obtener_espectro_suavizado()
        
        # Encontrar picos
        picos = []
        for i in range(1, len(espectro) - 1):
            if (espectro[i] > espectro[i-1] and 
                espectro[i] > espectro[i+1] and 
                espectro[i] > umbral_db):
                picos.append((freqs[i], espectro[i]))
        
        return picos

# Ejemplo de uso
analizador = AnalizadorEspectroAudio(2048, 44100)

# Señal de audio compleja
t = np.arange(0, 3, 1/44100)
f1, f2, f3 = 440, 880, 1320
señal = (np.sin(2*np.pi*f1*t) + 
         0.5*np.sin(2*np.pi*f2*t) + 
         0.3*np.sin(2*np.pi*f3*t) + 
         0.1*np.random.randn(len(t)))

# Procesar y visualizar en tiempo real
espectros = []
tiempos = []

for i, muestra in enumerate(señal):
    analizador.procesar_muestra(muestra)
    
    if i % 1000 == 0:  # Cada 1000 muestras
        freqs, espectro = analizador.obtener_espectro_suavizado()
        espectros.append(espectro)
        tiempos.append(i / 44100)

# Visualizar evolución del espectro
plt.figure(figsize=(12, 8))
espectros_array = np.array(espectros).T
plt.imshow(espectros_array, aspect='auto', origin='lower',
          extent=[tiempos[0], tiempos[-1], freqs[0], freqs[-1]])
plt.colorbar(label='Magnitud (dB)')
plt.title('Evolución del Espectro en Tiempo Real')
plt.xlabel('Tiempo (s)')
plt.ylabel('Frecuencia (Hz)')
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Análisis de Instrumento Musical
```python
def ejercicio_analisis_instrumento():
    """
    Analiza el espectro de diferentes instrumentos musicales
    """
    fs = 44100
    t = np.arange(0, 1, 1/fs)
    f0 = 440  # La4
    
    # Simular diferentes instrumentos
    instrumentos = {
        'Flauta': np.sin(2*np.pi*f0*t),  # Solo fundamental
        'Clarinete': (np.sin(2*np.pi*f0*t) + 
                     0.5*np.sin(2*np.pi*3*f0*t) + 
                     0.3*np.sin(2*np.pi*5*f0*t)),  # Armónicos impares
        'Violín': (np.sin(2*np.pi*f0*t) + 
                  0.7*np.sin(2*np.pi*2*f0*t) + 
                  0.5*np.sin(2*np.pi*3*f0*t) + 
                  0.3*np.sin(2*np.pi*4*f0*t))  # Armónicos pares e impares
    }
    
    plt.figure(figsize=(15, 10))
    
    for i, (nombre, señal) in enumerate(instrumentos.items()):
        # FFT
        X = np.fft.fft(señal)
        freqs = np.fft.fftfreq(len(señal), 1/fs)[:len(señal)//2]
        magnitud = np.abs(X[:len(X)//2])
        
        plt.subplot(2, 2, i+1)
        plt.plot(freqs, 20*np.log10(magnitud + 1e-10))
        plt.title(f'Espectro - {nombre}')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.grid(True)
        plt.xlim([0, 5000])
        plt.ylim([-100, 20])
    
    plt.tight_layout()
    plt.show()

ejercicio_analisis_instrumento()
```

### Ejercicio 2: Detección de Pitch en Tiempo Real
```python
def ejercicio_deteccion_pitch():
    """
    Implementa detección de pitch en tiempo real
    """
    fs = 44100
    detector = DetectorFrecuencia(2048, fs, (80, 2000))
    
    # Señal con pitch variable
    t = np.arange(0, 3, 1/fs)
    # Pitch que sube de 220 Hz a 880 Hz
    pitch = 220 * (2**(3*t/t[-1]))
    señal = np.sin(2*np.pi*pitch*t)
    
    # Procesar
    pitches_detectados = []
    for muestra in señal:
        detector.procesar_muestra(muestra)
        if detector.indice == 0:
            freq, _, _ = detector.detectar_frecuencia()
            pitches_detectados.append(freq)
    
    # Visualizar
    plt.figure(figsize=(12, 8))
    
    plt.subplot(2, 1, 1)
    plt.plot(t, pitch, label='Pitch Real', linewidth=2)
    plt.plot(np.linspace(0, 3, len(pitches_detectados)), pitches_detectados, 
             'ro-', label='Pitch Detectado', markersize=4)
    plt.title('Detección de Pitch en Tiempo Real')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Frecuencia (Hz)')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 1, 2)
    plt.plot(t, señal)
    plt.title('Señal de Audio')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_deteccion_pitch()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 5.3: Filtros Digitales IIR](clase-5-3-filtros-digitales-iir.md)
- **Clase Siguiente**: [Clase 5.5: Efectos de Audio](clase-5-5-efectos-audio.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Oppenheim, A. V., & Schafer, R. W. (2010). *Discrete-Time Signal Processing*
- Smith, J. O. (2007). *Introduction to Digital Filters*
- Proakis, J. G., & Manolakis, D. G. (2006). *Digital Signal Processing*

---
*Duración estimada: 4-5 horas*
