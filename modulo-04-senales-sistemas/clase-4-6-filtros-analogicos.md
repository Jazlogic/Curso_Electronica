# Clase 4.6: Filtros Analógicos

## 🎯 Objetivo de la Clase
Diseñar filtros analógicos para audio, incluyendo filtros pasivos, activos, Butterworth, Chebyshev y aplicaciones en sistemas de audio de alta calidad.

## 📋 Contenido de la Clase

### 1. Filtros Pasivos

#### 1.1 Filtro RC Pasa-Bajos
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

def analizar_filtro_rc():
    """
    Analizar filtro RC pasa-bajos
    """
    # Parámetros
    R = 1000  # Ohm
    C = 1e-6  # F
    RC = R * C
    fc = 1 / (2 * np.pi * RC)  # Frecuencia de corte
    
    # Función de transferencia: H(s) = 1/(RCs + 1)
    num = [1]
    den = [RC, 1]
    H = signal.TransferFunction(num, den)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(0, 5, 1000)
    w = 2 * np.pi * frecuencias
    w, mag, phase = signal.bode(H, w)
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag)
    plt.axvline(fc, color='red', linestyle='--', label=f'fc = {fc:.1f} Hz')
    plt.axhline(-3, color='green', linestyle='--', alpha=0.5, label='-3 dB')
    plt.title('Filtro RC Pasa-Bajos - Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase)
    plt.axvline(fc, color='red', linestyle='--', label=f'fc = {fc:.1f} Hz')
    plt.title('Filtro RC Pasa-Bajos - Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.legend()
    plt.grid(True)
    
    # Respuesta al escalón
    plt.subplot(2, 2, 3)
    t, y = signal.step(H)
    plt.plot(t, y)
    plt.title('Respuesta al Escalón')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Análisis de parámetros
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'Resistencia: {R} Ω', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Capacitancia: {C*1e6:.1f} μF', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Frecuencia de Corte: {fc:.1f} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Constante de Tiempo: {RC*1000:.1f} ms', transform=plt.gca().transAxes)
    plt.title('Parámetros del Filtro')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return H, fc, RC

H_rc, fc_rc, RC_rc = analizar_filtro_rc()
```

#### 1.2 Filtro RLC Resonante
```python
def analizar_filtro_rlc():
    """
    Analizar filtro RLC resonante
    """
    # Parámetros
    R = 100  # Ohm
    L = 1e-3  # H
    C = 1e-6  # F
    
    # Frecuencias características
    w0 = 1 / np.sqrt(L * C)  # Frecuencia de resonancia
    Q = w0 * L / R  # Factor de calidad
    BW = w0 / Q  # Ancho de banda
    
    # Función de transferencia: H(s) = R/(Ls + R + 1/(Cs))
    num = [R]
    den = [L, R, 1/C]
    H = signal.TransferFunction(num, den)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(1, 5, 1000)
    w = 2 * np.pi * frecuencias
    w, mag, phase = signal.bode(H, w)
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag)
    plt.axvline(w0/(2*np.pi), color='red', linestyle='--', label=f'f0 = {w0/(2*np.pi):.1f} Hz')
    plt.title('Filtro RLC Resonante - Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase)
    plt.axvline(w0/(2*np.pi), color='red', linestyle='--', label=f'f0 = {w0/(2*np.pi):.1f} Hz')
    plt.title('Filtro RLC Resonante - Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.legend()
    plt.grid(True)
    
    # Respuesta al escalón
    plt.subplot(2, 2, 3)
    t, y = signal.step(H)
    plt.plot(t, y)
    plt.title('Respuesta al Escalón')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Análisis de parámetros
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'Inductancia: {L*1000:.1f} mH', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Capacitancia: {C*1e6:.1f} μF', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Resistencia: {R} Ω', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Frecuencia de Resonancia: {w0/(2*np.pi):.1f} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Factor de Calidad: {Q:.1f}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.3, f'Ancho de Banda: {BW/(2*np.pi):.1f} Hz', transform=plt.gca().transAxes)
    plt.title('Parámetros del Filtro')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return H, w0, Q, BW

H_rlc, w0_rlc, Q_rlc, BW_rlc = analizar_filtro_rlc()
```

### 2. Filtros Activos

#### 2.1 Filtro Sallen-Key
```python
def diseñar_sallen_key():
    """
    Diseñar filtro Sallen-Key pasa-bajos
    """
    # Parámetros
    fc = 1000  # Hz
    Q = 0.707  # Butterworth
    
    # Componentes
    R1 = R2 = 1000  # Ohm
    C1 = C2 = 1 / (2 * np.pi * fc * R1)  # F
    
    # Función de transferencia
    num = [1]
    den = [R1*R2*C1*C2, R1*C1 + R2*C2, 1]
    H = signal.TransferFunction(num, den)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(1, 4, 1000)
    w = 2 * np.pi * frecuencias
    w, mag, phase = signal.bode(H, w)
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag)
    plt.axvline(fc, color='red', linestyle='--', label=f'fc = {fc} Hz')
    plt.axhline(-3, color='green', linestyle='--', alpha=0.5, label='-3 dB')
    plt.title('Filtro Sallen-Key - Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase)
    plt.axvline(fc, color='red', linestyle='--', label=f'fc = {fc} Hz')
    plt.title('Filtro Sallen-Key - Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.legend()
    plt.grid(True)
    
    # Respuesta al escalón
    plt.subplot(2, 2, 3)
    t, y = signal.step(H)
    plt.plot(t, y)
    plt.title('Respuesta al Escalón')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Análisis de parámetros
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'R1 = R2: {R1} Ω', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'C1 = C2: {C1*1e6:.1f} μF', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Frecuencia de Corte: {fc} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Factor de Calidad: {Q:.3f}', transform=plt.gca().transAxes)
    plt.title('Parámetros del Filtro')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return H, fc, Q

H_sallen, fc_sallen, Q_sallen = diseñar_sallen_key()
```

### 3. Filtros Butterworth

#### 3.1 Diseño de Filtros Butterworth
```python
def diseñar_butterworth():
    """
    Diseñar filtros Butterworth de diferentes órdenes
    """
    # Parámetros
    fc = 1000  # Hz
    órdenes = [1, 2, 4, 8]
    
    plt.figure(figsize=(15, 10))
    
    for i, orden in enumerate(órdenes):
        # Diseñar filtro Butterworth
        b, a = signal.butter(orden, fc, btype='low', analog=True)
        H = signal.TransferFunction(b, a)
        
        # Respuesta en frecuencia
        frecuencias = np.logspace(1, 4, 1000)
        w = 2 * np.pi * frecuencias
        w, mag, phase = signal.bode(H, w)
        
        # Magnitud
        plt.subplot(2, 2, 1)
        plt.semilogx(frecuencias, mag, label=f'Orden {orden}')
        plt.axvline(fc, color='red', linestyle='--', alpha=0.5)
        plt.axhline(-3, color='green', linestyle='--', alpha=0.5)
        plt.title('Filtros Butterworth - Magnitud')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.legend()
        plt.grid(True)
        
        # Fase
        plt.subplot(2, 2, 2)
        plt.semilogx(frecuencias, phase, label=f'Orden {orden}')
        plt.axvline(fc, color='red', linestyle='--', alpha=0.5)
        plt.title('Filtros Butterworth - Fase')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Fase (grados)')
        plt.legend()
        plt.grid(True)
        
        # Respuesta al escalón
        plt.subplot(2, 2, 3)
        t, y = signal.step(H)
        plt.plot(t, y, label=f'Orden {orden}')
        plt.title('Respuesta al Escalón')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.legend()
        plt.grid(True)
        
        # Análisis de parámetros
        plt.subplot(2, 2, 4)
        # Calcular atenuación a diferentes frecuencias
        f_test = [fc, 2*fc, 4*fc, 8*fc]
        atenuaciones = []
        for f in f_test:
            w_test = 2 * np.pi * f
            _, mag_test, _ = signal.bode(H, w_test)
            atenuaciones.append(mag_test[0])
        
        plt.semilogx(f_test, atenuaciones, 'o-', label=f'Orden {orden}')
        plt.title('Atenuación vs Frecuencia')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Atenuación (dB)')
        plt.legend()
        plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return órdenes, atenuaciones

órdenes_butter, atenuaciones_butter = diseñar_butterworth()
```

### 4. Filtros Chebyshev

#### 4.1 Comparación Butterworth vs Chebyshev
```python
def comparar_filtros():
    """
    Comparar filtros Butterworth y Chebyshev
    """
    # Parámetros
    fc = 1000  # Hz
    orden = 4
    ripple = 1  # dB
    
    # Diseñar filtros
    b_butter, a_butter = signal.butter(orden, fc, btype='low', analog=True)
    b_cheby, a_cheby = signal.cheby1(orden, ripple, fc, btype='low', analog=True)
    
    H_butter = signal.TransferFunction(b_butter, a_butter)
    H_cheby = signal.TransferFunction(b_cheby, a_cheby)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(1, 4, 1000)
    w = 2 * np.pi * frecuencias
    w, mag_butter, phase_butter = signal.bode(H_butter, w)
    w, mag_cheby, phase_cheby = signal.bode(H_cheby, w)
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag_butter, label='Butterworth')
    plt.semilogx(frecuencias, mag_cheby, label='Chebyshev')
    plt.axvline(fc, color='red', linestyle='--', alpha=0.5)
    plt.axhline(-3, color='green', linestyle='--', alpha=0.5)
    plt.title('Comparación de Filtros - Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase_butter, label='Butterworth')
    plt.semilogx(frecuencias, phase_cheby, label='Chebyshev')
    plt.axvline(fc, color='red', linestyle='--', alpha=0.5)
    plt.title('Comparación de Filtros - Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.legend()
    plt.grid(True)
    
    # Respuesta al escalón
    plt.subplot(2, 2, 3)
    t, y_butter = signal.step(H_butter)
    t, y_cheby = signal.step(H_cheby)
    plt.plot(t, y_butter, label='Butterworth')
    plt.plot(t, y_cheby, label='Chebyshev')
    plt.title('Respuesta al Escalón')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Análisis de parámetros
    plt.subplot(2, 2, 4)
    # Calcular atenuación a diferentes frecuencias
    f_test = [fc, 2*fc, 4*fc, 8*fc]
    atenuaciones_butter = []
    atenuaciones_cheby = []
    
    for f in f_test:
        w_test = 2 * np.pi * f
        _, mag_butter_test, _ = signal.bode(H_butter, w_test)
        _, mag_cheby_test, _ = signal.bode(H_cheby, w_test)
        atenuaciones_butter.append(mag_butter_test[0])
        atenuaciones_cheby.append(mag_cheby_test[0])
    
    plt.semilogx(f_test, atenuaciones_butter, 'o-', label='Butterworth')
    plt.semilogx(f_test, atenuaciones_cheby, 's-', label='Chebyshev')
    plt.title('Atenuación vs Frecuencia')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Atenuación (dB)')
    plt.legend()
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return H_butter, H_cheby, atenuaciones_butter, atenuaciones_cheby

H_butter_comp, H_cheby_comp, atenuaciones_butter_comp, atenuaciones_cheby_comp = comparar_filtros()
```

### 5. Aplicaciones en Audio

#### 5.1 Sistema de Filtrado Completo
```python
def sistema_filtrado_completo():
    """
    Implementar sistema de filtrado completo para audio
    """
    # Parámetros
    fs = 44100
    tiempo = np.linspace(0, 0.1, int(fs * 0.1))
    
    # Generar señal de audio compleja
    señal_audio = (np.sin(2 * np.pi * 100 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 5000 * tiempo) + 
                   0.1 * np.sin(2 * np.pi * 10000 * tiempo))
    
    # Diseñar filtros
    # Filtro pasa-bajos (subwoofer)
    b_low, a_low = signal.butter(4, 200, btype='low', fs=fs)
    
    # Filtro pasa-banda (medios)
    b_mid, a_mid = signal.butter(4, [200, 2000], btype='band', fs=fs)
    
    # Filtro pasa-altos (tweeter)
    b_high, a_high = signal.butter(4, 2000, btype='high', fs=fs)
    
    # Aplicar filtros
    señal_low = signal.lfilter(b_low, a_low, señal_audio)
    señal_mid = signal.lfilter(b_mid, a_mid, señal_audio)
    señal_high = signal.lfilter(b_high, a_high, señal_audio)
    
    # Combinar señales
    señal_combinada = señal_low + señal_mid + señal_high
    
    plt.figure(figsize=(15, 12))
    
    # Señal original
    plt.subplot(3, 2, 1)
    plt.plot(tiempo, señal_audio)
    plt.title('Señal Original')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Señales filtradas
    plt.subplot(3, 2, 2)
    plt.plot(tiempo, señal_low, label='Pasa-Bajos')
    plt.plot(tiempo, señal_mid, label='Pasa-Banda')
    plt.plot(tiempo, señal_high, label='Pasa-Altos')
    plt.title('Señales Filtradas')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Señal combinada
    plt.subplot(3, 2, 3)
    plt.plot(tiempo, señal_combinada)
    plt.title('Señal Combinada')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Espectros
    plt.subplot(3, 2, 4)
    fft_original = np.fft.fft(señal_audio)
    fft_combinada = np.fft.fft(señal_combinada)
    frecuencias_fft = np.fft.fftfreq(len(señal_audio), 1/fs)
    
    plt.semilogx(frecuencias_fft[:len(frecuencias_fft)//2], 
                 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), 
                 label='Original', alpha=0.7)
    plt.semilogx(frecuencias_fft[:len(frecuencias_fft)//2], 
                 20*np.log10(np.abs(fft_combinada[:len(fft_combinada)//2]) + 1e-10), 
                 label='Combinada', alpha=0.7)
    plt.title('Espectros')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Respuestas en frecuencia de los filtros
    plt.subplot(3, 2, 5)
    frecuencias = np.logspace(1, 4, 1000)
    w = 2 * np.pi * frecuencias / fs
    
    w, H_low = signal.freqz(b_low, a_low, worN=w)
    w, H_mid = signal.freqz(b_mid, a_mid, worN=w)
    w, H_high = signal.freqz(b_high, a_high, worN=w)
    
    frecuencias_hz = w * fs / (2 * np.pi)
    plt.semilogx(frecuencias_hz, 20*np.log10(np.abs(H_low) + 1e-10), label='Pasa-Bajos')
    plt.semilogx(frecuencias_hz, 20*np.log10(np.abs(H_mid) + 1e-10), label='Pasa-Banda')
    plt.semilogx(frecuencias_hz, 20*np.log10(np.abs(H_high) + 1e-10), label='Pasa-Altos')
    plt.title('Respuestas de los Filtros')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Análisis de parámetros
    plt.subplot(3, 2, 6)
    plt.text(0.1, 0.8, f'Frecuencia de Muestreo: {fs} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Filtro Pasa-Bajos: 200 Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Filtro Pasa-Banda: 200-2000 Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Filtro Pasa-Altos: 2000 Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Orden de Filtros: 4', transform=plt.gca().transAxes)
    plt.title('Parámetros del Sistema')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return señal_audio, señal_combinada, b_low, a_low, b_mid, a_mid, b_high, a_high

señal_audio_sistema, señal_combinada_sistema, b_low_sistema, a_low_sistema, b_mid_sistema, a_mid_sistema, b_high_sistema, a_high_sistema = sistema_filtrado_completo()
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Diseño de Filtro Personalizado
```python
def ejercicio_filtro_personalizado():
    """
    Diseñar filtro personalizado con especificaciones
    """
    # Especificaciones
    fs = 44100
    f_paso = 1000  # Hz
    f_parada = 2000  # Hz
    atenuacion_paso = 1  # dB
    atenuacion_parada = 40  # dB
    
    # Diseñar filtro
    nyquist = fs / 2
    wp = f_paso / nyquist
    ws = f_parada / nyquist
    
    N, Wn = signal.buttord(wp, ws, atenuacion_paso, atenuacion_parada)
    b, a = signal.butter(N, Wn, btype='low')
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(1, 4, 1000)
    w = 2 * np.pi * frecuencias / fs
    w, H = signal.freqz(b, a, worN=w)
    
    frecuencias_hz = w * fs / (2 * np.pi)
    magnitud_db = 20 * np.log10(np.abs(H))
    
    plt.figure(figsize=(12, 8))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias_hz, magnitud_db)
    plt.axvline(f_paso, color='green', linestyle='--', label=f'Frecuencia de Paso: {f_paso} Hz')
    plt.axvline(f_parada, color='red', linestyle='--', label=f'Frecuencia de Parada: {f_parada} Hz')
    plt.axhline(-atenuacion_paso, color='green', linestyle='--', alpha=0.5)
    plt.axhline(-atenuacion_parada, color='red', linestyle='--', alpha=0.5)
    plt.title(f'Filtro Butterworth de Orden {N}')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Aplicar a señal de audio
    plt.subplot(2, 2, 2)
    tiempo = np.linspace(0, 0.1, int(fs * 0.1))
    señal_audio = (np.sin(2 * np.pi * 500 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 1500 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 3000 * tiempo))
    
    señal_filtrada = signal.lfilter(b, a, señal_audio)
    
    plt.plot(tiempo, señal_audio, label='Original', alpha=0.7)
    plt.plot(tiempo, señal_filtrada, label='Filtrada', alpha=0.7)
    plt.title('Aplicación a Audio')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectros
    plt.subplot(2, 2, 3)
    fft_original = np.fft.fft(señal_audio)
    fft_filtrada = np.fft.fft(señal_filtrada)
    frecuencias_fft = np.fft.fftfreq(len(señal_audio), 1/fs)
    
    plt.semilogx(frecuencias_fft[:len(frecuencias_fft)//2], 
                 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), 
                 label='Original', alpha=0.7)
    plt.semilogx(frecuencias_fft[:len(frecuencias_fft)//2], 
                 20*np.log10(np.abs(fft_filtrada[:len(fft_filtrada)//2]) + 1e-10), 
                 label='Filtrada', alpha=0.7)
    plt.title('Espectros')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Análisis de parámetros
    plt.subplot(2, 2, 4)
    plt.text(0.1, 0.8, f'Orden del Filtro: {N}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Frecuencia de Corte: {Wn*fs/2:.1f} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Atenuación de Paso: {atenuacion_paso} dB', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Atenuación de Parada: {atenuacion_parada} dB', transform=plt.gca().transAxes)
    plt.title('Parámetros del Filtro')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return b, a, N, Wn

b_ejercicio, a_ejercicio, N_ejercicio, Wn_ejercicio = ejercicio_filtro_personalizado()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **Filtros Pasivos**: RC, RLC, resonantes
2. **Filtros Activos**: Sallen-Key, con amplificadores operacionales
3. **Filtros Butterworth**: Respuesta plana en banda de paso
4. **Filtros Chebyshev**: Ripple controlado en banda de paso
5. **Aplicaciones en Audio**: Sistemas de filtrado, ecualizadores

### Aplicaciones en Audio
- Sistemas de altavoces
- Ecualizadores
- Filtros anti-aliasing
- Procesamiento de señales

### Herramientas Utilizadas
- Diseño de filtros analógicos
- Análisis de respuesta en frecuencia
- Simulación de circuitos
- Aplicación a señales de audio

## 🎯 Objetivos Alcanzados
- ✅ Diseñar filtros pasivos
- ✅ Implementar filtros activos
- ✅ Comparar tipos de filtros
- ✅ Aplicar a sistemas de audio
- ✅ Optimizar parámetros

## 🚀 Navegación

**← Anterior**: [Clase 4.5: Respuesta en Frecuencia](clase-4-5-respuesta-frecuencia.md)
**Siguiente →**: [Clase 4.7: Distorsión y Ruido](clase-4-7-distorsion-ruido.md)

---

*Duración de la clase: 4-5 horas*
