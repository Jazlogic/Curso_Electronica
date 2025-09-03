# Clase 5.8: Implementación en Hardware

## 🎯 Objetivo de la Clase
Implementar procesamiento de audio en hardware especializado incluyendo DSPs, FPGAs, optimización de memoria y pipelines para aplicaciones de ultra baja latencia.

## 📚 Contenido

### 1. Introducción a la Implementación en Hardware

#### ¿Por qué Hardware Especializado?
El procesamiento de audio en hardware especializado ofrece ventajas significativas para aplicaciones de ultra baja latencia:

**Ventajas del hardware especializado:**
- **Latencia ultra baja**: Procesamiento en tiempo real garantizado
- **Determinismo**: Tiempo de procesamiento predecible
- **Eficiencia energética**: Menor consumo de energía
- **Paralelismo**: Procesamiento simultáneo de múltiples canales
- **Precisión**: Control exacto del timing

**Tipos de hardware:**
- **DSPs (Digital Signal Processors)**: Procesadores especializados
- **FPGAs (Field-Programmable Gate Arrays)**: Hardware reconfigurable
- **ASICs (Application-Specific Integrated Circuits)**: Circuitos específicos
- **GPUs**: Procesamiento paralelo masivo

### 2. Procesadores de Señal Digital (DSPs)

#### Arquitectura de DSPs
```python
import numpy as np
import matplotlib.pyplot as plt

class SimuladorDSP:
    def __init__(self, frecuencia_reloj=100e6, num_cores=4):
        """
        Simulador de DSP para audio
        
        frecuencia_reloj: frecuencia del reloj en Hz
        num_cores: número de núcleos de procesamiento
        """
        self.frecuencia_reloj = frecuencia_reloj
        self.num_cores = num_cores
        self.periodo_reloj = 1.0 / frecuencia_reloj
        
        # Configuración de memoria
        self.memoria_programa = 64 * 1024  # 64KB
        self.memoria_datos = 32 * 1024     # 32KB
        self.cache_l1 = 8 * 1024          # 8KB
        
        # Configuración de procesamiento
        self.instrucciones_por_ciclo = 1
        self.latencia_memoria = 3  # ciclos
        
        # Métricas
        self.ciclos_utilizados = 0
        self.accesos_memoria = 0
        self.cache_hits = 0
        self.cache_misses = 0
    
    def calcular_ciclos_instruccion(self, tipo_instruccion):
        """
        Calcula el número de ciclos para una instrucción
        """
        ciclos_por_tipo = {
            'suma': 1,
            'multiplicacion': 2,
            'carga_memoria': 3,
            'almacenar_memoria': 3,
            'salto': 2,
            'comparacion': 1
        }
        
        return ciclos_por_tipo.get(tipo_instruccion, 1)
    
    def simular_filtro_fir(self, coeficientes, señal):
        """
        Simula la implementación de un filtro FIR en DSP
        """
        N = len(coeficientes)
        M = len(señal)
        resultado = np.zeros(M, dtype=np.float32)
        
        ciclos_totales = 0
        
        for i in range(M):
            # Inicializar acumulador
            ciclos_totales += 1  # 1 ciclo para inicializar
            
            suma = 0.0
            for j in range(N):
                if i - j >= 0:
                    # Cargar coeficiente
                    ciclos_totales += self.calcular_ciclos_instruccion('carga_memoria')
                    self.accesos_memoria += 1
                    
                    # Cargar muestra
                    ciclos_totales += self.calcular_ciclos_instruccion('carga_memoria')
                    self.accesos_memoria += 1
                    
                    # Multiplicación
                    ciclos_totales += self.calcular_ciclos_instruccion('multiplicacion')
                    
                    # Suma
                    ciclos_totales += self.calcular_ciclos_instruccion('suma')
            
            # Almacenar resultado
            ciclos_totales += self.calcular_ciclos_instruccion('almacenar_memoria')
            self.accesos_memoria += 1
            
            resultado[i] = suma
        
        self.ciclos_utilizados = ciclos_totales
        
        # Calcular tiempo de procesamiento
        tiempo_procesamiento = ciclos_totales * self.periodo_reloj
        
        return resultado, tiempo_procesamiento
    
    def simular_fft(self, señal):
        """
        Simula la implementación de FFT en DSP
        """
        N = len(señal)
        
        # FFT radix-2
        ciclos_totales = 0
        
        # Cálculo aproximado de ciclos para FFT
        # FFT de N puntos requiere aproximadamente N*log2(N) operaciones
        ciclos_aproximados = N * np.log2(N) * 10  # 10 ciclos por operación
        
        ciclos_totales += ciclos_aproximados
        self.ciclos_utilizados = ciclos_totales
        
        # Calcular tiempo de procesamiento
        tiempo_procesamiento = ciclos_totales * self.periodo_reloj
        
        # FFT real (para comparación)
        resultado = np.fft.fft(señal)
        
        return resultado, tiempo_procesamiento
    
    def obtener_estadisticas(self):
        """
        Obtiene estadísticas del DSP
        """
        return {
            'ciclos_utilizados': self.ciclos_utilizados,
            'accesos_memoria': self.accesos_memoria,
            'tiempo_procesamiento': self.ciclos_utilizados * self.periodo_reloj,
            'utilizacion_cpu': (self.ciclos_utilizados / (self.frecuencia_reloj * 0.001)) * 100  # Para 1ms
        }

# Ejemplo de uso
dsp = SimuladorDSP(frecuencia_reloj=200e6, num_cores=4)

# Simular filtro FIR
coeficientes = np.random.randn(64)
señal = np.random.randn(1024)

resultado, tiempo = dsp.simular_filtro_fir(coeficientes, señal)

print(f"Tiempo de procesamiento FIR: {tiempo*1000:.3f} ms")
print(f"Estadísticas: {dsp.obtener_estadisticas()}")
```

#### Optimización para DSPs
```python
class OptimizadorDSP:
    def __init__(self, dsp):
        """
        Optimizador para DSPs
        """
        self.dsp = dsp
    
    def optimizar_filtro_fir(self, coeficientes, señal):
        """
        Versión optimizada del filtro FIR para DSP
        """
        N = len(coeficientes)
        M = len(señal)
        resultado = np.zeros(M, dtype=np.float32)
        
        # Usar buffer circular para coeficientes
        buffer_coeficientes = np.tile(coeficientes, (M // N) + 1)
        
        # Procesar en bloques para mejor uso de cache
        bloque_size = min(64, M)
        ciclos_totales = 0
        
        for i in range(0, M, bloque_size):
            fin_bloque = min(i + bloque_size, M)
            
            # Procesar bloque
            for j in range(i, fin_bloque):
                suma = 0.0
                
                # Usar instrucciones SIMD (Single Instruction, Multiple Data)
                for k in range(0, N, 4):  # Procesar 4 elementos a la vez
                    if j - k >= 0:
                        # Cargar 4 coeficientes
                        ciclos_totales += 1  # 1 ciclo para carga SIMD
                        
                        # Cargar 4 muestras
                        ciclos_totales += 1  # 1 ciclo para carga SIMD
                        
                        # Multiplicación SIMD
                        ciclos_totales += 2  # 2 ciclos para multiplicación SIMD
                        
                        # Suma SIMD
                        ciclos_totales += 1  # 1 ciclo para suma SIMD
                
                # Almacenar resultado
                ciclos_totales += 1
                resultado[j] = suma
        
        self.dsp.ciclos_utilizados = ciclos_totales
        
        # Calcular tiempo de procesamiento
        tiempo_procesamiento = ciclos_totales * self.dsp.periodo_reloj
        
        return resultado, tiempo_procesamiento
    
    def optimizar_fft(self, señal):
        """
        Versión optimizada de FFT para DSP
        """
        N = len(señal)
        
        # Usar FFT radix-4 para mejor eficiencia
        ciclos_totales = 0
        
        # Cálculo optimizado de ciclos
        ciclos_optimizados = N * np.log2(N) * 5  # 5 ciclos por operación (optimizado)
        
        ciclos_totales += ciclos_optimizados
        self.dsp.ciclos_utilizados = ciclos_totales
        
        # Calcular tiempo de procesamiento
        tiempo_procesamiento = ciclos_totales * self.dsp.periodo_reloj
        
        # FFT real
        resultado = np.fft.fft(señal)
        
        return resultado, tiempo_procesamiento

# Ejemplo de uso
dsp = SimuladorDSP(frecuencia_reloj=200e6, num_cores=4)
optimizador = OptimizadorDSP(dsp)

# Comparar rendimiento
coeficientes = np.random.randn(64)
señal = np.random.randn(1024)

# Versión no optimizada
resultado1, tiempo1 = dsp.simular_filtro_fir(coeficientes, señal)

# Versión optimizada
resultado2, tiempo2 = optimizador.optimizar_filtro_fir(coeficientes, señal)

print(f"Tiempo no optimizado: {tiempo1*1000:.3f} ms")
print(f"Tiempo optimizado: {tiempo2*1000:.3f} ms")
print(f"Mejora: {tiempo1/tiempo2:.2f}x")
```

### 3. Implementación en FPGA

#### Descripción de Hardware en Verilog
```python
class GeneradorVerilog:
    def __init__(self):
        """
        Generador de código Verilog para FPGA
        """
        self.modulos = []
    
    def generar_filtro_fir(self, coeficientes, nombre_modulo="filtro_fir"):
        """
        Genera código Verilog para un filtro FIR
        """
        N = len(coeficientes)
        
        codigo = f"""
module {nombre_modulo} (
    input wire clk,
    input wire rst,
    input wire [15:0] entrada,
    output reg [15:0] salida,
    output reg valido
);

    // Coeficientes del filtro
    localparam N = {N};
    reg [15:0] coeficientes [0:N-1];
    
    // Buffer de entrada
    reg [15:0] buffer_entrada [0:N-1];
    
    // Acumulador
    reg [31:0] acumulador;
    
    // Contador
    reg [7:0] contador;
    
    // Inicializar coeficientes
    initial begin
        {self._generar_inicializacion_coeficientes(coeficientes)}
    end
    
    always @(posedge clk) begin
        if (rst) begin
            contador <= 0;
            acumulador <= 0;
            salida <= 0;
            valido <= 0;
        end else begin
            // Desplazar buffer
            for (integer i = N-1; i > 0; i = i-1) begin
                buffer_entrada[i] <= buffer_entrada[i-1];
            end
            buffer_entrada[0] <= entrada;
            
            // Calcular salida
            acumulador <= 0;
            for (integer i = 0; i < N; i = i+1) begin
                acumulador <= acumulador + (buffer_entrada[i] * coeficientes[i]);
            end
            
            salida <= acumulador[31:16];  // Truncar a 16 bits
            valido <= 1;
        end
    end

endmodule
"""
        
        return codigo
    
    def _generar_inicializacion_coeficientes(self, coeficientes):
        """
        Genera la inicialización de coeficientes
        """
        inicializacion = ""
        for i, coef in enumerate(coeficientes):
            # Convertir a formato de punto fijo
            coef_fijo = int(coef * 32767)  # 16 bits
            inicializacion += f"        coeficientes[{i}] <= 16'd{coef_fijo};\n"
        
        return inicializacion
    
    def generar_fft(self, tamaño_fft, nombre_modulo="fft"):
        """
        Genera código Verilog para FFT
        """
        codigo = f"""
module {nombre_modulo} (
    input wire clk,
    input wire rst,
    input wire inicio,
    input wire [15:0] entrada_real,
    input wire [15:0] entrada_imag,
    output reg [15:0] salida_real,
    output reg [15:0] salida_imag,
    output reg valido
);

    localparam N = {tamaño_fft};
    localparam LOG2N = $clog2(N);
    
    // Buffer de entrada
    reg [15:0] buffer_real [0:N-1];
    reg [15:0] buffer_imag [0:N-1];
    
    // Buffer de salida
    reg [15:0] salida_real_buf [0:N-1];
    reg [15:0] salida_imag_buf [0:N-1];
    
    // Contador
    reg [LOG2N-1:0] contador;
    
    // Estado
    reg [2:0] estado;
    localparam IDLE = 3'b000;
    localparam CARGAR = 3'b001;
    localparam PROCESAR = 3'b010;
    localparam FINALIZAR = 3'b011;
    
    always @(posedge clk) begin
        if (rst) begin
            contador <= 0;
            estado <= IDLE;
            valido <= 0;
        end else begin
            case (estado)
                IDLE: begin
                    if (inicio) begin
                        estado <= CARGAR;
                        contador <= 0;
                    end
                end
                
                CARGAR: begin
                    buffer_real[contador] <= entrada_real;
                    buffer_imag[contador] <= entrada_imag;
                    
                    if (contador == N-1) begin
                        estado <= PROCESAR;
                        contador <= 0;
                    end else begin
                        contador <= contador + 1;
                    end
                end
                
                PROCESAR: begin
                    // Implementar FFT radix-2
                    // (Simplificado para este ejemplo)
                    salida_real_buf[contador] <= buffer_real[contador];
                    salida_imag_buf[contador] <= buffer_imag[contador];
                    
                    if (contador == N-1) begin
                        estado <= FINALIZAR;
                        contador <= 0;
                    end else begin
                        contador <= contador + 1;
                    end
                end
                
                FINALIZAR: begin
                    salida_real <= salida_real_buf[contador];
                    salida_imag <= salida_imag_buf[contador];
                    valido <= 1;
                    
                    if (contador == N-1) begin
                        estado <= IDLE;
                        valido <= 0;
                    end else begin
                        contador <= contador + 1;
                    end
                end
            endcase
        end
    end

endmodule
"""
        
        return codigo

# Ejemplo de uso
generador = GeneradorVerilog()

# Generar filtro FIR
coeficientes = np.array([0.1, 0.2, 0.4, 0.2, 0.1])
codigo_fir = generador.generar_filtro_fir(coeficientes)

print("Código Verilog para Filtro FIR:")
print(codigo_fir)
```

#### Simulación de FPGA
```python
class SimuladorFPGA:
    def __init__(self, frecuencia_reloj=100e6):
        """
        Simulador de FPGA para audio
        """
        self.frecuencia_reloj = frecuencia_reloj
        self.periodo_reloj = 1.0 / frecuencia_reloj
        
        # Configuración de recursos
        self.luts_disponibles = 100000  # Look-Up Tables
        self.ffs_disponibles = 200000   # Flip-Flops
        self.brams_disponibles = 1000   # Block RAMs
        self.dsps_disponibles = 1000    # DSP Slices
        
        # Recursos utilizados
        self.luts_utilizados = 0
        self.ffs_utilizados = 0
        self.brams_utilizados = 0
        self.dsps_utilizados = 0
        
        # Métricas de rendimiento
        self.ciclos_utilizados = 0
        self.latencia_clock = 0
    
    def estimar_recursos_filtro_fir(self, N):
        """
        Estima los recursos necesarios para un filtro FIR
        """
        # LUTs: aproximadamente N * 16 (para multiplicadores)
        luts = N * 16
        
        # FFs: aproximadamente N * 16 (para registros)
        ffs = N * 16
        
        # DSPs: aproximadamente N (para multiplicadores)
        dsps = N
        
        # BRAMs: 1 para coeficientes
        brams = 1
        
        return {
            'luts': luts,
            'ffs': ffs,
            'dsps': dsps,
            'brams': brams
        }
    
    def estimar_recursos_fft(self, N):
        """
        Estima los recursos necesarios para FFT
        """
        # LUTs: aproximadamente N * log2(N) * 20
        luts = int(N * np.log2(N) * 20)
        
        # FFs: aproximadamente N * 32 (para registros)
        ffs = N * 32
        
        # DSPs: aproximadamente N/2 (para multiplicadores complejos)
        dsps = N // 2
        
        # BRAMs: 2 para buffers de entrada y salida
        brams = 2
        
        return {
            'luts': luts,
            'ffs': ffs,
            'dsps': dsps,
            'brams': brams
        }
    
    def simular_filtro_fir(self, coeficientes, señal):
        """
        Simula la implementación de filtro FIR en FPGA
        """
        N = len(coeficientes)
        M = len(señal)
        
        # Estimar recursos
        recursos = self.estimar_recursos_filtro_fir(N)
        
        # Verificar si hay recursos suficientes
        if (recursos['luts'] > self.luts_disponibles or
            recursos['ffs'] > self.ffs_disponibles or
            recursos['dsps'] > self.dsps_disponibles or
            recursos['brams'] > self.brams_disponibles):
            raise ValueError("Recursos insuficientes en FPGA")
        
        # Simular procesamiento
        resultado = np.zeros(M, dtype=np.float32)
        
        # Latencia: N ciclos (tamaño del filtro)
        latencia_ciclos = N
        self.latencia_clock = latencia_ciclos * self.periodo_reloj
        
        # Procesar señal
        for i in range(M):
            suma = 0.0
            for j in range(N):
                if i - j >= 0:
                    suma += coeficientes[j] * señal[i - j]
            resultado[i] = suma
        
        # Actualizar recursos utilizados
        self.luts_utilizados += recursos['luts']
        self.ffs_utilizados += recursos['ffs']
        self.dsps_utilizados += recursos['dsps']
        self.brams_utilizados += recursos['brams']
        
        return resultado, self.latencia_clock
    
    def simular_fft(self, señal):
        """
        Simula la implementación de FFT en FPGA
        """
        N = len(señal)
        
        # Estimar recursos
        recursos = self.estimar_recursos_fft(N)
        
        # Verificar recursos
        if (recursos['luts'] > self.luts_disponibles or
            recursos['ffs'] > self.ffs_disponibles or
            recursos['dsps'] > self.dsps_disponibles or
            recursos['brams'] > self.brams_disponibles):
            raise ValueError("Recursos insuficientes en FPGA")
        
        # Simular procesamiento
        resultado = np.fft.fft(señal)
        
        # Latencia: log2(N) ciclos
        latencia_ciclos = int(np.log2(N))
        self.latencia_clock = latencia_ciclos * self.periodo_reloj
        
        # Actualizar recursos
        self.luts_utilizados += recursos['luts']
        self.ffs_utilizados += recursos['ffs']
        self.dsps_utilizados += recursos['dsps']
        self.brams_utilizados += recursos['brams']
        
        return resultado, self.latencia_clock
    
    def obtener_estadisticas(self):
        """
        Obtiene estadísticas del FPGA
        """
        return {
            'luts_utilizados': self.luts_utilizados,
            'ffs_utilizados': self.ffs_utilizados,
            'dsps_utilizados': self.dsps_utilizados,
            'brams_utilizados': self.brams_utilizados,
            'luts_disponibles': self.luts_disponibles,
            'ffs_disponibles': self.ffs_disponibles,
            'dsps_disponibles': self.dsps_disponibles,
            'brams_disponibles': self.brams_disponibles,
            'utilizacion_luts': (self.luts_utilizados / self.luts_disponibles) * 100,
            'utilizacion_ffs': (self.ffs_utilizados / self.ffs_disponibles) * 100,
            'utilizacion_dsps': (self.dsps_utilizados / self.dsps_disponibles) * 100,
            'utilizacion_brams': (self.brams_utilizados / self.brams_disponibles) * 100,
            'latencia_clock': self.latencia_clock
        }

# Ejemplo de uso
fpga = SimuladorFPGA(frecuencia_reloj=100e6)

# Simular filtro FIR
coeficientes = np.random.randn(32)
señal = np.random.randn(1024)

resultado, latencia = fpga.simular_filtro_fir(coeficientes, señal)

print(f"Latencia del filtro FIR: {latencia*1000:.3f} ms")
print(f"Estadísticas del FPGA: {fpga.obtener_estadisticas()}")
```

### 4. Optimización de Memoria

#### Gestión de Memoria en Hardware
```python
class GestorMemoriaHardware:
    def __init__(self, tamaño_memoria=1024*1024):
        """
        Gestor de memoria para hardware de audio
        """
        self.tamaño_memoria = tamaño_memoria
        self.memoria = np.zeros(tamaño_memoria, dtype=np.float32)
        
        # Configuración de memoria
        self.tamaño_cache = 1024
        self.cache = np.zeros(self.tamaño_cache, dtype=np.float32)
        self.cache_tags = np.zeros(self.tamaño_cache, dtype=np.int32)
        self.cache_valid = np.zeros(self.tamaño_cache, dtype=bool)
        
        # Métricas
        self.accesos_memoria = 0
        self.cache_hits = 0
        self.cache_misses = 0
        self.tiempo_acceso = 0
    
    def leer_memoria(self, direccion):
        """
        Lee de memoria con cache
        """
        self.accesos_memoria += 1
        
        # Calcular índice de cache
        indice_cache = direccion % self.tamaño_cache
        
        # Verificar cache hit
        if (self.cache_valid[indice_cache] and 
            self.cache_tags[indice_cache] == direccion):
            self.cache_hits += 1
            self.tiempo_acceso += 1  # 1 ciclo para cache hit
            return self.cache[indice_cache]
        else:
            # Cache miss
            self.cache_misses += 1
            self.tiempo_acceso += 10  # 10 ciclos para acceso a memoria principal
            
            # Cargar en cache
            self.cache[indice_cache] = self.memoria[direccion]
            self.cache_tags[indice_cache] = direccion
            self.cache_valid[indice_cache] = True
            
            return self.cache[indice_cache]
    
    def escribir_memoria(self, direccion, valor):
        """
        Escribe en memoria con cache
        """
        self.accesos_memoria += 1
        
        # Calcular índice de cache
        indice_cache = direccion % self.tamaño_cache
        
        # Escribir en cache
        self.cache[indice_cache] = valor
        self.cache_tags[indice_cache] = direccion
        self.cache_valid[indice_cache] = True
        
        # Escribir en memoria principal
        self.memoria[direccion] = valor
        self.tiempo_acceso += 10  # 10 ciclos para escritura
    
    def optimizar_acceso_memoria(self, patron_acceso):
        """
        Optimiza el acceso a memoria basado en el patrón
        """
        if patron_acceso == 'secuencial':
            # Prefetch para acceso secuencial
            self.tiempo_acceso *= 0.5  # 50% de mejora
        elif patron_acceso == 'aleatorio':
            # Sin optimización para acceso aleatorio
            pass
        elif patron_acceso == 'bloques':
            # Optimización para acceso por bloques
            self.tiempo_acceso *= 0.7  # 30% de mejora
    
    def obtener_estadisticas(self):
        """
        Obtiene estadísticas de memoria
        """
        if self.accesos_memoria == 0:
            return None
        
        return {
            'accesos_memoria': self.accesos_memoria,
            'cache_hits': self.cache_hits,
            'cache_misses': self.cache_misses,
            'hit_rate': (self.cache_hits / self.accesos_memoria) * 100,
            'tiempo_acceso_promedio': self.tiempo_acceso / self.accesos_memoria
        }

# Ejemplo de uso
gestor_memoria = GestorMemoriaHardware()

# Simular acceso a memoria
for i in range(1000):
    valor = gestor_memoria.leer_memoria(i % 100)  # Acceso secuencial

# Optimizar para acceso secuencial
gestor_memoria.optimizar_acceso_memoria('secuencial')

# Obtener estadísticas
estadisticas = gestor_memoria.obtener_estadisticas()
print("Estadísticas de Memoria:")
for metrica, valor in estadisticas.items():
    print(f"  {metrica}: {valor}")
```

### 5. Pipelines de Procesamiento

#### Pipeline de Audio
```python
class PipelineAudio:
    def __init__(self, fs, tamaño_buffer=64):
        """
        Pipeline de procesamiento de audio
        """
        self.fs = fs
        self.tamaño_buffer = tamaño_buffer
        
        # Etapas del pipeline
        self.etapas = []
        self.buffers_etapa = []
        
        # Métricas
        self.latencia_total = 0
        self.throughput = 0
    
    def agregar_etapa(self, nombre, latencia_ciclos, procesamiento):
        """
        Agrega una etapa al pipeline
        """
        etapa = {
            'nombre': nombre,
            'latencia_ciclos': latencia_ciclos,
            'procesamiento': procesamiento,
            'buffer': np.zeros(self.tamaño_buffer, dtype=np.float32)
        }
        
        self.etapas.append(etapa)
        self.buffers_etapa.append(np.zeros(self.tamaño_buffer, dtype=np.float32))
    
    def procesar_pipeline(self, datos_entrada):
        """
        Procesa datos a través del pipeline
        """
        datos_salida = np.zeros_like(datos_entrada, dtype=np.float32)
        
        # Procesar en bloques
        for i in range(0, len(datos_entrada), self.tamaño_buffer):
            bloque_entrada = datos_entrada[i:i+self.tamaño_buffer]
            bloque_salida = bloque_entrada.copy()
            
            # Procesar a través de cada etapa
            for j, etapa in enumerate(self.etapas):
                # Aplicar procesamiento de la etapa
                bloque_salida = etapa['procesamiento'](bloque_salida)
                
                # Actualizar buffer de la etapa
                self.buffers_etapa[j] = bloque_salida
            
            # Copiar resultado
            datos_salida[i:i+len(bloque_salida)] = bloque_salida
        
        return datos_salida
    
    def calcular_latencia_total(self):
        """
        Calcula la latencia total del pipeline
        """
        latencia_ciclos = sum(etapa['latencia_ciclos'] for etapa in self.etapas)
        self.latencia_total = latencia_ciclos / self.fs
        
        return self.latencia_total
    
    def calcular_throughput(self):
        """
        Calcula el throughput del pipeline
        """
        # Throughput = frecuencia de muestreo / número de etapas
        self.throughput = self.fs / len(self.etapas)
        
        return self.throughput
    
    def obtener_estadisticas(self):
        """
        Obtiene estadísticas del pipeline
        """
        return {
            'num_etapas': len(self.etapas),
            'latencia_total': self.latencia_total,
            'throughput': self.throughput,
            'tamaño_buffer': self.tamaño_buffer
        }

# Ejemplo de uso
pipeline = PipelineAudio(44100, tamaño_buffer=64)

# Agregar etapas
def filtro_pasa_bajas(datos):
    # Filtro pasa-bajas simple
    return datos * 0.5

def compresor(datos):
    # Compresión simple
    return np.tanh(datos * 2) * 0.5

def ecualizador(datos):
    # Ecualización simple
    return datos * 1.2

pipeline.agregar_etapa('Filtro', 10, filtro_pasa_bajas)
pipeline.agregar_etapa('Compresor', 15, compresor)
pipeline.agregar_etapa('Ecualizador', 5, ecualizador)

# Procesar audio
datos_entrada = np.random.randn(1024)
datos_salida = pipeline.procesar_pipeline(datos_entrada)

# Calcular métricas
latencia = pipeline.calcular_latencia_total()
throughput = pipeline.calcular_throughput()

print(f"Latencia total: {latencia*1000:.3f} ms")
print(f"Throughput: {throughput:.1f} muestras/segundo")
print(f"Estadísticas: {pipeline.obtener_estadisticas()}")
```

### 6. Aplicaciones en Audio

#### Sistema de Audio Completo en Hardware
```python
class SistemaAudioHardware:
    def __init__(self, fs, tamaño_buffer=64):
        """
        Sistema de audio completo implementado en hardware
        """
        self.fs = fs
        self.tamaño_buffer = tamaño_buffer
        
        # Componentes del sistema
        self.dsp = SimuladorDSP(frecuencia_reloj=200e6, num_cores=4)
        self.fpga = SimuladorFPGA(frecuencia_reloj=100e6)
        self.gestor_memoria = GestorMemoriaHardware()
        self.pipeline = PipelineAudio(fs, tamaño_buffer)
        
        # Configuración del sistema
        self.latencia_total = 0
        self.throughput = 0
    
    def configurar_sistema(self):
        """
        Configura el sistema de audio
        """
        # Configurar pipeline
        def filtro_anti_aliasing(datos):
            # Filtro anti-aliasing
            return datos * 0.8
        
        def ecualizador(datos):
            # Ecualización
            return datos * 1.1
        
        def compresor(datos):
            # Compresión
            return np.tanh(datos * 1.5) * 0.7
        
        self.pipeline.agregar_etapa('Anti-aliasing', 5, filtro_anti_aliasing)
        self.pipeline.agregar_etapa('Ecualizador', 8, ecualizador)
        self.pipeline.agregar_etapa('Compresor', 12, compresor)
    
    def procesar_audio(self, datos_entrada):
        """
        Procesa audio usando el sistema completo
        """
        # Procesar a través del pipeline
        datos_procesados = self.pipeline.procesar_pipeline(datos_entrada)
        
        # Calcular métricas
        self.latencia_total = self.pipeline.calcular_latencia_total()
        self.throughput = self.pipeline.calcular_throughput()
        
        return datos_procesados
    
    def obtener_estadisticas(self):
        """
        Obtiene estadísticas del sistema completo
        """
        return {
            'latencia_total': self.latencia_total,
            'throughput': self.throughput,
            'estadisticas_dsp': self.dsp.obtener_estadisticas(),
            'estadisticas_fpga': self.fpga.obtener_estadisticas(),
            'estadisticas_memoria': self.gestor_memoria.obtener_estadisticas(),
            'estadisticas_pipeline': self.pipeline.obtener_estadisticas()
        }

# Ejemplo de uso
sistema = SistemaAudioHardware(44100, tamaño_buffer=64)
sistema.configurar_sistema()

# Procesar audio
datos_entrada = np.random.randn(1024)
datos_salida = sistema.procesar_audio(datos_entrada)

# Obtener estadísticas
estadisticas = sistema.obtener_estadisticas()
print("Estadísticas del Sistema de Audio:")
for componente, stats in estadisticas.items():
    print(f"{componente}: {stats}")

# Visualizar
plt.figure(figsize=(12, 6))
plt.plot(datos_entrada, label='Entrada', alpha=0.7)
plt.plot(datos_salida, label='Salida')
plt.title('Sistema de Audio en Hardware')
plt.xlabel('Muestra')
plt.ylabel('Amplitud')
plt.legend()
plt.grid(True)
plt.show()
```

## 🎯 Ejercicios Prácticos

### Ejercicio 1: Comparación de Hardware
```python
def ejercicio_comparacion_hardware():
    """
    Compara el rendimiento de diferentes tipos de hardware
    """
    fs = 44100
    datos_entrada = np.random.randn(1024)
    
    # Configuraciones de hardware
    configuraciones = [
        {'tipo': 'DSP', 'frecuencia': 200e6, 'cores': 4},
        {'tipo': 'FPGA', 'frecuencia': 100e6, 'cores': 1},
        {'tipo': 'CPU', 'frecuencia': 2e9, 'cores': 8}
    ]
    
    resultados = []
    
    for config in configuraciones:
        if config['tipo'] == 'DSP':
            hardware = SimuladorDSP(config['frecuencia'], config['cores'])
            resultado, tiempo = hardware.simular_filtro_fir(np.random.randn(64), datos_entrada)
        elif config['tipo'] == 'FPGA':
            hardware = SimuladorFPGA(config['frecuencia'])
            resultado, tiempo = hardware.simular_filtro_fir(np.random.randn(64), datos_entrada)
        else:  # CPU
            # Simular CPU
            tiempo = len(datos_entrada) * 64 * 0.001 / config['cores']  # 1ns por operación
        
        resultados.append({
            'tipo': config['tipo'],
            'tiempo': tiempo,
            'latencia': tiempo * 1000  # en ms
        })
    
    # Visualizar resultados
    plt.figure(figsize=(12, 6))
    
    tipos = [r['tipo'] for r in resultados]
    tiempos = [r['tiempo'] * 1000 for r in resultados]  # en ms
    
    plt.bar(tipos, tiempos)
    plt.title('Comparación de Rendimiento de Hardware')
    plt.ylabel('Tiempo de Procesamiento (ms)')
    plt.grid(True, axis='y')
    
    # Agregar valores en las barras
    for i, tiempo in enumerate(tiempos):
        plt.text(i, tiempo + 0.1, f'{tiempo:.3f} ms', ha='center')
    
    plt.show()
    
    # Imprimir resultados
    print("Comparación de Hardware:")
    for resultado in resultados:
        print(f"{resultado['tipo']}: {resultado['latencia']:.3f} ms")

ejercicio_comparacion_hardware()
```

### Ejercicio 2: Análisis de Recursos
```python
def ejercicio_analisis_recursos():
    """
    Analiza el uso de recursos en FPGA
    """
    fpga = SimuladorFPGA(frecuencia_reloj=100e6)
    
    # Diferentes configuraciones
    configuraciones = [
        {'nombre': 'Filtro FIR 16', 'tipo': 'fir', 'parametro': 16},
        {'nombre': 'Filtro FIR 32', 'tipo': 'fir', 'parametro': 32},
        {'nombre': 'Filtro FIR 64', 'tipo': 'fir', 'parametro': 64},
        {'nombre': 'FFT 256', 'tipo': 'fft', 'parametro': 256},
        {'nombre': 'FFT 512', 'tipo': 'fft', 'parametro': 512},
        {'nombre': 'FFT 1024', 'tipo': 'fft', 'parametro': 1024}
    ]
    
    resultados = []
    
    for config in configuraciones:
        if config['tipo'] == 'fir':
            recursos = fpga.estimar_recursos_filtro_fir(config['parametro'])
        else:  # fft
            recursos = fpga.estimar_recursos_fft(config['parametro'])
        
        resultados.append({
            'nombre': config['nombre'],
            'luts': recursos['luts'],
            'ffs': recursos['ffs'],
            'dsps': recursos['dsps'],
            'brams': recursos['brams']
        })
    
    # Visualizar
    fig, axes = plt.subplots(2, 2, figsize=(15, 10))
    
    nombres = [r['nombre'] for r in resultados]
    
    # LUTs
    axes[0, 0].bar(nombres, [r['luts'] for r in resultados])
    axes[0, 0].set_title('Uso de LUTs')
    axes[0, 0].set_ylabel('LUTs')
    axes[0, 0].tick_params(axis='x', rotation=45)
    
    # FFs
    axes[0, 1].bar(nombres, [r['ffs'] for r in resultados])
    axes[0, 1].set_title('Uso de Flip-Flops')
    axes[0, 1].set_ylabel('FFs')
    axes[0, 1].tick_params(axis='x', rotation=45)
    
    # DSPs
    axes[1, 0].bar(nombres, [r['dsps'] for r in resultados])
    axes[1, 0].set_title('Uso de DSP Slices')
    axes[1, 0].set_ylabel('DSPs')
    axes[1, 0].tick_params(axis='x', rotation=45)
    
    # BRAMs
    axes[1, 1].bar(nombres, [r['brams'] for r in resultados])
    axes[1, 1].set_title('Uso de Block RAMs')
    axes[1, 1].set_ylabel('BRAMs')
    axes[1, 1].tick_params(axis='x', rotation=45)
    
    plt.tight_layout()
    plt.show()
    
    # Imprimir resultados
    print("Análisis de Recursos:")
    for resultado in resultados:
        print(f"{resultado['nombre']}:")
        print(f"  LUTs: {resultado['luts']}")
        print(f"  FFs: {resultado['ffs']}")
        print(f"  DSPs: {resultado['dsps']}")
        print(f"  BRAMs: {resultado['brams']}")
        print()

ejercicio_analisis_recursos()
```

## 🔗 Navegación
- **Clase Anterior**: [Clase 5.7: Procesamiento en Tiempo Real](clase-5-7-procesamiento-tiempo-real.md)
- **Clase Siguiente**: [Clase 6.1: Convertidores ADC/DAC](../modulo-06-hardware-audio/clase-6-1-convertidores-adc-dac.md)
- **Módulo**: [Módulo 5: Procesamiento Digital de Señales](README.md)

## 📚 Referencias
- Smith, J. O. (2007). *Introduction to Digital Filters*
- Pirkle, W. (2013). *Designing Audio Effect Plugins in C++*
- Zölzer, U. (2011). *DAFX: Digital Audio Effects*

---
*Duración estimada: 4-5 horas*
