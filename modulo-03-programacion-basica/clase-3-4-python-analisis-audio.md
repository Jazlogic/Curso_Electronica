# Clase 3.4: Python para Análisis de Audio

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender las librerías fundamentales de Python para audio
- Entender NumPy, SciPy y librosa para procesamiento de señales
- Crear herramientas de análisis espectral y visualización
- Implementar algoritmos de procesamiento de audio
- Desarrollar prototipos rápidos para validar conceptos

## Introducción a Python para Audio

### ¿Por qué Python para Audio?

**Python** es ideal para el análisis de audio porque:

1. **Rapidez de desarrollo**: Prototipado rápido de algoritmos
2. **Librerías especializadas**: NumPy, SciPy, librosa, matplotlib
3. **Visualización**: Gráficos y espectrogramas fáciles de crear
4. **Análisis**: Herramientas avanzadas de procesamiento de señales
5. **Integración**: Fácil conexión con sistemas C/C++

### Librerías Principales

- **NumPy**: Arrays multidimensionales y operaciones matemáticas
- **SciPy**: Algoritmos científicos y procesamiento de señales
- **librosa**: Análisis de audio y música
- **matplotlib**: Visualización de datos
- **soundfile**: Lectura y escritura de archivos de audio

## Configuración del Entorno

### Instalación de Librerías

```bash
# Instalar librerías básicas
pip install numpy scipy matplotlib

# Instalar librerías específicas para audio
pip install librosa soundfile

# Instalar librerías adicionales
pip install scikit-learn pandas
```

### Importación de Librerías

```python
import numpy as np
import matplotlib.pyplot as plt
import librosa
import soundfile as sf
from scipy import signal
from scipy.fft import fft, fftfreq
import warnings
warnings.filterwarnings('ignore')
```

## Fundamentos de NumPy

### Arrays y Operaciones Básicas

```python
# Crear arrays
audio_signal = np.array([0.1, 0.5, -0.3, 0.8, -0.2])
print("Señal de audio:", audio_signal)

# Crear arrays con rangos
time = np.linspace(0, 1, 44100)  # 1 segundo a 44.1 kHz
frequency = 440  # Hz
sine_wave = np.sin(2 * np.pi * frequency * time)

# Operaciones elementales
scaled_signal = audio_signal * 0.5
summed_signal = audio_signal + 0.1
squared_signal = audio_signal ** 2

# Operaciones estadísticas
mean_val = np.mean(audio_signal)
max_val = np.max(audio_signal)
min_val = np.min(audio_signal)
rms_val = np.sqrt(np.mean(audio_signal ** 2))

print(f"Media: {mean_val:.3f}")
print(f"RMS: {rms_val:.3f}")
```

### Generación de Señales

```python
def generate_tone(frequency, duration, sample_rate=44100, amplitude=0.5):
    """Generar un tono sinusoidal"""
    t = np.linspace(0, duration, int(sample_rate * duration))
    signal = amplitude * np.sin(2 * np.pi * frequency * t)
    return t, signal

def generate_chord(frequencies, duration, sample_rate=44100):
    """Generar un acorde con múltiples frecuencias"""
    t = np.linspace(0, duration, int(sample_rate * duration))
    chord = np.zeros_like(t)
    
    for freq in frequencies:
        chord += np.sin(2 * np.pi * freq * t)
    
    # Normalizar
    chord = chord / len(frequencies)
    return t, chord

# Ejemplo de uso
t, tone = generate_tone(440, 1.0)  # La musical por 1 segundo
t, chord = generate_chord([261.63, 329.63, 392.00], 1.0)  # Do mayor
```

## Análisis Espectral

### Transformada de Fourier

```python
def analyze_spectrum(signal, sample_rate):
    """Analizar el espectro de una señal"""
    # Calcular FFT
    fft_result = fft(signal)
    frequencies = fftfreq(len(signal), 1/sample_rate)
    
    # Solo frecuencias positivas
    positive_freqs = frequencies[:len(frequencies)//2]
    magnitude = np.abs(fft_result[:len(fft_result)//2])
    
    return positive_freqs, magnitude

def plot_spectrum(signal, sample_rate, title="Espectro de Frecuencia"):
    """Visualizar el espectro de una señal"""
    freqs, magnitude = analyze_spectrum(signal, sample_rate)
    
    plt.figure(figsize=(12, 6))
    plt.subplot(1, 2, 1)
    plt.plot(signal[:1000])  # Primeros 1000 muestras
    plt.title("Señal en el Tiempo")
    plt.xlabel("Muestras")
    plt.ylabel("Amplitud")
    
    plt.subplot(1, 2, 2)
    plt.plot(freqs, magnitude)
    plt.title(title)
    plt.xlabel("Frecuencia (Hz)")
    plt.ylabel("Magnitud")
    plt.xlim(0, sample_rate/2)
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

# Ejemplo de uso
t, signal = generate_tone(440, 1.0)
plot_spectrum(signal, 44100, "Espectro de La Musical")
```

### Espectrograma

```python
def create_spectrogram(signal, sample_rate, window_size=1024, hop_length=512):
    """Crear un espectrograma"""
    # Usar librosa para el espectrograma
    stft = librosa.stft(signal, n_fft=window_size, hop_length=hop_length)
    magnitude = np.abs(stft)
    
    # Convertir a dB
    magnitude_db = librosa.amplitude_to_db(magnitude)
    
    return magnitude_db

def plot_spectrogram(signal, sample_rate, title="Espectrograma"):
    """Visualizar espectrograma"""
    magnitude_db = create_spectrogram(signal, sample_rate)
    
    plt.figure(figsize=(12, 6))
    librosa.display.specshow(magnitude_db, sr=sample_rate, 
                           x_axis='time', y_axis='hz')
    plt.colorbar(format='%+2.0f dB')
    plt.title(title)
    plt.xlabel("Tiempo (s)")
    plt.ylabel("Frecuencia (Hz)")
    plt.show()

# Ejemplo con señal compleja
def create_chirp_signal(duration, sample_rate, start_freq, end_freq):
    """Crear señal chirp (frecuencia variable)"""
    t = np.linspace(0, duration, int(sample_rate * duration))
    # Frecuencia que varía linealmente
    freq = start_freq + (end_freq - start_freq) * t / duration
    signal = np.sin(2 * np.pi * freq * t)
    return t, signal

# Crear y visualizar chirp
t, chirp = create_chirp_signal(2.0, 44100, 100, 2000)
plot_spectrogram(chirp, 44100, "Espectrograma de Chirp")
```

## Filtros Digitales

### Filtros con SciPy

```python
def design_lowpass_filter(cutoff_freq, sample_rate, order=4):
    """Diseñar filtro pasa-bajo"""
    nyquist = sample_rate / 2
    normalized_cutoff = cutoff_freq / nyquist
    
    # Diseñar filtro Butterworth
    b, a = signal.butter(order, normalized_cutoff, btype='low')
    return b, a

def apply_filter(signal, b, a):
    """Aplicar filtro a una señal"""
    filtered_signal = signal.filtfilt(b, a, signal)
    return filtered_signal

def plot_filter_response(b, a, sample_rate, title="Respuesta del Filtro"):
    """Visualizar respuesta en frecuencia del filtro"""
    w, h = signal.freqz(b, a, worN=8000)
    frequencies = w * sample_rate / (2 * np.pi)
    
    plt.figure(figsize=(12, 6))
    plt.subplot(1, 2, 1)
    plt.plot(frequencies, 20 * np.log10(np.abs(h)))
    plt.title("Magnitud")
    plt.xlabel("Frecuencia (Hz)")
    plt.ylabel("Magnitud (dB)")
    plt.grid(True)
    
    plt.subplot(1, 2, 2)
    plt.plot(frequencies, np.angle(h))
    plt.title("Fase")
    plt.xlabel("Frecuencia (Hz)")
    plt.ylabel("Fase (radianes)")
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

# Ejemplo de uso
sample_rate = 44100
cutoff_freq = 1000

# Diseñar filtro
b, a = design_lowpass_filter(cutoff_freq, sample_rate)

# Crear señal de prueba con múltiples frecuencias
t = np.linspace(0, 1, sample_rate)
signal = (np.sin(2 * np.pi * 500 * t) + 
          np.sin(2 * np.pi * 1500 * t) + 
          np.sin(2 * np.pi * 3000 * t))

# Aplicar filtro
filtered_signal = apply_filter(signal, b, a)

# Visualizar resultados
plt.figure(figsize=(15, 10))

plt.subplot(3, 1, 1)
plt.plot(t[:1000], signal[:1000])
plt.title("Señal Original")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud")

plt.subplot(3, 1, 2)
plt.plot(t[:1000], filtered_signal[:1000])
plt.title("Señal Filtrada")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud")

plt.subplot(3, 1, 3)
freqs_orig, mag_orig = analyze_spectrum(signal, sample_rate)
freqs_filt, mag_filt = analyze_spectrum(filtered_signal, sample_rate)
plt.plot(freqs_orig, mag_orig, label="Original", alpha=0.7)
plt.plot(freqs_filt, mag_filt, label="Filtrada", alpha=0.7)
plt.title("Espectros")
plt.xlabel("Frecuencia (Hz)")
plt.ylabel("Magnitud")
plt.legend()
plt.xlim(0, 5000)

plt.tight_layout()
plt.show()
```

## Análisis de Características de Audio

### Extracción de Características con Librosa

```python
def extract_audio_features(audio_file):
    """Extraer características de un archivo de audio"""
    # Cargar audio
    y, sr = librosa.load(audio_file)
    
    # Extraer características
    features = {}
    
    # Características temporales
    features['rms'] = librosa.feature.rms(y=y)[0]
    features['zero_crossing_rate'] = librosa.feature.zero_crossing_rate(y)[0]
    features['spectral_centroid'] = librosa.feature.spectral_centroid(y=y, sr=sr)[0]
    features['spectral_bandwidth'] = librosa.feature.spectral_bandwidth(y=y, sr=sr)[0]
    features['spectral_rolloff'] = librosa.feature.spectral_rolloff(y=y, sr=sr)[0]
    
    # Características espectrales
    features['mfcc'] = librosa.feature.mfcc(y=y, sr=sr, n_mfcc=13)
    features['chroma'] = librosa.feature.chroma_stft(y=y, sr=sr)
    features['spectral_contrast'] = librosa.feature.spectral_contrast(y=y, sr=sr)
    
    return features, y, sr

def plot_audio_features(features, y, sr):
    """Visualizar características de audio"""
    plt.figure(figsize=(15, 12))
    
    # RMS
    plt.subplot(3, 2, 1)
    plt.plot(features['rms'])
    plt.title('RMS Energy')
    plt.ylabel('RMS')
    
    # Zero Crossing Rate
    plt.subplot(3, 2, 2)
    plt.plot(features['zero_crossing_rate'])
    plt.title('Zero Crossing Rate')
    plt.ylabel('ZCR')
    
    # Spectral Centroid
    plt.subplot(3, 2, 3)
    plt.plot(features['spectral_centroid'])
    plt.title('Spectral Centroid')
    plt.ylabel('Hz')
    
    # Spectral Bandwidth
    plt.subplot(3, 2, 4)
    plt.plot(features['spectral_bandwidth'])
    plt.title('Spectral Bandwidth')
    plt.ylabel('Hz')
    
    # MFCC
    plt.subplot(3, 2, 5)
    librosa.display.specshow(features['mfcc'], x_axis='time')
    plt.title('MFCC')
    plt.ylabel('MFCC Coefficients')
    plt.colorbar()
    
    # Chroma
    plt.subplot(3, 2, 6)
    librosa.display.specshow(features['chroma'], x_axis='time', y_axis='chroma')
    plt.title('Chroma Features')
    plt.ylabel('Pitch Class')
    plt.colorbar()
    
    plt.tight_layout()
    plt.show()

# Ejemplo de uso (requiere archivo de audio)
# features, y, sr = extract_audio_features('audio_file.wav')
# plot_audio_features(features, y, sr)
```

### Detección de Pitch

```python
def detect_pitch(signal, sample_rate, method='yin'):
    """Detectar pitch de una señal"""
    if method == 'yin':
        # Usar algoritmo YIN
        pitches, magnitudes = librosa.piptrack(y=signal, sr=sample_rate)
        pitch = []
        for t in range(pitches.shape[1]):
            index = magnitudes[:, t].argmax()
            pitch.append(pitches[index, t])
        return np.array(pitch)
    
    elif method == 'autocorr':
        # Usar autocorrelación
        pitches = []
        frame_length = 2048
        hop_length = 512
        
        for i in range(0, len(signal) - frame_length, hop_length):
            frame = signal[i:i + frame_length]
            autocorr = np.correlate(frame, frame, mode='full')
            autocorr = autocorr[autocorr.size // 2:]
            
            # Encontrar picos
            peaks = signal.find_peaks(autocorr, height=0.1 * np.max(autocorr))[0]
            if len(peaks) > 0:
                # Primer pico después del pico en 0
                pitch_period = peaks[0]
                if pitch_period > 0:
                    pitch_freq = sample_rate / pitch_period
                    pitches.append(pitch_freq)
                else:
                    pitches.append(0)
            else:
                pitches.append(0)
        
        return np.array(pitches)

def plot_pitch_contour(signal, sample_rate, pitch, title="Pitch Contour"):
    """Visualizar contorno de pitch"""
    plt.figure(figsize=(12, 8))
    
    # Señal de audio
    plt.subplot(2, 1, 1)
    time = np.linspace(0, len(signal) / sample_rate, len(signal))
    plt.plot(time, signal)
    plt.title("Señal de Audio")
    plt.xlabel("Tiempo (s)")
    plt.ylabel("Amplitud")
    
    # Contorno de pitch
    plt.subplot(2, 1, 2)
    pitch_time = np.linspace(0, len(signal) / sample_rate, len(pitch))
    plt.plot(pitch_time, pitch)
    plt.title(title)
    plt.xlabel("Tiempo (s)")
    plt.ylabel("Frecuencia (Hz)")
    plt.ylim(0, 1000)
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

# Ejemplo de uso
t, signal = generate_tone(440, 2.0)  # La musical
pitch = detect_pitch(signal, 44100)
plot_pitch_contour(signal, 44100, pitch, "Pitch de La Musical")
```

## Efectos de Audio

### Implementación de Efectos

```python
class AudioEffect:
    """Clase base para efectos de audio"""
    def __init__(self):
        self.enabled = True
    
    def process(self, signal):
        if self.enabled:
            return self._process(signal)
        return signal
    
    def _process(self, signal):
        raise NotImplementedError("Subclases deben implementar _process")

class DistortionEffect(AudioEffect):
    """Efecto de distorsión"""
    def __init__(self, gain=2.0, threshold=0.7):
        super().__init__()
        self.gain = gain
        self.threshold = threshold
    
    def _process(self, signal):
        # Aplicar ganancia
        distorted = signal * self.gain
        
        # Distorsión suave
        distorted = np.tanh(distorted)
        
        return distorted

class EchoEffect(AudioEffect):
    """Efecto de eco"""
    def __init__(self, delay_samples=4410, feedback=0.3, mix=0.5):
        super().__init__()
        self.delay_samples = delay_samples
        self.feedback = feedback
        self.mix = mix
        self.delay_buffer = np.zeros(delay_samples)
        self.write_pos = 0
    
    def _process(self, signal):
        output = np.zeros_like(signal)
        
        for i in range(len(signal)):
            # Leer del buffer de retraso
            read_pos = (self.write_pos + 1) % self.delay_samples
            delayed = self.delay_buffer[read_pos]
            
            # Combinar señal original con señal retardada
            output[i] = signal[i] + delayed * self.feedback
            
            # Escribir al buffer de retraso
            self.delay_buffer[self.write_pos] = output[i]
            self.write_pos = (self.write_pos + 1) % self.delay_samples
        
        # Mezclar señal original con señal procesada
        return signal * (1 - self.mix) + output * self.mix

class LowPassFilter(AudioEffect):
    """Filtro pasa-bajo"""
    def __init__(self, cutoff_freq, sample_rate, order=2):
        super().__init__()
        self.cutoff_freq = cutoff_freq
        self.sample_rate = sample_rate
        self.order = order
        
        # Diseñar filtro
        nyquist = sample_rate / 2
        normalized_cutoff = cutoff_freq / nyquist
        self.b, self.a = signal.butter(order, normalized_cutoff, btype='low')
        
        # Estado del filtro
        self.zi = signal.lfilter_zi(self.b, self.a)
    
    def _process(self, signal):
        filtered, self.zi = signal.lfilter(self.b, self.a, signal, zi=self.zi)
        return filtered

# Ejemplo de uso
def apply_effects_chain(signal, effects):
    """Aplicar cadena de efectos"""
    output = signal.copy()
    for effect in effects:
        output = effect.process(output)
    return output

# Crear señal de prueba
t, signal = generate_tone(440, 1.0)

# Crear efectos
distortion = DistortionEffect(gain=3.0, threshold=0.5)
echo = EchoEffect(delay_samples=2205, feedback=0.4, mix=0.3)
lowpass = LowPassFilter(cutoff_freq=2000, sample_rate=44100)

# Aplicar efectos
effects = [distortion, echo, lowpass]
processed_signal = apply_effects_chain(signal, effects)

# Visualizar resultados
plt.figure(figsize=(15, 6))

plt.subplot(1, 2, 1)
plt.plot(t[:1000], signal[:1000])
plt.title("Señal Original")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud")

plt.subplot(1, 2, 2)
plt.plot(t[:1000], processed_signal[:1000])
plt.title("Señal Procesada")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud")

plt.tight_layout()
plt.show()
```

## Análisis de Latencia

### Medición de Latencia

```python
import time

def measure_processing_latency(signal, processing_function, iterations=100):
    """Medir latencia de procesamiento"""
    latencies = []
    
    for _ in range(iterations):
        start_time = time.perf_counter()
        processed_signal = processing_function(signal)
        end_time = time.perf_counter()
        
        latency = (end_time - start_time) * 1000  # Convertir a ms
        latencies.append(latency)
    
    return np.array(latencies)

def analyze_latency_performance(latencies):
    """Analizar rendimiento de latencia"""
    stats = {
        'mean': np.mean(latencies),
        'std': np.std(latencies),
        'min': np.min(latencies),
        'max': np.max(latencies),
        'p95': np.percentile(latencies, 95),
        'p99': np.percentile(latencies, 99)
    }
    
    return stats

def plot_latency_analysis(latencies, title="Análisis de Latencia"):
    """Visualizar análisis de latencia"""
    stats = analyze_latency_performance(latencies)
    
    plt.figure(figsize=(15, 5))
    
    # Histograma de latencias
    plt.subplot(1, 3, 1)
    plt.hist(latencies, bins=30, alpha=0.7, edgecolor='black')
    plt.axvline(stats['mean'], color='red', linestyle='--', label=f"Media: {stats['mean']:.2f} ms")
    plt.axvline(stats['p95'], color='orange', linestyle='--', label=f"P95: {stats['p95']:.2f} ms")
    plt.xlabel("Latencia (ms)")
    plt.ylabel("Frecuencia")
    plt.title("Distribución de Latencias")
    plt.legend()
    plt.grid(True, alpha=0.3)
    
    # Serie temporal de latencias
    plt.subplot(1, 3, 2)
    plt.plot(latencies)
    plt.xlabel("Iteración")
    plt.ylabel("Latencia (ms)")
    plt.title("Latencia vs Iteración")
    plt.grid(True, alpha=0.3)
    
    # Estadísticas
    plt.subplot(1, 3, 3)
    stats_text = f"""
    Media: {stats['mean']:.2f} ms
    Std: {stats['std']:.2f} ms
    Min: {stats['min']:.2f} ms
    Max: {stats['max']:.2f} ms
    P95: {stats['p95']:.2f} ms
    P99: {stats['p99']:.2f} ms
    """
    plt.text(0.1, 0.5, stats_text, transform=plt.gca().transAxes, 
             fontsize=12, verticalalignment='center',
             bbox=dict(boxstyle="round,pad=0.3", facecolor="lightgray"))
    plt.axis('off')
    plt.title("Estadísticas de Latencia")
    
    plt.tight_layout()
    plt.show()
    
    return stats

# Ejemplo de uso
def simple_processing(signal):
    """Función de procesamiento simple"""
    return signal * 0.5

def complex_processing(signal):
    """Función de procesamiento compleja"""
    # Aplicar múltiples operaciones
    processed = signal.copy()
    processed = np.fft.fft(processed)
    processed = np.abs(processed)
    processed = np.fft.ifft(processed)
    return np.real(processed)

# Crear señal de prueba
t, signal = generate_tone(440, 0.1)  # 100ms de señal

# Medir latencias
simple_latencies = measure_processing_latency(signal, simple_processing)
complex_latencies = measure_processing_latency(signal, complex_processing)

# Analizar y visualizar
print("Análisis de Latencia - Procesamiento Simple:")
simple_stats = plot_latency_analysis(simple_latencies, "Procesamiento Simple")

print("\nAnálisis de Latencia - Procesamiento Complejo:")
complex_stats = plot_latency_analysis(complex_latencies, "Procesamiento Complejo")
```

## Ejemplo Práctico: Analizador de Audio Completo

```python
class AudioAnalyzer:
    """Analizador de audio completo"""
    
    def __init__(self, sample_rate=44100):
        self.sample_rate = sample_rate
        self.features = {}
    
    def load_audio(self, file_path):
        """Cargar archivo de audio"""
        self.audio, self.sr = librosa.load(file_path, sr=self.sample_rate)
        return self.audio, self.sr
    
    def generate_test_signal(self, duration=2.0, frequencies=[440, 880, 1320]):
        """Generar señal de prueba"""
        t = np.linspace(0, duration, int(self.sample_rate * duration))
        signal = np.zeros_like(t)
        
        for freq in frequencies:
            signal += np.sin(2 * np.pi * freq * t)
        
        signal = signal / len(frequencies)  # Normalizar
        self.audio = signal
        self.sr = self.sample_rate
        return self.audio, self.sr
    
    def analyze(self):
        """Realizar análisis completo"""
        if self.audio is None:
            raise ValueError("No hay señal de audio cargada")
        
        # Análisis temporal
        self.features['rms'] = librosa.feature.rms(y=self.audio)[0]
        self.features['zcr'] = librosa.feature.zero_crossing_rate(self.audio)[0]
        
        # Análisis espectral
        self.features['spectral_centroid'] = librosa.feature.spectral_centroid(y=self.audio, sr=self.sr)[0]
        self.features['spectral_bandwidth'] = librosa.feature.spectral_bandwidth(y=self.audio, sr=self.sr)[0]
        self.features['spectral_rolloff'] = librosa.feature.spectral_rolloff(y=self.audio, sr=self.sr)[0]
        
        # MFCC
        self.features['mfcc'] = librosa.feature.mfcc(y=self.audio, sr=self.sr, n_mfcc=13)
        
        # Pitch
        self.features['pitch'] = detect_pitch(self.audio, self.sr)
        
        return self.features
    
    def plot_analysis(self):
        """Visualizar análisis completo"""
        if not self.features:
            self.analyze()
        
        plt.figure(figsize=(20, 15))
        
        # Señal de audio
        plt.subplot(4, 3, 1)
        time = np.linspace(0, len(self.audio) / self.sr, len(self.audio))
        plt.plot(time, self.audio)
        plt.title("Señal de Audio")
        plt.xlabel("Tiempo (s)")
        plt.ylabel("Amplitud")
        
        # Espectrograma
        plt.subplot(4, 3, 2)
        magnitude_db = create_spectrogram(self.audio, self.sr)
        librosa.display.specshow(magnitude_db, sr=self.sr, x_axis='time', y_axis='hz')
        plt.colorbar(format='%+2.0f dB')
        plt.title("Espectrograma")
        
        # RMS
        plt.subplot(4, 3, 3)
        plt.plot(self.features['rms'])
        plt.title("RMS Energy")
        plt.ylabel("RMS")
        
        # Zero Crossing Rate
        plt.subplot(4, 3, 4)
        plt.plot(self.features['zcr'])
        plt.title("Zero Crossing Rate")
        plt.ylabel("ZCR")
        
        # Spectral Centroid
        plt.subplot(4, 3, 5)
        plt.plot(self.features['spectral_centroid'])
        plt.title("Spectral Centroid")
        plt.ylabel("Hz")
        
        # Spectral Bandwidth
        plt.subplot(4, 3, 6)
        plt.plot(self.features['spectral_bandwidth'])
        plt.title("Spectral Bandwidth")
        plt.ylabel("Hz")
        
        # Spectral Rolloff
        plt.subplot(4, 3, 7)
        plt.plot(self.features['spectral_rolloff'])
        plt.title("Spectral Rolloff")
        plt.ylabel("Hz")
        
        # MFCC
        plt.subplot(4, 3, 8)
        librosa.display.specshow(self.features['mfcc'], x_axis='time')
        plt.title("MFCC")
        plt.ylabel("MFCC Coefficients")
        plt.colorbar()
        
        # Pitch
        plt.subplot(4, 3, 9)
        pitch_time = np.linspace(0, len(self.audio) / self.sr, len(self.features['pitch']))
        plt.plot(pitch_time, self.features['pitch'])
        plt.title("Pitch Contour")
        plt.xlabel("Tiempo (s)")
        plt.ylabel("Frecuencia (Hz)")
        plt.ylim(0, 1000)
        
        # Espectro de frecuencia
        plt.subplot(4, 3, 10)
        freqs, magnitude = analyze_spectrum(self.audio, self.sr)
        plt.plot(freqs, magnitude)
        plt.title("Espectro de Frecuencia")
        plt.xlabel("Frecuencia (Hz)")
        plt.ylabel("Magnitud")
        plt.xlim(0, 5000)
        
        # Histograma de amplitudes
        plt.subplot(4, 3, 11)
        plt.hist(self.audio, bins=50, alpha=0.7, edgecolor='black')
        plt.title("Distribución de Amplitudes")
        plt.xlabel("Amplitud")
        plt.ylabel("Frecuencia")
        
        # Estadísticas
        plt.subplot(4, 3, 12)
        stats_text = f"""
        Duración: {len(self.audio) / self.sr:.2f} s
        Sample Rate: {self.sr} Hz
        Muestras: {len(self.audio)}
        RMS: {np.mean(self.features['rms']):.3f}
        ZCR: {np.mean(self.features['zcr']):.3f}
        Centroid: {np.mean(self.features['spectral_centroid']):.1f} Hz
        """
        plt.text(0.1, 0.5, stats_text, transform=plt.gca().transAxes, 
                 fontsize=10, verticalalignment='center',
                 bbox=dict(boxstyle="round,pad=0.3", facecolor="lightgray"))
        plt.axis('off')
        plt.title("Estadísticas")
        
        plt.tight_layout()
        plt.show()
    
    def save_analysis(self, filename):
        """Guardar análisis en archivo"""
        if not self.features:
            self.analyze()
        
        # Guardar características
        np.savez(filename, **self.features)
        print(f"Análisis guardado en {filename}")

# Ejemplo de uso
analyzer = AudioAnalyzer()

# Generar señal de prueba
analyzer.generate_test_signal(duration=2.0, frequencies=[440, 880, 1320])

# Realizar análisis
analyzer.analyze()

# Visualizar resultados
analyzer.plot_analysis()

# Guardar análisis
analyzer.save_analysis("audio_analysis.npz")
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **NumPy**: Arrays, operaciones matemáticas, generación de señales
2. **Análisis Espectral**: FFT, espectrogramas, características espectrales
3. **Filtros Digitales**: Diseño, aplicación, visualización
4. **Características de Audio**: RMS, ZCR, MFCC, pitch
5. **Efectos de Audio**: Distorsión, eco, filtros
6. **Análisis de Latencia**: Medición, estadísticas, visualización
7. **Herramientas Completas**: Analizador de audio integrado

## Próxima Clase

En la siguiente clase estudiaremos **Verilog/VHDL básico**, que nos permitirá implementar procesamiento de audio en hardware.

---

## Navegación
- **Anterior**: [Clase 3.3: Programación Orientada a Objetos](clase-3-3-programacion-orientada-objetos.md)
- **Siguiente**: [Clase 3.5: Verilog/VHDL Básico](clase-3-5-verilog-vhdl-basico.md)
- **Volver al**: [Índice Principal](../../README.md)
