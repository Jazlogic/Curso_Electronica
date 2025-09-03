# Clase 5.6: Síntesis de Audio

## 🎯 Objetivo de la Clase
Generar y sintetizar audio digital usando diferentes técnicas como osciladores, modulación, síntesis aditiva, substractiva y FM, optimizadas para ultra baja latencia.

## 📚 Contenido

### 1. Introducción a la Síntesis de Audio

#### ¿Qué es la Síntesis de Audio?
La **síntesis de audio** es el proceso de generar sonidos artificiales usando algoritmos matemáticos y procesamiento digital de señales.

**Tipos de síntesis:**
- **Síntesis aditiva**: Suma de sinusoides
- **Síntesis substractiva**: Filtrado de señales complejas
- **Síntesis FM**: Modulación de frecuencia
- **Síntesis granular**: Procesamiento de granos de sonido
- **Síntesis por tabla de ondas**: Reproducción de formas de onda predefinidas

### 2. Osciladores Básicos

#### Oscilador Sinusoidal
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

class OsciladorSinusoidal:
    def __init__(self, fs, frecuencia=440.0, amplitud=1.0, fase=0.0):
        """
        Oscilador sinusoidal
        
        fs: frecuencia de muestreo
        frecuencia: frecuencia en Hz
        amplitud: amplitud (0-1)
        fase: fase inicial en radianes
        """
        self.fs = fs
        self.frecuencia = frecuencia
        self.amplitud = amplitud
        self.fase = fase
        
        # Incremento de fase por muestra
        self.incremento_fase = 2 * np.pi * frecuencia / fs
    
    def generar_muestra(self):
        """
        Genera una muestra del oscilador
        """
        muestra = self.amplitud * np.sin(self.fase)
        self.fase += self.incremento_fase
        
        # Mantener fase en [0, 2π)
        if self.fase >= 2 * np.pi:
            self.fase -= 2 * np.pi
        
        return muestra
    
    def generar_señal(self, duracion):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra()
        
        return señal
    
    def set_frecuencia(self, frecuencia):
        """
        Cambia la frecuencia del oscilador
        """
        self.frecuencia = frecuencia
        self.incremento_fase = 2 * np.pi * frecuencia / self.fs
    
    def set_amplitud(self, amplitud):
        """
        Cambia la amplitud del oscilador
        """
        self.amplitud = amplitud

# Ejemplo de uso
fs = 44100
oscilador = OsciladorSinusoidal(fs, frecuencia=440, amplitud=0.5)

# Generar señal
señal = oscilador.generar_señal(1.0)  # 1 segundo

# Visualizar
t = np.arange(len(señal)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal)
plt.title('Oscilador Sinusoidal')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

#### Oscilador de Diente de Sierra
```python
class OsciladorDienteSierra:
    def __init__(self, fs, frecuencia=440.0, amplitud=1.0):
        """
        Oscilador de diente de sierra
        """
        self.fs = fs
        self.frecuencia = frecuencia
        self.amplitud = amplitud
        
        # Incremento de fase por muestra
        self.incremento_fase = frecuencia / fs
        self.fase = 0.0
    
    def generar_muestra(self):
        """
        Genera una muestra del oscilador
        """
        # Diente de sierra: fase lineal de 0 a 1
        muestra = self.amplitud * (2 * self.fase - 1)
        
        self.fase += self.incremento_fase
        
        # Mantener fase en [0, 1)
        if self.fase >= 1.0:
            self.fase -= 1.0
        
        return muestra
    
    def generar_señal(self, duracion):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra()
        
        return señal

# Ejemplo de uso
oscilador_sierra = OsciladorDienteSierra(fs, frecuencia=440, amplitud=0.5)
señal_sierra = oscilador_sierra.generar_señal(1.0)

# Visualizar
t = np.arange(len(señal_sierra)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal_sierra)
plt.title('Oscilador de Diente de Sierra')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

#### Oscilador Cuadrado
```python
class OsciladorCuadrado:
    def __init__(self, fs, frecuencia=440.0, amplitud=1.0, duty_cycle=0.5):
        """
        Oscilador cuadrado
        
        duty_cycle: ciclo de trabajo (0-1)
        """
        self.fs = fs
        self.frecuencia = frecuencia
        self.amplitud = amplitud
        self.duty_cycle = duty_cycle
        
        # Incremento de fase por muestra
        self.incremento_fase = frecuencia / fs
        self.fase = 0.0
    
    def generar_muestra(self):
        """
        Genera una muestra del oscilador
        """
        # Onda cuadrada basada en duty cycle
        if self.fase < self.duty_cycle:
            muestra = self.amplitud
        else:
            muestra = -self.amplitud
        
        self.fase += self.incremento_fase
        
        # Mantener fase en [0, 1)
        if self.fase >= 1.0:
            self.fase -= 1.0
        
        return muestra
    
    def generar_señal(self, duracion):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra()
        
        return señal

# Ejemplo de uso
oscilador_cuadrado = OsciladorCuadrado(fs, frecuencia=440, amplitud=0.5, duty_cycle=0.3)
señal_cuadrado = oscilador_cuadrado.generar_señal(1.0)

# Visualizar
t = np.arange(len(señal_cuadrado)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal_cuadrado)
plt.title('Oscilador Cuadrado')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 3. Síntesis Aditiva

#### Síntesis de Armónicos
```python
class SintetizadorAditivo:
    def __init__(self, fs, frecuencia_fundamental=440.0):
        """
        Sintetizador aditivo que suma múltiples armónicos
        """
        self.fs = fs
        self.frecuencia_fundamental = frecuencia_fundamental
        self.armonicos = {}  # {número_armonico: amplitud}
    
    def agregar_armonico(self, numero, amplitud):
        """
        Agrega un armónico con su amplitud
        """
        self.armonicos[numero] = amplitud
    
    def generar_muestra(self):
        """
        Genera una muestra del sintetizador
        """
        muestra = 0.0
        
        for numero, amplitud in self.armonicos.items():
            frecuencia = self.frecuencia_fundamental * numero
            fase = 2 * np.pi * frecuencia * self.fase / self.fs
            muestra += amplitud * np.sin(fase)
        
        self.fase += 1
        
        return muestra
    
    def generar_señal(self, duracion):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        self.fase = 0
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra()
        
        return señal

# Ejemplo: Simular instrumento musical
sintetizador = SintetizadorAditivo(fs, frecuencia_fundamental=220)

# Agregar armónicos para simular clarinete (armónicos impares)
sintetizador.agregar_armonico(1, 1.0)   # Fundamental
sintetizador.agregar_armonico(3, 0.5)   # 3er armónico
sintetizador.agregar_armonico(5, 0.3)   # 5to armónico
sintetizador.agregar_armonico(7, 0.2)   # 7mo armónico

# Generar señal
señal_clarinete = sintetizador.generar_señal(1.0)

# Visualizar
t = np.arange(len(señal_clarinete)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal_clarinete)
plt.title('Síntesis Aditiva - Simulación de Clarinete')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 4. Síntesis FM (Modulación de Frecuencia)

#### Oscilador FM Básico
```python
class OsciladorFM:
    def __init__(self, fs, frecuencia_carrier=440.0, frecuencia_modulador=220.0, 
                 indice_modulacion=1.0, amplitud=1.0):
        """
        Oscilador con modulación de frecuencia
        
        frecuencia_carrier: frecuencia de la portadora
        frecuencia_modulador: frecuencia del modulador
        indice_modulacion: índice de modulación
        """
        self.fs = fs
        self.frecuencia_carrier = frecuencia_carrier
        self.frecuencia_modulador = frecuencia_modulador
        self.indice_modulacion = indice_modulacion
        self.amplitud = amplitud
        
        # Fases
        self.fase_carrier = 0.0
        self.fase_modulador = 0.0
        
        # Incrementos de fase
        self.incremento_carrier = 2 * np.pi * frecuencia_carrier / fs
        self.incremento_modulador = 2 * np.pi * frecuencia_modulador / fs
    
    def generar_muestra(self):
        """
        Genera una muestra del oscilador FM
        """
        # Señal moduladora
        señal_moduladora = np.sin(self.fase_modulador)
        
        # Frecuencia instantánea de la portadora
        frecuencia_instantanea = self.frecuencia_carrier + self.indice_modulacion * señal_moduladora
        
        # Generar muestra de la portadora
        muestra = self.amplitud * np.sin(self.fase_carrier)
        
        # Actualizar fases
        self.fase_carrier += 2 * np.pi * frecuencia_instantanea / self.fs
        self.fase_modulador += self.incremento_modulador
        
        # Mantener fases en [0, 2π)
        if self.fase_carrier >= 2 * np.pi:
            self.fase_carrier -= 2 * np.pi
        if self.fase_modulador >= 2 * np.pi:
            self.fase_modulador -= 2 * np.pi
        
        return muestra
    
    def generar_señal(self, duracion):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra()
        
        return señal

# Ejemplo de uso
oscilador_fm = OsciladorFM(fs, frecuencia_carrier=440, frecuencia_modulador=220, 
                          indice_modulacion=2.0, amplitud=0.5)

# Generar señal
señal_fm = oscilador_fm.generar_señal(1.0)

# Visualizar
t = np.arange(len(señal_fm)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal_fm)
plt.title('Síntesis FM')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 5. Síntesis Substractiva

#### Sintetizador Substractivo
```python
class SintetizadorSubstractivo:
    def __init__(self, fs, frecuencia=440.0, amplitud=1.0):
        """
        Sintetizador substractivo que filtra una señal compleja
        """
        self.fs = fs
        self.frecuencia = frecuencia
        self.amplitud = amplitud
        
        # Oscilador de diente de sierra como fuente
        self.oscilador = OsciladorDienteSierra(fs, frecuencia, amplitud)
        
        # Filtros
        self.filtro_pasa_bajas = signal.butter(2, 2*1000/fs, btype='low')
        self.filtro_pasa_altas = signal.butter(2, 2*100/fs, btype='high')
        
        # Estado de los filtros
        self.estado_lowpass = signal.lfilter_zi(self.filtro_pasa_bajas[0], self.filtro_pasa_bajas[1])
        self.estado_highpass = signal.lfilter_zi(self.filtro_pasa_altas[0], self.filtro_pasa_altas[1])
    
    def generar_muestra(self, cutoff_low=1000, cutoff_high=100, resonancia=0.5):
        """
        Genera una muestra del sintetizador substractivo
        """
        # Generar muestra del oscilador
        muestra = self.oscilador.generar_muestra()
        
        # Aplicar filtro pasa-bajas
        muestra_filtrada, self.estado_lowpass = signal.lfilter(
            self.filtro_pasa_bajas[0], self.filtro_pasa_bajas[1], 
            [muestra], zi=self.estado_lowpass)
        
        # Aplicar filtro pasa-altas
        muestra_final, self.estado_highpass = signal.lfilter(
            self.filtro_pasa_altas[0], self.filtro_pasa_altas[1], 
            muestra_filtrada, zi=self.estado_highpass)
        
        return muestra_final[0]
    
    def generar_señal(self, duracion, cutoff_low=1000, cutoff_high=100):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra(cutoff_low, cutoff_high)
        
        return señal

# Ejemplo de uso
sintetizador_substractivo = SintetizadorSubstractivo(fs, frecuencia=440, amplitud=0.5)

# Generar señal
señal_substractiva = sintetizador_substractivo.generar_señal(1.0, cutoff_low=800, cutoff_high=150)

# Visualizar
t = np.arange(len(señal_substractiva)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal_substractiva)
plt.title('Síntesis Substractiva')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 6. Síntesis por Tabla de Ondas

#### Tabla de Ondas
```python
class TablaOndas:
    def __init__(self, fs, tamaño_tabla=1024):
        """
        Tabla de ondas para síntesis
        """
        self.fs = fs
        self.tamaño_tabla = tamaño_tabla
        
        # Crear tablas de diferentes formas de onda
        self.tablas = {
            'sinusoidal': self._crear_tabla_sinusoidal(),
            'cuadrada': self._crear_tabla_cuadrada(),
            'sierra': self._crear_tabla_sierra(),
            'triangular': self._crear_tabla_triangular()
        }
        
        # Tabla activa
        self.tabla_activa = self.tablas['sinusoidal']
    
    def _crear_tabla_sinusoidal(self):
        """
        Crea tabla de onda sinusoidal
        """
        indices = np.arange(self.tamaño_tabla)
        return np.sin(2 * np.pi * indices / self.tamaño_tabla)
    
    def _crear_tabla_cuadrada(self):
        """
        Crea tabla de onda cuadrada
        """
        tabla = np.ones(self.tamaño_tabla)
        tabla[self.tamaño_tabla//2:] = -1
        return tabla
    
    def _crear_tabla_sierra(self):
        """
        Crea tabla de onda de diente de sierra
        """
        indices = np.arange(self.tamaño_tabla)
        return 2 * indices / self.tamaño_tabla - 1
    
    def _crear_tabla_triangular(self):
        """
        Crea tabla de onda triangular
        """
        tabla = np.zeros(self.tamaño_tabla)
        mitad = self.tamaño_tabla // 2
        
        # Primera mitad: subida
        tabla[:mitad] = 2 * np.arange(mitad) / mitad - 1
        
        # Segunda mitad: bajada
        tabla[mitad:] = 1 - 2 * np.arange(mitad) / mitad
        
        return tabla
    
    def set_tabla(self, nombre_tabla):
        """
        Cambia la tabla activa
        """
        if nombre_tabla in self.tablas:
            self.tabla_activa = self.tablas[nombre_tabla]
    
    def interpolar(self, indice):
        """
        Interpola entre muestras de la tabla
        """
        # Índice entero y fraccionario
        indice_entero = int(indice) % self.tamaño_tabla
        indice_fraccionario = indice - int(indice)
        
        # Interpolación lineal
        muestra1 = self.tabla_activa[indice_entero]
        muestra2 = self.tabla_activa[(indice_entero + 1) % self.tamaño_tabla]
        
        return muestra1 + indice_fraccionario * (muestra2 - muestra1)

class OsciladorTablaOndas:
    def __init__(self, fs, frecuencia=440.0, amplitud=1.0, tabla_ondas='sinusoidal'):
        """
        Oscilador basado en tabla de ondas
        """
        self.fs = fs
        self.frecuencia = frecuencia
        self.amplitud = amplitud
        
        # Tabla de ondas
        self.tabla = TablaOndas(fs)
        self.tabla.set_tabla(tabla_ondas)
        
        # Índice en la tabla
        self.indice_tabla = 0.0
        
        # Incremento de índice por muestra
        self.incremento_indice = self.tabla.tamaño_tabla * frecuencia / fs
    
    def generar_muestra(self):
        """
        Genera una muestra del oscilador
        """
        # Interpolar en la tabla
        muestra = self.tabla.interpolar(self.indice_tabla)
        
        # Actualizar índice
        self.indice_tabla += self.incremento_indice
        
        # Mantener índice en [0, tamaño_tabla)
        if self.indice_tabla >= self.tabla.tamaño_tabla:
            self.indice_tabla -= self.tabla.tamaño_tabla
        
        return self.amplitud * muestra
    
    def generar_señal(self, duracion):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra()
        
        return señal
    
    def set_tabla(self, nombre_tabla):
        """
        Cambia la tabla de ondas
        """
        self.tabla.set_tabla(nombre_tabla)

# Ejemplo de uso
oscilador_tabla = OsciladorTablaOndas(fs, frecuencia=440, amplitud=0.5, tabla_ondas='triangular')

# Generar señal
señal_tabla = oscilador_tabla.generar_señal(1.0)

# Visualizar
t = np.arange(len(señal_tabla)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal_tabla)
plt.title('Oscilador de Tabla de Ondas - Triangular')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 7. Síntesis Granular

#### Sintetizador Granular
```python
class SintetizadorGranular:
    def __init__(self, fs, tamaño_grano=0.1, solapamiento=0.5):
        """
        Sintetizador granular
        
        tamaño_grano: tamaño del grano en segundos
        solapamiento: solapamiento entre granos (0-1)
        """
        self.fs = fs
        self.tamaño_grano = tamaño_grano
        self.solapamiento = solapamiento
        
        # Tamaño del grano en muestras
        self.tamaño_grano_muestras = int(tamaño_grano * fs)
        
        # Paso entre granos
        self.paso_grano = int(self.tamaño_grano_muestras * (1 - solapamiento))
        
        # Buffer de granos
        self.buffer_granos = np.zeros(self.tamaño_grano_muestras, dtype=np.float32)
        
        # Índices
        self.indice_escritura = 0
        self.indice_lectura = 0
        
        # Ventana para el grano
        self.ventana_grano = np.hanning(self.tamaño_grano_muestras)
    
    def agregar_grano(self, señal):
        """
        Agrega un grano al buffer
        """
        # Aplicar ventana al grano
        grano_ventaneado = señal * self.ventana_grano
        
        # Agregar al buffer
        self.buffer_granos = grano_ventaneado
    
    def generar_muestra(self):
        """
        Genera una muestra del sintetizador granular
        """
        if self.indice_lectura < self.tamaño_grano_muestras:
            muestra = self.buffer_granos[self.indice_lectura]
            self.indice_lectura += 1
        else:
            muestra = 0.0
        
        return muestra
    
    def generar_señal(self, duracion):
        """
        Genera una señal de duración especificada
        """
        num_muestras = int(duracion * self.fs)
        señal = np.zeros(num_muestras, dtype=np.float32)
        
        for i in range(num_muestras):
            señal[i] = self.generar_muestra()
        
        return señal

# Ejemplo de uso
sintetizador_granular = SintetizadorGranular(fs, tamaño_grano=0.05, solapamiento=0.7)

# Crear grano de prueba
grano = np.sin(2*np.pi*440*np.arange(sintetizador_granular.tamaño_grano_muestras)/fs)

# Agregar grano
sintetizador_granular.agregar_grano(grano)

# Generar señal
señal_granular = sintetizador_granular.generar_señal(1.0)

# Visualizar
t = np.arange(len(señal_granular)) / fs
plt.figure(figsize=(12, 6))
plt.plot(t, señal_granular)
plt.title('Síntesis Granular')
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 8. Aplicaciones en Audio

#### Sintetizador Completo
```python
class SintetizadorCompleto:
    def __init__(self, fs):
        """
        Sintetizador completo con múltiples técnicas
        """
        self.fs = fs
        
        # Osciladores
        self.oscilador1 = OsciladorSinusoidal(fs)
        self.oscilador2 = OsciladorTablaOndas(fs)
        self.oscilador_fm = OsciladorFM(fs)
        
        # Sintetizadores
        self.sintetizador_aditivo = SintetizadorAditivo(fs)
        self.sintetizador_substractivo = SintetizadorSubstractivo(fs)
        
        # Parámetros
        self.volumen = 0.5
        self.mezcla = 0.5  # Mezcla entre osciladores
    
    def generar_nota(self, frecuencia, duracion, tipo_sintesis='sinusoidal'):
        """
        Genera una nota musical
        """
        if tipo_sintesis == 'sinusoidal':
            self.oscilador1.set_frecuencia(frecuencia)
            señal = self.oscilador1.generar_señal(duracion)
        
        elif tipo_sintesis == 'tabla_ondas':
            self.oscilador2.frecuencia = frecuencia
            señal = self.oscilador2.generar_señal(duracion)
        
        elif tipo_sintesis == 'fm':
            self.oscilador_fm.frecuencia_carrier = frecuencia
            señal = self.oscilador_fm.generar_señal(duracion)
        
        elif tipo_sintesis == 'aditiva':
            self.sintetizador_aditivo.frecuencia_fundamental = frecuencia
            señal = self.sintetizador_aditivo.generar_señal(duracion)
        
        elif tipo_sintesis == 'substractiva':
            self.sintetizador_substractivo.frecuencia = frecuencia
            señal = self.sintetizador_substractivo.generar_señal(duracion)
        
        # Aplicar volumen
        señal *= self.volumen
        
        return señal
    
    def generar_acorde(self, frecuencias, duracion, tipo_sintesis='sinusoidal'):
        """
        Genera un acorde (múltiples notas simultáneas)
        """
        acorde = np.zeros(int(duracion * self.fs), dtype=np.float32)
        
        for frecuencia in frecuencias:
            nota = self.generar_nota(frecuencia, duracion, tipo_sintesis)
            acorde += nota
        
        # Normalizar
        acorde /= len(frecuencias)
        
        return acorde

# Ejemplo de uso
sintetizador = SintetizadorCompleto(fs)

# Generar diferentes tipos de síntesis
frecuencia = 440  # La4
duracion = 1.0

tipos_sintesis = ['sinusoidal', 'tabla_ondas', 'fm', 'aditiva', 'substractiva']
señales = {}

for tipo in tipos_sintesis:
    señales[tipo] = sintetizador.generar_nota(frecuencia, duracion, tipo)

# Visualizar
plt.figure(figsize=(15, 12))

for i, (tipo, señal) in enumerate(señales.items()):
    plt.subplot(3, 2, i+1)
    t = np.arange(len(señal)) / fs
    plt.plot(t, señal)
    plt.title(f'Síntesis {tipo.capitalize()}')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)

plt.tight_layout()
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Simulador de Instrumentos Musicales
```python
def ejercicio_simulador_instrumentos():
    """
    Simula diferentes instrumentos musicales usando síntesis
    """
    fs = 44100
    sintetizador = SintetizadorCompleto(fs)
    
    # Configurar sintetizador aditivo para diferentes instrumentos
    def configurar_clarinete(sintetizador, frecuencia):
        sintetizador.sintetizador_aditivo.frecuencia_fundamental = frecuencia
        sintetizador.sintetizador_aditivo.armonicos = {
            1: 1.0,   # Fundamental
            3: 0.5,   # 3er armónico
            5: 0.3,   # 5to armónico
            7: 0.2    # 7mo armónico
        }
    
    def configurar_violin(sintetizador, frecuencia):
        sintetizador.sintetizador_aditivo.frecuencia_fundamental = frecuencia
        sintetizador.sintetizador_aditivo.armonicos = {
            1: 1.0,   # Fundamental
            2: 0.7,   # 2do armónico
            3: 0.5,   # 3er armónico
            4: 0.3,   # 4to armónico
            5: 0.2    # 5to armónico
        }
    
    # Generar notas
    frecuencia = 440  # La4
    duracion = 2.0
    
    # Clarinete
    configurar_clarinete(sintetizador, frecuencia)
    señal_clarinete = sintetizador.sintetizador_aditivo.generar_señal(duracion)
    
    # Violín
    configurar_violin(sintetizador, frecuencia)
    señal_violin = sintetizador.sintetizador_aditivo.generar_señal(duracion)
    
    # Visualizar
    plt.figure(figsize=(12, 8))
    
    t = np.arange(len(señal_clarinete)) / fs
    
    plt.subplot(2, 1, 1)
    plt.plot(t, señal_clarinete)
    plt.title('Simulación de Clarinete')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    plt.subplot(2, 1, 2)
    plt.plot(t, señal_violin)
    plt.title('Simulación de Violín')
    plt.xlabel('Tiempo (s)')
    plt.ylabel('Amplitud')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()

ejercicio_simulador_instrumentos()
```

### Ejercicio 2: Análisis Espectral de Síntesis
```python
def ejercicio_analisis_espectral_sintesis():
    """
    Analiza el espectro de diferentes técnicas de síntesis
    """
    fs = 44100
    sintetizador = SintetizadorCompleto(fs)
    
    # Generar señales
    frecuencia = 440
    duracion = 1.0
    
    señales = {
        'Sinusoidal': sintetizador.generar_nota(frecuencia, duracion, 'sinusoidal'),
        'FM': sintetizador.generar_nota(frecuencia, duracion, 'fm'),
        'Aditiva': sintetizador.generar_nota(frecuencia, duracion, 'aditiva'),
        'Substractiva': sintetizador.generar_nota(frecuencia, duracion, 'substractiva')
    }
    
    # Análisis espectral
    from scipy.fft import fft, fftfreq
    
    plt.figure(figsize=(15, 10))
    
    for i, (nombre, señal) in enumerate(señales.items()):
        # FFT
        X = fft(señal)
        freqs = fftfreq(len(señal), 1/fs)[:len(señal)//2]
        magnitud = np.abs(X[:len(X)//2])
        
        plt.subplot(2, 2, i+1)
        plt.plot(freqs, 20*np.log10(magnitud + 1e-10))
        plt.title(f'Espectro - {nombre}')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud (dB)')
        plt.grid(True)
        plt.xlim([0, 5000])
        plt.ylim([-100, 20])
    
    plt.tight_layout()
    plt.show()

ejercicio_analisis_espectral_sintesis()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 5.5: Efectos de Audio](clase-5-5-efectos-audio.md)
- **Clase Siguiente**: [Clase 5.7: Procesamiento en Tiempo Real](clase-5-7-procesamiento-tiempo-real.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Smith, J. O. (2007). *Introduction to Digital Filters*
- Roads, C. (1996). *The Computer Music Tutorial*
- Chowning, J. (1973). "The Synthesis of Complex Audio Spectra by Means of Frequency Modulation"

---
*Duración estimada: 4-5 horas*
