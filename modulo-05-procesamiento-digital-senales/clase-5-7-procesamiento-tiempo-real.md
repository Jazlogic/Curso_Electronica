# Clase 5.7: Procesamiento en Tiempo Real

## 🎯 Objetivo de la Clase
Optimizar algoritmos para procesamiento en tiempo real, incluyendo buffers circulares, threading, optimización de CPU y minimización de latencia para aplicaciones de ultra baja latencia.

## 📚 Contenido

### 1. Introducción al Procesamiento en Tiempo Real

#### ¿Qué es el Procesamiento en Tiempo Real?
El **procesamiento en tiempo real** es la capacidad de procesar señales de audio con latencia mínima, típicamente menos de 10ms, para aplicaciones interactivas como instrumentos musicales o sistemas de audio en vivo.

**Características del procesamiento en tiempo real:**
- **Latencia baja**: Respuesta inmediata
- **Determinismo**: Tiempo de procesamiento predecible
- **Estabilidad**: Sin interrupciones o glitches
- **Eficiencia**: Uso óptimo de recursos

#### Métricas de Rendimiento
```python
import time
import threading
import queue
import numpy as np
import matplotlib.pyplot as plt

class MetricasRendimiento:
    def __init__(self):
        """
        Clase para medir métricas de rendimiento en tiempo real
        """
        self.tiempos_procesamiento = []
        self.latencia_total = []
        self.uso_cpu = []
        
    def medir_tiempo_procesamiento(self, funcion, *args, **kwargs):
        """
        Mide el tiempo de procesamiento de una función
        """
        inicio = time.perf_counter()
        resultado = funcion(*args, **kwargs)
        fin = time.perf_counter()
        
        tiempo_procesamiento = (fin - inicio) * 1000  # en ms
        self.tiempos_procesamiento.append(tiempo_procesamiento)
        
        return resultado, tiempo_procesamiento
    
    def calcular_estadisticas(self):
        """
        Calcula estadísticas de rendimiento
        """
        if not self.tiempos_procesamiento:
            return None
        
        tiempos = np.array(self.tiempos_procesamiento)
        
        estadisticas = {
            'promedio': np.mean(tiempos),
            'maximo': np.max(tiempos),
            'minimo': np.min(tiempos),
            'desviacion': np.std(tiempos),
            'percentil_95': np.percentile(tiempos, 95),
            'percentil_99': np.percentile(tiempos, 99)
        }
        
        return estadisticas
    
    def visualizar_rendimiento(self):
        """
        Visualiza las métricas de rendimiento
        """
        if not self.tiempos_procesamiento:
            print("No hay datos para visualizar")
            return
        
        plt.figure(figsize=(12, 8))
        
        # Tiempo de procesamiento vs tiempo
        plt.subplot(2, 2, 1)
        plt.plot(self.tiempos_procesamiento)
        plt.title('Tiempo de Procesamiento vs Tiempo')
        plt.xlabel('Muestra')
        plt.ylabel('Tiempo (ms)')
        plt.grid(True)
        
        # Histograma de tiempos
        plt.subplot(2, 2, 2)
        plt.hist(self.tiempos_procesamiento, bins=50, alpha=0.7)
        plt.title('Distribución de Tiempos de Procesamiento')
        plt.xlabel('Tiempo (ms)')
        plt.ylabel('Frecuencia')
        plt.grid(True)
        
        # Estadísticas
        estadisticas = self.calcular_estadisticas()
        plt.subplot(2, 2, 3)
        metricas = ['Promedio', 'Máximo', 'P95', 'P99']
        valores = [estadisticas['promedio'], estadisticas['maximo'], 
                  estadisticas['percentil_95'], estadisticas['percentil_99']]
        plt.bar(metricas, valores)
        plt.title('Estadísticas de Rendimiento')
        plt.ylabel('Tiempo (ms)')
        plt.grid(True, axis='y')
        
        # Tiempo acumulado
        plt.subplot(2, 2, 4)
        tiempo_acumulado = np.cumsum(self.tiempos_procesamiento)
        plt.plot(tiempo_acumulado)
        plt.title('Tiempo de Procesamiento Acumulado')
        plt.xlabel('Muestra')
        plt.ylabel('Tiempo Acumulado (ms)')
        plt.grid(True)
        
        plt.tight_layout()
        plt.show()
        
        # Imprimir estadísticas
        print("Estadísticas de Rendimiento:")
        for metrica, valor in estadisticas.items():
            print(f"  {metrica}: {valor:.3f} ms")

# Ejemplo de uso
metricas = MetricasRendimiento()

# Función de prueba
def procesar_audio(muestra):
    """
    Función de procesamiento de audio de prueba
    """
    # Simular procesamiento
    resultado = muestra * 0.5 + np.sin(muestra * 10) * 0.1
    time.sleep(0.001)  # Simular 1ms de procesamiento
    return resultado

# Medir rendimiento
for i in range(100):
    muestra = np.random.randn()
    resultado, tiempo = metricas.medir_tiempo_procesamiento(procesar_audio, muestra)

# Visualizar resultados
metricas.visualizar_rendimiento()
```

### 2. Buffers Circulares

#### Implementación de Buffer Circular
```python
class BufferCircular:
    def __init__(self, tamaño, dtype=np.float32):
        """
        Buffer circular optimizado para audio
        
        tamaño: tamaño del buffer en muestras
        dtype: tipo de datos
        """
        self.tamaño = tamaño
        self.buffer = np.zeros(tamaño, dtype=dtype)
        self.indice_escritura = 0
        self.indice_lectura = 0
        self.muestras_disponibles = 0
        
        # Lock para thread safety
        self.lock = threading.Lock()
    
    def escribir(self, datos):
        """
        Escribe datos en el buffer
        """
        with self.lock:
            num_datos = len(datos)
            espacio_disponible = self.tamaño - self.muestras_disponibles
            
            if num_datos > espacio_disponible:
                # Buffer lleno, sobrescribir datos más antiguos
                num_datos = espacio_disponible
            
            # Escribir datos
            for i in range(num_datos):
                self.buffer[self.indice_escritura] = datos[i]
                self.indice_escritura = (self.indice_escritura + 1) % self.tamaño
            
            self.muestras_disponibles = min(self.muestras_disponibles + num_datos, self.tamaño)
    
    def leer(self, num_muestras):
        """
        Lee datos del buffer
        """
        with self.lock:
            num_muestras = min(num_muestras, self.muestras_disponibles)
            
            if num_muestras == 0:
                return np.array([], dtype=self.buffer.dtype)
            
            datos = np.zeros(num_muestras, dtype=self.buffer.dtype)
            
            for i in range(num_muestras):
                datos[i] = self.buffer[self.indice_lectura]
                self.indice_lectura = (self.indice_lectura + 1) % self.tamaño
            
            self.muestras_disponibles -= num_muestras
            
            return datos
    
    def leer_muestra(self):
        """
        Lee una muestra del buffer
        """
        with self.lock:
            if self.muestras_disponibles == 0:
                return 0.0
            
            muestra = self.buffer[self.indice_lectura]
            self.indice_lectura = (self.indice_lectura + 1) % self.tamaño
            self.muestras_disponibles -= 1
            
            return muestra
    
    def escribir_muestra(self, muestra):
        """
        Escribe una muestra en el buffer
        """
        with self.lock:
            self.buffer[self.indice_escritura] = muestra
            self.indice_escritura = (self.indice_escritura + 1) % self.tamaño
            
            if self.muestras_disponibles < self.tamaño:
                self.muestras_disponibles += 1
    
    def obtener_estado(self):
        """
        Obtiene el estado del buffer
        """
        with self.lock:
            return {
                'muestras_disponibles': self.muestras_disponibles,
                'espacio_disponible': self.tamaño - self.muestras_disponibles,
                'indice_escritura': self.indice_escritura,
                'indice_lectura': self.indice_lectura
            }

# Ejemplo de uso
buffer = BufferCircular(1024)

# Escribir datos
datos_entrada = np.random.randn(100)
buffer.escribir(datos_entrada)

# Leer datos
datos_salida = buffer.leer(50)

print(f"Datos escritos: {len(datos_entrada)}")
print(f"Datos leídos: {len(datos_salida)}")
print(f"Estado del buffer: {buffer.obtener_estado()}")
```

#### Buffer Circular con Interpolación
```python
class BufferCircularInterpolado:
    def __init__(self, tamaño, dtype=np.float32):
        """
        Buffer circular con interpolación para lectura suave
        """
        self.tamaño = tamaño
        self.buffer = np.zeros(tamaño, dtype=dtype)
        self.indice_escritura = 0
        self.indice_lectura = 0.0  # Permite valores fraccionarios
        self.muestras_disponibles = 0
        
        self.lock = threading.Lock()
    
    def escribir_muestra(self, muestra):
        """
        Escribe una muestra en el buffer
        """
        with self.lock:
            self.buffer[self.indice_escritura] = muestra
            self.indice_escritura = (self.indice_escritura + 1) % self.tamaño
            
            if self.muestras_disponibles < self.tamaño:
                self.muestras_disponibles += 1
    
    def leer_muestra_interpolada(self, incremento=1.0):
        """
        Lee una muestra con interpolación
        """
        with self.lock:
            if self.muestras_disponibles == 0:
                return 0.0
            
            # Índices para interpolación
            indice_entero = int(self.indice_lectura) % self.tamaño
            indice_fraccionario = self.indice_lectura - int(self.indice_lectura)
            
            # Interpolación lineal
            muestra1 = self.buffer[indice_entero]
            muestra2 = self.buffer[(indice_entero + 1) % self.tamaño]
            
            muestra_interpolada = muestra1 + indice_fraccionario * (muestra2 - muestra1)
            
            # Actualizar índice de lectura
            self.indice_lectura += incremento
            
            # Mantener índice en [0, tamaño)
            if self.indice_lectura >= self.tamaño:
                self.indice_lectura -= self.tamaño
            
            return muestra_interpolada
    
    def set_frecuencia_lectura(self, frecuencia_lectura, frecuencia_escritura):
        """
        Establece la frecuencia de lectura relativa a la escritura
        """
        self.incremento_lectura = frecuencia_lectura / frecuencia_escritura
    
    def leer_muestra(self):
        """
        Lee una muestra con la frecuencia establecida
        """
        return self.leer_muestra_interpolada(self.incremento_lectura)

# Ejemplo de uso
buffer_interpolado = BufferCircularInterpolado(1024)

# Configurar frecuencias
frecuencia_escritura = 44100
frecuencia_lectura = 48000  # Cambio de frecuencia de muestreo
buffer_interpolado.set_frecuencia_lectura(frecuencia_lectura, frecuencia_escritura)

# Escribir datos
for i in range(1000):
    muestra = np.sin(2*np.pi*440*i/frecuencia_escritura)
    buffer_interpolado.escribir_muestra(muestra)

# Leer datos con interpolación
datos_leidos = []
for i in range(1000):
    muestra = buffer_interpolado.leer_muestra()
    datos_leidos.append(muestra)

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(datos_leidos[:100])
plt.title('Datos Leídos con Interpolación')
plt.xlabel('Muestra')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

### 3. Threading para Audio

#### Procesador de Audio Multi-Thread
```python
class ProcesadorAudioMultiThread:
    def __init__(self, fs, tamaño_buffer=1024, num_threads=4):
        """
        Procesador de audio multi-thread
        
        fs: frecuencia de muestreo
        tamaño_buffer: tamaño del buffer de procesamiento
        num_threads: número de threads de procesamiento
        """
        self.fs = fs
        self.tamaño_buffer = tamaño_buffer
        self.num_threads = num_threads
        
        # Buffers
        self.buffer_entrada = BufferCircular(tamaño_buffer * 2)
        self.buffer_salida = BufferCircular(tamaño_buffer * 2)
        
        # Colas para comunicación entre threads
        self.cola_procesamiento = queue.Queue()
        self.cola_resultados = queue.Queue()
        
        # Control de threads
        self.threads_activos = []
        self.procesando = False
        
        # Métricas
        self.metricas = MetricasRendimiento()
    
    def iniciar_threads(self):
        """
        Inicia los threads de procesamiento
        """
        self.procesando = True
        
        for i in range(self.num_threads):
            thread = threading.Thread(target=self._thread_procesamiento, args=(i,))
            thread.daemon = True
            thread.start()
            self.threads_activos.append(thread)
    
    def detener_threads(self):
        """
        Detiene los threads de procesamiento
        """
        self.procesando = False
        
        # Esperar a que terminen los threads
        for thread in self.threads_activos:
            thread.join()
        
        self.threads_activos.clear()
    
    def _thread_procesamiento(self, thread_id):
        """
        Thread de procesamiento
        """
        while self.procesando:
            try:
                # Obtener datos para procesar
                datos = self.cola_procesamiento.get(timeout=0.1)
                
                # Procesar datos
                inicio = time.perf_counter()
                datos_procesados = self._procesar_datos(datos)
                fin = time.perf_counter()
                
                # Registrar métricas
                tiempo_procesamiento = (fin - inicio) * 1000
                self.metricas.tiempos_procesamiento.append(tiempo_procesamiento)
                
                # Enviar resultado
                self.cola_resultados.put(datos_procesados)
                
                # Marcar tarea como completada
                self.cola_procesamiento.task_done()
                
            except queue.Empty:
                continue
            except Exception as e:
                print(f"Error en thread {thread_id}: {e}")
    
    def _procesar_datos(self, datos):
        """
        Procesa los datos de audio
        """
        # Simular procesamiento complejo
        resultado = np.zeros_like(datos)
        
        for i in range(len(datos)):
            # Aplicar filtro simple
            resultado[i] = datos[i] * 0.5 + np.sin(datos[i] * 10) * 0.1
            
            # Simular procesamiento adicional
            time.sleep(0.0001)  # 0.1ms por muestra
        
        return resultado
    
    def procesar_audio(self, datos_entrada):
        """
        Procesa audio usando múltiples threads
        """
        # Escribir datos de entrada
        self.buffer_entrada.escribir(datos_entrada)
        
        # Procesar en chunks
        chunk_size = self.tamaño_buffer
        datos_salida = []
        
        while self.buffer_entrada.muestras_disponibles >= chunk_size:
            # Leer chunk
            chunk = self.buffer_entrada.leer(chunk_size)
            
            # Enviar a procesamiento
            self.cola_procesamiento.put(chunk)
            
            # Obtener resultado
            try:
                resultado = self.cola_resultados.get(timeout=1.0)
                datos_salida.extend(resultado)
            except queue.Empty:
                print("Timeout en procesamiento")
                break
        
        return np.array(datos_salida, dtype=np.float32)
    
    def obtener_estadisticas(self):
        """
        Obtiene estadísticas de rendimiento
        """
        return self.metricas.calcular_estadisticas()

# Ejemplo de uso
procesador = ProcesadorAudioMultiThread(44100, tamaño_buffer=512, num_threads=4)

# Iniciar threads
procesador.iniciar_threads()

# Procesar audio
datos_entrada = np.random.randn(2048)
datos_salida = procesador.procesar_audio(datos_entrada)

# Obtener estadísticas
estadisticas = procesador.obtener_estadisticas()
print("Estadísticas de Procesamiento Multi-Thread:")
for metrica, valor in estadisticas.items():
    print(f"  {metrica}: {valor:.3f} ms")

# Detener threads
procesador.detener_threads()
```

### 4. Optimización de CPU

#### Optimización de Algoritmos
```python
class OptimizadorCPU:
    def __init__(self):
        """
        Optimizador de CPU para algoritmos de audio
        """
        self.cache = {}
        self.prefetch_size = 64
    
    def optimizar_filtro_fir(self, coeficientes, señal):
        """
        Versión optimizada del filtro FIR
        """
        N = len(coeficientes)
        M = len(señal)
        
        # Usar vectorización de NumPy
        resultado = np.zeros(M, dtype=np.float32)
        
        # Procesar en bloques para mejor uso de cache
        bloque_size = min(self.prefetch_size, M)
        
        for i in range(0, M, bloque_size):
            fin_bloque = min(i + bloque_size, M)
            
            # Procesar bloque
            for j in range(i, fin_bloque):
                suma = 0.0
                for k in range(N):
                    if j - k >= 0:
                        suma += coeficientes[k] * señal[j - k]
                resultado[j] = suma
        
        return resultado
    
    def optimizar_fft(self, señal):
        """
        Versión optimizada de FFT
        """
        # Usar FFT de NumPy que está optimizada
        return np.fft.fft(señal)
    
    def optimizar_convolucion(self, señal1, señal2):
        """
        Versión optimizada de convolución
        """
        # Usar convolución de NumPy que está optimizada
        return np.convolve(señal1, señal2, mode='same')
    
    def optimizar_interpolacion(self, señal, factor):
        """
        Optimización de interpolación
        """
        # Usar interpolación de SciPy
        from scipy import interpolate
        
        x_original = np.arange(len(señal))
        x_nuevo = np.linspace(0, len(señal)-1, int(len(señal) * factor))
        
        f = interpolate.interp1d(x_original, señal, kind='linear')
        return f(x_nuevo)

# Ejemplo de uso
optimizador = OptimizadorCPU()

# Comparar rendimiento
señal = np.random.randn(1024)
coeficientes = np.random.randn(64)

# Versión no optimizada
inicio = time.perf_counter()
resultado_no_opt = np.convolve(señal, coeficientes, mode='same')
fin = time.perf_counter()
tiempo_no_opt = (fin - inicio) * 1000

# Versión optimizada
inicio = time.perf_counter()
resultado_opt = optimizador.optimizar_filtro_fir(coeficientes, señal)
fin = time.perf_counter()
tiempo_opt = (fin - inicio) * 1000

print(f"Tiempo no optimizado: {tiempo_no_opt:.3f} ms")
print(f"Tiempo optimizado: {tiempo_opt:.3f} ms")
print(f"Mejora: {tiempo_no_opt/tiempo_opt:.2f}x")
```

### 5. Minimización de Latencia

#### Sistema de Ultra Baja Latencia
```python
class SistemaUltraBajaLatencia:
    def __init__(self, fs, tamaño_buffer=64):
        """
        Sistema optimizado para ultra baja latencia
        
        fs: frecuencia de muestreo
        tamaño_buffer: tamaño del buffer (menor = menor latencia)
        """
        self.fs = fs
        self.tamaño_buffer = tamaño_buffer
        self.latencia_muestras = tamaño_buffer
        self.latencia_ms = (tamaño_buffer / fs) * 1000
        
        # Buffers optimizados
        self.buffer_entrada = BufferCircular(tamaño_buffer * 2)
        self.buffer_salida = BufferCircular(tamaño_buffer * 2)
        
        # Procesador optimizado
        self.procesador = OptimizadorCPU()
        
        # Métricas de latencia
        self.latencia_medida = []
        self.tiempo_procesamiento = []
    
    def procesar_muestra(self, muestra_entrada):
        """
        Procesa una muestra con latencia mínima
        """
        inicio = time.perf_counter()
        
        # Escribir muestra de entrada
        self.buffer_entrada.escribir_muestra(muestra_entrada)
        
        # Procesar si hay suficientes muestras
        if self.buffer_entrada.muestras_disponibles >= self.tamaño_buffer:
            # Leer bloque
            bloque = self.buffer_entrada.leer(self.tamaño_buffer)
            
            # Procesar bloque
            bloque_procesado = self._procesar_bloque(bloque)
            
            # Escribir resultado
            self.buffer_salida.escribir(bloque_procesado)
        
        # Leer muestra de salida
        muestra_salida = self.buffer_salida.leer_muestra()
        
        fin = time.perf_counter()
        tiempo_procesamiento = (fin - inicio) * 1000
        self.tiempo_procesamiento.append(tiempo_procesamiento)
        
        return muestra_salida
    
    def _procesar_bloque(self, bloque):
        """
        Procesa un bloque de audio
        """
        # Aplicar filtro simple optimizado
        coeficientes = np.array([0.1, 0.2, 0.4, 0.2, 0.1], dtype=np.float32)
        return self.procesador.optimizar_filtro_fir(coeficientes, bloque)
    
    def medir_latencia(self, duracion=1.0):
        """
        Mide la latencia del sistema
        """
        num_muestras = int(duracion * self.fs)
        latencia_medida = []
        
        for i in range(num_muestras):
            # Generar señal de prueba
            señal_prueba = np.sin(2*np.pi*440*i/self.fs)
            
            # Procesar
            inicio = time.perf_counter()
            señal_procesada = self.procesar_muestra(señal_prueba)
            fin = time.perf_counter()
            
            # Medir latencia
            latencia = (fin - inicio) * 1000
            latencia_medida.append(latencia)
        
        self.latencia_medida = latencia_medida
        
        # Calcular estadísticas
        latencia_promedio = np.mean(latencia_medida)
        latencia_maxima = np.max(latencia_medida)
        latencia_p95 = np.percentile(latencia_medida, 95)
        
        return {
            'promedio': latencia_promedio,
            'maxima': latencia_maxima,
            'p95': latencia_p95,
            'teorica': self.latencia_ms
        }
    
    def visualizar_latencia(self):
        """
        Visualiza las métricas de latencia
        """
        if not self.latencia_medida:
            print("No hay datos de latencia para visualizar")
            return
        
        plt.figure(figsize=(12, 8))
        
        # Latencia vs tiempo
        plt.subplot(2, 2, 1)
        plt.plot(self.latencia_medida)
        plt.title('Latencia vs Tiempo')
        plt.xlabel('Muestra')
        plt.ylabel('Latencia (ms)')
        plt.grid(True)
        
        # Histograma de latencia
        plt.subplot(2, 2, 2)
        plt.hist(self.latencia_medida, bins=50, alpha=0.7)
        plt.title('Distribución de Latencia')
        plt.xlabel('Latencia (ms)')
        plt.ylabel('Frecuencia')
        plt.grid(True)
        
        # Tiempo de procesamiento
        plt.subplot(2, 2, 3)
        plt.plot(self.tiempo_procesamiento)
        plt.title('Tiempo de Procesamiento')
        plt.xlabel('Muestra')
        plt.ylabel('Tiempo (ms)')
        plt.grid(True)
        
        # Comparación con latencia teórica
        plt.subplot(2, 2, 4)
        plt.axhline(y=self.latencia_ms, color='r', linestyle='--', label='Latencia Teórica')
        plt.plot(self.latencia_medida, label='Latencia Medida')
        plt.title('Latencia Teórica vs Medida')
        plt.xlabel('Muestra')
        plt.ylabel('Latencia (ms)')
        plt.legend()
        plt.grid(True)
        
        plt.tight_layout()
        plt.show()

# Ejemplo de uso
sistema = SistemaUltraBajaLatencia(44100, tamaño_buffer=32)

# Medir latencia
estadisticas = sistema.medir_latencia(0.5)

print("Estadísticas de Latencia:")
for metrica, valor in estadisticas.items():
    print(f"  {metrica}: {valor:.3f} ms")

# Visualizar
sistema.visualizar_latencia()
```

### 6. Aplicaciones en Audio

#### Procesador de Audio en Tiempo Real
```python
class ProcesadorAudioTiempoReal:
    def __init__(self, fs, tamaño_buffer=128):
        """
        Procesador de audio completo en tiempo real
        """
        self.fs = fs
        self.tamaño_buffer = tamaño_buffer
        
        # Sistema de ultra baja latencia
        self.sistema = SistemaUltraBajaLatencia(fs, tamaño_buffer)
        
        # Efectos
        self.delay = Delay(0.1, fs, feedback=0.2, mix=0.3)
        self.compresor = Compresor(fs, ratio=2.0, threshold=-12.0)
        
        # Métricas
        self.metricas = MetricasRendimiento()
    
    def procesar_audio(self, datos_entrada):
        """
        Procesa audio en tiempo real
        """
        datos_salida = np.zeros_like(datos_entrada, dtype=np.float32)
        
        for i, muestra in enumerate(datos_entrada):
            # Procesar con sistema de ultra baja latencia
            muestra_procesada = self.sistema.procesar_muestra(muestra)
            
            # Aplicar efectos
            muestra_con_delay = self.delay.procesar_muestra(muestra_procesada)
            muestra_final = self.compresor.procesar_muestra(muestra_con_delay)
            
            datos_salida[i] = muestra_final
        
        return datos_salida
    
    def obtener_estadisticas(self):
        """
        Obtiene estadísticas del procesador
        """
        return {
            'latencia_teorica': self.sistema.latencia_ms,
            'tamaño_buffer': self.sistema.tamaño_buffer,
            'frecuencia_muestreo': self.fs
        }

# Ejemplo de uso
procesador = ProcesadorAudioTiempoReal(44100, tamaño_buffer=64)

# Procesar audio
datos_entrada = np.random.randn(1024)
datos_salida = procesador.procesar_audio(datos_entrada)

# Obtener estadísticas
estadisticas = procesador.obtener_estadisticas()
print("Estadísticas del Procesador:")
for metrica, valor in estadisticas.items():
    print(f"  {metrica}: {valor}")

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(datos_entrada, label='Entrada', alpha=0.7)
plt.plot(datos_salida, label='Salida')
plt.title('Procesamiento de Audio en Tiempo Real')
plt.xlabel('Muestra')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Comparación de Rendimiento
```python
def ejercicio_comparacion_rendimiento():
    """
    Compara el rendimiento de diferentes configuraciones
    """
    fs = 44100
    configuraciones = [
        {'tamaño_buffer': 32, 'num_threads': 1},
        {'tamaño_buffer': 64, 'num_threads': 2},
        {'tamaño_buffer': 128, 'num_threads': 4},
        {'tamaño_buffer': 256, 'num_threads': 8}
    ]
    
    resultados = []
    
    for config in configuraciones:
        # Crear procesador
        procesador = ProcesadorAudioMultiThread(fs, **config)
        procesador.iniciar_threads()
        
        # Medir rendimiento
        datos_entrada = np.random.randn(2048)
        inicio = time.perf_counter()
        datos_salida = procesador.procesar_audio(datos_entrada)
        fin = time.perf_counter()
        
        tiempo_procesamiento = (fin - inicio) * 1000
        latencia_teorica = (config['tamaño_buffer'] / fs) * 1000
        
        resultados.append({
            'configuracion': config,
            'tiempo_procesamiento': tiempo_procesamiento,
            'latencia_teorica': latencia_teorica,
            'throughput': len(datos_entrada) / tiempo_procesamiento
        })
        
        procesador.detener_threads()
    
    # Visualizar resultados
    plt.figure(figsize=(12, 8))
    
    configs = [f"B{config['tamaño_buffer']}_T{config['num_threads']}" for config in configuraciones]
    tiempos = [r['tiempo_procesamiento'] for r in resultados]
    latencias = [r['latencia_teorica'] for r in resultados]
    throughputs = [r['throughput'] for r in resultados]
    
    plt.subplot(2, 2, 1)
    plt.bar(configs, tiempos)
    plt.title('Tiempo de Procesamiento')
    plt.ylabel('Tiempo (ms)')
    plt.xticks(rotation=45)
    
    plt.subplot(2, 2, 2)
    plt.bar(configs, latencias)
    plt.title('Latencia Teórica')
    plt.ylabel('Latencia (ms)')
    plt.xticks(rotation=45)
    
    plt.subplot(2, 2, 3)
    plt.bar(configs, throughputs)
    plt.title('Throughput')
    plt.ylabel('Muestras/ms')
    plt.xticks(rotation=45)
    
    plt.tight_layout()
    plt.show()
    
    # Imprimir resultados
    print("Resultados de Comparación:")
    for i, resultado in enumerate(resultados):
        print(f"Configuración {i+1}: {resultado['configuracion']}")
        print(f"  Tiempo: {resultado['tiempo_procesamiento']:.3f} ms")
        print(f"  Latencia: {resultado['latencia_teorica']:.3f} ms")
        print(f"  Throughput: {resultado['throughput']:.1f} muestras/ms")
        print()

ejercicio_comparacion_rendimiento()
```

### Ejercicio 2: Análisis de Latencia
```python
def ejercicio_analisis_latencia():
    """
    Analiza la latencia de diferentes tamaños de buffer
    """
    fs = 44100
    tamaños_buffer = [16, 32, 64, 128, 256, 512]
    
    latencias_teoricas = []
    latencias_medidas = []
    
    for tamaño in tamaños_buffer:
        # Crear sistema
        sistema = SistemaUltraBajaLatencia(fs, tamaño)
        
        # Medir latencia
        estadisticas = sistema.medir_latencia(0.1)
        
        latencias_teoricas.append(estadisticas['teorica'])
        latencias_medidas.append(estadisticas['promedio'])
    
    # Visualizar
    plt.figure(figsize=(12, 6))
    
    plt.subplot(1, 2, 1)
    plt.plot(tamaños_buffer, latencias_teoricas, 'bo-', label='Latencia Teórica')
    plt.plot(tamaños_buffer, latencias_medidas, 'ro-', label='Latencia Medida')
    plt.title('Latencia vs Tamaño de Buffer')
    plt.xlabel('Tamaño de Buffer (muestras)')
    plt.ylabel('Latencia (ms)')
    plt.legend()
    plt.grid(True)
    
    plt.subplot(1, 2, 2)
    plt.plot(tamaños_buffer, np.array(latencias_medidas) - np.array(latencias_teoricas), 'go-')
    plt.title('Diferencia entre Latencia Medida y Teórica')
    plt.xlabel('Tamaño de Buffer (muestras)')
    plt.ylabel('Diferencia (ms)')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    # Imprimir resultados
    print("Análisis de Latencia:")
    for i, tamaño in enumerate(tamaños_buffer):
        print(f"Buffer {tamaño}: Teórica={latencias_teoricas[i]:.3f}ms, Medida={latencias_medidas[i]:.3f}ms")

ejercicio_analisis_latencia()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 5.6: Síntesis de Audio](clase-5-6-sintesis-audio.md)
- **Clase Siguiente**: [Clase 5.8: Implementación en Hardware](clase-5-8-implementacion-hardware.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Smith, J. O. (2007). *Introduction to Digital Filters*
- Pirkle, W. (2013). *Designing Audio Effect Plugins in C++*
- Zölzer, U. (2011). *DAFX: Digital Audio Effects*

---
*Duración estimada: 4-5 horas*
