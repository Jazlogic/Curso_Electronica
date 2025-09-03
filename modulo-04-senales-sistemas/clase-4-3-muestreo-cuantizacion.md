# Clase 4.3: Muestreo y Cuantización

## 🎯 Objetivo de la Clase
Dominar la conversión analógica-digital, incluyendo el teorema de Nyquist, aliasing, cuantización y técnicas de dithering para sistemas de audio de alta calidad.

## 📋 Contenido de la Clase

### 1. Teorema de Nyquist

#### 1.1 Frecuencia de Nyquist
La frecuencia de Nyquist es la mitad de la frecuencia de muestreo. Para evitar aliasing, la frecuencia máxima de la señal debe ser menor que la frecuencia de Nyquist.

```python
import numpy as np
import matplotlib.pyplot as plt

def demostrar_nyquist():
    # Parámetros
    fs = 1000  # Frecuencia de muestreo
    f_nyquist = fs / 2  # 500 Hz
    
    # Generar señal analógica
    tiempo_analogico = np.linspace(0, 0.01, 10000)
    frecuencia_senal = 100  # Hz (menor que Nyquist)
    señal_analogica = np.sin(2 * np.pi * frecuencia_senal * tiempo_analogico)
    
    # Muestrear la señal
    tiempo_muestreo = np.arange(0, 0.01, 1/fs)
    señal_muestreada = np.sin(2 * np.pi * frecuencia_senal * tiempo_muestreo)
    
    plt.figure(figsize=(12, 8))
    
    # Señal analógica
    plt.subplot(2, 2, 1)
    plt.plot(tiempo_analogico, señal_analogica, 'b-', label='Señal Analógica')
    plt.plot(tiempo_muestreo, señal_muestreada, 'ro', label='Muestras')
    plt.title(f'Señal a {frecuencia_senal} Hz (fs={fs} Hz)')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Caso con aliasing
    frecuencia_aliasing = 800  # Hz (mayor que Nyquist)
    señal_aliasing = np.sin(2 * np.pi * frecuencia_aliasing * tiempo_analogico)
    señal_aliasing_muestreada = np.sin(2 * np.pi * frecuencia_aliasing * tiempo_muestreo)
    
    plt.subplot(2, 2, 2)
    plt.plot(tiempo_analogico, señal_aliasing, 'b-', label='Señal Analógica')
    plt.plot(tiempo_muestreo, señal_aliasing_muestreada, 'ro', label='Muestras')
    plt.title(f'Señal a {frecuencia_aliasing} Hz (Aliasing)')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectros
    plt.subplot(2, 2, 3)
    fft_normal = np.fft.fft(señal_muestreada)
    frecuencias = np.fft.fftfreq(len(señal_muestreada), 1/fs)
    plt.plot(frecuencias, np.abs(fft_normal))
    plt.title('Espectro Sin Aliasing')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud')
    plt.xlim(0, fs)
    plt.grid(True)
    
    plt.subplot(2, 2, 4)
    fft_aliasing = np.fft.fft(señal_aliasing_muestreada)
    plt.plot(frecuencias, np.abs(fft_aliasing))
    plt.title('Espectro Con Aliasing')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud')
    plt.xlim(0, fs)
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

demostrar_nyquist()
```

### 2. Aliasing y Filtros Anti-Aliasing

#### 2.1 Efectos del Aliasing
```python
def analizar_aliasing():
    # Diferentes frecuencias de muestreo
    frecuencias_muestreo = [800, 1000, 1200, 1600]
    frecuencia_senal = 600  # Hz
    
    plt.figure(figsize=(15, 10))
    
    for i, fs in enumerate(frecuencias_muestreo):
        # Generar señal
        tiempo = np.linspace(0, 0.02, int(fs * 0.02))
        señal = np.sin(2 * np.pi * frecuencia_senal * tiempo)
        
        # Calcular FFT
        fft = np.fft.fft(señal)
        frecuencias = np.fft.fftfreq(len(señal), 1/fs)
        
        plt.subplot(2, 2, i+1)
        plt.plot(frecuencias, np.abs(fft))
        plt.title(f'fs = {fs} Hz, f_nyquist = {fs/2} Hz')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud')
        plt.xlim(0, fs)
        plt.axvline(fs/2, color='red', linestyle='--', label='Nyquist')
        plt.axvline(frecuencia_senal, color='green', linestyle='--', label='Señal')
        plt.legend()
        plt.grid(True)
    
    plt.tight_layout()
    plt.show()

analizar_aliasing()
```

#### 2.2 Filtro Anti-Aliasing
```python
def filtro_anti_aliasing(señal, fs, frecuencia_corte):
    """
    Implementar filtro anti-aliasing
    """
    # Filtro pasa-bajos simple
    alpha = 2 * np.pi * frecuencia_corte / fs
    señal_filtrada = np.zeros_like(señal)
    
    for i in range(1, len(señal)):
        señal_filtrada[i] = señal_filtrada[i-1] + alpha * (señal[i] - señal_filtrada[i-1])
    
    return señal_filtrada

# Ejemplo de filtro anti-aliasing
fs = 1000
tiempo = np.linspace(0, 0.1, int(fs * 0.1))
señal_original = (np.sin(2 * np.pi * 100 * tiempo) + 
                 0.5 * np.sin(2 * np.pi * 300 * tiempo) + 
                 0.3 * np.sin(2 * np.pi * 500 * tiempo))

señal_filtrada = filtro_anti_aliasing(señal_original, fs, 400)

plt.figure(figsize=(12, 6))
plt.subplot(1, 2, 1)
plt.plot(tiempo, señal_original, label='Original')
plt.plot(tiempo, señal_filtrada, label='Filtrada')
plt.title('Filtro Anti-Aliasing')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(1, 2, 2)
fft_original = np.fft.fft(señal_original)
fft_filtrada = np.fft.fft(señal_filtrada)
frecuencias = np.fft.fftfreq(len(señal_original), 1/fs)

plt.plot(frecuencias, np.abs(fft_original), label='Original')
plt.plot(frecuencias, np.abs(fft_filtrada), label='Filtrada')
plt.title('Espectros')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud')
plt.xlim(0, fs/2)
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
```

### 3. Cuantización

#### 3.1 Cuantización Uniforme
```python
def cuantizar_señal(señal, bits):
    """
    Cuantizar señal con número específico de bits
    """
    # Calcular niveles de cuantización
    niveles = 2**bits
    max_valor = np.max(np.abs(señal))
    
    # Normalizar y cuantizar
    señal_normalizada = señal / max_valor
    señal_cuantizada = np.round(señal_normalizada * (niveles - 1)) / (niveles - 1)
    
    # Restaurar escala original
    señal_cuantizada *= max_valor
    
    return señal_cuantizada

# Comparar diferentes resoluciones
fs = 44100
tiempo = np.linspace(0, 0.01, int(fs * 0.01))
señal_original = np.sin(2 * np.pi * 1000 * tiempo)

bits_resoluciones = [4, 8, 16, 24]

plt.figure(figsize=(15, 10))

for i, bits in enumerate(bits_resoluciones):
    señal_cuantizada = cuantizar_señal(señal_original, bits)
    
    plt.subplot(2, 2, i+1)
    plt.plot(tiempo, señal_original, 'b-', label='Original', alpha=0.7)
    plt.plot(tiempo, señal_cuantizada, 'r-', label=f'{bits} bits')
    plt.title(f'Cuantización a {bits} bits')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)

plt.tight_layout()
plt.show()
```

#### 3.2 Ruido de Cuantización
```python
def calcular_ruido_cuantizacion(bits):
    """
    Calcular ruido de cuantización
    """
    # SNR teórico para cuantización uniforme
    snr_db = 6.02 * bits + 1.76
    return snr_db

def analizar_ruido_cuantizacion():
    bits = np.arange(4, 25)
    snr_teorico = [calcular_ruido_cuantizacion(b) for b in bits]
    
    plt.figure(figsize=(10, 6))
    plt.plot(bits, snr_teorico, 'b-o', linewidth=2, markersize=8)
    plt.title('SNR vs Resolución de Cuantización')
    plt.xlabel('Bits')
    plt.ylabel('SNR (dB)')
    plt.grid(True)
    plt.xticks(bits[::2])
    
    # Marcar resoluciones comunes
    resoluciones_comunes = [8, 16, 24, 32]
    for res in resoluciones_comunes:
        if res in bits:
            snr = calcular_ruido_cuantizacion(res)
            plt.axvline(res, color='red', linestyle='--', alpha=0.7)
            plt.text(res, snr, f'{res} bits\n{snr:.1f} dB', 
                    ha='center', va='bottom', fontsize=10)
    
    plt.show()

analizar_ruido_cuantizacion()
```

### 4. Dithering

#### 4.1 Tipos de Dithering
```python
def aplicar_dithering(señal, bits, tipo_dither='rectangular'):
    """
    Aplicar dithering antes de cuantización
    """
    # Calcular niveles de cuantización
    niveles = 2**bits
    max_valor = np.max(np.abs(señal))
    
    # Normalizar
    señal_normalizada = señal / max_valor
    
    # Generar dither
    if tipo_dither == 'rectangular':
        dither = (np.random.rand(len(señal)) - 0.5) * (2 / niveles)
    elif tipo_dither == 'triangular':
        dither = (np.random.rand(len(señal)) - 0.5) * (2 / niveles) + (np.random.rand(len(señal)) - 0.5) * (2 / niveles)
    else:
        dither = np.zeros_like(señal)
    
    # Aplicar dither y cuantizar
    señal_con_dither = señal_normalizada + dither
    señal_cuantizada = np.round(señal_con_dither * (niveles - 1)) / (niveles - 1)
    
    # Restaurar escala
    señal_cuantizada *= max_valor
    
    return señal_cuantizada, dither

# Comparar con y sin dithering
fs = 44100
tiempo = np.linspace(0, 0.01, int(fs * 0.01))
señal_original = np.sin(2 * np.pi * 1000 * tiempo)

# Sin dithering
señal_sin_dither = cuantizar_señal(señal_original, 8)

# Con dithering
señal_con_dither, dither = aplicar_dithering(señal_original, 8, 'rectangular')

plt.figure(figsize=(15, 8))

plt.subplot(2, 2, 1)
plt.plot(tiempo, señal_original, 'b-', label='Original')
plt.plot(tiempo, señal_sin_dither, 'r-', label='Sin Dither')
plt.title('Cuantización Sin Dithering')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 2)
plt.plot(tiempo, señal_original, 'b-', label='Original')
plt.plot(tiempo, señal_con_dither, 'g-', label='Con Dither')
plt.title('Cuantización Con Dithering')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 3)
plt.plot(tiempo, dither)
plt.title('Señal de Dithering')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 4)
# Análisis espectral
fft_original = np.fft.fft(señal_original)
fft_sin_dither = np.fft.fft(señal_sin_dither)
fft_con_dither = np.fft.fft(señal_con_dither)
frecuencias = np.fft.fftfreq(len(señal_original), 1/fs)

plt.plot(frecuencias, 20*np.log10(np.abs(fft_original) + 1e-10), label='Original')
plt.plot(frecuencias, 20*np.log10(np.abs(fft_sin_dither) + 1e-10), label='Sin Dither')
plt.plot(frecuencias, 20*np.log10(np.abs(fft_con_dither) + 1e-10), label='Con Dither')
plt.title('Espectros')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, fs/2)
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
```

### 5. Aplicaciones en Audio

#### 5.1 Convertidor ADC Simulado
```python
def simulador_adc(señal_analogica, fs, bits, frecuencia_corte):
    """
    Simular un convertidor ADC completo
    """
    # 1. Filtro anti-aliasing
    señal_filtrada = filtro_anti_aliasing(señal_analogica, fs, frecuencia_corte)
    
    # 2. Muestreo
    tiempo_muestreo = np.arange(0, len(señal_analogica)/fs, 1/fs)
    señal_muestreada = np.interp(tiempo_muestreo, np.linspace(0, len(señal_analogica)/fs, len(señal_analogica)), señal_filtrada)
    
    # 3. Dithering
    señal_con_dither, _ = aplicar_dithering(señal_muestreada, bits, 'rectangular')
    
    # 4. Cuantización
    señal_digital = cuantizar_señal(señal_con_dither, bits)
    
    return señal_digital, señal_filtrada, señal_muestreada

# Simular ADC
fs = 44100
bits = 16
frecuencia_corte = 20000
tiempo = np.linspace(0, 0.01, int(fs * 0.01))
señal_analogica = (np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 3000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 5000 * tiempo))

señal_digital, señal_filtrada, señal_muestreada = simulador_adc(señal_analogica, fs, bits, frecuencia_corte)

plt.figure(figsize=(15, 10))

plt.subplot(3, 1, 1)
plt.plot(tiempo, señal_analogica, label='Analógica')
plt.plot(tiempo, señal_filtrada, label='Filtrada')
plt.title('Filtro Anti-Aliasing')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(tiempo, señal_muestreada, 'ro', markersize=3, label='Muestreada')
plt.plot(tiempo, señal_digital, 'g-', label='Digital')
plt.title('Muestreo y Cuantización')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(3, 1, 3)
# Análisis espectral
fft_analogica = np.fft.fft(señal_analogica)
fft_digital = np.fft.fft(señal_digital)
frecuencias = np.fft.fftfreq(len(señal_analogica), 1/fs)

plt.plot(frecuencias, 20*np.log10(np.abs(fft_analogica) + 1e-10), label='Analógica')
plt.plot(frecuencias, 20*np.log10(np.abs(fft_digital) + 1e-10), label='Digital')
plt.title('Espectros')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, fs/2)
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Análisis de Aliasing
```python
def ejercicio_aliasing():
    """
    Analizar efectos del aliasing en diferentes frecuencias
    """
    frecuencias_senal = [100, 300, 500, 700, 900]
    fs = 1000  # Hz
    
    plt.figure(figsize=(15, 10))
    
    for i, f_senal in enumerate(frecuencias_senal):
        tiempo = np.linspace(0, 0.01, int(fs * 0.01))
        señal = np.sin(2 * np.pi * f_senal * tiempo)
        
        plt.subplot(2, 3, i+1)
        plt.plot(tiempo, señal)
        plt.title(f'Señal a {f_senal} Hz (fs={fs} Hz)')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.grid(True)
        
        # Verificar aliasing
        if f_senal > fs/2:
            plt.text(0.5, 0.8, 'ALIASING!', transform=plt.gca().transAxes, 
                    ha='center', va='center', fontsize=16, color='red',
                    bbox=dict(boxstyle='round', facecolor='yellow', alpha=0.7))
    
    plt.tight_layout()
    plt.show()

ejercicio_aliasing()
```

### Ejercicio 2: Optimización de Parámetros
```python
def ejercicio_optimizacion():
    """
    Optimizar parámetros de muestreo y cuantización
    """
    # Generar señal de prueba
    fs = 44100
    tiempo = np.linspace(0, 0.1, int(fs * 0.1))
    señal_original = (np.sin(2 * np.pi * 1000 * tiempo) + 
                     0.5 * np.sin(2 * np.pi * 3000 * tiempo))
    
    # Diferentes configuraciones
    configuraciones = [
        {'fs': 8000, 'bits': 8, 'nombre': 'Telefonía'},
        {'fs': 22050, 'bits': 16, 'nombre': 'Radio AM'},
        {'fs': 44100, 'bits': 16, 'nombre': 'CD'},
        {'fs': 48000, 'bits': 24, 'nombre': 'Profesional'},
        {'fs': 96000, 'bits': 32, 'nombre': 'Alta Resolución'}
    ]
    
    plt.figure(figsize=(15, 10))
    
    for i, config in enumerate(configuraciones):
        # Simular ADC
        señal_digital, _, _ = simulador_adc(señal_original, config['fs'], config['bits'], config['fs']/2)
        
        # Calcular SNR
        snr = 20 * np.log10(np.std(señal_original) / np.std(señal_original - señal_digital))
        
        plt.subplot(2, 3, i+1)
        plt.plot(tiempo, señal_original, 'b-', label='Original', alpha=0.7)
        plt.plot(tiempo, señal_digital, 'r-', label='Digital', alpha=0.7)
        plt.title(f"{config['nombre']}\nfs={config['fs']} Hz, {config['bits']} bits\nSNR={snr:.1f} dB")
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.legend()
        plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_optimizacion()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **Teorema de Nyquist**: fs > 2 * fmax
2. **Aliasing**: Distorsión por submuestreo
3. **Filtro Anti-Aliasing**: Pasa-bajos antes del muestreo
4. **Cuantización**: Conversión a valores discretos
5. **Ruido de Cuantización**: SNR = 6.02 * bits + 1.76 dB
6. **Dithering**: Ruido controlado para mejorar calidad

### Aplicaciones en Audio
- Diseño de convertidores ADC/DAC
- Optimización de parámetros de muestreo
- Minimización de aliasing
- Mejora de calidad con dithering

### Herramientas Utilizadas
- Simulación de ADC
- Análisis de aliasing
- Medición de ruido de cuantización
- Implementación de dithering

## 🎯 Objetivos Alcanzados
- ✅ Comprender el teorema de Nyquist
- ✅ Identificar y prevenir aliasing
- ✅ Implementar cuantización
- ✅ Aplicar técnicas de dithering
- ✅ Optimizar parámetros de conversión

## 🚀 Navegación

**← Anterior**: [Clase 4.2: Características de las Señales de Audio](clase-4-2-caracteristicas-audio.md)
**Siguiente →**: [Clase 4.4: Sistemas Lineales](clase-4-4-sistemas-lineales.md)

---

*Duración de la clase: 4-5 horas*
