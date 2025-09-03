# Clase 5.3: Filtros Digitales IIR

## 🎯 Objetivo de la Clase
Diseñar e implementar filtros digitales de Respuesta al Impulso Infinita (IIR) para audio, incluyendo transformación bilineal, análisis de estabilidad y optimización para ultra baja latencia.

## 📚 Contenido

### 1. Introducción a los Filtros IIR

#### ¿Qué es un Filtro IIR?
Un **Filtro IIR (Infinite Impulse Response)** es un filtro digital cuya salida depende tanto de las muestras de entrada como de las muestras de salida anteriores.

**Ecuación de diferencias:**
```
y[n] = Σ(k=0 to M) b[k] * x[n-k] - Σ(k=1 to N) a[k] * y[n-k]
```

Donde:
- `y[n]` es la salida en el instante n
- `x[n-k]` es la entrada en el instante n-k
- `b[k]` son los coeficientes de alimentación directa
- `a[k]` son los coeficientes de realimentación
- `M` es el orden del numerador
- `N` es el orden del denominador

#### Ventajas de los Filtros IIR:
1. **Eficiencia**: Menos coeficientes que FIR para la misma selectividad
2. **Baja latencia**: Menor retardo de grupo
3. **Selectividad**: Mejor respuesta en frecuencia con menos recursos

#### Desventajas:
1. **Estabilidad**: Pueden ser inestables
2. **Fase no lineal**: Retardo de grupo variable
3. **Complejidad**: Más difícil de diseñar y analizar

### 2. Función de Transferencia

#### Representación en Z
La función de transferencia de un filtro IIR es:

```
H(z) = Y(z)/X(z) = (b₀ + b₁z⁻¹ + ... + bₘz⁻ᵐ) / (1 + a₁z⁻¹ + ... + aₙz⁻ⁿ)
```

#### Ejemplo de Análisis:
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal
from scipy.fft import fft, fftfreq

def analizar_filtro_iir(b, a, fs=1):
    """
    Analiza un filtro IIR y visualiza sus características
    """
    # Respuesta en frecuencia
    w, H = signal.freqz(b, a, worN=1024)
    freqs = w * fs / (2 * np.pi)
    
    # Polos y ceros
    z, p, k = signal.tf2zpk(b, a)
    
    # Visualizar
    fig, ((ax1, ax2), (ax3, ax4)) = plt.subplots(2, 2, figsize=(12, 10))
    
    # Magnitud
    ax1.plot(freqs, 20*np.log10(np.abs(H) + 1e-10))
    ax1.set_title('Respuesta en Magnitud')
    ax1.set_xlabel('Frecuencia (Hz)')
    ax1.set_ylabel('Magnitud (dB)')
    ax1.grid(True)
    
    # Fase
    ax2.plot(freqs, np.angle(H))
    ax2.set_title('Respuesta en Fase')
    ax2.set_xlabel('Frecuencia (Hz)')
    ax2.set_ylabel('Fase (rad)')
    ax2.grid(True)
    
    # Retardo de grupo
    w_gd, gd = signal.group_delay((b, a), w=w)
    ax3.plot(freqs, gd)
    ax3.set_title('Retardo de Grupo')
    ax3.set_xlabel('Frecuencia (Hz)')
    ax3.set_ylabel('Retardo (muestras)')
    ax3.grid(True)
    
    # Diagrama de polos y ceros
    unit_circle = plt.Circle((0, 0), 1, fill=False, color='black', linestyle='--', alpha=0.3)
    ax4.add_patch(unit_circle)
    ax4.plot(np.real(z), np.imag(z), 'bo', label='Ceros', markersize=8)
    ax4.plot(np.real(p), np.imag(p), 'rx', label='Polos', markersize=8)
    ax4.set_title('Diagrama de Polos y Ceros')
    ax4.set_xlabel('Parte Real')
    ax4.set_ylabel('Parte Imaginaria')
    ax4.legend()
    ax4.grid(True)
    ax4.axis('equal')
    
    plt.tight_layout()
    plt.show()
    
    # Verificar estabilidad
    es_estable = np.all(np.abs(p) < 1)
    print(f"Filtro estable: {es_estable}")
    
    return es_estable

# Ejemplo: Filtro pasa-bajas de primer orden
b = [0.1, 0.1]  # Numerador
a = [1, -0.8]   # Denominador

analizar_filtro_iir(b, a)
```

### 3. Diseño de Filtros IIR

#### Transformación Bilineal
La transformación bilineal convierte filtros analógicos a digitales preservando la estabilidad.

**Transformación:**
```
s = (2/T) * (z-1)/(z+1)
```

Donde T es el período de muestreo.

```python
def diseno_filtro_iir_bilineal(tipo, fc, fs, orden=2):
    """
    Diseña un filtro IIR usando transformación bilineal
    
    tipo: 'lowpass', 'highpass', 'bandpass', 'bandstop'
    fc: frecuencia de corte (o frecuencias para pasa-banda)
    fs: frecuencia de muestreo
    orden: orden del filtro
    """
    # Frecuencia de corte normalizada
    if isinstance(fc, (list, tuple)):
        fc1, fc2 = fc
        fc1_norm = 2 * fc1 / fs
        fc2_norm = 2 * fc2 / fs
    else:
        fc_norm = 2 * fc / fs
    
    # Diseñar filtro analógico
    if tipo == 'lowpass':
        b_analog, a_analog = signal.butter(orden, fc_norm, btype='low', analog=True)
    elif tipo == 'highpass':
        b_analog, a_analog = signal.butter(orden, fc_norm, btype='high', analog=True)
    elif tipo == 'bandpass':
        b_analog, a_analog = signal.butter(orden, [fc1_norm, fc2_norm], btype='band', analog=True)
    elif tipo == 'bandstop':
        b_analog, a_analog = signal.butter(orden, [fc1_norm, fc2_norm], btype='bandstop', analog=True)
    
    # Transformación bilineal
    b_digital, a_digital = signal.bilinear(b_analog, a_analog, fs)
    
    return b_digital, a_digital

# Ejemplo: Filtro pasa-bajas Butterworth
fs = 44100
fc = 2000
orden = 4

b, a = diseno_filtro_iir_bilineal('lowpass', fc, fs, orden)
print(f"Coeficientes del numerador (b): {b}")
print(f"Coeficientes del denominador (a): {a}")

# Analizar el filtro
analizar_filtro_iir(b, a, fs)
```

#### Filtros Butterworth
Los filtros Butterworth tienen una respuesta en magnitud máxima plana en la banda de paso.

```python
def comparar_filtros_butterworth():
    """
    Compara filtros Butterworth de diferentes órdenes
    """
    fs = 44100
    fc = 2000
    ordenes = [1, 2, 4, 6, 8]
    
    plt.figure(figsize=(12, 8))
    
    for i, orden in enumerate(ordenes):
        b, a = diseno_filtro_iir_bilineal('lowpass', fc, fs, orden)
        w, H = signal.freqz(b, a, worN=1024)
        freqs = w * fs / (2 * np.pi)
        
        plt.subplot(2, 1, 1)
        plt.plot(freqs, 20*np.log10(np.abs(H) + 1e-10), label=f'Orden {orden}')
        
        plt.subplot(2, 1, 2)
        w_gd, gd = signal.group_delay((b, a), w=w)
        plt.plot(freqs, gd, label=f'Orden {orden}')
    
    plt.subplot(2, 1, 1)
    plt.title('Respuesta en Magnitud - Filtros Butterworth')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    plt.legend()
    plt.ylim([-100, 5])
    
    plt.subplot(2, 1, 2)
    plt.title('Retardo de Grupo - Filtros Butterworth')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Retardo (muestras)')
    plt.grid(True)
    plt.legend()
    
    plt.tight_layout()
    plt.show()

comparar_filtros_butterworth()
```

#### Filtros Chebyshev
Los filtros Chebyshev tienen rizado en la banda de paso (Tipo I) o en la banda de rechazo (Tipo II).

```python
def diseno_filtros_chebyshev():
    """
    Compara filtros Chebyshev Tipo I y II
    """
    fs = 44100
    fc = 2000
    orden = 4
    rizado = 1  # dB
    
    # Chebyshev Tipo I (rizado en banda de paso)
    b1, a1 = signal.cheby1(orden, rizado, 2*fc/fs, btype='low')
    
    # Chebyshev Tipo II (rizado en banda de rechazo)
    b2, a2 = signal.cheby2(orden, rizado, 2*fc/fs, btype='low')
    
    # Butterworth para comparación
    b3, a3 = signal.butter(orden, 2*fc/fs, btype='low')
    
    plt.figure(figsize=(12, 6))
    
    filtros = [
        (b1, a1, 'Chebyshev I'),
        (b2, a2, 'Chebyshev II'),
        (b3, a3, 'Butterworth')
    ]
    
    for b, a, nombre in filtros:
        w, H = signal.freqz(b, a, worN=1024)
        freqs = w * fs / (2 * np.pi)
        plt.plot(freqs, 20*np.log10(np.abs(H) + 1e-10), label=nombre)
    
    plt.title('Comparación de Filtros IIR')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    plt.legend()
    plt.ylim([-100, 5])
    plt.show()

diseno_filtros_chebyshev()
```

### 4. Implementación de Filtros IIR

#### Forma Directa I
```python
class FiltroIIR:
    def __init__(self, b, a):
        """
        Implementa un filtro IIR en forma directa I
        
        b: coeficientes del numerador
        a: coeficientes del denominador
        """
        self.b = np.array(b, dtype=np.float32)
        self.a = np.array(a, dtype=np.float32)
        self.M = len(self.b) - 1  # Orden del numerador
        self.N = len(self.a) - 1  # Orden del denominador
        
        # Buffers para entrada y salida
        self.x_buffer = np.zeros(self.M + 1, dtype=np.float32)
        self.y_buffer = np.zeros(self.N + 1, dtype=np.float32)
        
        # Índices para buffers circulares
        self.x_idx = 0
        self.y_idx = 0
    
    def procesar_muestra(self, x):
        """
        Procesa una muestra de entrada
        """
        # Agregar entrada al buffer
        self.x_buffer[self.x_idx] = x
        
        # Calcular salida
        y = 0
        
        # Términos de alimentación directa
        for i in range(self.M + 1):
            y += self.b[i] * self.x_buffer[(self.x_idx - i) % (self.M + 1)]
        
        # Términos de realimentación
        for i in range(1, self.N + 1):
            y -= self.a[i] * self.y_buffer[(self.y_idx - i) % (self.N + 1)]
        
        # Normalizar por a[0]
        y /= self.a[0]
        
        # Agregar salida al buffer
        self.y_buffer[self.y_idx] = y
        
        # Actualizar índices
        self.x_idx = (self.x_idx + 1) % (self.M + 1)
        self.y_idx = (self.y_idx + 1) % (self.N + 1)
        
        return y
    
    def procesar_señal(self, x):
        """
        Procesa una señal completa
        """
        y = np.zeros_like(x, dtype=np.float32)
        for i in range(len(x)):
            y[i] = self.procesar_muestra(x[i])
        return y

# Ejemplo de uso
b = [0.1, 0.2, 0.1]  # Numerador
a = [1, -1.2, 0.5]   # Denominador

filtro = FiltroIIR(b, a)

# Probar con señal
t = np.arange(0, 1, 1/44100)
f1, f2 = 1000, 4000
señal = np.sin(2*np.pi*f1*t) + 0.5*np.sin(2*np.pi*f2*t)

señal_filtrada = filtro.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t[:1000], señal[:1000], label='Original', alpha=0.7)
plt.plot(t[:1000], señal_filtrada[:1000], label='Filtrada')
plt.title('Procesamiento con Filtro IIR')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

#### Forma Directa II (Canónica)
```python
class FiltroIIRCanonico:
    def __init__(self, b, a):
        """
        Implementa un filtro IIR en forma directa II (canónica)
        Más eficiente en memoria
        """
        self.b = np.array(b, dtype=np.float32)
        self.a = np.array(a, dtype=np.float32)
        self.M = len(self.b) - 1
        self.N = len(self.a) - 1
        
        # Un solo buffer para estados internos
        self.estados = np.zeros(max(self.M, self.N) + 1, dtype=np.float32)
        self.idx = 0
    
    def procesar_muestra(self, x):
        """
        Procesa una muestra usando forma canónica
        """
        # Calcular salida
        y = self.b[0] * x
        
        for i in range(1, self.M + 1):
            y += self.b[i] * self.estados[(self.idx - i) % len(self.estados)]
        
        for i in range(1, self.N + 1):
            y -= self.a[i] * self.estados[(self.idx - i) % len(self.estados)]
        
        y /= self.a[0]
        
        # Actualizar estados
        self.estados[self.idx] = x
        self.idx = (self.idx + 1) % len(self.estados)
        
        return y

# Comparar eficiencia de memoria
b = [0.1, 0.2, 0.1]
a = [1, -1.2, 0.5]

filtro_directo = FiltroIIR(b, a)
filtro_canonico = FiltroIIRCanonico(b, a)

print(f"Memoria forma directa I: {len(filtro_directo.x_buffer) + len(filtro_directo.y_buffer)} elementos")
print(f"Memoria forma canónica: {len(filtro_canonico.estados)} elementos")
```

### 5. Análisis de Estabilidad

#### Criterio de Estabilidad
Un filtro IIR es estable si todos sus polos están dentro del círculo unitario.

```python
def analizar_estabilidad_iir(b, a):
    """
    Analiza la estabilidad de un filtro IIR
    """
    # Encontrar polos
    polos = np.roots(a)
    
    # Verificar estabilidad
    es_estable = np.all(np.abs(polos) < 1)
    
    print(f"Polos: {polos}")
    print(f"Módulos de los polos: {np.abs(polos)}")
    print(f"Filtro estable: {es_estable}")
    
    # Visualizar polos en el plano Z
    plt.figure(figsize=(8, 8))
    
    # Círculo unitario
    theta = np.linspace(0, 2*np.pi, 100)
    plt.plot(np.cos(theta), np.sin(theta), 'k--', alpha=0.3)
    
    # Polos
    plt.plot(np.real(polos), np.imag(polos), 'rx', markersize=10, label='Polos')
    
    plt.title('Diagrama de Polos en el Plano Z')
    plt.xlabel('Parte Real')
    plt.ylabel('Parte Imaginaria')
    plt.legend()
    plt.grid(True)
    plt.axis('equal')
    plt.xlim([-1.5, 1.5])
    plt.ylim([-1.5, 1.5])
    plt.show()
    
    return es_estable

# Ejemplo: Filtro estable
b_estable = [0.1, 0.2, 0.1]
a_estable = [1, -0.5, 0.25]
analizar_estabilidad_iir(b_estable, a_estable)

# Ejemplo: Filtro inestable
b_inestable = [0.1, 0.2, 0.1]
a_inestable = [1, -1.5, 0.8]  # Polos fuera del círculo unitario
analizar_estabilidad_iir(b_inestable, a_inestable)
```

### 6. Aplicaciones en Audio

#### Filtro Pasa-Bajas para Audio
```python
def filtro_audio_pasa_bajas(fc, fs, orden=4):
    """
    Diseña un filtro pasa-bajas optimizado para audio
    """
    # Usar Butterworth para respuesta suave
    b, a = signal.butter(orden, 2*fc/fs, btype='low')
    
    # Verificar estabilidad
    polos = np.roots(a)
    if not np.all(np.abs(polos) < 1):
        print("Advertencia: Filtro potencialmente inestable")
    
    return b, a

# Ejemplo: Filtro anti-aliasing
fs = 44100
fc = 20000  # Frecuencia de Nyquist
orden = 6

b, a = filtro_audio_pasa_bajas(fc, fs, orden)

# Probar con señal de audio
t = np.arange(0, 0.1, 1/fs)
f1, f2, f3 = 1000, 15000, 25000
audio = (np.sin(2*np.pi*f1*t) + 
         0.5*np.sin(2*np.pi*f2*t) + 
         0.3*np.sin(2*np.pi*f3*t))

# Aplicar filtro
filtro = FiltroIIR(b, a)
audio_filtrado = filtro.procesar_señal(audio)

# Análisis espectral
fft_original = fft(audio)
fft_filtrado = fft(audio_filtrado)
freqs = np.linspace(0, fs/2, len(fft_original)//2)

plt.figure(figsize=(12, 8))

plt.subplot(2, 1, 1)
plt.plot(t, audio, label='Original', alpha=0.7)
plt.plot(t, audio_filtrado, label='Filtrado')
plt.title('Señal de Audio - Original vs Filtrada')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(2, 1, 2)
plt.plot(freqs, 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), label='Original')
plt.plot(freqs, 20*np.log10(np.abs(fft_filtrado[:len(fft_filtrado)//2]) + 1e-10), label='Filtrado')
plt.title('Espectros de Frecuencia')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.legend()
plt.grid(True)
plt.axvline(fc, color='r', linestyle='--', label=f'Fc = {fc} Hz')
plt.legend()

plt.tight_layout()
plt.show()
```

#### Ecualizador Paramétrico
```python
class EcualizadorParametrico:
    def __init__(self, fs):
        """
        Ecualizador paramétrico usando filtros IIR
        """
        self.fs = fs
        self.filtros = []
        self.ganancias = []
    
    def agregar_banda(self, fc, Q, ganancia_db):
        """
        Agrega una banda de ecualización
        
        fc: frecuencia central
        Q: factor de calidad
        ganancia_db: ganancia en dB
        """
        # Convertir ganancia a lineal
        ganancia_lin = 10**(ganancia_db/20)
        
        # Diseñar filtro peak/notch
        if ganancia_db > 0:
            # Boost
            b, a = signal.iirpeak(2*fc/self.fs, Q)
        else:
            # Cut
            b, a = signal.iirnotch(2*fc/self.fs, Q)
        
        # Ajustar ganancia
        b = b * ganancia_lin
        
        self.filtros.append((b, a))
        self.ganancias.append(ganancia_lin)
    
    def procesar(self, señal):
        """
        Procesa la señal con todos los filtros
        """
        señal_procesada = señal.copy()
        
        for (b, a), ganancia in zip(self.filtros, self.ganancias):
            filtro = FiltroIIR(b, a)
            señal_procesada = filtro.procesar_señal(señal_procesada)
        
        return señal_procesada

# Ejemplo de uso
fs = 44100
ecualizador = EcualizadorParametrico(fs)

# Configurar bandas
ecualizador.agregar_banda(100, 1, 3)    # Boost en graves
ecualizador.agregar_banda(1000, 2, -2)  # Cut en medios
ecualizador.agregar_banda(8000, 1, 2)   # Boost en agudos

# Procesar señal
t = np.arange(0, 1, 1/fs)
audio = np.sin(2*np.pi*440*t) + 0.5*np.sin(2*np.pi*880*t) + 0.3*np.sin(2*np.pi*1760*t)

audio_ecualizado = ecualizador.procesar(audio)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t[:1000], audio[:1000], label='Original', alpha=0.7)
plt.plot(t[:1000], audio_ecualizado[:1000], label='Ecualizado')
plt.title('Ecualizador Paramétrico IIR')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Diseño de Filtro IIR Personalizado
```python
def ejercicio_filtro_iir_personalizado():
    """
    Diseña un filtro IIR que elimine frecuencias entre 2000-3000 Hz
    """
    fs = 44100
    fc1, fc2 = 2000, 3000
    orden = 4
    
    # Diseñar filtro notch
    b, a = signal.iirnotch(2*(fc1+fc2)/(2*fs), (fc2-fc1)/fs)
    
    # Analizar
    analizar_filtro_iir(b, a, fs)
    
    # Probar con señal
    t = np.arange(0, 0.1, 1/fs)
    f1, f2, f3 = 1000, 2500, 4000
    señal = (np.sin(2*np.pi*f1*t) + 
             np.sin(2*np.pi*f2*t) + 
             np.sin(2*np.pi*f3*t))
    
    filtro = FiltroIIR(b, a)
    señal_filtrada = filtro.procesar_señal(señal)
    
    # Visualizar
    plt.figure(figsize=(12, 6))
    plt.plot(t, señal, label='Original', alpha=0.7)
    plt.plot(t, señal_filtrada, label='Filtrada')
    plt.title('Filtro Notch IIR')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    plt.show()

ejercicio_filtro_iir_personalizado()
```

### Ejercicio 2: Análisis de Latencia vs FIR
```python
def ejercicio_comparacion_latencia():
    """
    Compara la latencia de filtros FIR e IIR
    """
    fs = 44100
    fc = 2000
    
    # Filtro FIR
    N_fir = 64
    h_fir = signal.firwin(N_fir, 2*fc/fs)
    latencia_fir = (N_fir - 1) / 2
    
    # Filtro IIR equivalente
    b_iir, a_iir = signal.butter(4, 2*fc/fs, btype='low')
    
    # Calcular retardo de grupo del IIR
    w, gd = signal.group_delay((b_iir, a_iir), w=np.linspace(0, np.pi, 1000))
    latencia_iir = np.mean(gd)
    
    print(f"Latencia FIR: {latencia_fir:.1f} muestras = {(latencia_fir/fs)*1000:.2f} ms")
    print(f"Latencia IIR promedio: {latencia_iir:.1f} muestras = {(latencia_iir/fs)*1000:.2f} ms")
    
    # Comparar respuestas en frecuencia
    w_fir, H_fir = signal.freqz(h_fir, worN=1024)
    w_iir, H_iir = signal.freqz(b_iir, a_iir, worN=1024)
    freqs = w_fir * fs / (2 * np.pi)
    
    plt.figure(figsize=(12, 6))
    plt.plot(freqs, 20*np.log10(np.abs(H_fir) + 1e-10), label='FIR')
    plt.plot(freqs, 20*np.log10(np.abs(H_iir) + 1e-10), label='IIR')
    plt.title('Comparación FIR vs IIR')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    plt.ylim([-100, 5])
    plt.show()

ejercicio_comparacion_latencia()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 5.2: Filtros Digitales FIR](clase-5-2-filtros-digitales-fir.md)
- **Clase Siguiente**: [Clase 5.4: Análisis Espectral](clase-5-4-analisis-espectral.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Oppenheim, A. V., & Schafer, R. W. (2010). *Discrete-Time Signal Processing*
- Proakis, J. G., & Manolakis, D. G. (2006). *Digital Signal Processing*
- Parks, T. W., & Burrus, C. S. (1987). *Digital Filter Design*

---
*Duración estimada: 4-5 horas*
