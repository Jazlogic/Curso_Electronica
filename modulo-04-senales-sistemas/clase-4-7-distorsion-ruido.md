# Clase 4.7: Distorsión y Ruido

## 🎯 Objetivo de la Clase
Analizar y minimizar distorsión y ruido en sistemas de audio, incluyendo THD, IMD, ruido térmico, ruido de cuantización y técnicas de mejora de calidad.

## 📋 Contenido de la Clase

### 1. Tipos de Distorsión

#### 1.1 Distorsión Armónica (THD)
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

def analizar_thd():
    """
    Analizar Total Harmonic Distortion (THD)
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Señal fundamental
    f0 = 1000  # Hz
    señal_fundamental = np.sin(2 * np.pi * f0 * tiempo)
    
    # Agregar armónicos (distorsión)
    armónicos = [2, 3, 4, 5]  # Armónicos 2, 3, 4, 5
    amplitudes = [0.1, 0.05, 0.03, 0.02]  # Amplitudes de distorsión
    
    señal_con_thd = señal_fundamental.copy()
    for armónico, amplitud in zip(armónicos, amplitudes):
        señal_con_thd += amplitud * np.sin(2 * np.pi * f0 * armónico * tiempo)
    
    # Calcular THD
    def calcular_thd(señal, f0, fs):
        # Calcular FFT
        fft = np.fft.fft(señal)
        frecuencias = np.fft.fftfreq(len(señal), 1/fs)
        
        # Encontrar armónicos
        thd_total = 0
        for n in range(2, 6):  # Armónicos 2-5
            freq_armonico = n * f0
            idx = np.argmin(np.abs(frecuencias - freq_armonico))
            thd_total += np.abs(fft[idx])**2
        
        # Frecuencia fundamental
        idx_fundamental = np.argmin(np.abs(frecuencias - f0))
        amplitud_fundamental = np.abs(fft[idx_fundamental])
        
        # THD en porcentaje
        thd_percent = np.sqrt(thd_total) / amplitud_fundamental * 100
        
        return thd_percent, frecuencias, fft
    
    thd, frecuencias, fft = calcular_thd(señal_con_thd, f0, fs)
    
    plt.figure(figsize=(15, 10))
    
    # Señales en tiempo
    plt.subplot(2, 2, 1)
    plt.plot(tiempo[:1000], señal_fundamental[:1000], label='Fundamental', alpha=0.7)
    plt.plot(tiempo[:1000], señal_con_thd[:1000], label='Con THD', alpha=0.7)
    plt.title('Señales en Dominio Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectro
    plt.subplot(2, 2, 2)
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    plt.plot(frecuencias_positivas, magnitud)
    plt.axvline(f0, color='red', linestyle='--', label=f'Fundamental: {f0} Hz')
    for armónico in armónicos:
        plt.axvline(f0 * armónico, color='orange', linestyle='--', alpha=0.7, label=f'Armónico {armónico}')
    plt.title('Espectro de Frecuencias')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud')
    plt.xlim(0, 6000)
    plt.legend()
    plt.grid(True)
    
    # Análisis de THD
    plt.subplot(2, 2, 3)
    # Calcular THD para diferentes niveles de distorsión
    niveles_distorsion = np.linspace(0, 0.2, 20)
    thd_values = []
    
    for nivel in niveles_distorsion:
        señal_test = señal_fundamental.copy()
        for armónico, amplitud in zip(armónicos, amplitudes):
            señal_test += nivel * amplitud * np.sin(2 * np.pi * f0 * armónico * tiempo)
        
        thd_test, _, _ = calcular_thd(señal_test, f0, fs)
        thd_values.append(thd_test)
    
    plt.plot(niveles_distorsion, thd_values, 'o-')
    plt.title('THD vs Nivel de Distorsión')
    plt.xlabel('Nivel de Distorsión')
    plt.ylabel('THD (%)')
    plt.grid(True)
    
    # Parámetros de THD
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'THD Total: {thd:.2f}%', transform=plt.gca().transAxes, fontsize=14)
    plt.text(0.1, 0.7, f'Frecuencia Fundamental: {f0} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Armónicos Analizados: {len(armónicos)}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Frecuencia de Muestreo: {fs} Hz', transform=plt.gca().transAxes)
    plt.title('Parámetros de THD')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return thd, frecuencias, fft

thd_resultado, frecuencias_thd, fft_thd = analizar_thd()
```

#### 1.2 Distorsión de Intermodulación (IMD)
```python
def analizar_imd():
    """
    Analizar Intermodulation Distortion (IMD)
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Dos frecuencias de prueba
    f1 = 1000  # Hz
    f2 = 1100  # Hz
    
    # Señales de prueba
    señal1 = np.sin(2 * np.pi * f1 * tiempo)
    señal2 = np.sin(2 * np.pi * f2 * tiempo)
    señal_combinada = señal1 + señal2
    
    # Agregar distorsión de intermodulación
    # Productos de intermodulación: 2f1-f2, 2f2-f1, f1+f2, etc.
    imd_2f1_f2 = 0.05 * np.sin(2 * np.pi * (2*f1 - f2) * tiempo)
    imd_2f2_f1 = 0.05 * np.sin(2 * np.pi * (2*f2 - f1) * tiempo)
    imd_f1_f2 = 0.03 * np.sin(2 * np.pi * (f1 + f2) * tiempo)
    
    señal_con_imd = señal_combinada + imd_2f1_f2 + imd_2f2_f1 + imd_f1_f2
    
    # Calcular IMD
    def calcular_imd(señal, f1, f2, fs):
        # Calcular FFT
        fft = np.fft.fft(señal)
        frecuencias = np.fft.fftfreq(len(señal), 1/fs)
        
        # Frecuencias de intermodulación
        imd_freqs = [2*f1 - f2, 2*f2 - f1, f1 + f2]
        
        # Encontrar amplitudes
        imd_amplitudes = []
        for freq in imd_freqs:
            idx = np.argmin(np.abs(frecuencias - freq))
            imd_amplitudes.append(np.abs(fft[idx]))
        
        # Calcular IMD
        imd_total = np.sqrt(np.sum(np.array(imd_amplitudes)**2))
        
        # Amplitud de las señales originales
        idx1 = np.argmin(np.abs(frecuencias - f1))
        idx2 = np.argmin(np.abs(frecuencias - f2))
        amplitud_original = (np.abs(fft[idx1]) + np.abs(fft[idx2])) / 2
        
        # IMD en porcentaje
        imd_percent = imd_total / amplitud_original * 100
        
        return imd_percent, frecuencias, fft, imd_freqs, imd_amplitudes
    
    imd, frecuencias, fft, imd_freqs, imd_amplitudes = calcular_imd(señal_con_imd, f1, f2, fs)
    
    plt.figure(figsize=(15, 10))
    
    # Señales en tiempo
    plt.subplot(2, 2, 1)
    plt.plot(tiempo[:1000], señal_combinada[:1000], label='Original', alpha=0.7)
    plt.plot(tiempo[:1000], señal_con_imd[:1000], label='Con IMD', alpha=0.7)
    plt.title('Señales en Dominio Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectro
    plt.subplot(2, 2, 2)
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    magnitud = np.abs(fft[:len(fft)//2])
    plt.plot(frecuencias_positivas, magnitud)
    plt.axvline(f1, color='red', linestyle='--', label=f'f1: {f1} Hz')
    plt.axvline(f2, color='red', linestyle='--', label=f'f2: {f2} Hz')
    for freq in imd_freqs:
        plt.axvline(freq, color='orange', linestyle='--', alpha=0.7, label=f'IMD: {freq} Hz')
    plt.title('Espectro de Frecuencias')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud')
    plt.xlim(0, 3000)
    plt.legend()
    plt.grid(True)
    
    # Análisis de IMD
    plt.subplot(2, 2, 3)
    # Calcular IMD para diferentes niveles de distorsión
    niveles_distorsion = np.linspace(0, 0.1, 20)
    imd_values = []
    
    for nivel in niveles_distorsion:
        señal_test = señal_combinada.copy()
        señal_test += nivel * imd_2f1_f2
        señal_test += nivel * imd_2f2_f1
        señal_test += nivel * imd_f1_f2
        
        imd_test, _, _, _, _ = calcular_imd(señal_test, f1, f2, fs)
        imd_values.append(imd_test)
    
    plt.plot(niveles_distorsion, imd_values, 'o-')
    plt.title('IMD vs Nivel de Distorsión')
    plt.xlabel('Nivel de Distorsión')
    plt.ylabel('IMD (%)')
    plt.grid(True)
    
    # Parámetros de IMD
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'IMD Total: {imd:.2f}%', transform=plt.gca().transAxes, fontsize=14)
    plt.text(0.1, 0.7, f'Frecuencia 1: {f1} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Frecuencia 2: {f2} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Productos IMD: {len(imd_freqs)}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Frecuencia de Muestreo: {fs} Hz', transform=plt.gca().transAxes)
    plt.title('Parámetros de IMD')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return imd, frecuencias, fft, imd_freqs, imd_amplitudes

imd_resultado, frecuencias_imd, fft_imd, imd_freqs_resultado, imd_amplitudes_resultado = analizar_imd()
```

### 2. Tipos de Ruido

#### 2.1 Ruido Térmico
```python
def analizar_ruido_termico():
    """
    Analizar ruido térmico en sistemas de audio
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Señal de audio
    señal_audio = np.sin(2 * np.pi * 1000 * tiempo)
    
    # Generar ruido térmico
    def generar_ruido_termico(tiempo, temperatura=300, resistencia=1000):
        # Constante de Boltzmann
        k = 1.38e-23  # J/K
        
        # Ancho de banda
        BW = fs / 2  # Hz
        
        # Potencia del ruido térmico
        P_ruido = k * temperatura * BW
        
        # Voltaje RMS del ruido
        V_rms = np.sqrt(P_ruido * resistencia)
        
        # Generar ruido gaussiano
        ruido = np.random.normal(0, V_rms, len(tiempo))
        
        return ruido, V_rms, P_ruido
    
    ruido_termico, V_rms, P_ruido = generar_ruido_termico(tiempo)
    
    # Aplicar ruido a la señal
    señal_con_ruido = señal_audio + ruido_termico
    
    # Calcular SNR
    def calcular_snr(señal, ruido):
        potencia_senal = np.mean(señal**2)
        potencia_ruido = np.mean(ruido**2)
        snr_db = 10 * np.log10(potencia_senal / potencia_ruido)
        return snr_db
    
    snr = calcular_snr(señal_audio, ruido_termico)
    
    plt.figure(figsize=(15, 10))
    
    # Señales en tiempo
    plt.subplot(2, 2, 1)
    plt.plot(tiempo[:1000], señal_audio[:1000], label='Señal Original', alpha=0.7)
    plt.plot(tiempo[:1000], señal_con_ruido[:1000], label='Con Ruido Térmico', alpha=0.7)
    plt.title('Señales en Dominio Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectros
    plt.subplot(2, 2, 2)
    fft_original = np.fft.fft(señal_audio)
    fft_con_ruido = np.fft.fft(señal_con_ruido)
    frecuencias = np.fft.fftfreq(len(señal_audio), 1/fs)
    
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), 
                 label='Original', alpha=0.7)
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_con_ruido[:len(fft_con_ruido)//2]) + 1e-10), 
                 label='Con Ruido', alpha=0.7)
    plt.title('Espectros de Frecuencias')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Análisis de ruido
    plt.subplot(2, 2, 3)
    # Histograma del ruido
    plt.hist(ruido_termico, bins=50, alpha=0.7, edgecolor='black')
    plt.title('Distribución del Ruido Térmico')
    plt.xlabel('Amplitud')
    plt.ylabel('Frecuencia')
    plt.grid(True)
    
    # Parámetros del ruido
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'SNR: {snr:.1f} dB', transform=plt.gca().transAxes, fontsize=14)
    plt.text(0.1, 0.7, f'Voltaje RMS: {V_rms*1e6:.2f} μV', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Potencia del Ruido: {P_ruido*1e12:.2f} pW', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Temperatura: 300 K', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Resistencia: 1000 Ω', transform=plt.gca().transAxes)
    plt.text(0.1, 0.3, f'Ancho de Banda: {fs/2} Hz', transform=plt.gca().transAxes)
    plt.title('Parámetros del Ruido Térmico')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return ruido_termico, V_rms, P_ruido, snr

ruido_termico_resultado, V_rms_resultado, P_ruido_resultado, snr_resultado = analizar_ruido_termico()
```

#### 2.2 Ruido de Cuantización
```python
def analizar_ruido_cuantizacion():
    """
    Analizar ruido de cuantización
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Señal de audio
    señal_audio = np.sin(2 * np.pi * 1000 * tiempo)
    
    # Diferentes resoluciones de cuantización
    bits_resoluciones = [4, 8, 16, 24]
    
    plt.figure(figsize=(15, 10))
    
    for i, bits in enumerate(bits_resoluciones):
        # Cuantizar señal
        niveles = 2**bits
        señal_cuantizada = np.round(señal_audio * (niveles - 1)) / (niveles - 1)
        
        # Calcular ruido de cuantización
        ruido_cuantizacion = señal_audio - señal_cuantizada
        
        # Calcular SNR teórico
        snr_teorico = 6.02 * bits + 1.76
        
        # Calcular SNR real
        snr_real = 20 * np.log10(np.std(señal_audio) / np.std(ruido_cuantizacion))
        
        # Señales en tiempo
        plt.subplot(2, 2, 1)
        if i == 0:
            plt.plot(tiempo[:1000], señal_audio[:1000], label='Original', alpha=0.7)
        plt.plot(tiempo[:1000], señal_cuantizada[:1000], label=f'{bits} bits', alpha=0.7)
        plt.title('Señales Cuantizadas')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.legend()
        plt.grid(True)
        
        # Ruido de cuantización
        plt.subplot(2, 2, 2)
        plt.plot(tiempo[:1000], ruido_cuantizacion[:1000], label=f'{bits} bits', alpha=0.7)
        plt.title('Ruido de Cuantización')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.legend()
        plt.grid(True)
        
        # Espectros
        plt.subplot(2, 2, 3)
        fft_original = np.fft.fft(señal_audio)
        fft_cuantizada = np.fft.fft(señal_cuantizada)
        frecuencias = np.fft.fftfreq(len(señal_audio), 1/fs)
        
        frecuencias_positivas = frecuencias[:len(frecuencias)//2]
        if i == 0:
            plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), 
                         label='Original', alpha=0.7)
        plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_cuantizada[:len(fft_cuantizada)//2]) + 1e-10), 
                     label=f'{bits} bits', alpha=0.7)
        plt.title('Espectros de Frecuencias')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.legend()
        plt.grid(True)
        
        # Análisis de SNR
        plt.subplot(2, 2, 4)
        plt.bar([f'{bits} bits'], [snr_real], label=f'{bits} bits', alpha=0.7)
        plt.title('SNR vs Resolución')
        plt.xlabel('Resolución (bits)')
        plt.ylabel('SNR (dB)')
        plt.legend()
        plt.grid(True)
        
        print(f"{bits} bits: SNR teórico = {snr_teorico:.1f} dB, SNR real = {snr_real:.1f} dB")
    
    plt.tight_layout()
    plt.show()
    
    return bits_resoluciones, snr_teorico, snr_real

bits_resultado, snr_teorico_resultado, snr_real_resultado = analizar_ruido_cuantizacion()
```

### 3. Técnicas de Mejora de Calidad

#### 3.1 Dithering
```python
def implementar_dithering():
    """
    Implementar técnicas de dithering
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Señal de audio
    señal_audio = np.sin(2 * np.pi * 1000 * tiempo)
    
    # Cuantización sin dithering
    bits = 8
    niveles = 2**bits
    señal_sin_dither = np.round(señal_audio * (niveles - 1)) / (niveles - 1)
    
    # Cuantización con dithering rectangular
    dither_rectangular = (np.random.rand(len(tiempo)) - 0.5) * (2 / niveles)
    señal_con_dither_rect = np.round((señal_audio + dither_rectangular) * (niveles - 1)) / (niveles - 1)
    
    # Cuantización con dithering triangular
    dither_triangular = (np.random.rand(len(tiempo)) - 0.5) * (2 / niveles) + (np.random.rand(len(tiempo)) - 0.5) * (2 / niveles)
    señal_con_dither_tri = np.round((señal_audio + dither_triangular) * (niveles - 1)) / (niveles - 1)
    
    plt.figure(figsize=(15, 10))
    
    # Señales en tiempo
    plt.subplot(2, 2, 1)
    plt.plot(tiempo[:1000], señal_audio[:1000], label='Original', alpha=0.7)
    plt.plot(tiempo[:1000], señal_sin_dither[:1000], label='Sin Dither', alpha=0.7)
    plt.plot(tiempo[:1000], señal_con_dither_rect[:1000], label='Con Dither Rectangular', alpha=0.7)
    plt.plot(tiempo[:1000], señal_con_dither_tri[:1000], label='Con Dither Triangular', alpha=0.7)
    plt.title('Señales en Dominio Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectros
    plt.subplot(2, 2, 2)
    fft_original = np.fft.fft(señal_audio)
    fft_sin_dither = np.fft.fft(señal_sin_dither)
    fft_con_dither_rect = np.fft.fft(señal_con_dither_rect)
    fft_con_dither_tri = np.fft.fft(señal_con_dither_tri)
    frecuencias = np.fft.fftfreq(len(señal_audio), 1/fs)
    
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), 
                 label='Original', alpha=0.7)
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_sin_dither[:len(fft_sin_dither)//2]) + 1e-10), 
                 label='Sin Dither', alpha=0.7)
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_con_dither_rect[:len(fft_con_dither_rect)//2]) + 1e-10), 
                 label='Con Dither Rectangular', alpha=0.7)
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_con_dither_tri[:len(fft_con_dither_tri)//2]) + 1e-10), 
                 label='Con Dither Triangular', alpha=0.7)
    plt.title('Espectros de Frecuencias')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Análisis de calidad
    plt.subplot(2, 2, 3)
    # Calcular métricas de calidad
    def calcular_metricas(señal_original, señal_procesada):
        # SNR
        snr = 20 * np.log10(np.std(señal_original) / np.std(señal_original - señal_procesada))
        
        # THD
        fft_original = np.fft.fft(señal_original)
        fft_procesada = np.fft.fft(señal_procesada)
        frecuencias = np.fft.fftfreq(len(señal_original), 1/fs)
        
        # Encontrar armónicos
        f0 = 1000  # Hz
        thd_total = 0
        for n in range(2, 6):
            freq_armonico = n * f0
            idx = np.argmin(np.abs(frecuencias - freq_armonico))
            thd_total += np.abs(fft_procesada[idx])**2
        
        idx_fundamental = np.argmin(np.abs(frecuencias - f0))
        amplitud_fundamental = np.abs(fft_procesada[idx_fundamental])
        thd_percent = np.sqrt(thd_total) / amplitud_fundamental * 100
        
        return snr, thd_percent
    
    snr_sin_dither, thd_sin_dither = calcular_metricas(señal_audio, señal_sin_dither)
    snr_con_dither_rect, thd_con_dither_rect = calcular_metricas(señal_audio, señal_con_dither_rect)
    snr_con_dither_tri, thd_con_dither_tri = calcular_metricas(señal_audio, señal_con_dither_tri)
    
    # Gráfico de barras
    métodos = ['Sin Dither', 'Con Dither Rectangular', 'Con Dither Triangular']
    snr_values = [snr_sin_dither, snr_con_dither_rect, snr_con_dither_tri]
    thd_values = [thd_sin_dither, thd_con_dither_rect, thd_con_dither_tri]
    
    x = np.arange(len(métodos))
    width = 0.35
    
    plt.bar(x - width/2, snr_values, width, label='SNR (dB)', alpha=0.7)
    plt.bar(x + width/2, thd_values, width, label='THD (%)', alpha=0.7)
    plt.title('Métricas de Calidad')
    plt.xlabel('Método')
    plt.ylabel('Valor')
    plt.xticks(x, métodos)
    plt.legend()
    plt.grid(True)
    
    # Parámetros del dithering
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'Resolución: {bits} bits', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Niveles: {niveles}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'SNR Sin Dither: {snr_sin_dither:.1f} dB', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'SNR Con Dither Rect: {snr_con_dither_rect:.1f} dB', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'SNR Con Dither Tri: {snr_con_dither_tri:.1f} dB', transform=plt.gca().transAxes)
    plt.text(0.1, 0.3, f'THD Sin Dither: {thd_sin_dither:.2f}%', transform=plt.gca().transAxes)
    plt.text(0.1, 0.2, f'THD Con Dither Rect: {thd_con_dither_rect:.2f}%', transform=plt.gca().transAxes)
    plt.text(0.1, 0.1, f'THD Con Dither Tri: {thd_con_dither_tri:.2f}%', transform=plt.gca().transAxes)
    plt.title('Parámetros del Dithering')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return señal_audio, señal_sin_dither, señal_con_dither_rect, señal_con_dither_tri

señal_audio_dither, señal_sin_dither_resultado, señal_con_dither_rect_resultado, señal_con_dither_tri_resultado = implementar_dithering()
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Análisis de Calidad Integral
```python
def ejercicio_analisis_calidad():
    """
    Análisis integral de calidad de audio
    """
    # Parámetros
    fs = 44100
    duracion = 1.0
    tiempo = np.linspace(0, duracion, int(fs * duracion))
    
    # Generar señal de audio compleja
    señal_audio = (np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 2000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 3000 * tiempo))
    
    # Agregar diferentes tipos de distorsión y ruido
    # THD
    thd_armonicos = [0.1, 0.05, 0.03, 0.02]
    for i, amplitud in enumerate(thd_armonicos):
        señal_audio += amplitud * np.sin(2 * np.pi * 1000 * (i+2) * tiempo)
    
    # IMD
    imd_2f1_f2 = 0.05 * np.sin(2 * np.pi * (2*1000 - 2000) * tiempo)
    imd_2f2_f1 = 0.05 * np.sin(2 * np.pi * (2*2000 - 1000) * tiempo)
    señal_audio += imd_2f1_f2 + imd_2f2_f1
    
    # Ruido térmico
    ruido_termico = 0.01 * np.random.randn(len(tiempo))
    señal_audio += ruido_termico
    
    # Ruido de cuantización
    bits = 16
    niveles = 2**bits
    señal_cuantizada = np.round(señal_audio * (niveles - 1)) / (niveles - 1)
    ruido_cuantizacion = señal_audio - señal_cuantizada
    
    # Análisis completo
    plt.figure(figsize=(15, 12))
    
    # Señales en tiempo
    plt.subplot(3, 2, 1)
    plt.plot(tiempo[:1000], señal_audio[:1000], label='Con Distorsión', alpha=0.7)
    plt.plot(tiempo[:1000], señal_cuantizada[:1000], label='Cuantizada', alpha=0.7)
    plt.title('Señales en Dominio Temporal')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectros
    plt.subplot(3, 2, 2)
    fft_original = np.fft.fft(señal_audio)
    fft_cuantizada = np.fft.fft(señal_cuantizada)
    frecuencias = np.fft.fftfreq(len(señal_audio), 1/fs)
    
    frecuencias_positivas = frecuencias[:len(frecuencias)//2]
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), 
                 label='Con Distorsión', alpha=0.7)
    plt.semilogx(frecuencias_positivas, 20*np.log10(np.abs(fft_cuantizada[:len(fft_cuantizada)//2]) + 1e-10), 
                 label='Cuantizada', alpha=0.7)
    plt.title('Espectros de Frecuencias')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Análisis de THD
    plt.subplot(3, 2, 3)
    # Calcular THD
    f0 = 1000  # Hz
    thd_total = 0
    for n in range(2, 6):
        freq_armonico = n * f0
        idx = np.argmin(np.abs(frecuencias - freq_armonico))
        thd_total += np.abs(fft_cuantizada[idx])**2
    
    idx_fundamental = np.argmin(np.abs(frecuencias - f0))
    amplitud_fundamental = np.abs(fft_cuantizada[idx_fundamental])
    thd_percent = np.sqrt(thd_total) / amplitud_fundamental * 100
    
    plt.bar(['THD'], [thd_percent], color='red', alpha=0.7)
    plt.title('Total Harmonic Distortion')
    plt.ylabel('THD (%)')
    plt.grid(True)
    
    # Análisis de IMD
    plt.subplot(3, 2, 4)
    # Calcular IMD
    f1, f2 = 1000, 2000  # Hz
    imd_freqs = [2*f1 - f2, 2*f2 - f1]
    imd_amplitudes = []
    
    for freq in imd_freqs:
        idx = np.argmin(np.abs(frecuencias - freq))
        imd_amplitudes.append(np.abs(fft_cuantizada[idx]))
    
    imd_total = np.sqrt(np.sum(np.array(imd_amplitudes)**2))
    amplitud_original = (np.abs(fft_cuantizada[idx_fundamental]) + np.abs(fft_cuantizada[np.argmin(np.abs(frecuencias - f2))])) / 2
    imd_percent = imd_total / amplitud_original * 100
    
    plt.bar(['IMD'], [imd_percent], color='orange', alpha=0.7)
    plt.title('Intermodulation Distortion')
    plt.ylabel('IMD (%)')
    plt.grid(True)
    
    # Análisis de SNR
    plt.subplot(3, 2, 5)
    # Calcular SNR
    snr_termico = 20 * np.log10(np.std(señal_audio) / np.std(ruido_termico))
    snr_cuantizacion = 20 * np.log10(np.std(señal_audio) / np.std(ruido_cuantizacion))
    
    plt.bar(['SNR Térmico', 'SNR Cuantización'], [snr_termico, snr_cuantizacion], 
            color=['blue', 'green'], alpha=0.7)
    plt.title('Signal-to-Noise Ratio')
    plt.ylabel('SNR (dB)')
    plt.grid(True)
    
    # Resumen de métricas
    plt.subplot(3, 2, 6)
    plt.text(0.1, 0.8, f'THD: {thd_percent:.2f}%', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.7, f'IMD: {imd_percent:.2f}%', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.6, f'SNR Térmico: {snr_termico:.1f} dB', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.5, f'SNR Cuantización: {snr_cuantizacion:.1f} dB', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.4, f'Resolución: {bits} bits', transform=plt.gca().transAxes, fontsize=12)
    plt.text(0.1, 0.3, f'Frecuencia de Muestreo: {fs} Hz', transform=plt.gca().transAxes, fontsize=12)
    plt.title('Resumen de Métricas')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return thd_percent, imd_percent, snr_termico, snr_cuantizacion

thd_ejercicio, imd_ejercicio, snr_termico_ejercicio, snr_cuantizacion_ejercicio = ejercicio_analisis_calidad()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **THD**: Total Harmonic Distortion
2. **IMD**: Intermodulation Distortion
3. **Ruido Térmico**: Ruido inherente de componentes
4. **Ruido de Cuantización**: Ruido por discretización
5. **Dithering**: Técnica para mejorar calidad

### Aplicaciones en Audio
- Medición de calidad
- Optimización de sistemas
- Diseño de convertidores
- Análisis de distorsión

### Herramientas Utilizadas
- Análisis espectral
- Medición de distorsión
- Técnicas de dithering
- Métricas de calidad

## 🎯 Objetivos Alcanzados
- ✅ Analizar THD e IMD
- ✅ Comprender tipos de ruido
- ✅ Implementar dithering
- ✅ Medir calidad de audio
- ✅ Optimizar sistemas

## 🚀 Navegación

**← Anterior**: [Clase 4.6: Filtros Analógicos](clase-4-6-filtros-analogicos.md)
**Siguiente →**: [Clase 4.8: Medición de Señales](clase-4-8-medicion-senales.md)

---

*Duración de la clase: 3-4 horas*
