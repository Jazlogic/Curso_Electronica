# Clase 4.8: Medición de Señales

## 🎯 Objetivo de la Clase
Usar instrumentos para medir señales de audio, incluyendo osciloscopio, analizador de espectro, multímetro y técnicas de calibración para sistemas de audio de alta calidad.

## 📋 Contenido de la Clase

### 1. Osciloscopio

#### 1.1 Fundamentos del Osciloscopio
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

def simular_osciloscopio():
    """
    Simular funcionamiento de un osciloscopio
    """
    # Parámetros
    fs = 100000  # Frecuencia de muestreo del osciloscopio
    duracion = 0.01  # 10 ms
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar señal de prueba
    señal_audio = (np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 3000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 5000 * tiempo))
    
    # Agregar ruido
    ruido = 0.1 * np.random.randn(len(tiempo))
    señal_con_ruido = señal_audio + ruido
    
    plt.figure(figsize=(15, 10))
    
    # Señal original
    plt.subplot(2, 2, 1)
    plt.plot(tiempo * 1000, señal_audio)  # Convertir a ms
    plt.title('Señal de Audio Original')
    plt.xlabel('Tiempo (ms)')
    plt.ylabel('Amplitud (V)')
    plt.grid(True)
    
    # Señal con ruido
    plt.subplot(2, 2, 2)
    plt.plot(tiempo * 1000, señal_con_ruido)
    plt.title('Señal con Ruido')
    plt.xlabel('Tiempo (ms)')
    plt.ylabel('Amplitud (V)')
    plt.grid(True)
    
    # Medición de parámetros
    plt.subplot(2, 2, 3)
    # Calcular parámetros
    amplitud_pico = np.max(np.abs(señal_con_ruido))
    amplitud_rms = np.sqrt(np.mean(señal_con_ruido**2))
    frecuencia_fundamental = 1000  # Hz
    
    plt.text(0.1, 0.8, f'Amplitud Pico: {amplitud_pico:.3f} V', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Amplitud RMS: {amplitud_rms:.3f} V', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Frecuencia: {frecuencia_fundamental} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Período: {1/frecuencia_fundamental*1000:.3f} ms', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Crest Factor: {amplitud_pico/amplitud_rms:.2f}', transform=plt.gca().transAxes)
    plt.title('Parámetros Medidos')
    plt.axis('off')
    
    # Análisis de ruido
    plt.subplot(2, 2, 4)
    # Calcular SNR
    snr = 20 * np.log10(amplitud_rms / np.std(ruido))
    
    plt.text(0.1, 0.8, f'SNR: {snr:.1f} dB', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Ruido RMS: {np.std(ruido):.3f} V', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Rango Dinámico: {20*np.log10(amplitud_pico/np.std(ruido)):.1f} dB', transform=plt.gca().transAxes)
    plt.title('Análisis de Ruido')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return señal_con_ruido, amplitud_pico, amplitud_rms, snr

señal_osciloscopio, amplitud_pico_osciloscopio, amplitud_rms_osciloscopio, snr_osciloscopio = simular_osciloscopio()
```

#### 1.2 Medición de Distorsión
```python
def medir_distorsion_osciloscopio():
    """
    Medir distorsión usando osciloscopio
    """
    # Parámetros
    fs = 100000
    duracion = 0.01
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar señal con distorsión
    f0 = 1000  # Hz
    señal_fundamental = np.sin(2 * np.pi * f0 * tiempo)
    
    # Agregar distorsión armónica
    distorsion_2 = 0.1 * np.sin(2 * np.pi * 2 * f0 * tiempo)
    distorsion_3 = 0.05 * np.sin(2 * np.pi * 3 * f0 * tiempo)
    distorsion_4 = 0.03 * np.sin(2 * np.pi * 4 * f0 * tiempo)
    
    señal_con_distorsion = señal_fundamental + distorsion_2 + distorsion_3 + distorsion_4
    
    plt.figure(figsize=(15, 10))
    
    # Señal en tiempo
    plt.subplot(2, 2, 1)
    plt.plot(tiempo * 1000, señal_fundamental, label='Fundamental', alpha=0.7)
    plt.plot(tiempo * 1000, señal_con_distorsion, label='Con Distorsión', alpha=0.7)
    plt.title('Señal en Dominio Temporal')
    plt.xlabel('Tiempo (ms)')
    plt.ylabel('Amplitud (V)')
    plt.legend()
    plt.grid(True)
    
    # Análisis espectral
    plt.subplot(2, 2, 2)
    fft = np.fft.fft(señal_con_distorsion)
    frecuencias = np.fft.fftfreq(len(señal_con_distorsion), 1/fs)
    
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    
    plt.plot(frecuencias_positivas, magnitud)
    plt.axvline(f0, color='red', linestyle='--', label=f'Fundamental: {f0} Hz')
    plt.axvline(2*f0, color='orange', linestyle='--', label=f'Armónico 2: {2*f0} Hz')
    plt.axvline(3*f0, color='orange', linestyle='--', label=f'Armónico 3: {3*f0} Hz')
    plt.axvline(4*f0, color='orange', linestyle='--', label=f'Armónico 4: {4*f0} Hz')
    plt.title('Espectro de Frecuencias')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud')
    plt.xlim(0, 5000)
    plt.legend()
    plt.grid(True)
    
    # Medición de THD
    plt.subplot(2, 2, 3)
    # Calcular THD
    thd_total = 0
    for n in range(2, 5):
        freq_armonico = n * f0
        idx = np.argmin(np.abs(frecuencias - freq_armonico))
        thd_total += np.abs(fft[idx])**2
    
    idx_fundamental = np.argmin(np.abs(frecuencias - f0))
    amplitud_fundamental = np.abs(fft[idx_fundamental])
    thd_percent = np.sqrt(thd_total) / amplitud_fundamental * 100
    
    plt.bar(['THD'], [thd_percent], color='red', alpha=0.7)
    plt.title('Total Harmonic Distortion')
    plt.ylabel('THD (%)')
    plt.grid(True)
    
    # Parámetros de distorsión
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'THD: {thd_percent:.2f}%', transform=plt.gca().transAxes, fontsize=14)
    plt.text(0.1, 0.7, f'Frecuencia Fundamental: {f0} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Armónicos Medidos: 2, 3, 4', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Amplitud Fundamental: {amplitud_fundamental:.3f}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Frecuencia de Muestreo: {fs} Hz', transform=plt.gca().transAxes)
    plt.title('Parámetros de Distorsión')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return thd_percent, frecuencias, fft

thd_osciloscopio, frecuencias_osciloscopio, fft_osciloscopio = medir_distorsion_osciloscopio()
```

### 2. Analizador de Espectro

#### 2.1 Análisis Espectral
```python
def analizador_espectro():
    """
    Simular analizador de espectro
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar señal compleja
    señal_audio = (np.sin(2 * np.pi * 100 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 5000 * tiempo) + 
                   0.1 * np.sin(2 * np.pi * 10000 * tiempo))
    
    # Agregar ruido
    ruido = 0.05 * np.random.randn(len(tiempo))
    señal_con_ruido = señal_audio + ruido
    
    plt.figure(figsize=(15, 10))
    
    # Señal en tiempo
    plt.subplot(2, 2, 1)
    plt.plot(tiempo, señal_con_ruido)
    plt.title('Señal en Dominio Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud (V)')
    plt.grid(True)
    
    # Espectro de magnitud
    plt.subplot(2, 2, 2)
    fft = np.fft.fft(señal_con_ruido)
    frecuencias = np.fft.fftfreq(len(señal_con_ruido), 1/fs)
    
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    magnitud_db = 20 * np.log10(magnitud + 1e-10)
    
    plt.semilogx(frecuencias_positivas, magnitud_db)
    plt.title('Espectro de Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    # Espectro de fase
    plt.subplot(2, 2, 3)
    fase = np.angle(fft[:len(fft)//2])
    fase_grados = np.degrees(fase)
    
    plt.semilogx(frecuencias_positivas, fase_grados)
    plt.title('Espectro de Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.grid(True)
    
    # Análisis de picos
    plt.subplot(2, 2, 4)
    # Encontrar picos espectrales
    from scipy.signal import find_peaks
    picos, _ = find_peaks(magnitud_db, height=np.max(magnitud_db) - 20)
    
    plt.semilogx(frecuencias_positivas, magnitud_db)
    plt.plot(frecuencias_positivas[picos], magnitud_db[picos], 'ro', markersize=8)
    plt.title('Picos Espectrales Identificados')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return frecuencias_positivas, magnitud_db, fase_grados, picos

frecuencias_espectro, magnitud_espectro, fase_espectro, picos_espectro = analizador_espectro()
```

#### 2.2 Análisis de Ruido
```python
def analizar_ruido_espectro():
    """
    Analizar ruido usando analizador de espectro
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar diferentes tipos de ruido
    ruido_blanco = np.random.randn(len(tiempo))
    ruido_rosa = np.random.randn(len(tiempo)) * np.sqrt(1 / (np.arange(len(tiempo)) + 1))
    ruido_marrón = np.random.randn(len(tiempo)) * (1 / (np.arange(len(tiempo)) + 1))
    
    plt.figure(figsize=(15, 10))
    
    # Ruido blanco
    plt.subplot(2, 3, 1)
    plt.plot(tiempo[:1000], ruido_blanco[:1000])
    plt.title('Ruido Blanco')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Ruido rosa
    plt.subplot(2, 3, 2)
    plt.plot(tiempo[:1000], ruido_rosa[:1000])
    plt.title('Ruido Rosa')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Ruido marrón
    plt.subplot(2, 3, 3)
    plt.plot(tiempo[:1000], ruido_marrón[:1000])
    plt.title('Ruido Marrón')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Espectros
    plt.subplot(2, 3, 4)
    fft_blanco = np.fft.fft(ruido_blanco)
    frecuencias = np.fft.fftfreq(len(ruido_blanco), 1/fs)
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    magnitud_blanco = 20 * np.log10(np.abs(fft_blanco[:len(fft_blanco)//2]) + 1e-10)
    
    plt.semilogx(frecuencias_positivas, magnitud_blanco)
    plt.title('Espectro Ruido Blanco')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.subplot(2, 3, 5)
    fft_rosa = np.fft.fft(ruido_rosa)
    magnitud_rosa = 20 * np.log10(np.abs(fft_rosa[:len(fft_rosa)//2]) + 1e-10)
    
    plt.semilogx(frecuencias_positivas, magnitud_rosa)
    plt.title('Espectro Ruido Rosa')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.subplot(2, 3, 6)
    fft_marrón = np.fft.fft(ruido_marrón)
    magnitud_marrón = 20 * np.log10(np.abs(fft_marrón[:len(fft_marrón)//2]) + 1e-10)
    
    plt.semilogx(frecuencias_positivas, magnitud_marrón)
    plt.title('Espectro Ruido Marrón')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return ruido_blanco, ruido_rosa, ruido_marrón, frecuencias_positivas

ruido_blanco_espectro, ruido_rosa_espectro, ruido_marrón_espectro, frecuencias_ruido = analizar_ruido_espectro()
```

### 3. Multímetro

#### 3.1 Medición de Parámetros Básicos
```python
def medir_parametros_multimetro():
    """
    Medir parámetros básicos usando multímetro
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar señal de audio
    señal_audio = (np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 2000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 3000 * tiempo))
    
    # Agregar ruido
    ruido = 0.1 * np.random.randn(len(tiempo))
    señal_con_ruido = señal_audio + ruido
    
    plt.figure(figsize=(15, 10))
    
    # Señal en tiempo
    plt.subplot(2, 2, 1)
    plt.plot(tiempo, señal_con_ruido)
    plt.title('Señal de Audio')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud (V)')
    plt.grid(True)
    
    # Medición de voltaje
    plt.subplot(2, 2, 2)
    voltaje_dc = np.mean(señal_con_ruido)
    voltaje_ac = np.sqrt(np.mean((señal_con_ruido - voltaje_dc)**2))
    voltaje_pico = np.max(np.abs(señal_con_ruido))
    voltaje_pico_pico = np.max(señal_con_ruido) - np.min(señal_con_ruido)
    
    plt.bar(['DC', 'AC RMS', 'Pico', 'Pico-Pico'], 
            [voltaje_dc, voltaje_ac, voltaje_pico, voltaje_pico_pico],
            color=['blue', 'green', 'red', 'orange'], alpha=0.7)
    plt.title('Mediciones de Voltaje')
    plt.ylabel('Voltaje (V)')
    plt.grid(True)
    
    # Medición de frecuencia
    plt.subplot(2, 2, 3)
    # Calcular FFT
    fft = np.fft.fft(señal_con_ruido)
    frecuencias = np.fft.fftfreq(len(señal_con_ruido), 1/fs)
    
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    
    # Encontrar frecuencia fundamental
    idx_fundamental = np.argmax(magnitud)
    frecuencia_fundamental = frecuencias_positivas[idx_fundamental]
    
    plt.plot(frecuencias_positivas, magnitud)
    plt.axvline(frecuencia_fundamental, color='red', linestyle='--', 
                label=f'Fundamental: {frecuencia_fundamental:.1f} Hz')
    plt.title('Análisis de Frecuencia')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud')
    plt.legend()
    plt.grid(True)
    
    # Resumen de mediciones
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'Voltaje DC: {voltaje_dc:.3f} V', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Voltaje AC RMS: {voltaje_ac:.3f} V', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Voltaje Pico: {voltaje_pico:.3f} V', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Voltaje Pico-Pico: {voltaje_pico_pico:.3f} V', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Frecuencia Fundamental: {frecuencia_fundamental:.1f} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.3, f'Período: {1/frecuencia_fundamental*1000:.3f} ms', transform=plt.gca().transAxes)
    plt.text(0.1, 0.2, f'Crest Factor: {voltaje_pico/voltaje_ac:.2f}', transform=plt.gca().transAxes)
    plt.title('Resumen de Mediciones')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return voltaje_dc, voltaje_ac, voltaje_pico, frecuencia_fundamental

voltaje_dc_multimetro, voltaje_ac_multimetro, voltaje_pico_multimetro, frecuencia_fundamental_multimetro = medir_parametros_multimetro()
```

### 4. Calibración de Sistemas

#### 4.1 Calibración de Frecuencia
```python
def calibrar_frecuencia():
    """
    Calibrar sistema de medición de frecuencia
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Frecuencias de calibración
    frecuencias_calibracion = [100, 1000, 10000]  # Hz
    
    plt.figure(figsize=(15, 10))
    
    for i, f_cal in enumerate(frecuencias_calibracion):
        # Generar señal de calibración
        señal_calibracion = np.sin(2 * np.pi * f_cal * tiempo)
        
        # Agregar ruido
        ruido = 0.05 * np.random.randn(len(tiempo))
        señal_con_ruido = señal_calibracion + ruido
        
        # Medir frecuencia
        fft = np.fft.fft(señal_con_ruido)
        frecuencias = np.fft.fftfreq(len(señal_con_ruido), 1/fs)
        
        frecuencias_positivas = frecuencias[:len(frecuencias)//2]
        magnitud = np.abs(fft[:len(fft)//2])
        
        idx_medido = np.argmax(magnitud)
        frecuencia_medida = frecuencias_positivas[idx_medido]
        
        # Error de medición
        error_frecuencia = abs(frecuencia_medida - f_cal)
        error_porcentaje = (error_frecuencia / f_cal) * 100
        
        # Señal en tiempo
        plt.subplot(2, 3, i+1)
        plt.plot(tiempo[:1000], señal_con_ruido[:1000])
        plt.title(f'Señal de Calibración: {f_cal} Hz')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud (V)')
        plt.grid(True)
        
        # Espectro
        plt.subplot(2, 3, i+4)
        plt.plot(frecuencias_positivas, magnitud)
        plt.axvline(f_cal, color='red', linestyle='--', label=f'Frecuencia Real: {f_cal} Hz')
        plt.axvline(frecuencia_medida, color='green', linestyle='--', 
                    label=f'Frecuencia Medida: {frecuencia_medida:.1f} Hz')
        plt.title(f'Medición de Frecuencia')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud')
        plt.legend()
        plt.grid(True)
        
        print(f"Frecuencia real: {f_cal} Hz, Frecuencia medida: {frecuencia_medida:.1f} Hz, Error: {error_porcentaje:.2f}%")
    
    plt.tight_layout()
    plt.show()
    
    return frecuencias_calibracion, frecuencia_medida, error_porcentaje

frecuencias_calibracion_resultado, frecuencia_medida_resultado, error_porcentaje_resultado = calibrar_frecuencia()
```

#### 4.2 Calibración de Amplitud
```python
def calibrar_amplitud():
    """
    Calibrar sistema de medición de amplitud
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Amplitudes de calibración
    amplitudes_calibracion = [0.1, 0.5, 1.0, 2.0]  # V
    
    plt.figure(figsize=(15, 10))
    
    for i, a_cal in enumerate(amplitudes_calibracion):
        # Generar señal de calibración
        señal_calibracion = a_cal * np.sin(2 * np.pi * 1000 * tiempo)
        
        # Agregar ruido
        ruido = 0.01 * np.random.randn(len(tiempo))
        señal_con_ruido = señal_calibracion + ruido
        
        # Medir amplitud
        amplitud_medida = np.sqrt(np.mean(señal_con_ruido**2))
        
        # Error de medición
        error_amplitud = abs(amplitud_medida - a_cal)
        error_porcentaje = (error_amplitud / a_cal) * 100
        
        # Señal en tiempo
        plt.subplot(2, 2, i+1)
        plt.plot(tiempo[:1000], señal_con_ruido[:1000])
        plt.title(f'Amplitud de Calibración: {a_cal} V')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud (V)')
        plt.grid(True)
        
        print(f"Amplitud real: {a_cal} V, Amplitud medida: {amplitud_medida:.3f} V, Error: {error_porcentaje:.2f}%")
    
    plt.tight_layout()
    plt.show()
    
    return amplitudes_calibracion, amplitud_medida, error_porcentaje

amplitudes_calibracion_resultado, amplitud_medida_resultado, error_porcentaje_amplitud = calibrar_amplitud()
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Medición Completa de Sistema
```python
def ejercicio_medicion_completa():
    """
    Realizar medición completa de un sistema de audio
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar señal de prueba compleja
    señal_audio = (np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 2000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 3000 * tiempo) + 
                   0.1 * np.sin(2 * np.pi * 4000 * tiempo))
    
    # Agregar distorsión y ruido
    # THD
    for n in range(2, 5):
        señal_audio += 0.05 * np.sin(2 * np.pi * 1000 * n * tiempo)
    
    # Ruido
    ruido = 0.02 * np.random.randn(len(tiempo))
    señal_con_ruido = señal_audio + ruido
    
    plt.figure(figsize=(15, 12))
    
    # Señal en tiempo
    plt.subplot(3, 2, 1)
    plt.plot(tiempo, señal_con_ruido)
    plt.title('Señal de Audio Completa')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud (V)')
    plt.grid(True)
    
    # Espectro
    plt.subplot(3, 2, 2)
    fft = np.fft.fft(señal_con_ruido)
    frecuencias = np.fft.fftfreq(len(señal_con_ruido), 1/fs)
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = 20 * np.log10(np.abs(fft[:len(fft)//2]) + 1e-10)
    
    plt.semilogx(frecuencias_positivas, magnitud)
    plt.title('Espectro de Frecuencias')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    # Medición de THD
    plt.subplot(3, 2, 3)
    f0 = 1000  # Hz
    thd_total = 0
    for n in range(2, 5):
        freq_armonico = n * f0
        idx = np.argmin(np.abs(frecuencias - freq_armonico))
        thd_total += np.abs(fft[idx])**2
    
    idx_fundamental = np.argmin(np.abs(frecuencias - f0))
    amplitud_fundamental = np.abs(fft[idx_fundamental])
    thd_percent = np.sqrt(thd_total) / amplitud_fundamental * 100
    
    plt.bar(['THD'], [thd_percent], color='red', alpha=0.7)
    plt.title('Total Harmonic Distortion')
    plt.ylabel('THD (%)')
    plt.grid(True)
    
    # Medición de SNR
    plt.subplot(3, 2, 4)
    snr = 20 * np.log10(np.std(señal_audio) / np.std(ruido))
    
    plt.bar(['SNR'], [snr], color='blue', alpha=0.7)
    plt.title('Signal-to-Noise Ratio')
    plt.ylabel('SNR (dB)')
    plt.grid(True)
    
    # Medición de amplitud
    plt.subplot(3, 2, 5)
    amplitud_rms = np.sqrt(np.mean(señal_con_ruido**2))
    amplitud_pico = np.max(np.abs(señal_con_ruido))
    crest_factor = amplitud_pico / amplitud_rms
    
    plt.bar(['RMS', 'Pico', 'Crest Factor'], [amplitud_rms, amplitud_pico, crest_factor], 
            color=['green', 'orange', 'purple'], alpha=0.7)
    plt.title('Mediciones de Amplitud')
    plt.ylabel('Valor')
    plt.grid(True)
    
    # Resumen de mediciones
    plt.subplot(3, 2, 6)
    plt.text(0.1, 0.8, f'THD: {thd_percent:.2f}%', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.7, f'SNR: {snr:.1f} dB', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.6, f'Amplitud RMS: {amplitud_rms:.3f} V', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.5, f'Amplitud Pico: {amplitud_pico:.3f} V', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.4, f'Crest Factor: {crest_factor:.2f}', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.3, f'Frecuencia Fundamental: {f0} Hz', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.2, f'Frecuencia de Muestreo: {fs} Hz', transform=plt.gca().transAxes, fontsize=12)
    plt.title('Resumen de Mediciones')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return thd_percent, snr, amplitud_rms, amplitud_pico, crest_factor

thd_ejercicio, snr_ejercicio, amplitud_rms_ejercicio, amplitud_pico_ejercicio, crest_factor_ejercicio = ejercicio_medicion_completa()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **Osciloscopio**: Medición en dominio temporal
2. **Analizador de Espectro**: Análisis en dominio frecuencial
3. **Multímetro**: Medición de parámetros básicos
4. **Calibración**: Verificación de precisión
5. **Medición de Calidad**: THD, SNR, distorsión

### Aplicaciones en Audio
- Medición de señales
- Análisis de calidad
- Calibración de sistemas
- Diagnóstico de problemas

### Herramientas Utilizadas
- Simulación de instrumentos
- Análisis espectral
- Medición de parámetros
- Técnicas de calibración

## 🎯 Objetivos Alcanzados
- ✅ Usar osciloscopio para mediciones
- ✅ Analizar espectros de frecuencia
- ✅ Medir parámetros básicos
- ✅ Calibrar sistemas
- ✅ Evaluar calidad de audio

## 🚀 Navegación

**← Anterior**: [Clase 4.7: Distorsión y Ruido](clase-4-7-distorsion-ruido.md)
**Siguiente →**: [Módulo 5: Procesamiento Digital de Señales](../modulo-05-procesamiento-digital/README.md)

---

*Duración de la clase: 2-3 horas*
