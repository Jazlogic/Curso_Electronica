# Clase 4.5: Respuesta en Frecuencia

## 🎯 Objetivo de la Clase
Entender cómo responden los sistemas a diferentes frecuencias, incluyendo función de transferencia, diagramas de Bode, resonancia y análisis de estabilidad en el dominio frecuencial.

## 📋 Contenido de la Clase

### 1. Función de Transferencia

#### 1.1 Definición y Propiedades
La función de transferencia H(s) relaciona la salida con la entrada en el dominio de Laplace.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

def analizar_funcion_transferencia():
    """
    Analizar función de transferencia de un sistema RC
    """
    # Sistema RC: H(s) = 1/(RCs + 1)
    R = 1000  # Ohm
    C = 1e-6  # F
    RC = R * C
    
    # Función de transferencia
    num = [1]
    den = [RC, 1]
    H = signal.TransferFunction(num, den)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(0, 6, 1000)  # 1 Hz a 1 MHz
    w = 2 * np.pi * frecuencias
    
    # Calcular respuesta
    w, mag, phase = signal.bode(H, w)
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag)
    plt.title('Respuesta en Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase)
    plt.title('Respuesta en Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.grid(True)
    
    # Diagrama de Nyquist
    plt.subplot(2, 2, 3)
    real_part = mag * np.cos(np.radians(phase))
    imag_part = mag * np.sin(np.radians(phase))
    plt.plot(real_part, imag_part)
    plt.title('Diagrama de Nyquist')
    plt.xlabel('Parte Real')
    plt.ylabel('Parte Imaginaria')
    plt.grid(True)
    plt.axis('equal')
    
    # Respuesta al impulso
    plt.subplot(2, 2, 4)
    t, h = signal.impulse(H)
    plt.plot(t, h)
    plt.title('Respuesta al Impulso')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return H, frecuencias, mag, phase

H_rc, frecuencias, mag, phase = analizar_funcion_transferencia()
```

#### 1.2 Sistemas de Segundo Orden
```python
def analizar_sistema_segundo_orden():
    """
    Analizar sistema de segundo orden con diferentes amortiguamientos
    """
    # Parámetros del sistema
    wn = 1000  # Frecuencia natural (rad/s)
    zetas = [0.1, 0.5, 0.707, 1.0, 2.0]  # Diferentes amortiguamientos
    
    plt.figure(figsize=(15, 10))
    
    for i, zeta in enumerate(zetas):
        # Función de transferencia: H(s) = wn²/(s² + 2*zeta*wn*s + wn²)
        num = [wn**2]
        den = [1, 2*zeta*wn, wn**2]
        H = signal.TransferFunction(num, den)
        
        # Respuesta en frecuencia
        frecuencias = np.logspace(1, 4, 1000)
        w = 2 * np.pi * frecuencias
        w, mag, phase = signal.bode(H, w)
        
        # Magnitud
        plt.subplot(2, 2, 1)
        plt.semilogx(frecuencias, mag, label=f'ζ = {zeta}')
        plt.title('Respuesta en Magnitud')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.legend()
        plt.grid(True)
        
        # Fase
        plt.subplot(2, 2, 2)
        plt.semilogx(frecuencias, phase, label=f'ζ = {zeta}')
        plt.title('Respuesta en Fase')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Fase (grados)')
        plt.legend()
        plt.grid(True)
        
        # Respuesta al escalón
        plt.subplot(2, 2, 3)
        t, y = signal.step(H)
        plt.plot(t, y, label=f'ζ = {zeta}')
        plt.title('Respuesta al Escalón')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.legend()
        plt.grid(True)
        
        # Respuesta al impulso
        plt.subplot(2, 2, 4)
        t, h = signal.impulse(H)
        plt.plot(t, h, label=f'ζ = {zeta}')
        plt.title('Respuesta al Impulso')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.legend()
        plt.grid(True)
    
    plt.tight_layout()
    plt.show()

analizar_sistema_segundo_orden()
```

### 2. Diagramas de Bode

#### 2.1 Construcción de Diagramas de Bode
```python
def construir_diagrama_bode():
    """
    Construir diagrama de Bode paso a paso
    """
    # Sistema: H(s) = 10(s+100)/(s+10)(s+1000)
    num = [10, 1000]  # 10(s+100)
    den = [1, 1010, 10000]  # (s+10)(s+1000)
    H = signal.TransferFunction(num, den)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(0, 5, 1000)
    w = 2 * np.pi * frecuencias
    w, mag, phase = signal.bode(H, w)
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag)
    plt.title('Diagrama de Bode - Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase)
    plt.title('Diagrama de Bode - Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.grid(True)
    
    # Aproximación asintótica
    plt.subplot(2, 2, 3)
    # Ceros: s = -100
    # Polos: s = -10, s = -1000
    
    # Construir aproximación asintótica
    f_cero = 100
    f_polo1 = 10
    f_polo2 = 1000
    
    # Magnitud asintótica
    mag_asintotica = np.zeros_like(frecuencias)
    
    # Ganancia DC
    ganancia_dc = 20 * np.log10(10 * 100 / (10 * 1000))  # 0 dB
    
    for i, f in enumerate(frecuencias):
        mag_asintotica[i] = ganancia_dc
        
        # Cero en 100 Hz
        if f > f_cero:
            mag_asintotica[i] += 20 * np.log10(f / f_cero)
        
        # Polo en 10 Hz
        if f > f_polo1:
            mag_asintotica[i] -= 20 * np.log10(f / f_polo1)
        
        # Polo en 1000 Hz
        if f > f_polo2:
            mag_asintotica[i] -= 20 * np.log10(f / f_polo2)
    
    plt.semilogx(frecuencias, mag, label='Exacto')
    plt.semilogx(frecuencias, mag_asintotica, '--', label='Asintótico')
    plt.title('Comparación: Exacto vs Asintótico')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Análisis de estabilidad
    plt.subplot(2, 2, 4)
    # Calcular margen de fase y ganancia
    w_cross = frecuencias[np.argmin(np.abs(mag))]
    fase_cross = phase[np.argmin(np.abs(mag))]
    
    margen_fase = 180 + fase_cross
    margen_ganancia = -mag[np.argmin(np.abs(phase + 180))]
    
    plt.text(0.1, 0.8, f'Margen de Fase: {margen_fase:.1f}°', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Margen de Ganancia: {margen_ganancia:.1f} dB', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Frecuencia de Cruce: {w_cross:.1f} Hz', transform=plt.gca().transAxes)
    plt.title('Análisis de Estabilidad')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return H, frecuencias, mag, phase

H_bode, frecuencias, mag, phase = construir_diagrama_bode()
```

#### 2.2 Análisis de Estabilidad
```python
def analizar_estabilidad():
    """
    Analizar estabilidad de diferentes sistemas
    """
    # Sistema estable
    H_estable = signal.TransferFunction([1], [1, 2, 1])
    
    # Sistema marginalmente estable
    H_marginal = signal.TransferFunction([1], [1, 0, 1])
    
    # Sistema inestable
    H_inestable = signal.TransferFunction([1], [1, -1, 1])
    
    sistemas = [
        (H_estable, 'Estable'),
        (H_marginal, 'Marginalmente Estable'),
        (H_inestable, 'Inestable')
    ]
    
    plt.figure(figsize=(15, 10))
    
    for i, (H, nombre) in enumerate(sistemas):
        # Respuesta en frecuencia
        frecuencias = np.logspace(-1, 2, 1000)
        w = 2 * np.pi * frecuencias
        w, mag, phase = signal.bode(H, w)
        
        # Magnitud
        plt.subplot(2, 3, i+1)
        plt.semilogx(frecuencias, mag)
        plt.title(f'{nombre} - Magnitud')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.grid(True)
        
        # Fase
        plt.subplot(2, 3, i+4)
        plt.semilogx(frecuencias, phase)
        plt.title(f'{nombre} - Fase')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Fase (grados)')
        plt.grid(True)
        
        # Polos y ceros
        polos = H.poles
        ceros = H.zeros
        
        print(f"{nombre}:")
        print(f"  Polos: {polos}")
        print(f"  Ceros: {ceros}")
        print(f"  Estable: {np.all(np.real(polos) < 0)}")
        print()
    
    plt.tight_layout()
    plt.show()

analizar_estabilidad()
```

### 3. Resonancia

#### 3.1 Análisis de Resonancia
```python
def analizar_resonancia():
    """
    Analizar fenómeno de resonancia
    """
    # Sistema resonante: H(s) = wn²/(s² + 2*zeta*wn*s + wn²)
    wn = 1000  # Frecuencia natural
    zeta = 0.1  # Amortiguamiento bajo
    
    num = [wn**2]
    den = [1, 2*zeta*wn, wn**2]
    H = signal.TransferFunction(num, den)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(1, 4, 1000)
    w = 2 * np.pi * frecuencias
    w, mag, phase = signal.bode(H, w)
    
    # Encontrar frecuencia de resonancia
    idx_resonancia = np.argmax(mag)
    f_resonancia = frecuencias[idx_resonancia]
    mag_resonancia = mag[idx_resonancia]
    
    # Ancho de banda
    mag_3db = mag_resonancia - 3
    idx_3db = np.where(mag >= mag_3db)[0]
    if len(idx_3db) > 0:
        f_low = frecuencias[idx_3db[0]]
        f_high = frecuencias[idx_3db[-1]]
        ancho_banda = f_high - f_low
    else:
        ancho_banda = 0
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag)
    plt.axvline(f_resonancia, color='red', linestyle='--', label=f'Resonancia: {f_resonancia:.1f} Hz')
    plt.axhline(mag_resonancia, color='red', linestyle='--', alpha=0.5)
    plt.axhline(mag_3db, color='green', linestyle='--', alpha=0.5, label='-3 dB')
    plt.title('Respuesta en Magnitud - Resonancia')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase)
    plt.axvline(f_resonancia, color='red', linestyle='--', label=f'Resonancia: {f_resonancia:.1f} Hz')
    plt.title('Respuesta en Fase - Resonancia')
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
    plt.text(0.1, 0.8, f'Frecuencia Natural: {wn:.1f} rad/s', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Frecuencia de Resonancia: {f_resonancia:.1f} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Magnitud de Resonancia: {mag_resonancia:.1f} dB', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Ancho de Banda: {ancho_banda:.1f} Hz', transform=plt.gca().transAxes)
    plt.text(0.1, 0.4, f'Factor de Calidad Q: {wn/(2*zeta*wn):.1f}', transform=plt.gca().transAxes)
    plt.title('Parámetros de Resonancia')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return H, f_resonancia, mag_resonancia, ancho_banda

H_resonante, f_resonancia, mag_resonancia, ancho_banda = analizar_resonancia()
```

#### 3.2 Filtros Resonantes
```python
def diseñar_filtro_resonante():
    """
    Diseñar filtro resonante para audio
    """
    # Parámetros del filtro
    fs = 44100
    f_centro = 1000  # Hz
    Q = 10  # Factor de calidad
    
    # Convertir a frecuencia normalizada
    w_centro = 2 * np.pi * f_centro / fs
    
    # Diseñar filtro IIR resonante
    b, a = signal.iirpeak(w_centro, Q, fs=fs)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(1, 4, 1000)
    w = 2 * np.pi * frecuencias / fs
    w, H = signal.freqz(b, a, worN=w)
    
    # Convertir a Hz
    frecuencias_hz = w * fs / (2 * np.pi)
    magnitud_db = 20 * np.log10(np.abs(H))
    fase_grados = np.degrees(np.angle(H))
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias_hz, magnitud_db)
    plt.axvline(f_centro, color='red', linestyle='--', label=f'Centro: {f_centro} Hz')
    plt.title('Filtro Resonante - Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias_hz, fase_grados)
    plt.axvline(f_centro, color='red', linestyle='--', label=f'Centro: {f_centro} Hz')
    plt.title('Filtro Resonante - Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.legend()
    plt.grid(True)
    
    # Respuesta al impulso
    plt.subplot(2, 2, 3)
    impulso = np.zeros(1000)
    impulso[0] = 1
    respuesta = signal.lfilter(b, a, impulso)
    plt.plot(respuesta)
    plt.title('Respuesta al Impulso')
    plt.xlabel('Muestras')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Aplicar a señal de audio
    plt.subplot(2, 2, 4)
    tiempo = np.linspace(0, 0.1, int(fs * 0.1))
    señal_audio = np.sin(2 * np.pi * 1000 * tiempo) + 0.5 * np.sin(2 * np.pi * 2000 * tiempo)
    señal_filtrada = signal.lfilter(b, a, señal_audio)
    
    plt.plot(tiempo, señal_audio, label='Original', alpha=0.7)
    plt.plot(tiempo, señal_filtrada, label='Filtrada', alpha=0.7)
    plt.title('Aplicación a Audio')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return b, a, f_centro, Q

b_resonante, a_resonante, f_centro, Q = diseñar_filtro_resonante()
```

### 4. Aplicaciones en Audio

#### 4.1 Ecualizador Gráfico
```python
def diseñar_ecualizador_grafico():
    """
    Diseñar ecualizador gráfico con múltiples bandas
    """
    fs = 44100
    bandas = [31, 62, 125, 250, 500, 1000, 2000, 4000, 8000, 16000]  # Hz
    ganancias = [0, 3, -2, 1, 0, -1, 2, 0, -3, 1]  # dB
    
    plt.figure(figsize=(15, 10))
    
    # Diseñar filtros para cada banda
    filtros = []
    for i, (f_centro, ganancia) in enumerate(zip(bandas, ganancias)):
        # Convertir ganancia a lineal
        ganancia_lineal = 10**(ganancia/20)
        
        # Diseñar filtro resonante
        w_centro = 2 * np.pi * f_centro / fs
        Q = 2  # Factor de calidad moderado
        
        b, a = signal.iirpeak(w_centro, Q, fs=fs)
        
        # Aplicar ganancia
        b = b * ganancia_lineal
        
        filtros.append((b, a, f_centro, ganancia))
        
        # Respuesta en frecuencia
        frecuencias = np.logspace(1, 5, 1000)
        w = 2 * np.pi * frecuencias / fs
        w, H = signal.freqz(b, a, worN=w)
        
        frecuencias_hz = w * fs / (2 * np.pi)
        magnitud_db = 20 * np.log10(np.abs(H))
        
        plt.subplot(2, 2, 1)
        plt.semilogx(frecuencias_hz, magnitud_db, label=f'{f_centro} Hz: {ganancia} dB')
        plt.title('Respuestas Individuales de Bandas')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.legend()
        plt.grid(True)
    
    # Combinar filtros
    b_total = np.array([1.0])
    a_total = np.array([1.0])
    
    for b, a, f_centro, ganancia in filtros:
        b_total = np.convolve(b_total, b)
        a_total = np.convolve(a_total, a)
    
    # Respuesta total
    frecuencias = np.logspace(1, 5, 1000)
    w = 2 * np.pi * frecuencias / fs
    w, H_total = signal.freqz(b_total, a_total, worN=w)
    
    frecuencias_hz = w * fs / (2 * np.pi)
    magnitud_total_db = 20 * np.log10(np.abs(H_total))
    
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias_hz, magnitud_total_db)
    plt.title('Respuesta Total del Ecualizador')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    # Aplicar a señal de audio
    plt.subplot(2, 2, 3)
    tiempo = np.linspace(0, 0.1, int(fs * 0.1))
    señal_audio = (np.sin(2 * np.pi * 100 * tiempo) + 
                   0.5 * np.sin(2 * np.pi * 1000 * tiempo) + 
                   0.3 * np.sin(2 * np.pi * 5000 * tiempo))
    
    señal_ecualizada = signal.lfilter(b_total, a_total, señal_audio)
    
    plt.plot(tiempo, señal_audio, label='Original', alpha=0.7)
    plt.plot(tiempo, señal_ecualizada, label='Ecualizada', alpha=0.7)
    plt.title('Aplicación a Audio')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    # Espectros
    plt.subplot(2, 2, 4)
    fft_original = np.fft.fft(señal_audio)
    fft_ecualizada = np.fft.fft(señal_ecualizada)
    frecuencias_fft = np.fft.fftfreq(len(señal_audio), 1/fs)
    
    plt.semilogx(frecuencias_fft[:len(frecuencias_fft)//2], 
                 20*np.log10(np.abs(fft_original[:len(fft_original)//2]) + 1e-10), 
                 label='Original', alpha=0.7)
    plt.semilogx(frecuencias_fft[:len(frecuencias_fft)//2], 
                 20*np.log10(np.abs(fft_ecualizada[:len(fft_ecualizada)//2]) + 1e-10), 
                 label='Ecualizada', alpha=0.7)
    plt.title('Espectros')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.legend()
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return filtros, b_total, a_total

filtros_eq, b_eq_total, a_eq_total = diseñar_ecualizador_grafico()
```

#### 4.2 Análisis de Estabilidad en Audio
```python
def analizar_estabilidad_audio():
    """
    Analizar estabilidad de sistemas de audio
    """
    # Sistema estable (filtro pasa-bajos)
    H_estable = signal.TransferFunction([1], [1, 2, 1])
    
    # Sistema con realimentación positiva (puede ser inestable)
    H_realimentacion = signal.TransferFunction([1], [1, -0.5, 0.25])
    
    sistemas = [
        (H_estable, 'Sistema Estable'),
        (H_realimentacion, 'Sistema con Realimentación')
    ]
    
    plt.figure(figsize=(15, 10))
    
    for i, (H, nombre) in enumerate(sistemas):
        # Respuesta en frecuencia
        frecuencias = np.logspace(-1, 2, 1000)
        w = 2 * np.pi * frecuencias
        w, mag, phase = signal.bode(H, w)
        
        # Magnitud
        plt.subplot(2, 3, i+1)
        plt.semilogx(frecuencias, mag)
        plt.title(f'{nombre} - Magnitud')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.grid(True)
        
        # Fase
        plt.subplot(2, 3, i+2)
        plt.semilogx(frecuencias, phase)
        plt.title(f'{nombre} - Fase')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Fase (grados)')
        plt.grid(True)
        
        # Respuesta al escalón
        plt.subplot(2, 3, i+3)
        t, y = signal.step(H)
        plt.plot(t, y)
        plt.title(f'{nombre} - Respuesta al Escalón')
        plt.xlabel('Tiempo (s)')
        plt.ylabel('Amplitud')
        plt.grid(True)
        
        # Análisis de estabilidad
        polos = H.poles
        es_estable = np.all(np.real(polos) < 0)
        
        print(f"{nombre}:")
        print(f"  Polos: {polos}")
        print(f"  Estable: {es_estable}")
        print(f"  Margen de estabilidad: {np.min(-np.real(polos)):.3f}")
        print()
    
    plt.tight_layout()
    plt.show()

analizar_estabilidad_audio()
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
    
    # Diseñar filtro Butterworth
    nyquist = fs / 2
    wp = f_paso / nyquist
    ws = f_parada / nyquist
    
    # Calcular orden del filtro
    N, Wn = signal.buttord(wp, ws, atenuacion_paso, atenuacion_parada)
    
    # Diseñar filtro
    b, a = signal.butter(N, Wn, btype='low')
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(1, 4, 1000)
    w = 2 * np.pi * frecuencias / fs
    w, H = signal.freqz(b, a, worN=w)
    
    frecuencias_hz = w * fs / (2 * np.pi)
    magnitud_db = 20 * np.log10(np.abs(H))
    fase_grados = np.degrees(np.angle(H))
    
    plt.figure(figsize=(15, 10))
    
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
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias_hz, fase_grados)
    plt.title('Respuesta en Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.grid(True)
    
    # Respuesta al impulso
    plt.subplot(2, 2, 3)
    impulso = np.zeros(1000)
    impulso[0] = 1
    respuesta = signal.lfilter(b, a, impulso)
    plt.plot(respuesta)
    plt.title('Respuesta al Impulso')
    plt.xlabel('Muestras')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    # Aplicar a señal de audio
    plt.subplot(2, 2, 4)
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
    
    plt.tight_layout()
    plt.show()
    
    return b, a, N, Wn

b_ejercicio, a_ejercicio, N_ejercicio, Wn_ejercicio = ejercicio_filtro_personalizado()
```

### Ejercicio 2: Análisis de Sistema Complejo
```python
def ejercicio_sistema_complejo():
    """
    Analizar sistema complejo con múltiples polos y ceros
    """
    # Sistema: H(s) = (s+100)(s+1000)/(s+10)(s+100)(s+10000)
    num = [1, 1100, 100000]  # (s+100)(s+1000)
    den = [1, 10110, 100100, 1000000]  # (s+10)(s+100)(s+10000)
    H = signal.TransferFunction(num, den)
    
    # Respuesta en frecuencia
    frecuencias = np.logspace(0, 5, 1000)
    w = 2 * np.pi * frecuencias
    w, mag, phase = signal.bode(H, w)
    
    plt.figure(figsize=(15, 10))
    
    # Magnitud
    plt.subplot(2, 2, 1)
    plt.semilogx(frecuencias, mag)
    plt.title('Respuesta en Magnitud')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    # Fase
    plt.subplot(2, 2, 2)
    plt.semilogx(frecuencias, phase)
    plt.title('Respuesta en Fase')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Fase (grados)')
    plt.grid(True)
    
    # Polos y ceros
    plt.subplot(2, 2, 3)
    polos = H.poles
    ceros = H.zeros
    
    # Diagrama de polos y ceros
    plt.plot(np.real(polos), np.imag(polos), 'x', markersize=10, label='Polos')
    plt.plot(np.real(ceros), np.imag(ceros), 'o', markersize=10, label='Ceros')
    plt.axhline(0, color='black', linestyle='-', alpha=0.3)
    plt.axvline(0, color='black', linestyle='-', alpha=0.3)
    plt.title('Diagrama de Polos y Ceros')
    plt.xlabel('Parte Real')
    plt.ylabel('Parte Imaginaria')
    plt.legend()
    plt.grid(True)
    plt.axis('equal')
    
    # Análisis de estabilidad
    plt.subplot(2, 2, 4)
    es_estable = np.all(np.real(polos) < 0)
    margen_estabilidad = np.min(-np.real(polos))
    
    plt.text(0.1, 0.8, f'Polos: {polos}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.7, f'Ceros: {ceros}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.6, f'Estable: {es_estable}', transform=plt.gca().transAxes)
    plt.text(0.1, 0.5, f'Margen de Estabilidad: {margen_estabilidad:.3f}', transform=plt.gca().transAxes)
    plt.title('Análisis de Estabilidad')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()
    
    return H, polos, ceros, es_estable

H_complejo, polos_complejo, ceros_complejo, estable_complejo = ejercicio_sistema_complejo()
```

## 📚 Resumen de la Clase

### Conceptos Clave
1. **Función de Transferencia**: Relación entrada-salida en dominio s
2. **Diagramas de Bode**: Representación de magnitud y fase
3. **Resonancia**: Amplificación en frecuencia específica
4. **Estabilidad**: Comportamiento del sistema en el tiempo
5. **Análisis de Polos y Ceros**: Caracterización del sistema

### Aplicaciones en Audio
- Diseño de filtros
- Ecualizadores
- Análisis de estabilidad
- Sistemas de realimentación

### Herramientas Utilizadas
- Análisis de respuesta en frecuencia
- Diagramas de Bode
- Análisis de estabilidad
- Diseño de filtros

## 🎯 Objetivos Alcanzados
- ✅ Comprender función de transferencia
- ✅ Construir diagramas de Bode
- ✅ Analizar resonancia
- ✅ Evaluar estabilidad
- ✅ Diseñar filtros para audio

## 🚀 Navegación

**← Anterior**: [Clase 4.4: Sistemas Lineales](clase-4-4-sistemas-lineales.md)
**Siguiente →**: [Clase 4.6: Filtros Analógicos](clase-4-6-filtros-analogicos.md)

---

*Duración de la clase: 4-5 horas*
