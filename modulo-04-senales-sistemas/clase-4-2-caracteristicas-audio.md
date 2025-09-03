# Clase 4.2: Características de las Señales de Audio

## 🎯 Objetivo de la Clase
Entender las propiedades específicas de las señales de audio, incluyendo rango de frecuencias, dinámica, distorsión y parámetros de calidad, fundamentales para el diseño de sistemas de audio de alta fidelidad.

## 📋 Contenido de la Clase

### 1. Rango de Frecuencias del Audio

#### 1.1 Espectro de Frecuencias Audibles
El oído humano puede percibir frecuencias aproximadamente entre 20 Hz y 20 kHz, aunque este rango varía con la edad y exposición al ruido.

```python
import numpy as np
import matplotlib.pyplot as plt

def visualizar_espectro_audible():
    # Definir rangos de frecuencia
    frecuencias = np.logspace(1, 5, 1000)  # 10 Hz a 100 kHz
    
    # Rango audible humano
    audible_min = 20
    audible_max = 20000
    
    # Rangos de instrumentos musicales
    instrumentos = {
        'Subgraves': (20, 60),
        'Graves': (60, 250),
        'Medios': (250, 2000),
        'Agudos': (2000, 8000),
        'Súper agudos': (8000, 20000)
    }
    
    plt.figure(figsize=(12, 8))
    
    # Crear gráfico de barras para rangos
    y_pos = np.arange(len(instrumentos))
    colores = ['red', 'orange', 'yellow', 'green', 'blue']
    
    for i, (nombre, (f_min, f_max)) in enumerate(instrumentos.items()):
        plt.barh(i, f_max - f_min, left=f_min, color=colores[i], alpha=0.7, label=nombre)
    
    plt.axvline(audible_min, color='red', linestyle='--', linewidth=2, label='Límite audible inferior')
    plt.axvline(audible_max, color='red', linestyle='--', linewidth=2, label='Límite audible superior')
    
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Rangos de Frecuencia')
    plt.title('Espectro de Frecuencias Audibles y Rangos Musicales')
    plt.xscale('log')
    plt.legend()
    plt.grid(True, alpha=0.3)
    plt.show()

visualizar_espectro_audible()
```

#### 1.2 Análisis de Contenido Espectral
```python
def analizar_contenido_espectral(señal, fs):
    """
    Analizar el contenido espectral de una señal de audio
    """
    # Calcular FFT
    fft = np.fft.fft(señal)
    frecuencias = np.fft.fftfreq(len(señal), 1/fs)
    
    # Solo frecuencias positivas
    frec_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    
    # Convertir a dB
    magnitud_db = 20 * np.log10(magnitud + 1e-10)
    
    # Encontrar picos espectrales
    from scipy.signal import find_peaks
    picos, _ = find_peaks(magnitud_db, height=np.max(magnitud_db) - 20)
    
    return frec_positivas, magnitud_db, picos

# Ejemplo con señal musical compleja
def generar_señal_musical_compleja():
    fs = 44100
    duracion = 2.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Acorde mayor (Do-Mi-Sol)
    frecuencias = [261.63, 329.63, 392.00]  # C4, E4, G4
    
    señal = np.zeros_like(tiempo)
    for freq in frecuencias:
        señal += np.sin(2 * np.pi * freq * tiempo)
    
    # Agregar armónicos
    for freq in frecuencias:
        for armónico in range(2, 5):
            señal += 0.3/armónico * np.sin(2 * np.pi * freq * armónico * tiempo)
    
    # Normalizar
    señal = señal / np.max(np.abs(señal))
    
    return tiempo, señal, fs

tiempo, señal_musical, fs = generar_señal_musical_compleja()
frecuencias, magnitud_db, picos = analizar_contenido_espectral(señal_musical, fs)

plt.figure(figsize=(15, 10))

# Señal temporal
plt.subplot(3, 1, 1)
plt.plot(tiempo, señal_musical)
plt.title('Señal Musical Compleja (Acorde Do-Mi-Sol)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

# Espectro completo
plt.subplot(3, 1, 2)
plt.plot(frecuencias, magnitud_db)
plt.title('Espectro de Frecuencias')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 2000)
plt.grid(True)

# Espectro con picos marcados
plt.subplot(3, 1, 3)
plt.plot(frecuencias, magnitud_db)
plt.plot(frecuencias[picos], magnitud_db[picos], 'ro', markersize=8)
plt.title('Picos Espectrales Identificados')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 2000)
plt.grid(True)

plt.tight_layout()
plt.show()

print("Frecuencias de los picos principales:")
for i, pico in enumerate(picos[:10]):
    print(f"  Pico {i+1}: {frecuencias[pico]:.1f} Hz, {magnitud_db[pico]:.1f} dB")
```

### 2. Dinámica de las Señales de Audio

#### 2.1 Rango Dinámico
El rango dinámico es la diferencia entre el nivel más alto y más bajo de una señal.

```python
def calcular_rango_dinamico(señal):
    """
    Calcular el rango dinámico de una señal
    """
    # Calcular RMS
    rms = np.sqrt(np.mean(señal**2))
    
    # Calcular pico
    pico = np.max(np.abs(señal))
    
    # Rango dinámico en dB
    rango_dinamico = 20 * np.log10(pico / (rms + 1e-10))
    
    # Crest factor (relación pico/RMS)
    crest_factor = pico / (rms + 1e-10)
    
    return rango_dinamico, crest_factor, rms, pico

def generar_señal_con_dinamica():
    """
    Generar señal con diferentes niveles de dinámica
    """
    fs = 44100
    duracion = 3.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Señal con variación de amplitud
    envolvente = np.exp(-tiempo) * (1 + 0.5 * np.sin(2 * np.pi * 0.5 * tiempo))
    señal_base = np.sin(2 * np.pi * 440 * tiempo)
    
    # Aplicar envolvente
    señal_dinamica = señal_base * envolvente
    
    return tiempo, señal_dinamica

tiempo, señal_dinamica = generar_señal_con_dinamica()
rango_dinamico, crest_factor, rms, pico = calcular_rango_dinamico(señal_dinamica)

plt.figure(figsize=(15, 8))

plt.subplot(2, 2, 1)
plt.plot(tiempo, señal_dinamica)
plt.title('Señal con Dinámica Variable')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 2)
plt.plot(tiempo, np.abs(señal_dinamica))
plt.title('Envolvente de Amplitud')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 2, 3)
plt.hist(señal_dinamica, bins=50, alpha=0.7, edgecolor='black')
plt.title('Distribución de Amplitudes')
plt.xlabel('Amplitud')
plt.ylabel('Frecuencia')
plt.grid(True)

plt.subplot(2, 2, 4)
# Mostrar métricas
plt.text(0.1, 0.8, f'Rango Dinámico: {rango_dinamico:.1f} dB', transform=plt.gca().transAxes, fontsize=12)
plt.text(0.1, 0.7, f'Crest Factor: {crest_factor:.2f}', transform=plt.gca().transAxes, fontsize=12)
plt.text(0.1, 0.6, f'RMS: {rms:.3f}', transform=plt.gca().transAxes, fontsize=12)
plt.text(0.1, 0.5, f'Pico: {pico:.3f}', transform=plt.gca().transAxes, fontsize=12)
plt.title('Métricas de Dinámica')
plt.axis('off')

plt.tight_layout()
plt.show()
```

#### 2.2 Compresión de Dinámica
```python
def compresor_dinamico(señal, ratio, threshold, attack, release, fs):
    """
    Implementar un compresor de dinámica simple
    """
    señal_comprimida = np.zeros_like(señal)
    ganancia = np.ones_like(señal)
    
    # Parámetros de tiempo
    attack_samples = int(attack * fs)
    release_samples = int(release * fs)
    
    for i in range(len(señal)):
        # Calcular nivel de entrada
        nivel_entrada = np.abs(señal[i])
        
        # Determinar ganancia necesaria
        if nivel_entrada > threshold:
            # Calcular compresión
            exceso = nivel_entrada - threshold
            ganancia_objetivo = threshold + exceso / ratio
            ganancia_objetivo = ganancia_objetivo / nivel_entrada
        else:
            ganancia_objetivo = 1.0
        
        # Aplicar tiempos de ataque y liberación
        if ganancia_objetivo < ganancia[i-1] if i > 0 else 1.0:
            # Ataque
            alpha = np.exp(-1 / attack_samples)
        else:
            # Liberación
            alpha = np.exp(-1 / release_samples)
        
        ganancia[i] = alpha * ganancia[i-1] + (1 - alpha) * ganancia_objetivo
        
        # Aplicar ganancia
        señal_comprimida[i] = señal[i] * ganancia[i]
    
    return señal_comprimida, ganancia

# Aplicar compresión
señal_comprimida, ganancia = compresor_dinamico(
    señal_dinamica, 
    ratio=4, 
    threshold=0.3, 
    attack=0.01, 
    release=0.1, 
    fs=44100
)

plt.figure(figsize=(15, 10))

plt.subplot(3, 1, 1)
plt.plot(tiempo, señal_dinamica, label='Original', alpha=0.7)
plt.plot(tiempo, señal_comprimida, label='Comprimida', alpha=0.7)
plt.title('Comparación: Señal Original vs Comprimida')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(tiempo, ganancia)
plt.title('Ganancia del Compresor')
plt.xlabel('Tiempo (s)')
plt.ylabel('Ganancia')
plt.grid(True)

plt.subplot(3, 1, 3)
# Comparar rangos dinámicos
rango_original, _, _, _ = calcular_rango_dinamico(señal_dinamica)
rango_comprimido, _, _, _ = calcular_rango_dinamico(señal_comprimida)

plt.bar(['Original', 'Comprimida'], [rango_original, rango_comprimido], color=['blue', 'red'])
plt.title('Comparación de Rangos Dinámicos')
plt.ylabel('Rango Dinámico (dB)')
plt.grid(True)

plt.tight_layout()
plt.show()

print(f"Rango dinámico original: {rango_original:.1f} dB")
print(f"Rango dinámico comprimido: {rango_comprimido:.1f} dB")
print(f"Reducción de rango dinámico: {rango_original - rango_comprimido:.1f} dB")
```

### 3. Distorsión en Señales de Audio

#### 3.1 Tipos de Distorsión
```python
def generar_distorsion_armonica(señal, nivel_distorsion):
    """
    Generar distorsión armónica
    """
    # Distorsión de segundo orden (armónico 2)
    distorsion_2 = nivel_distorsion * señal**2
    
    # Distorsión de tercer orden (armónico 3)
    distorsion_3 = nivel_distorsion * señal**3
    
    # Señal con distorsión
    señal_distorsionada = señal + distorsion_2 + distorsion_3
    
    return señal_distorsionada

def generar_distorsion_intermodulacion(señal1, señal2, nivel_distorsion):
    """
    Generar distorsión de intermodulación
    """
    # Productos de intermodulación
    imd_2 = nivel_distorsion * señal1 * señal2
    imd_3 = nivel_distorsion * señal1**2 * señal2
    
    # Señal combinada con IMD
    señal_imd = señal1 + señal2 + imd_2 + imd_3
    
    return señal_imd

# Generar señales de prueba
fs = 44100
duracion = 1.0
tiempo = np.linspace(0, duracion, int(fs * duracion))

# Señal fundamental
frecuencia_fundamental = 1000  # Hz
señal_fundamental = np.sin(2 * np.pi * frecuencia_fundamental * tiempo)

# Generar distorsión armónica
señal_armonica = generar_distorsion_armonica(señal_fundamental, 0.1)

# Generar distorsión de intermodulación
frecuencia_2 = 1100  # Hz
señal_2 = np.sin(2 * np.pi * frecuencia_2 * tiempo)
señal_imd = generar_distorsion_intermodulacion(señal_fundamental, señal_2, 0.05)

plt.figure(figsize=(15, 12))

# Señal original
plt.subplot(3, 2, 1)
plt.plot(tiempo[:1000], señal_fundamental[:1000])
plt.title('Señal Original (1 kHz)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

# Señal con distorsión armónica
plt.subplot(3, 2, 2)
plt.plot(tiempo[:1000], señal_armonica[:1000])
plt.title('Señal con Distorsión Armónica')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

# Espectro original
plt.subplot(3, 2, 3)
frecuencias, magnitud_db, _ = analizar_contenido_espectral(señal_fundamental, fs)
plt.plot(frecuencias, magnitud_db)
plt.title('Espectro Original')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 5000)
plt.grid(True)

# Espectro con distorsión armónica
plt.subplot(3, 2, 4)
frecuencias, magnitud_db, _ = analizar_contenido_espectral(señal_armonica, fs)
plt.plot(frecuencias, magnitud_db)
plt.title('Espectro con Distorsión Armónica')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 5000)
plt.grid(True)

# Señal con IMD
plt.subplot(3, 2, 5)
plt.plot(tiempo[:1000], señal_imd[:1000])
plt.title('Señal con Distorsión de Intermodulación')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

# Espectro con IMD
plt.subplot(3, 2, 6)
frecuencias, magnitud_db, _ = analizar_contenido_espectral(señal_imd, fs)
plt.plot(frecuencias, magnitud_db)
plt.title('Espectro con IMD')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 5000)
plt.grid(True)

plt.tight_layout()
plt.show()
```

#### 3.2 Medición de Distorsión
```python
def calcular_thd(señal, frecuencia_fundamental, fs):
    """
    Calcular Total Harmonic Distortion (THD)
    """
    # Calcular FFT
    fft = np.fft.fft(señal)
    frecuencias = np.fft.fftfreq(len(señal), 1/fs)
    
    # Encontrar armónicos
    armonicos = []
    for n in range(1, 6):  # Primeros 5 armónicos
        freq_armonico = n * frecuencia_fundamental
        # Encontrar el bin más cercano
        idx = np.argmin(np.abs(frecuencias - freq_armonico))
        armonicos.append(np.abs(fft[idx]))
    
    # Calcular THD
    thd = np.sqrt(np.sum(np.array(armonicos[1:])**2)) / armonicos[0] * 100
    
    return thd, armonicos

def calcular_imd(señal, frecuencia1, frecuencia2, fs):
    """
    Calcular Intermodulation Distortion (IMD)
    """
    # Calcular FFT
    fft = np.fft.fft(señal)
    frecuencias = np.fft.fftfreq(len(señal), 1/fs)
    
    # Frecuencias de intermodulación
    imd_freqs = [2*frecuencia1 - frecuencia2, 2*frecuencia2 - frecuencia1]
    
    # Encontrar amplitudes
    imd_amplitudes = []
    for freq in imd_freqs:
        idx = np.argmin(np.abs(frecuencias - freq))
        imd_amplitudes.append(np.abs(fft[idx]))
    
    # Calcular IMD
    imd = np.sqrt(np.sum(np.array(imd_amplitudes)**2)) / np.max(imd_amplitudes) * 100
    
    return imd, imd_amplitudes

# Calcular métricas de distorsión
thd, armonicos = calcular_thd(señal_armonica, frecuencia_fundamental, fs)
imd, imd_amps = calcular_imd(señal_imd, frecuencia_fundamental, frecuencia_2, fs)

print("Métricas de Distorsión:")
print(f"THD: {thd:.2f}%")
print(f"IMD: {imd:.2f}%")
print(f"Armónicos: {[f'{amp:.3f}' for amp in armonicos]}")
print(f"Productos IMD: {[f'{amp:.3f}' for amp in imd_amps]}")
```

### 4. Parámetros de Calidad de Audio

#### 4.1 Signal-to-Noise Ratio (SNR)
```python
def calcular_snr(señal, ruido):
    """
    Calcular Signal-to-Noise Ratio
    """
    # Calcular potencias
    potencia_senal = np.mean(señal**2)
    potencia_ruido = np.mean(ruido**2)
    
    # SNR en dB
    snr_db = 10 * np.log10(potencia_senal / potencia_ruido)
    
    return snr_db

def generar_ruido_tipos():
    """
    Generar diferentes tipos de ruido
    """
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Señal de referencia
    señal = np.sin(2 * np.pi * 1000 * tiempo)
    
    # Diferentes tipos de ruido
    ruido_blanco = np.random.randn(len(tiempo))
    ruido_rosa = np.random.randn(len(tiempo)) * np.sqrt(1 / (np.arange(len(tiempo)) + 1))
    ruido_marrón = np.random.randn(len(tiempo)) * (1 / (np.arange(len(tiempo)) + 1))
    
    return tiempo, señal, ruido_blanco, ruido_rosa, ruido_marrón

tiempo, señal, ruido_blanco, ruido_rosa, ruido_marrón = generar_ruido_tipos()

# Calcular SNR para diferentes tipos de ruido
snr_blanco = calcular_snr(señal, ruido_blanco)
snr_rosa = calcular_snr(señal, ruido_rosa)
snr_marrón = calcular_snr(señal, ruido_marrón)

plt.figure(figsize=(15, 10))

# Comparar tipos de ruido
plt.subplot(2, 3, 1)
plt.plot(tiempo[:1000], ruido_blanco[:1000])
plt.title('Ruido Blanco')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 3, 2)
plt.plot(tiempo[:1000], ruido_rosa[:1000])
plt.title('Ruido Rosa')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

plt.subplot(2, 3, 3)
plt.plot(tiempo[:1000], ruido_marrón[:1000])
plt.title('Ruido Marrón')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)

# Espectros de ruido
plt.subplot(2, 3, 4)
frecuencias, magnitud_db, _ = analizar_contenido_espectral(ruido_blanco, 44100)
plt.plot(frecuencias, magnitud_db)
plt.title('Espectro Ruido Blanco')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 10000)
plt.grid(True)

plt.subplot(2, 3, 5)
frecuencias, magnitud_db, _ = analizar_contenido_espectral(ruido_rosa, 44100)
plt.plot(frecuencias, magnitud_db)
plt.title('Espectro Ruido Rosa')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 10000)
plt.grid(True)

plt.subplot(2, 3, 6)
frecuencias, magnitud_db, _ = analizar_contenido_espectral(ruido_marrón, 44100)
plt.plot(frecuencias, magnitud_db)
plt.title('Espectro Ruido Marrón')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 10000)
plt.grid(True)

plt.tight_layout()
plt.show()

print(f"SNR con ruido blanco: {snr_blanco:.1f} dB")
print(f"SNR con ruido rosa: {snr_rosa:.1f} dB")
print(f"SNR con ruido marrón: {snr_marrón:.1f} dB")
```

#### 4.2 Análisis de Calidad Integral
```python
def analisis_calidad_integral(señal, fs):
    """
    Análisis completo de calidad de audio
    """
    # Calcular métricas básicas
    rango_dinamico, crest_factor, rms, pico = calcular_rango_dinamico(señal)
    
    # Calcular THD (asumiendo señal fundamental)
    thd, _ = calcular_thd(señal, 1000, fs)
    
    # Calcular SNR (estimado)
    ruido_estimado = señal - np.sin(2 * np.pi * 1000 * np.linspace(0, len(señal)/fs, len(señal)))
    snr = calcular_snr(señal, ruido_estimado)
    
    # Calcular ancho de banda
    frecuencias, magnitud_db, _ = analizar_contenido_espectral(señal, fs)
    # Encontrar -3dB points
    max_magnitud = np.max(magnitud_db)
    idx_3db = np.where(magnitud_db >= max_magnitud - 3)[0]
    if len(idx_3db) > 0:
        ancho_banda = frecuencias[idx_3db[-1]] - frecuencias[idx_3db[0]]
    else:
        ancho_banda = 0
    
    return {
        'rango_dinamico': rango_dinamico,
        'crest_factor': crest_factor,
        'thd': thd,
        'snr': snr,
        'ancho_banda': ancho_banda,
        'rms': rms,
        'pico': pico
    }

# Análisis de calidad
metricas = analisis_calidad_integral(señal_musical, fs)

print("Análisis de Calidad de Audio:")
print("=" * 40)
for metrica, valor in metricas.items():
    if metrica in ['rango_dinamico', 'snr']:
        print(f"{metrica.replace('_', ' ').title()}: {valor:.1f} dB")
    elif metrica in ['thd']:
        print(f"{metrica.upper()}: {valor:.2f}%")
    elif metrica in ['ancho_banda']:
        print(f"{metrica.replace('_', ' ').title()}: {valor:.0f} Hz")
    else:
        print(f"{metrica.replace('_', ' ').title()}: {valor:.3f}")
```

### 5. Aplicaciones en Sistemas de Audio

#### 5.1 Diseño de Preamplificadores
```python
def diseñar_preamplificador(señal_entrada, ganancia, ruido_entrada):
    """
    Simular un preamplificador de audio
    """
    # Aplicar ganancia
    señal_amplificada = señal_entrada * ganancia
    
    # Agregar ruido del amplificador
    ruido_amplificador = ruido_entrada * np.random.randn(len(señal_entrada))
    señal_salida = señal_amplificada + ruido_amplificador
    
    # Limitar a rango válido
    señal_salida = np.clip(señal_salida, -1, 1)
    
    return señal_salida

# Simular preamplificador
señal_preamplificada = diseñar_preamplificador(señal_musical, 10, 0.001)

plt.figure(figsize=(15, 8))

plt.subplot(2, 2, 1)
plt.plot(tiempo, señal_musical, label='Entrada', alpha=0.7)
plt.plot(tiempo, señal_preamplificada, label='Salida', alpha=0.7)
plt.title('Preamplificador de Audio')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 2)
# Comparar espectros
frecuencias, magnitud_entrada, _ = analizar_contenido_espectral(señal_musical, fs)
frecuencias, magnitud_salida, _ = analizar_contenido_espectral(señal_preamplificada, fs)

plt.plot(frecuencias, magnitud_entrada, label='Entrada', alpha=0.7)
plt.plot(frecuencias, magnitud_salida, label='Salida', alpha=0.7)
plt.title('Espectros de Entrada y Salida')
plt.xlabel('Frecuencia (Hz)')
plt.ylabel('Magnitud (dB)')
plt.xlim(0, 5000)
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 3)
# Análisis de distorsión
thd_entrada, _ = calcular_thd(señal_musical, 261.63, fs)
thd_salida, _ = calcular_thd(señal_preamplificada, 261.63, fs)

plt.bar(['Entrada', 'Salida'], [thd_entrada, thd_salida], color=['blue', 'red'])
plt.title('THD: Entrada vs Salida')
plt.ylabel('THD (%)')
plt.grid(True)

plt.subplot(2, 2, 4)
# Análisis de SNR
snr_entrada = calcular_snr(señal_musical, np.random.randn(len(señal_musical)) * 0.001)
snr_salida = calcular_snr(señal_preamplificada, np.random.randn(len(señal_preamplificada)) * 0.001)

plt.bar(['Entrada', 'Salida'], [snr_entrada, snr_salida], color=['blue', 'red'])
plt.title('SNR: Entrada vs Salida')
plt.ylabel('SNR (dB)')
plt.grid(True)

plt.tight_layout()
plt.show()
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Análisis de Señal de Audio Real
```python
def ejercicio_analisis_audio():
    """
    Análisis completo de una señal de audio simulada
    """
    # Generar señal compleja
    fs = 44100
    duracion = 2.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Señal con múltiples componentes
    señal = (np.sin(2 * np.pi * 440 * tiempo) +
             0.5 * np.sin(2 * np.pi * 880 * tiempo) +
             0.3 * np.sin(2 * np.pi * 1320 * tiempo) +
             0.1 * np.sin(2 * np.pi * 1760 * tiempo))
    
    # Agregar ruido
    ruido = 0.05 * np.random.randn(len(tiempo))
    señal_con_ruido = señal + ruido
    
    # Análisis completo
    print("Análisis de Señal de Audio")
    print("=" * 50)
    
    # Métricas básicas
    rango_dinamico, crest_factor, rms, pico = calcular_rango_dinamico(señal_con_ruido)
    print(f"Rango Dinámico: {rango_dinamico:.1f} dB")
    print(f"Crest Factor: {crest_factor:.2f}")
    print(f"RMS: {rms:.3f}")
    print(f"Pico: {pico:.3f}")
    
    # Análisis espectral
    frecuencias, magnitud_db, picos = analizar_contenido_espectral(señal_con_ruido, fs)
    print(f"\nFrecuencias dominantes:")
    for i, pico in enumerate(picos[:5]):
        print(f"  {i+1}. {frecuencias[pico]:.1f} Hz: {magnitud_db[pico]:.1f} dB")
    
    # THD
    thd, _ = calcular_thd(señal_con_ruido, 440, fs)
    print(f"\nTHD: {thd:.2f}%")
    
    # SNR
    snr = calcular_snr(señal, ruido)
    print(f"SNR: {snr:.1f} dB")
    
    return señal_con_ruido, tiempo, fs

señal_ejercicio, tiempo_ejercicio, fs_ejercicio = ejercicio_analisis_audio()
```

### Ejercicio 2: Comparación de Calidad
```python
def ejercicio_comparacion_calidad():
    """
    Comparar calidad de diferentes señales
    """
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar diferentes señales
    señales = {
        'Alta Calidad': np.sin(2 * np.pi * 1000 * tiempo),
        'Media Calidad': np.sin(2 * np.pi * 1000 * tiempo) + 0.01 * np.random.randn(len(tiempo)),
        'Baja Calidad': np.sin(2 * np.pi * 1000 * tiempo) + 0.1 * np.random.randn(len(tiempo))
    }
    
    # Analizar cada señal
    resultados = {}
    for nombre, señal in señales.items():
        resultados[nombre] = analisis_calidad_integral(señal, fs)
    
    # Crear gráfico comparativo
    fig, axes = plt.subplots(2, 2, figsize=(15, 10))
    
    # SNR
    snr_values = [resultados[nombre]['snr'] for nombre in señales.keys()]
    axes[0, 0].bar(señales.keys(), snr_values, color=['green', 'orange', 'red'])
    axes[0, 0].set_title('SNR Comparativo')
    axes[0, 0].set_ylabel('SNR (dB)')
    axes[0, 0].grid(True)
    
    # THD
    thd_values = [resultados[nombre]['thd'] for nombre in señales.keys()]
    axes[0, 1].bar(señales.keys(), thd_values, color=['green', 'orange', 'red'])
    axes[0, 1].set_title('THD Comparativo')
    axes[0, 1].set_ylabel('THD (%)')
    axes[0, 1].grid(True)
    
    # Rango Dinámico
    rango_values = [resultados[nombre]['rango_dinamico'] for nombre in señales.keys()]
    axes[1, 0].bar(señales.keys(), rango_values, color=['green', 'orange', 'red'])
    axes[1, 0].set_title('Rango Dinámico Comparativo')
    axes[1, 0].set_ylabel('Rango Dinámico (dB)')
    axes[1, 0].grid(True)
    
    # Crest Factor
    crest_values = [resultados[nombre]['crest_factor'] for nombre in señales.keys()]
    axes[1, 1].bar(señales.keys(), crest_values, color=['green', 'orange', 'red'])
    axes[1, 1].set_title('Crest Factor Comparativo')
    axes[1, 1].set_ylabel('Crest Factor')
    axes[1, 1].grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return resultados

resultados_comparacion = ejercicio_comparacion_calidad()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **Rango de Frecuencias**: 20 Hz - 20 kHz (audible humano)
2. **Dinámica**: Diferencia entre niveles máximo y mínimo
3. **Distorsión**: Alteración no deseada de la señal
4. **THD**: Total Harmonic Distortion
5. **IMD**: Intermodulation Distortion
6. **SNR**: Signal-to-Noise Ratio
7. **Crest Factor**: Relación pico/RMS

### Aplicaciones en Audio
- Diseño de preamplificadores
- Análisis de calidad de audio
- Compresión de dinámica
- Medición de distorsión
- Optimización de sistemas

### Herramientas Utilizadas
- Análisis espectral (FFT)
- Medición de distorsión
- Análisis de ruido
- Compresión de dinámica
- Métricas de calidad

## 🎯 Objetivos Alcanzados
- ✅ Comprender el rango de frecuencias del audio
- ✅ Analizar la dinámica de las señales
- ✅ Identificar tipos de distorsión
- ✅ Medir parámetros de calidad
- ✅ Aplicar conceptos a sistemas reales

## 🚀 Navegación

**← Anterior**: [Clase 4.1: Introducción a las Señales](clase-4-1-introduccion-senales.md)
**Siguiente →**: [Clase 4.3: Muestreo y Cuantización](clase-4-3-muestreo-cuantizacion.md)

---

*Duración de la clase: 3-4 horas*
