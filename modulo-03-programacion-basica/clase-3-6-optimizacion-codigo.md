# Clase 3.6: Optimización de Código

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender los principios de optimización de código
- Entender las técnicas de optimización para audio en tiempo real
- Conocer las herramientas de profiling y análisis de rendimiento
- Aplicar optimizaciones específicas para minimizar latencia
- Entender el balance entre rendimiento y mantenibilidad

## Introducción a la Optimización

### ¿Por qué es importante la optimización?

La **optimización de código** es crítica para audio de ultra baja latencia porque:

1. **Latencia**: Cada microsegundo cuenta en aplicaciones de tiempo real
2. **Rendimiento**: Procesamiento eficiente permite más canales y efectos
3. **Recursos**: Optimización de CPU, memoria y ancho de banda
4. **Escalabilidad**: Sistemas que pueden crecer sin degradación
5. **Eficiencia energética**: Menor consumo de energía

### Principios de Optimización

1. **Medir primero**: No optimizar sin datos
2. **Optimizar el cuello de botella**: Enfocarse en lo que más importa
3. **Mantener la legibilidad**: Código optimizado debe ser mantenible
4. **Considerar el contexto**: Optimizaciones específicas para audio
5. **Validar resultados**: Verificar que las optimizaciones funcionen

## Análisis de Rendimiento

### Herramientas de Profiling

#### 1. Profiling en C/C++

```c
#include <time.h>
#include <stdio.h>

// Medición de tiempo básica
void measure_function_time() {
    clock_t start, end;
    double cpu_time_used;
    
    start = clock();
    
    // Código a medir
    process_audio_buffer(audio_buffer, buffer_size);
    
    end = clock();
    cpu_time_used = ((double)(end - start)) / CLOCKS_PER_SEC;
    
    printf("Tiempo de procesamiento: %f segundos\n", cpu_time_used);
}

// Medición de tiempo de alta precisión
#include <sys/time.h>

void measure_high_precision_time() {
    struct timeval start, end;
    long seconds, useconds;
    double mtime;
    
    gettimeofday(&start, NULL);
    
    // Código a medir
    process_audio_buffer(audio_buffer, buffer_size);
    
    gettimeofday(&end, NULL);
    
    seconds = end.tv_sec - start.tv_sec;
    useconds = end.tv_usec - start.tv_usec;
    mtime = ((seconds) * 1000 + useconds/1000.0);
    
    printf("Tiempo de procesamiento: %f milisegundos\n", mtime);
}
```

#### 2. Profiling con gprof

```bash
# Compilar con profiling
gcc -pg -O2 -o audio_processor audio_processor.c

# Ejecutar programa
./audio_processor

# Generar reporte
gprof audio_processor gmon.out > profile_report.txt
```

#### 3. Profiling con Valgrind

```bash
# Profiling de memoria
valgrind --tool=massif ./audio_processor

# Profiling de cache
valgrind --tool=cachegrind ./audio_processor

# Análisis de callgrind
valgrind --tool=callgrind ./audio_processor
```

### Análisis de Rendimiento en Python

```python
import time
import cProfile
import pstats
from memory_profiler import profile

# Medición de tiempo
def measure_time(func):
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()
        result = func(*args, **kwargs)
        end_time = time.perf_counter()
        print(f"Tiempo de ejecución: {end_time - start_time:.6f} segundos")
        return result
    return wrapper

# Profiling de funciones
@measure_time
def process_audio_signal(signal):
    # Procesamiento de audio
    return processed_signal

# Profiling detallado
def profile_function():
    cProfile.run('process_audio_signal(audio_data)', 'profile_output.prof')
    
    # Analizar resultados
    p = pstats.Stats('profile_output.prof')
    p.sort_stats('cumulative').print_stats(10)

# Profiling de memoria
@profile
def memory_intensive_function():
    # Función que usa mucha memoria
    large_array = np.zeros(1000000)
    return large_array
```

## Optimizaciones de Algoritmos

### Optimización de Bucles

#### 1. Desenrollado de Bucles

```c
// Bucle original
void process_audio_original(float* buffer, int size) {
    for (int i = 0; i < size; i++) {
        buffer[i] *= 0.5f;
    }
}

// Bucle desenrollado (unroll)
void process_audio_unrolled(float* buffer, int size) {
    int i;
    for (i = 0; i < size - 3; i += 4) {
        buffer[i] *= 0.5f;
        buffer[i+1] *= 0.5f;
        buffer[i+2] *= 0.5f;
        buffer[i+3] *= 0.5f;
    }
    
    // Procesar elementos restantes
    for (; i < size; i++) {
        buffer[i] *= 0.5f;
    }
}
```

#### 2. Optimización de Acceso a Memoria

```c
// Acceso no optimizado
void process_audio_non_optimized(float* buffer, int size) {
    for (int i = 0; i < size; i++) {
        buffer[i] = buffer[i] * 0.5f + buffer[i+1] * 0.3f;
    }
}

// Acceso optimizado (mejor localidad de cache)
void process_audio_optimized(float* buffer, int size) {
    float prev_sample = buffer[0];
    float current_sample;
    
    for (int i = 1; i < size; i++) {
        current_sample = buffer[i];
        buffer[i-1] = prev_sample * 0.5f + current_sample * 0.3f;
        prev_sample = current_sample;
    }
}
```

### Optimización de Operaciones Matemáticas

#### 1. Uso de Operaciones Enteras

```c
// Operaciones flotantes (más lentas)
float process_float(float* buffer, int size) {
    float sum = 0.0f;
    for (int i = 0; i < size; i++) {
        sum += buffer[i] * buffer[i];
    }
    return sqrt(sum / size);
}

// Operaciones enteras (más rápidas)
int process_fixed_point(int* buffer, int size) {
    long long sum = 0;
    for (int i = 0; i < size; i++) {
        sum += (long long)buffer[i] * buffer[i];
    }
    return (int)sqrt(sum / size);
}
```

#### 2. Tablas de Búsqueda

```c
// Cálculo de seno (lento)
float slow_sine(float angle) {
    return sin(angle);
}

// Tabla de búsqueda (rápido)
#define SINE_TABLE_SIZE 1024
float sine_table[SINE_TABLE_SIZE];

void init_sine_table() {
    for (int i = 0; i < SINE_TABLE_SIZE; i++) {
        sine_table[i] = sin(2.0f * M_PI * i / SINE_TABLE_SIZE);
    }
}

float fast_sine(float angle) {
    // Normalizar ángulo
    while (angle < 0) angle += 2.0f * M_PI;
    while (angle >= 2.0f * M_PI) angle -= 2.0f * M_PI;
    
    // Convertir a índice
    int index = (int)(angle * SINE_TABLE_SIZE / (2.0f * M_PI));
    
    // Interpolación lineal
    float t = (angle * SINE_TABLE_SIZE / (2.0f * M_PI)) - index;
    int next_index = (index + 1) % SINE_TABLE_SIZE;
    
    return sine_table[index] + t * (sine_table[next_index] - sine_table[index]);
}
```

### Optimización de Filtros

#### 1. Filtro IIR Optimizado

```c
// Filtro IIR original
float iir_filter_original(float input, float* coeffs, float* state, int order) {
    float output = input * coeffs[0];
    
    for (int i = 1; i <= order; i++) {
        output += state[i-1] * coeffs[i];
    }
    
    // Actualizar estado
    for (int i = order-1; i > 0; i--) {
        state[i] = state[i-1];
    }
    state[0] = input;
    
    return output;
}

// Filtro IIR optimizado (estructura directa II)
typedef struct {
    float* b_coeffs;  // Coeficientes del numerador
    float* a_coeffs;  // Coeficientes del denominador
    float* delay_line; // Línea de retraso
    int order;
} iir_filter_t;

float iir_filter_optimized(float input, iir_filter_t* filter) {
    float output = input * filter->b_coeffs[0];
    
    // Procesar coeficientes del numerador
    for (int i = 1; i <= filter->order; i++) {
        output += filter->delay_line[i-1] * filter->b_coeffs[i];
    }
    
    // Procesar coeficientes del denominador
    for (int i = 1; i <= filter->order; i++) {
        output -= filter->delay_line[i-1] * filter->a_coeffs[i];
    }
    
    // Actualizar línea de retraso
    for (int i = filter->order-1; i > 0; i--) {
        filter->delay_line[i] = filter->delay_line[i-1];
    }
    filter->delay_line[0] = output;
    
    return output;
}
```

#### 2. Filtro FIR Optimizado

```c
// Filtro FIR original
float fir_filter_original(float input, float* coeffs, float* delay_line, int order) {
    float output = 0.0f;
    
    // Desplazar muestras
    for (int i = order-1; i > 0; i--) {
        delay_line[i] = delay_line[i-1];
    }
    delay_line[0] = input;
    
    // Calcular salida
    for (int i = 0; i < order; i++) {
        output += delay_line[i] * coeffs[i];
    }
    
    return output;
}

// Filtro FIR optimizado (estructura circular)
typedef struct {
    float* coeffs;
    float* delay_line;
    int order;
    int write_pos;
} fir_filter_t;

float fir_filter_optimized(float input, fir_filter_t* filter) {
    float output = 0.0f;
    
    // Escribir nueva muestra
    filter->delay_line[filter->write_pos] = input;
    
    // Calcular salida
    for (int i = 0; i < filter->order; i++) {
        int read_pos = (filter->write_pos - i + filter->order) % filter->order;
        output += filter->delay_line[read_pos] * filter->coeffs[i];
    }
    
    // Actualizar posición de escritura
    filter->write_pos = (filter->write_pos + 1) % filter->order;
    
    return output;
}
```

## Optimizaciones de Compilador

### Flags de Compilación

#### 1. Optimizaciones Generales

```bash
# Optimización básica
gcc -O2 -o audio_processor audio_processor.c

# Optimización agresiva
gcc -O3 -o audio_processor audio_processor.c

# Optimización para tamaño
gcc -Os -o audio_processor audio_processor.c

# Optimización específica para procesador
gcc -march=native -mtune=native -o audio_processor audio_processor.c
```

#### 2. Optimizaciones Específicas para Audio

```bash
# Optimización para matemáticas rápidas
gcc -ffast-math -o audio_processor audio_processor.c

# Optimización para bucles
gcc -funroll-loops -o audio_processor audio_processor.c

# Optimización para funciones inline
gcc -finline-functions -o audio_processor audio_processor.c

# Optimización para vectorización
gcc -ftree-vectorize -o audio_processor audio_processor.c
```

#### 3. Optimizaciones Avanzadas

```bash
# Optimización completa para audio
gcc -O3 -march=native -mtune=native -ffast-math -funroll-loops \
    -finline-functions -ftree-vectorize -flto \
    -o audio_processor audio_processor.c
```

### Directivas del Compilador

```c
// Función inline
inline float fast_multiply(float a, float b) {
    return a * b;
}

// Función siempre inline
__attribute__((always_inline))
inline float always_inline_multiply(float a, float b) {
    return a * b;
}

// Función no inline
__attribute__((noinline))
float noinline_function(float a, float b) {
    return a * b;
}

// Optimización específica
__attribute__((optimize("O3")))
float optimized_function(float a, float b) {
    return a * b;
}

// Alineación de memoria
__attribute__((aligned(16)))
float aligned_buffer[1024];

// Función pura (sin efectos secundarios)
__attribute__((pure))
float pure_function(float a, float b) {
    return a * b;
}
```

## Optimizaciones de Memoria

### Gestión de Memoria

#### 1. Alineación de Memoria

```c
// Alineación manual
float* aligned_buffer = (float*)aligned_alloc(16, 1024 * sizeof(float));

// Alineación con atributos
__attribute__((aligned(16)))
float buffer[1024];

// Verificar alineación
bool is_aligned(void* ptr, size_t alignment) {
    return ((uintptr_t)ptr % alignment) == 0;
}
```

#### 2. Prefetching

```c
// Prefetch manual
void process_audio_with_prefetch(float* buffer, int size) {
    for (int i = 0; i < size; i++) {
        // Prefetch próxima iteración
        if (i + 1 < size) {
            __builtin_prefetch(&buffer[i + 1], 0, 3);
        }
        
        // Procesar muestra actual
        buffer[i] *= 0.5f;
    }
}
```

#### 3. Pool de Memoria

```c
// Pool de memoria para buffers de audio
typedef struct {
    float* buffers[16];
    bool in_use[16];
    int buffer_size;
} audio_buffer_pool_t;

float* get_audio_buffer(audio_buffer_pool_t* pool) {
    for (int i = 0; i < 16; i++) {
        if (!pool->in_use[i]) {
            pool->in_use[i] = true;
            return pool->buffers[i];
        }
    }
    return NULL; // No hay buffers disponibles
}

void return_audio_buffer(audio_buffer_pool_t* pool, float* buffer) {
    for (int i = 0; i < 16; i++) {
        if (pool->buffers[i] == buffer) {
            pool->in_use[i] = false;
            break;
        }
    }
}
```

## Optimizaciones Específicas para Audio

### Procesamiento en Bloques

```c
// Procesamiento muestra por muestra (lento)
void process_audio_sample_by_sample(float* buffer, int size) {
    for (int i = 0; i < size; i++) {
        buffer[i] = apply_filter(buffer[i]);
    }
}

// Procesamiento en bloques (rápido)
void process_audio_in_blocks(float* buffer, int size, int block_size) {
    for (int i = 0; i < size; i += block_size) {
        int current_block_size = (i + block_size > size) ? size - i : block_size;
        process_audio_block(&buffer[i], current_block_size);
    }
}

void process_audio_block(float* block, int size) {
    // Procesar bloque completo
    for (int i = 0; i < size; i++) {
        block[i] *= 0.5f;
    }
}
```

### Vectorización SIMD

```c
#include <immintrin.h>

// Procesamiento vectorizado con AVX
void process_audio_vectorized(float* buffer, int size) {
    int i;
    
    // Procesar 8 muestras a la vez
    for (i = 0; i < size - 7; i += 8) {
        __m256 input = _mm256_load_ps(&buffer[i]);
        __m256 gain = _mm256_set1_ps(0.5f);
        __m256 output = _mm256_mul_ps(input, gain);
        _mm256_store_ps(&buffer[i], output);
    }
    
    // Procesar muestras restantes
    for (; i < size; i++) {
        buffer[i] *= 0.5f;
    }
}
```

### Optimización de FFT

```c
// FFT optimizada para audio
void optimized_fft(float* real, float* imag, int n) {
    // Usar FFTW para optimización automática
    fftwf_plan plan = fftwf_plan_dft_1d(n, (fftwf_complex*)real, 
                                       (fftwf_complex*)real, 
                                       FFTW_FORWARD, FFTW_ESTIMATE);
    
    fftwf_execute(plan);
    fftwf_destroy_plan(plan);
}

// FFT con plan precalculado
typedef struct {
    fftwf_plan forward_plan;
    fftwf_plan inverse_plan;
    int size;
} fft_optimizer_t;

void init_fft_optimizer(fft_optimizer_t* optimizer, int size) {
    optimizer->size = size;
    float* temp = (float*)fftwf_malloc(size * sizeof(float));
    
    optimizer->forward_plan = fftwf_plan_dft_1d(size, (fftwf_complex*)temp,
                                               (fftwf_complex*)temp,
                                               FFTW_FORWARD, FFTW_MEASURE);
    
    optimizer->inverse_plan = fftwf_plan_dft_1d(size, (fftwf_complex*)temp,
                                               (fftwf_complex*)temp,
                                               FFTW_BACKWARD, FFTW_MEASURE);
    
    fftwf_free(temp);
}

void optimized_fft_with_plan(float* data, fft_optimizer_t* optimizer) {
    fftwf_execute_dft(optimizer->forward_plan, (fftwf_complex*)data,
                     (fftwf_complex*)data);
}
```

## Ejemplo Práctico: Procesador de Audio Optimizado

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
#include <immintrin.h>
#include <time.h>

// Estructura para procesador optimizado
typedef struct {
    float* input_buffer;
    float* output_buffer;
    float* delay_line;
    float* filter_coeffs;
    int buffer_size;
    int filter_order;
    int delay_length;
    int write_pos;
} optimized_audio_processor_t;

// Inicializar procesador
optimized_audio_processor_t* create_optimized_processor(int buffer_size, int filter_order) {
    optimized_audio_processor_t* processor = (optimized_audio_processor_t*)malloc(sizeof(optimized_audio_processor_t));
    if (processor == NULL) return NULL;
    
    // Asignar memoria alineada
    processor->input_buffer = (float*)aligned_alloc(32, buffer_size * sizeof(float));
    processor->output_buffer = (float*)aligned_alloc(32, buffer_size * sizeof(float));
    processor->delay_line = (float*)aligned_alloc(32, filter_order * sizeof(float));
    processor->filter_coeffs = (float*)aligned_alloc(32, filter_order * sizeof(float));
    
    if (processor->input_buffer == NULL || processor->output_buffer == NULL ||
        processor->delay_line == NULL || processor->filter_coeffs == NULL) {
        free(processor);
        return NULL;
    }
    
    processor->buffer_size = buffer_size;
    processor->filter_order = filter_order;
    processor->delay_length = filter_order;
    processor->write_pos = 0;
    
    // Inicializar coeficientes del filtro
    for (int i = 0; i < filter_order; i++) {
        processor->filter_coeffs[i] = 1.0f / filter_order;
    }
    
    return processor;
}

// Procesamiento optimizado con vectorización
void process_audio_optimized(optimized_audio_processor_t* processor) {
    float* input = processor->input_buffer;
    float* output = processor->output_buffer;
    float* delay_line = processor->delay_line;
    float* coeffs = processor->filter_coeffs;
    int size = processor->buffer_size;
    int order = processor->filter_order;
    
    // Procesar en bloques vectorizados
    int i;
    for (i = 0; i < size - 7; i += 8) {
        __m256 input_vec = _mm256_load_ps(&input[i]);
        __m256 gain = _mm256_set1_ps(0.5f);
        __m256 output_vec = _mm256_mul_ps(input_vec, gain);
        _mm256_store_ps(&output[i], output_vec);
    }
    
    // Procesar muestras restantes
    for (; i < size; i++) {
        output[i] = input[i] * 0.5f;
    }
    
    // Aplicar filtro FIR optimizado
    for (i = 0; i < size; i++) {
        float sample = output[i];
        
        // Escribir nueva muestra
        delay_line[processor->write_pos] = sample;
        
        // Calcular salida del filtro
        float filtered = 0.0f;
        for (int j = 0; j < order; j++) {
            int read_pos = (processor->write_pos - j + order) % order;
            filtered += delay_line[read_pos] * coeffs[j];
        }
        
        output[i] = filtered;
        processor->write_pos = (processor->write_pos + 1) % order;
    }
}

// Medición de rendimiento
void benchmark_processor(optimized_audio_processor_t* processor, int iterations) {
    struct timespec start, end;
    double total_time = 0.0;
    
    // Llenar buffer de entrada con datos de prueba
    for (int i = 0; i < processor->buffer_size; i++) {
        processor->input_buffer[i] = sin(2.0f * M_PI * 440.0f * i / 44100.0f);
    }
    
    // Medir tiempo de procesamiento
    for (int iter = 0; iter < iterations; iter++) {
        clock_gettime(CLOCK_MONOTONIC, &start);
        
        process_audio_optimized(processor);
        
        clock_gettime(CLOCK_MONOTONIC, &end);
        
        double time_taken = (end.tv_sec - start.tv_sec) * 1e9 + (end.tv_nsec - start.tv_nsec);
        total_time += time_taken;
    }
    
    double avg_time = total_time / iterations;
    double samples_per_second = (processor->buffer_size * 1e9) / avg_time;
    double latency_ms = (avg_time / 1e6);
    
    printf("Rendimiento del procesador:\n");
    printf("  Tiempo promedio: %.2f ns\n", avg_time);
    printf("  Muestras por segundo: %.0f\n", samples_per_second);
    printf("  Latencia: %.3f ms\n", latency_ms);
    printf("  Factor de tiempo real: %.2fx\n", samples_per_second / 44100.0);
}

// Función principal
int main() {
    const int buffer_size = 1024;
    const int filter_order = 64;
    const int iterations = 1000;
    
    // Crear procesador optimizado
    optimized_audio_processor_t* processor = create_optimized_processor(buffer_size, filter_order);
    if (processor == NULL) {
        printf("Error: No se pudo crear el procesador\n");
        return 1;
    }
    
    // Ejecutar benchmark
    benchmark_processor(processor, iterations);
    
    // Limpiar
    free(processor->input_buffer);
    free(processor->output_buffer);
    free(processor->delay_line);
    free(processor->filter_coeffs);
    free(processor);
    
    return 0;
}
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Análisis de Rendimiento**: Herramientas de profiling, medición de tiempo
2. **Optimización de Algoritmos**: Bucles, operaciones matemáticas, filtros
3. **Optimizaciones de Compilador**: Flags, directivas, optimizaciones específicas
4. **Optimizaciones de Memoria**: Alineación, prefetching, pools de memoria
5. **Optimizaciones Específicas**: Procesamiento en bloques, vectorización SIMD
6. **FFT Optimizada**: Planes precalculados, optimizaciones automáticas
7. **Sistema Completo**: Procesador de audio optimizado con benchmark

## Próxima Clase

En la siguiente clase del Módulo 4 estudiaremos los **fundamentos de procesamiento digital de señales**, aplicando todos los conceptos de programación aprendidos.

---

## Navegación
- **Anterior**: [Clase 3.5: Verilog/VHDL Básico](clase-3-5-verilog-vhdl-basico.md)
- **Siguiente**: [Módulo 4 - Índice](../modulo-04-procesamiento-digital-senales/README.md)
- **Volver al**: [Índice Principal](../../README.md)
