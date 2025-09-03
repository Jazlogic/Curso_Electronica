# Clase 5.2: Filtros Digitales FIR

## 🎯 Objetivo de la Clase
Diseñar e implementar filtros digitales de Respuesta al Impulso Finita (FIR) para aplicaciones de audio, incluyendo diferentes métodos de diseño y optimización para ultra baja latencia.

## 📚 Contenido

### 1. Introducción a los Filtros FIR

#### ¿Qué es un Filtro FIR?
Un **Filtro FIR (Finite Impulse Response)** es un filtro digital cuya respuesta al impulso tiene duración finita. La salida depende únicamente de las muestras de entrada actuales y pasadas.

**Ecuación de diferencias:**
```
y[n] = Σ(k=0 to N-1) h[k] * x[n-k]
```

Donde:
- `y[n]` es la salida en el instante n
- `x[n-k]` es la entrada en el instante n-k
- `h[k]` son los coeficientes del filtro
- `N` es el orden del filtro

#### Ventajas de los Filtros FIR:
1. **Estabilidad**: Siempre estables
2. **Linealidad de fase**: Retardo de grupo constante
3. **Simplicidad**: Fácil implementación
4. **Precisión**: Control exacto de la respuesta en frecuencia

#### Desventajas:
1. **Orden alto**: Requieren más coeficientes que IIR
2. **Latencia**: Mayor retardo de grupo
3. **Computación**: Más operaciones por muestra

### 2. Diseño de Filtros FIR

#### Método de Muestreo en Frecuencia
Este método especifica la respuesta en frecuencia deseada en puntos discretos y calcula los coeficientes mediante la IDFT.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.fft import ifft

def diseno_fir_muestreo_frecuencia(N, H_deseada):
    """
    Diseña un filtro FIR usando muestreo en frecuencia
    
    N: orden del filtro
    H_deseada: respuesta en frecuencia deseada
    """
    # Asegurar simetría para filtro de fase lineal
    if N % 2 == 0:
        # Orden par
        H_simetrica = np.concatenate([H_deseada, np.conj(H_deseada[-2:0:-1])])
    else:
        # Orden impar
        H_simetrica = np.concatenate([H_deseada, np.conj(H_deseada[-1:0:-1])])
    
    # IDFT para obtener coeficientes
    h = np.real(ifft(H_simetrica))
    
    return h

# Ejemplo: Filtro pasa-bajas
N = 32  # Orden del filtro
frecuencias = np.linspace(0, 1, N//2 + 1)
fc = 0.3  # Frecuencia de corte normalizada

# Respuesta en frecuencia deseada
H_deseada = np.where(frecuencias <= fc, 1, 0)

# Diseñar filtro
h = diseno_fir_muestreo_frecuencia(N, H_deseada)

# Visualizar
plt.figure(figsize=(12, 8))

plt.subplot(2, 2, 1)
plt.stem(np.arange(len(h)), h)
plt.title('Coeficientes del Filtro FIR')
plt.xlabel('n')
plt.ylabel('h[n]')
plt.grid(True)

# Respuesta en frecuencia
from scipy.fft import fft
H = fft(h, 1024)
freqs = np.linspace(0, 1, len(H))

plt.subplot(2, 2, 2)
plt.plot(freqs, np.abs(H))
plt.title('Respuesta en Magnitud')
plt.xlabel('Frecuencia Normalizada')
plt.ylabel('|H(ω)|')
plt.grid(True)

plt.subplot(2, 2, 3)
plt.plot(freqs, np.angle(H))
plt.title('Respuesta en Fase')
plt.xlabel('Frecuencia Normalizada')
plt.ylabel('∠H(ω)')
plt.grid(True)

plt.subplot(2, 2, 4)
plt.plot(freqs, -np.diff(np.angle(H), prepend=np.angle(H)[0]))
plt.title('Retardo de Grupo')
plt.xlabel('Frecuencia Normalizada')
plt.ylabel('Retardo (muestras)')
plt.grid(True)

plt.tight_layout()
plt.show()
```

#### Método de Ventanas
Este método multiplica la respuesta al impulso ideal por una ventana para limitar su duración.

```python
def diseno_fir_ventana(tipo_ventana, N, fc):
    """
    Diseña un filtro FIR usando el método de ventanas
    
    tipo_ventana: 'rectangular', 'hamming', 'hanning', 'blackman'
    N: orden del filtro
    fc: frecuencia de corte normalizada
    """
    # Respuesta al impulso ideal (filtro pasa-bajas)
    n = np.arange(-N//2, N//2 + 1)
    h_ideal = np.sinc(2 * fc * n)
    
    # Seleccionar ventana
    if tipo_ventana == 'rectangular':
        w = np.ones(N + 1)
    elif tipo_ventana == 'hamming':
        w = np.hamming(N + 1)
    elif tipo_ventana == 'hanning':
        w = np.hanning(N + 1)
    elif tipo_ventana == 'blackman':
        w = np.blackman(N + 1)
    else:
        raise ValueError("Tipo de ventana no válido")
    
    # Aplicar ventana
    h = h_ideal * w
    
    return h

# Comparar diferentes ventanas
N = 64
fc = 0.3
ventanas = ['rectangular', 'hamming', 'hanning', 'blackman']

plt.figure(figsize=(12, 8))

for i, ventana in enumerate(ventanas):
    h = diseno_fir_ventana(ventana, N, fc)
    H = fft(h, 1024)
    freqs = np.linspace(0, 1, len(H))
    
    plt.subplot(2, 2, i+1)
    plt.plot(freqs, 20*np.log10(np.abs(H) + 1e-10))
    plt.title(f'Filtro FIR - Ventana {ventana.capitalize()}')
    plt.xlabel('Frecuencia Normalizada')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    plt.ylim([-100, 5])

plt.tight_layout()
plt.show()
```

### 3. Tipos de Filtros FIR

#### Filtro Pasa-Bajas
```python
def filtro_pasa_bajas_fir(fc, N, fs):
    """
    Diseña un filtro pasa-bajas FIR
    
    fc: frecuencia de corte en Hz
    N: orden del filtro
    fs: frecuencia de muestreo
    """
    # Frecuencia de corte normalizada
    fc_norm = fc / (fs/2)
    
    # Respuesta al impulso ideal
    n = np.arange(-N//2, N//2 + 1)
    h_ideal = 2 * fc_norm * np.sinc(2 * fc_norm * n)
    
    # Aplicar ventana de Hamming
    w = np.hamming(N + 1)
    h = h_ideal * w
    
    return h

# Ejemplo de uso
fs = 44100  # Frecuencia de muestreo
fc = 2000   # Frecuencia de corte
N = 64      # Orden del filtro

h_lowpass = filtro_pasa_bajas_fir(fc, N, fs)

# Probar con señal de audio
t = np.arange(0, 1, 1/fs)
f1, f2 = 1000, 4000  # Frecuencias en Hz
audio = np.sin(2*np.pi*f1*t) + 0.5*np.sin(2*np.pi*f2*t)

# Aplicar filtro
audio_filtrado = np.convolve(audio, h_lowpass, mode='same')

# Visualizar resultados
plt.figure(figsize=(12, 6))

plt.subplot(2, 1, 1)
plt.plot(t[:1000], audio[:1000], label='Original')
plt.plot(t[:1000], audio_filtrado[:1000], label='Filtrado')
plt.title('Señal de Audio - Original vs Filtrada')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

# Espectros
fft_original = fft(audio)
fft_filtrado = fft(audio_filtrado)
freqs = np.linspace(0, fs/2, len(fft_original)//2)

plt.subplot(2, 1, 2)
plt.plot(freqs, 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), label='Original')
plt.plot(freqs, 20*np.log10(np.abs(fft_filtrado[:len(fft_filtrado)//2]) + 1e-10), label='Filtrado')
plt.title('Espectros de Frecuencia')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
```

#### Filtro Pasa-Altas
```python
def filtro_pasa_altas_fir(fc, N, fs):
    """
    Diseña un filtro pasa-altas FIR
    """
    # Filtro pasa-bajas complementario
    h_lowpass = filtro_pasa_bajas_fir(fc, N, fs)
    
    # Filtro pasa-altas = δ[n] - h_lowpass[n]
    h_highpass = -h_lowpass.copy()
    h_highpass[N//2] += 1  # Impulso unitario en el centro
    
    return h_highpass

# Ejemplo
h_highpass = filtro_pasa_altas_fir(fc, N, fs)

# Visualizar respuesta en frecuencia
H_lowpass = fft(h_lowpass, 1024)
H_highpass = fft(h_highpass, 1024)
freqs = np.linspace(0, fs/2, len(H_lowpass)//2)

plt.figure(figsize=(10, 6))
plt.plot(freqs, 20*np.log10(np.abs(H_lowpass[:len(H_lowpass)//2]) + 1e-10), label='Pasa-Bajas')
plt.plot(freqs, 20*np.log10(np.abs(H_highpass[:len(H_highpass)//2]) + 1e-10), label='Pasa-Altas')
plt.title('Respuesta en Frecuencia - Filtros FIR')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.legend()
plt.grid(True)
plt.ylim([-100, 5])
plt.show()
```

#### Filtro Pasa-Banda
```python
def filtro_pasa_banda_fir(fc1, fc2, N, fs):
    """
    Diseña un filtro pasa-banda FIR
    
    fc1: frecuencia de corte inferior
    fc2: frecuencia de corte superior
    """
    # Filtros pasa-bajas
    h_low1 = filtro_pasa_bajas_fir(fc1, N, fs)
    h_low2 = filtro_pasa_bajas_fir(fc2, N, fs)
    
    # Filtro pasa-banda = h_low2 - h_low1
    h_bandpass = h_low2 - h_low1
    
    return h_bandpass

# Ejemplo
fc1, fc2 = 1000, 3000  # Frecuencias de corte
h_bandpass = filtro_pasa_banda_fir(fc1, fc2, N, fs)

# Visualizar
H_bandpass = fft(h_bandpass, 1024)
freqs = np.linspace(0, fs/2, len(H_bandpass)//2)

plt.figure(figsize=(10, 6))
plt.plot(freqs, 20*np.log10(np.abs(H_bandpass[:len(H_bandpass)//2]) + 1e-10))
plt.title('Filtro Pasa-Banda FIR')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.grid(True)
plt.ylim([-100, 5])
plt.axvline(fc1, color='r', linestyle='--', label=f'fc1 = {fc1} Hz')
plt.axvline(fc2, color='r', linestyle='--', label=f'fc2 = {fc2} Hz')
plt.legend()
plt.show()
```

### 4. Implementación Eficiente

#### Implementación Directa
```python
class FiltroFIR:
    def __init__(self, coeficientes):
        """
        Inicializa un filtro FIR con los coeficientes dados
        """
        self.h = np.array(coeficientes)
        self.N = len(self.h)
        self.buffer = np.zeros(self.N)
        self.indice = 0
    
    def procesar_muestra(self, x):
        """
        Procesa una muestra de entrada
        """
        # Agregar nueva muestra al buffer
        self.buffer[self.indice] = x
        
        # Calcular salida
        y = 0
        for i in range(self.N):
            y += self.h[i] * self.buffer[(self.indice - i) % self.N]
        
        # Actualizar índice del buffer circular
        self.indice = (self.indice + 1) % self.N
        
        return y
    
    def procesar_señal(self, x):
        """
        Procesa una señal completa
        """
        y = np.zeros_like(x)
        for i in range(len(x)):
            y[i] = self.procesar_muestra(x[i])
        return y

# Ejemplo de uso
coeficientes = filtro_pasa_bajas_fir(2000, 32, 44100)
filtro = FiltroFIR(coeficientes)

# Procesar señal
t = np.arange(0, 0.1, 1/44100)
f1, f2 = 1000, 4000
señal = np.sin(2*np.pi*f1*t) + 0.5*np.sin(2*np.pi*f2*t)

señal_filtrada = filtro.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 4))
plt.plot(t, señal, label='Original', alpha=0.7)
plt.plot(t, señal_filtrada, label='Filtrada')
plt.title('Procesamiento con Filtro FIR')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

#### Optimización para Ultra Baja Latencia
```python
class FiltroFIROptimizado:
    def __init__(self, coeficientes):
        """
        Implementación optimizada para ultra baja latencia
        """
        self.h = np.array(coeficientes, dtype=np.float32)  # Usar float32
        self.N = len(self.h)
        self.buffer = np.zeros(self.N, dtype=np.float32)
        self.indice = 0
        
        # Pre-calcular índices para evitar módulo
        self.indices = np.arange(self.N)
    
    def procesar_muestra_optimizado(self, x):
        """
        Versión optimizada del procesamiento
        """
        # Agregar muestra al buffer
        self.buffer[self.indice] = x
        
        # Calcular salida usando vectorización
        indices_buffer = (self.indice - self.indices) % self.N
        y = np.dot(self.h, self.buffer[indices_buffer])
        
        # Actualizar índice
        self.indice = (self.indice + 1) % self.N
        
        return y

# Comparar rendimiento
import time

# Crear filtros
coeficientes = filtro_pasa_bajas_fir(2000, 64, 44100)
filtro_normal = FiltroFIR(coeficientes)
filtro_optimizado = FiltroFIROptimizado(coeficientes)

# Señal de prueba
señal_prueba = np.random.randn(10000)

# Medir tiempo de procesamiento
inicio = time.time()
for muestra in señal_prueba:
    filtro_normal.procesar_muestra(muestra)
tiempo_normal = time.time() - inicio

inicio = time.time()
for muestra in señal_prueba:
    filtro_optimizado.procesar_muestra_optimizado(muestra)
tiempo_optimizado = time.time() - inicio

print(f"Tiempo normal: {tiempo_normal:.4f} segundos")
print(f"Tiempo optimizado: {tiempo_optimizado:.4f} segundos")
print(f"Mejora: {tiempo_normal/tiempo_optimizado:.2f}x")
```

### 5. Aplicaciones en Audio

#### Ecualizador Gráfico
```python
class EcualizadorGrafico:
    def __init__(self, fs, bandas_frecuencia):
        """
        Ecualizador gráfico usando filtros FIR
        
        fs: frecuencia de muestreo
        bandas_frecuencia: lista de frecuencias centrales de las bandas
        """
        self.fs = fs
        self.bandas = bandas_frecuencia
        self.filtros = []
        self.ganancias = np.ones(len(bandas_frecuencia))
        
        # Crear filtros para cada banda
        for fc in bandas_frecuencia:
            # Ancho de banda de 1/3 de octava
            bw = fc * (2**(1/6) - 2**(-1/6))
            fc1 = fc - bw/2
            fc2 = fc + bw/2
            
            h = filtro_pasa_banda_fir(fc1, fc2, 64, fs)
            self.filtros.append(FiltroFIR(h))
    
    def set_ganancia(self, banda, ganancia_db):
        """
        Establece la ganancia de una banda en dB
        """
        self.ganancias[banda] = 10**(ganancia_db/20)
    
    def procesar(self, señal):
        """
        Procesa la señal con el ecualizador
        """
        señal_ecualizada = np.zeros_like(señal)
        
        for i, (filtro, ganancia) in enumerate(zip(self.filtros, self.ganancias)):
            banda_filtrada = filtro.procesar_señal(señal)
            señal_ecualizada += ganancia * banda_filtrada
        
        return señal_ecualizada

# Ejemplo de uso
fs = 44100
bandas = [100, 200, 400, 800, 1600, 3200, 6400, 12800]  # Frecuencias en Hz

ecualizador = EcualizadorGrafico(fs, bandas)

# Configurar ecualización (ejemplo: boost en graves y agudos)
ecualizador.set_ganancia(0, 3)   # 100 Hz: +3 dB
ecualizador.set_ganancia(1, 2)   # 200 Hz: +2 dB
ecualizador.set_ganancia(4, -1)  # 1600 Hz: -1 dB
ecualizador.set_ganancia(7, 2)   # 12800 Hz: +2 dB

# Procesar señal de audio
t = np.arange(0, 1, 1/fs)
audio = np.sin(2*np.pi*440*t) + 0.5*np.sin(2*np.pi*880*t) + 0.3*np.sin(2*np.pi*1760*t)

audio_ecualizado = ecualizador.procesar(audio)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t[:1000], audio[:1000], label='Original', alpha=0.7)
plt.plot(t[:1000], audio_ecualizado[:1000], label='Ecualizado')
plt.title('Ecualizador Gráfico FIR')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Diseño de Filtro FIR Personalizado
```python
def ejercicio_filtro_personalizado():
    """
    Diseña un filtro FIR que elimine frecuencias entre 2000-3000 Hz
    """
    fs = 44100
    fc1, fc2 = 2000, 3000
    
    # Diseñar filtro notch (elimina banda específica)
    h_low1 = filtro_pasa_bajas_fir(fc1, 64, fs)
    h_low2 = filtro_pasa_bajas_fir(fc2, 64, fs)
    h_notch = h_low1 - h_low2
    
    # Filtro pasa-todo menos notch
    h_pasatodo = np.zeros(65)
    h_pasatodo[32] = 1  # Impulso unitario
    h_final = h_pasatodo - h_notch
    
    # Visualizar respuesta
    H = fft(h_final, 1024)
    freqs = np.linspace(0, fs/2, len(H)//2)
    
    plt.figure(figsize=(10, 6))
    plt.plot(freqs, 20*np.log10(np.abs(H[:len(H)//2]) + 1e-10))
    plt.title('Filtro FIR Notch (Elimina 2000-3000 Hz)')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    plt.axvspan(fc1, fc2, alpha=0.3, color='red', label='Banda eliminada')
    plt.legend()
    plt.show()

ejercicio_filtro_personalizado()
```

### Ejercicio 2: Análisis de Latencia
```python
def ejercicio_analisis_latencia():
    """
    Analiza la latencia de diferentes filtros FIR
    """
    fs = 44100
    ordenes = [16, 32, 64, 128]
    
    latencias = []
    for N in ordenes:
        # Latencia = (N-1)/2 muestras
        latencia_muestras = (N-1) / 2
        latencia_ms = (latencia_muestras / fs) * 1000
        latencias.append(latencia_ms)
        
        print(f"Orden {N}: {latencia_muestras:.1f} muestras = {latencia_ms:.2f} ms")
    
    # Visualizar
    plt.figure(figsize=(10, 6))
    plt.plot(ordenes, latencias, 'bo-')
    plt.title('Latencia vs Orden del Filtro FIR')
    plt.xlabel('Orden del Filtro')
    plt.ylabel('Latencia (ms)')
    plt.grid(True)
    
    # Líneas de referencia para audio
    plt.axhline(y=1, color='r', linestyle='--', label='1 ms (Ultra baja latencia)')
    plt.axhline(y=10, color='orange', linestyle='--', label='10 ms (Baja latencia)')
    plt.legend()
    plt.show()

ejercicio_analisis_latencia()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 5.1: Fundamentos del DSP](clase-5-1-fundamentos-dsp.md)
- **Clase Siguiente**: [Clase 5.3: Filtros Digitales IIR](clase-5-3-filtros-digitales-iir.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Parks, T. W., & Burrus, C. S. (1987). *Digital Filter Design*
- Harris, F. J. (1978). "On the use of windows for harmonic analysis"
- Smith, J. O. (2007). *Introduction to Digital Filters*

---
*Duración estimada: 4-5 horas*
