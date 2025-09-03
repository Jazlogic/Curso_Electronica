# Clase 5.5: Efectos de Audio

## 🎯 Objetivo de la Clase
Crear efectos de audio profesionales incluyendo reverb, delay, chorus, flanger, distorsión y compresión, optimizados para ultra baja latencia.

## 📚 Contenido

### 1. Introducción a los Efectos de Audio

#### ¿Qué son los Efectos de Audio?
Los **efectos de audio** son procesamientos que modifican las características de una señal de audio para crear sonidos específicos o mejorar la calidad del audio.

**Clasificación de efectos:**
- **Efectos de tiempo**: Delay, reverb, echo
- **Efectos de modulación**: Chorus, flanger, phaser
- **Efectos dinámicos**: Compresión, limitación, gate
- **Efectos de distorsión**: Overdrive, fuzz, bit-crushing
- **Efectos de filtrado**: Wah-wah, filtros automáticos

### 2. Efectos de Tiempo

#### Delay (Eco)
El delay crea copias retardadas de la señal original.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

class Delay:
    def __init__(self, tiempo_delay, fs, feedback=0.3, mix=0.5):
        """
        Efecto de delay
        
        tiempo_delay: tiempo de delay en segundos
        fs: frecuencia de muestreo
        feedback: cantidad de realimentación (0-1)
        mix: mezcla entre señal original y procesada (0-1)
        """
        self.fs = fs
        self.feedback = feedback
        self.mix = mix
        
        # Calcular tamaño del buffer
        self.delay_samples = int(tiempo_delay * fs)
        self.buffer = np.zeros(self.delay_samples, dtype=np.float32)
        self.indice = 0
    
    def procesar_muestra(self, entrada):
        """
        Procesa una muestra con efecto delay
        """
        # Leer muestra retardada
        salida_delay = self.buffer[self.indice]
        
        # Calcular nueva entrada con feedback
        entrada_con_feedback = entrada + self.feedback * salida_delay
        
        # Escribir en el buffer
        self.buffer[self.indice] = entrada_con_feedback
        
        # Actualizar índice
        self.indice = (self.indice + 1) % self.delay_samples
        
        # Mezclar señal original y procesada
        salida = (1 - self.mix) * entrada + self.mix * salida_delay
        
        return salida
    
    def procesar_señal(self, señal):
        """
        Procesa una señal completa
        """
        salida = np.zeros_like(señal, dtype=np.float32)
        for i in range(len(señal)):
            salida[i] = self.procesar_muestra(señal[i])
        return salida

# Ejemplo de uso
fs = 44100
delay = Delay(0.5, fs, feedback=0.4, mix=0.3)

# Señal de prueba
t = np.arange(0, 2, 1/fs)
f = 440  # Hz
señal = np.sin(2*np.pi*f*t)

# Aplicar delay
señal_con_delay = delay.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t, señal, label='Original', alpha=0.7)
plt.plot(t, señal_con_delay, label='Con Delay')
plt.title('Efecto Delay')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

#### Reverb (Reverberación)
El reverb simula la acústica de un espacio.

```python
class Reverb:
    def __init__(self, fs, tiempo_decaimiento=2.0, habitacion_tamaño=0.5):
        """
        Efecto de reverb usando múltiples delays
        
        fs: frecuencia de muestreo
        tiempo_decaimiento: tiempo de decaimiento en segundos
        habitacion_tamaño: tamaño relativo de la habitación (0-1)
        """
        self.fs = fs
        self.tiempo_decaimiento = tiempo_decaimiento
        
        # Crear múltiples delays con diferentes tiempos
        self.delays = []
        self.ganancias = []
        
        # Tiempos de delay basados en el tamaño de la habitación
        tiempos_base = [0.03, 0.05, 0.07, 0.11, 0.13, 0.17, 0.19, 0.23]
        
        for i, tiempo_base in enumerate(tiempos_base):
            # Variar el tiempo ligeramente
            tiempo_delay = tiempo_base * (1 + habitacion_tamaño * 0.5)
            
            # Crear delay
            delay = Delay(tiempo_delay, fs, feedback=0.2, mix=1.0)
            self.delays.append(delay)
            
            # Ganancia que decae con el tiempo
            ganancia = np.exp(-tiempo_delay / tiempo_decaimiento)
            self.ganancias.append(ganancia)
        
        # Filtro pasa-altas para simular absorción de graves
        self.filtro_altas = signal.butter(2, 2*200/fs, btype='high')
    
    def procesar_muestra(self, entrada):
        """
        Procesa una muestra con efecto reverb
        """
        salida = entrada
        
        # Procesar con cada delay
        for delay, ganancia in zip(self.delays, self.ganancias):
            salida_delay = delay.procesar_muestra(entrada)
            salida += ganancia * salida_delay
        
        # Aplicar filtro pasa-altas
        salida_filtrada, _ = signal.lfilter(self.filtro_altas[0], self.filtro_altas[1], [salida])
        
        return salida_filtrada[0]
    
    def procesar_señal(self, señal):
        """
        Procesa una señal completa
        """
        salida = np.zeros_like(señal, dtype=np.float32)
        for i in range(len(señal)):
            salida[i] = self.procesar_muestra(señal[i])
        return salida

# Ejemplo de uso
reverb = Reverb(44100, tiempo_decaimiento=1.5, habitacion_tamaño=0.7)

# Señal de prueba
t = np.arange(0, 3, 1/44100)
f = 440
señal = np.sin(2*np.pi*f*t) * np.exp(-t)  # Señal que decae

# Aplicar reverb
señal_con_reverb = reverb.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t, señal, label='Original', alpha=0.7)
plt.plot(t, señal_con_reverb, label='Con Reverb')
plt.title('Efecto Reverb')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

### 3. Efectos de Modulación

#### Chorus
El chorus crea múltiples copias ligeramente desintonizadas de la señal.

```python
class Chorus:
    def __init__(self, fs, frecuencia_modulacion=0.5, profundidad=0.02, num_voces=3):
        """
        Efecto de chorus
        
        fs: frecuencia de muestreo
        frecuencia_modulacion: frecuencia de modulación en Hz
        profundidad: profundidad de modulación (0-1)
        num_voces: número de voces del chorus
        """
        self.fs = fs
        self.frecuencia_modulacion = frecuencia_modulacion
        self.profundidad = profundidad
        self.num_voces = num_voces
        
        # Buffer para delay variable
        self.tamaño_buffer = int(0.1 * fs)  # 100ms máximo
        self.buffer = np.zeros(self.tamaño_buffer, dtype=np.float32)
        self.indice_escritura = 0
        
        # Fases para cada voz
        self.fases = np.linspace(0, 2*np.pi, num_voces, endpoint=False)
        
        # Filtro pasa-altas para eliminar DC
        self.filtro_dc = signal.butter(1, 2*20/fs, btype='high')
    
    def procesar_muestra(self, entrada):
        """
        Procesa una muestra con efecto chorus
        """
        # Escribir en el buffer
        self.buffer[self.indice_escritura] = entrada
        
        salida = entrada  # Señal original
        
        # Procesar cada voz
        for i, fase in enumerate(self.fases):
            # Calcular delay variable usando LFO
            tiempo_delay_base = 0.02  # 20ms base
            modulacion = self.profundidad * np.sin(2*np.pi*self.frecuencia_modulacion*self.indice_escritura/self.fs + fase)
            delay_samples = int((tiempo_delay_base + modulacion) * self.fs)
            
            # Leer del buffer con delay variable
            indice_lectura = (self.indice_escritura - delay_samples) % self.tamaño_buffer
            salida_delay = self.buffer[indice_lectura]
            
            # Mezclar con la señal original
            salida += 0.3 * salida_delay
        
        # Actualizar índice
        self.indice_escritura = (self.indice_escritura + 1) % self.tamaño_buffer
        
        # Aplicar filtro DC
        salida_filtrada, _ = signal.lfilter(self.filtro_dc[0], self.filtro_dc[1], [salida])
        
        return salida_filtrada[0]
    
    def procesar_señal(self, señal):
        """
        Procesa una señal completa
        """
        salida = np.zeros_like(señal, dtype=np.float32)
        for i in range(len(señal)):
            salida[i] = self.procesar_muestra(señal[i])
        return salida

# Ejemplo de uso
chorus = Chorus(44100, frecuencia_modulacion=0.8, profundidad=0.015, num_voces=4)

# Señal de prueba
t = np.arange(0, 2, 1/44100)
f = 440
señal = np.sin(2*np.pi*f*t)

# Aplicar chorus
señal_con_chorus = chorus.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t, señal, label='Original', alpha=0.7)
plt.plot(t, señal_con_chorus, label='Con Chorus')
plt.title('Efecto Chorus')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

#### Flanger
El flanger es similar al chorus pero con feedback y modulación más rápida.

```python
class Flanger:
    def __init__(self, fs, frecuencia_modulacion=1.0, profundidad=0.005, feedback=0.3):
        """
        Efecto de flanger
        
        fs: frecuencia de muestreo
        frecuencia_modulacion: frecuencia de modulación en Hz
        profundidad: profundidad de modulación (0-1)
        feedback: cantidad de realimentación (0-1)
        """
        self.fs = fs
        self.frecuencia_modulacion = frecuencia_modulacion
        self.profundidad = profundidad
        self.feedback = feedback
        
        # Buffer para delay variable
        self.tamaño_buffer = int(0.02 * fs)  # 20ms máximo
        self.buffer = np.zeros(self.tamaño_buffer, dtype=np.float32)
        self.indice_escritura = 0
        
        # Filtro pasa-altas para eliminar DC
        self.filtro_dc = signal.butter(1, 2*20/fs, btype='high')
    
    def procesar_muestra(self, entrada):
        """
        Procesa una muestra con efecto flanger
        """
        # Calcular delay variable usando LFO
        tiempo_delay_base = 0.005  # 5ms base
        modulacion = self.profundidad * np.sin(2*np.pi*self.frecuencia_modulacion*self.indice_escritura/self.fs)
        delay_samples = int((tiempo_delay_base + modulacion) * self.fs)
        
        # Leer del buffer con delay variable
        indice_lectura = (self.indice_escritura - delay_samples) % self.tamaño_buffer
        salida_delay = self.buffer[indice_lectura]
        
        # Calcular salida con feedback
        salida = entrada + salida_delay
        
        # Escribir en el buffer con feedback
        self.buffer[self.indice_escritura] = entrada + self.feedback * salida_delay
        
        # Actualizar índice
        self.indice_escritura = (self.indice_escritura + 1) % self.tamaño_buffer
        
        # Aplicar filtro DC
        salida_filtrada, _ = signal.lfilter(self.filtro_dc[0], self.filtro_dc[1], [salida])
        
        return salida_filtrada[0]
    
    def procesar_señal(self, señal):
        """
        Procesa una señal completa
        """
        salida = np.zeros_like(señal, dtype=np.float32)
        for i in range(len(señal)):
            salida[i] = self.procesar_muestra(señal[i])
        return salida

# Ejemplo de uso
flanger = Flanger(44100, frecuencia_modulacion=0.5, profundidad=0.003, feedback=0.2)

# Señal de prueba
t = np.arange(0, 2, 1/44100)
f = 440
señal = np.sin(2*np.pi*f*t)

# Aplicar flanger
señal_con_flanger = flanger.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t, señal, label='Original', alpha=0.7)
plt.plot(t, señal_con_flanger, label='Con Flanger')
plt.title('Efecto Flanger')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

### 4. Efectos Dinámicos

#### Compresor
El compresor reduce el rango dinámico de la señal.

```python
class Compresor:
    def __init__(self, fs, ratio=4.0, threshold=-20.0, attack=0.01, release=0.1):
        """
        Compresor de audio
        
        fs: frecuencia de muestreo
        ratio: relación de compresión (ej: 4:1)
        threshold: umbral en dB
        attack: tiempo de ataque en segundos
        release: tiempo de liberación en segundos
        """
        self.fs = fs
        self.ratio = ratio
        self.threshold = threshold
        self.attack = attack
        self.release = release
        
        # Coeficientes para filtros de ataque y liberación
        self.alpha_attack = np.exp(-1.0 / (attack * fs))
        self.alpha_release = np.exp(-1.0 / (release * fs))
        
        # Estado del compresor
        self.envelope = 0.0
        self.gain_reduction = 0.0
    
    def procesar_muestra(self, entrada):
        """
        Procesa una muestra con compresión
        """
        # Calcular envolvente de la señal
        entrada_abs = abs(entrada)
        
        if entrada_abs > self.envelope:
            # Ataque
            self.envelope = self.alpha_attack * self.envelope + (1 - self.alpha_attack) * entrada_abs
        else:
            # Liberación
            self.envelope = self.alpha_release * self.envelope + (1 - self.alpha_release) * entrada_abs
        
        # Convertir a dB
        envelope_db = 20 * np.log10(self.envelope + 1e-10)
        
        # Calcular reducción de ganancia
        if envelope_db > self.threshold:
            # Aplicar compresión
            exceso = envelope_db - self.threshold
            reduccion = exceso * (1 - 1/self.ratio)
            self.gain_reduction = reduccion
        else:
            self.gain_reduction = 0
        
        # Aplicar reducción de ganancia
        ganancia_linear = 10**(-self.gain_reduction/20)
        salida = entrada * ganancia_linear
        
        return salida
    
    def procesar_señal(self, señal):
        """
        Procesa una señal completa
        """
        salida = np.zeros_like(señal, dtype=np.float32)
        for i in range(len(señal)):
            salida[i] = self.procesar_muestra(señal[i])
        return salida

# Ejemplo de uso
compresor = Compresor(44100, ratio=3.0, threshold=-15.0, attack=0.005, release=0.1)

# Señal de prueba con variación dinámica
t = np.arange(0, 2, 1/44100)
f = 440
# Señal con variación de amplitud
amplitud = 0.5 + 0.5 * np.sin(2*np.pi*0.5*t)  # Modulación lenta
señal = amplitud * np.sin(2*np.pi*f*t)

# Aplicar compresión
señal_comprimida = compresor.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 8))

plt.subplot(3, 1, 1)
plt.plot(t, señal, label='Original')
plt.title('Señal Original')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(t, señal_comprimida, label='Comprimida')
plt.title('Señal Comprimida')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(t, np.abs(señal), label='Envolvente Original', alpha=0.7)
plt.plot(t, np.abs(señal_comprimida), label='Envolvente Comprimida')
plt.title('Envolventes')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
```

### 5. Efectos de Distorsión

#### Overdrive
El overdrive simula la saturación de amplificadores.

```python
class Overdrive:
    def __init__(self, fs, drive=0.5, tone=0.5, level=0.7):
        """
        Efecto de overdrive
        
        fs: frecuencia de muestreo
        drive: cantidad de distorsión (0-1)
        tone: control de tono (0-1)
        level: nivel de salida (0-1)
        """
        self.fs = fs
        self.drive = drive
        self.tone = tone
        self.level = level
        
        # Filtro pasa-altas para control de tono
        self.filtro_altas = signal.butter(2, 2*100/fs, btype='high')
        
        # Filtro pasa-bajas para control de tono
        self.filtro_bajas = signal.butter(2, 2*8000/fs, btype='low')
    
    def funcion_distorsion(self, x):
        """
        Función de distorsión no lineal
        """
        # Aplicar ganancia
        x_gain = x * (1 + self.drive * 10)
        
        # Función de saturación suave
        if x_gain > 1:
            return 1
        elif x_gain < -1:
            return -1
        else:
            # Saturación suave usando tangente hiperbólica
            return np.tanh(x_gain)
    
    def procesar_muestra(self, entrada):
        """
        Procesa una muestra con overdrive
        """
        # Aplicar distorsión
        salida_distorsion = self.funcion_distorsion(entrada)
        
        # Control de tono
        if self.tone < 0.5:
            # Más graves
            salida_filtrada, _ = signal.lfilter(self.filtro_bajas[0], self.filtro_bajas[1], [salida_distorsion])
        else:
            # Más agudos
            salida_filtrada, _ = signal.lfilter(self.filtro_altas[0], self.filtro_altas[1], [salida_distorsion])
        
        # Aplicar nivel
        salida = salida_filtrada[0] * self.level
        
        return salida
    
    def procesar_señal(self, señal):
        """
        Procesa una señal completa
        """
        salida = np.zeros_like(señal, dtype=np.float32)
        for i in range(len(señal)):
            salida[i] = self.procesar_muestra(señal[i])
        return salida

# Ejemplo de uso
overdrive = Overdrive(44100, drive=0.7, tone=0.6, level=0.8)

# Señal de prueba
t = np.arange(0, 1, 1/44100)
f = 440
señal = np.sin(2*np.pi*f*t)

# Aplicar overdrive
señal_con_overdrive = overdrive.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t, señal, label='Original', alpha=0.7)
plt.plot(t, señal_con_overdrive, label='Con Overdrive')
plt.title('Efecto Overdrive')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

### 6. Cadena de Efectos

#### Procesador de Efectos Múltiples
```python
class ProcesadorEfectos:
    def __init__(self, fs):
        """
        Procesador que combina múltiples efectos
        """
        self.fs = fs
        
        # Inicializar efectos
        self.delay = Delay(0.3, fs, feedback=0.2, mix=0.3)
        self.chorus = Chorus(fs, frecuencia_modulacion=0.5, profundidad=0.01, num_voces=3)
        self.compresor = Compresor(fs, ratio=2.0, threshold=-12.0, attack=0.01, release=0.1)
        self.overdrive = Overdrive(fs, drive=0.3, tone=0.5, level=0.8)
        
        # Orden de procesamiento
        self.efectos = [self.compresor, self.overdrive, self.chorus, self.delay]
    
    def procesar_muestra(self, entrada):
        """
        Procesa una muestra a través de toda la cadena
        """
        salida = entrada
        
        for efecto in self.efectos:
            salida = efecto.procesar_muestra(salida)
        
        return salida
    
    def procesar_señal(self, señal):
        """
        Procesa una señal completa
        """
        salida = np.zeros_like(señal, dtype=np.float32)
        for i in range(len(señal)):
            salida[i] = self.procesar_muestra(señal[i])
        return salida

# Ejemplo de uso
procesador = ProcesadorEfectos(44100)

# Señal de prueba
t = np.arange(0, 3, 1/44100)
f = 440
señal = np.sin(2*np.pi*f*t)

# Aplicar cadena de efectos
señal_procesada = procesador.procesar_señal(señal)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(t, señal, label='Original', alpha=0.7)
plt.plot(t, señal_procesada, label='Con Efectos')
plt.title('Cadena de Efectos de Audio')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Simulador de Amplificador de Guitarra
```python
def ejercicio_simulador_amplificador():
    """
    Simula un amplificador de guitarra con múltiples efectos
    """
    fs = 44100
    
    # Crear simulador
    simulador = ProcesadorEfectos(fs)
    
    # Señal de guitarra simulada
    t = np.arange(0, 2, 1/fs)
    f = 220  # A3
    
    # Simular ataque de guitarra
    ataque = np.exp(-t * 5)
    señal_guitarra = ataque * np.sin(2*np.pi*f*t)
    
    # Aplicar efectos
    señal_amplificada = simulador.procesar_señal(señal_guitarra)
    
    # Visualizar
    plt.figure(figsize=(12, 8))
    
    plt.subplot(2, 1, 1)
    plt.plot(t, señal_guitarra, label='Guitarra Original')
    plt.title('Señal de Guitarra Original')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(2, 1, 2)
    plt.plot(t, señal_amplificada, label='Guitarra Amplificada')
    plt.title('Guitarra con Efectos')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.legend()
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_simulador_amplificador()
```

### Ejercicio 2: Análisis de Efectos en el Dominio de la Frecuencia
```python
def ejercicio_analisis_frecuencia_efectos():
    """
    Analiza cómo los efectos modifican el espectro de frecuencia
    """
    fs = 44100
    
    # Señal de prueba
    t = np.arange(0, 1, 1/fs)
    f1, f2 = 440, 880
    señal = np.sin(2*np.pi*f1*t) + 0.5*np.sin(2*np.pi*f2*t)
    
    # Aplicar diferentes efectos
    delay = Delay(0.1, fs, feedback=0.3, mix=0.5)
    chorus = Chorus(fs, frecuencia_modulacion=0.5, profundidad=0.01, num_voces=3)
    overdrive = Overdrive(fs, drive=0.5, tone=0.5, level=0.8)
    
    señal_delay = delay.procesar_señal(señal)
    señal_chorus = chorus.procesar_señal(señal)
    señal_overdrive = overdrive.procesar_señal(señal)
    
    # Análisis espectral
    from scipy.fft import fft, fftfreq
    
    def analizar_espectro(señal, nombre):
        X = fft(señal)
        freqs = fftfreq(len(señal), 1/fs)[:len(señal)//2]
        magnitud = np.abs(X[:len(X)//2])
        return freqs, magnitud
    
    # Obtener espectros
    freqs, magnitud_original = analizar_espectro(señal, 'Original')
    _, magnitud_delay = analizar_espectro(señal_delay, 'Delay')
    _, magnitud_chorus = analizar_espectro(señal_chorus, 'Chorus')
    _, magnitud_overdrive = analizar_espectro(señal_overdrive, 'Overdrive')
    
    # Visualizar
    plt.figure(figsize=(12, 10))
    
    plt.subplot(2, 2, 1)
    plt.plot(freqs, 20*np.log10(magnitud_original + 1e-10))
    plt.title('Espectro Original')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.subplot(2, 2, 2)
    plt.plot(freqs, 20*np.log10(magnitud_delay + 1e-10))
    plt.title('Espectro con Delay')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.subplot(2, 2, 3)
    plt.plot(freqs, 20*np.log10(magnitud_chorus + 1e-10))
    plt.title('Espectro con Chorus')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.subplot(2, 2, 4)
    plt.plot(freqs, 20*np.log10(magnitud_overdrive + 1e-10))
    plt.title('Espectro con Overdrive')
    plt.xlabel('Frecuencia (Hz)')
    plt.ylabel('Magnitud (dB)')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_analisis_frecuencia_efectos()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 5.4: Análisis Espectral](clase-5-4-analisis-espectral.md)
- **Clase Siguiente**: [Clase 5.6: Síntesis de Audio](clase-5-6-sintesis-audio.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Zölzer, U. (2011). *DAFX: Digital Audio Effects*
- Smith, J. O. (2007). *Introduction to Digital Filters*
- Pirkle, W. (2013). *Designing Audio Effect Plugins in C++*

---
*Duración estimada: 4-5 horas*
