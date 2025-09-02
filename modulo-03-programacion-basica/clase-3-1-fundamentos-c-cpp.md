# Clase 3.1: Fundamentos de C/C++

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender los conceptos básicos de programación en C/C++
- Entender las variables, tipos de datos y operadores
- Conocer las estructuras de control (if, for, while)
- Aplicar funciones y punteros en el procesamiento de audio
- Entender la gestión de memoria para aplicaciones de tiempo real

## Introducción a C/C++

### ¿Por qué C/C++ para Audio?

**C/C++** es el lenguaje de programación preferido para aplicaciones de audio de ultra baja latencia porque:

1. **Rendimiento**: Código compilado nativo, sin interpretación
2. **Control**: Acceso directo al hardware y memoria
3. **Latencia**: Mínima sobrecarga del sistema
4. **Precisión**: Control exacto de tipos de datos
5. **Estándar**: Ampliamente usado en la industria del audio

### Diferencias entre C y C++

**C**:
- Lenguaje procedural
- Más simple y directo
- Ideal para DSP y procesamiento de señales

**C++**:
- Lenguaje orientado a objetos
- Más funcionalidades (clases, templates)
- Mejor para aplicaciones complejas

## Variables y Tipos de Datos

### Tipos Básicos

#### 1. Enteros
```c
int audio_sample = 32767;        // 32 bits, rango: -2^31 a 2^31-1
short sample_16bit = 16383;      // 16 bits, rango: -32768 a 32767
long long sample_64bit = 1000000; // 64 bits
```

#### 2. Punto Flotante
```c
float audio_float = 0.5f;        // 32 bits, precisión simple
double audio_double = 0.5;       // 64 bits, precisión doble
```

#### 3. Caracteres
```c
char audio_char = 'A';           // 8 bits
unsigned char audio_byte = 255;  // 8 bits sin signo
```

### Tipos Específicos para Audio

#### 1. Muestras de Audio
```c
// Para audio de 16 bits
typedef short audio_sample_t;

// Para audio de 24 bits (usando 32 bits)
typedef int audio_sample_24_t;

// Para audio de 32 bits
typedef int audio_sample_32_t;
```

#### 2. Frecuencias y Tiempos
```c
typedef unsigned int sample_rate_t;  // Frecuencia de muestreo
typedef unsigned int buffer_size_t;  // Tamaño de buffer
typedef double frequency_t;          // Frecuencia en Hz
```

### Constantes y Enumeraciones

```c
// Constantes para audio
#define SAMPLE_RATE_44_1K 44100
#define SAMPLE_RATE_48K   48000
#define SAMPLE_RATE_96K   96000

// Enumeración para tipos de audio
typedef enum {
    AUDIO_FORMAT_16BIT,
    AUDIO_FORMAT_24BIT,
    AUDIO_FORMAT_32BIT,
    AUDIO_FORMAT_FLOAT
} audio_format_t;
```

## Operadores

### Operadores Aritméticos

```c
int a = 10, b = 3;

int suma = a + b;        // 13
int resta = a - b;       // 7
int multiplicacion = a * b; // 30
int division = a / b;    // 3
int modulo = a % b;      // 1
```

### Operadores de Asignación

```c
int x = 5;
x += 3;    // x = x + 3 = 8
x -= 2;    // x = x - 2 = 6
x *= 2;    // x = x * 2 = 12
x /= 3;    // x = x / 3 = 4
```

### Operadores de Comparación

```c
int a = 5, b = 3;

bool igual = (a == b);        // false
bool diferente = (a != b);    // true
bool mayor = (a > b);         // true
bool menor = (a < b);         // false
bool mayor_igual = (a >= b);  // true
bool menor_igual = (a <= b);  // false
```

### Operadores Lógicos

```c
bool p = true, q = false;

bool and_logico = p && q;     // false
bool or_logico = p || q;      // true
bool not_logico = !p;         // false
```

## Estructuras de Control

### Condicionales

#### 1. if-else
```c
float audio_level = 0.8f;

if (audio_level > 1.0f) {
    printf("Audio saturado!\n");
} else if (audio_level > 0.7f) {
    printf("Audio alto\n");
} else if (audio_level > 0.3f) {
    printf("Audio medio\n");
} else {
    printf("Audio bajo\n");
}
```

#### 2. switch-case
```c
audio_format_t format = AUDIO_FORMAT_24BIT;

switch (format) {
    case AUDIO_FORMAT_16BIT:
        printf("Procesando audio 16-bit\n");
        break;
    case AUDIO_FORMAT_24BIT:
        printf("Procesando audio 24-bit\n");
        break;
    case AUDIO_FORMAT_32BIT:
        printf("Procesando audio 32-bit\n");
        break;
    default:
        printf("Formato no soportado\n");
        break;
}
```

### Bucles

#### 1. for
```c
// Procesar buffer de audio
const int buffer_size = 1024;
float audio_buffer[buffer_size];

for (int i = 0; i < buffer_size; i++) {
    audio_buffer[i] = audio_buffer[i] * 0.5f; // Reducir volumen
}
```

#### 2. while
```c
// Procesar hasta que se alcance un nivel
float audio_level = 1.0f;
float threshold = 0.1f;

while (audio_level > threshold) {
    audio_level *= 0.9f; // Reducir gradualmente
    printf("Nivel actual: %.2f\n", audio_level);
}
```

#### 3. do-while
```c
// Procesar al menos una vez
int sample_count = 0;
do {
    process_audio_sample();
    sample_count++;
} while (sample_count < 100);
```

## Funciones

### Definición de Funciones

```c
// Función para procesar una muestra de audio
float process_audio_sample(float input, float gain) {
    float output = input * gain;
    
    // Limitar el rango
    if (output > 1.0f) output = 1.0f;
    if (output < -1.0f) output = -1.0f;
    
    return output;
}
```

### Parámetros por Valor y Referencia

```c
// Por valor (copia)
void process_by_value(float sample) {
    sample = sample * 2.0f; // No afecta la variable original
}

// Por referencia (puntero)
void process_by_reference(float* sample) {
    *sample = *sample * 2.0f; // Modifica la variable original
}

// Por referencia (C++)
void process_by_reference_cpp(float& sample) {
    sample = sample * 2.0f; // Modifica la variable original
}
```

### Funciones para Audio

```c
// Aplicar ganancia a un buffer
void apply_gain(float* buffer, int size, float gain) {
    for (int i = 0; i < size; i++) {
        buffer[i] *= gain;
    }
}

// Calcular RMS de un buffer
float calculate_rms(float* buffer, int size) {
    float sum = 0.0f;
    for (int i = 0; i < size; i++) {
        sum += buffer[i] * buffer[i];
    }
    return sqrt(sum / size);
}

// Aplicar filtro pasa-bajo simple
void apply_lowpass_filter(float* buffer, int size, float alpha) {
    static float prev_sample = 0.0f;
    
    for (int i = 0; i < size; i++) {
        buffer[i] = alpha * buffer[i] + (1.0f - alpha) * prev_sample;
        prev_sample = buffer[i];
    }
}
```

## Punteros

### Conceptos Básicos

```c
int audio_sample = 1000;
int* ptr = &audio_sample;  // ptr apunta a audio_sample

printf("Valor: %d\n", audio_sample);    // 1000
printf("Dirección: %p\n", ptr);         // Dirección de memoria
printf("Valor via puntero: %d\n", *ptr); // 1000

*ptr = 2000;  // Modifica audio_sample
printf("Nuevo valor: %d\n", audio_sample); // 2000
```

### Punteros y Arrays

```c
float audio_buffer[1024];
float* ptr = audio_buffer;  // Equivalente a &audio_buffer[0]

// Acceso via array
audio_buffer[0] = 0.5f;
audio_buffer[1] = 0.3f;

// Acceso via puntero
*ptr = 0.5f;        // Equivalente a audio_buffer[0]
*(ptr + 1) = 0.3f;  // Equivalente a audio_buffer[1]
ptr[1] = 0.3f;      // También equivalente a audio_buffer[1]
```

### Aritmética de Punteros

```c
float* ptr = audio_buffer;

ptr++;      // Avanza al siguiente elemento
ptr += 10;  // Avanza 10 elementos
ptr--;      // Retrocede un elemento

// Calcular diferencia entre punteros
int difference = ptr - audio_buffer; // Número de elementos
```

## Gestión de Memoria

### Memoria Estática

```c
// Array estático
float static_buffer[1024];

// Variable estática (persiste entre llamadas)
float get_next_sample() {
    static float phase = 0.0f;
    phase += 0.01f;
    return sin(phase);
}
```

### Memoria Dinámica

```c
// Asignar memoria
int buffer_size = 1024;
float* dynamic_buffer = (float*)malloc(buffer_size * sizeof(float));

if (dynamic_buffer == NULL) {
    printf("Error: No se pudo asignar memoria\n");
    return;
}

// Usar la memoria
for (int i = 0; i < buffer_size; i++) {
    dynamic_buffer[i] = 0.0f;
}

// Liberar memoria
free(dynamic_buffer);
dynamic_buffer = NULL;
```

### Memoria Dinámica en C++

```cpp
// Usando new/delete
float* buffer = new float[1024];
// ... usar buffer ...
delete[] buffer;

// Usando smart pointers (C++11+)
std::unique_ptr<float[]> buffer(new float[1024]);
// Se libera automáticamente
```

## Estructuras

### Definición de Estructuras

```c
// Estructura para configuración de audio
typedef struct {
    int sample_rate;
    int channels;
    int bits_per_sample;
    int buffer_size;
} audio_config_t;

// Estructura para filtro
typedef struct {
    float alpha;
    float prev_sample;
    float cutoff_frequency;
} lowpass_filter_t;
```

### Uso de Estructuras

```c
// Crear e inicializar
audio_config_t config = {
    .sample_rate = 44100,
    .channels = 2,
    .bits_per_sample = 16,
    .buffer_size = 1024
};

// Acceder a miembros
printf("Sample rate: %d\n", config.sample_rate);

// Pasar a función
void process_audio_config(audio_config_t* config) {
    printf("Procesando %d canales a %d Hz\n", 
           config->channels, config->sample_rate);
}
```

## Ejemplo Práctico: Procesador de Audio Simple

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

// Estructura para el procesador
typedef struct {
    float gain;
    float cutoff_freq;
    float prev_sample;
    int sample_rate;
} audio_processor_t;

// Inicializar procesador
void init_processor(audio_processor_t* proc, float gain, float cutoff_freq, int sample_rate) {
    proc->gain = gain;
    proc->cutoff_freq = cutoff_freq;
    proc->prev_sample = 0.0f;
    proc->sample_rate = sample_rate;
}

// Procesar una muestra
float process_sample(audio_processor_t* proc, float input) {
    // Aplicar ganancia
    float output = input * proc->gain;
    
    // Aplicar filtro pasa-bajo simple
    float alpha = 2.0f * M_PI * proc->cutoff_freq / proc->sample_rate;
    output = alpha * output + (1.0f - alpha) * proc->prev_sample;
    proc->prev_sample = output;
    
    // Limitar
    if (output > 1.0f) output = 1.0f;
    if (output < -1.0f) output = -1.0f;
    
    return output;
}

// Procesar buffer completo
void process_buffer(audio_processor_t* proc, float* buffer, int size) {
    for (int i = 0; i < size; i++) {
        buffer[i] = process_sample(proc, buffer[i]);
    }
}

int main() {
    // Crear procesador
    audio_processor_t processor;
    init_processor(&processor, 0.8f, 1000.0f, 44100);
    
    // Crear buffer de prueba
    const int buffer_size = 1024;
    float* audio_buffer = (float*)malloc(buffer_size * sizeof(float));
    
    // Llenar con señal de prueba
    for (int i = 0; i < buffer_size; i++) {
        audio_buffer[i] = 0.5f * sin(2.0f * M_PI * 440.0f * i / 44100.0f);
    }
    
    // Procesar
    process_buffer(&processor, audio_buffer, buffer_size);
    
    // Mostrar algunos resultados
    for (int i = 0; i < 10; i++) {
        printf("Muestra %d: %.4f\n", i, audio_buffer[i]);
    }
    
    // Limpiar
    free(audio_buffer);
    
    return 0;
}
```

## Compilación y Ejecución

### Compilación Básica

```bash
# Compilar C
gcc -o audio_processor audio_processor.c -lm

# Compilar C++
g++ -o audio_processor audio_processor.cpp -lm

# Compilar con optimizaciones
gcc -O3 -o audio_processor audio_processor.c -lm
```

### Flags de Compilación Importantes

```bash
# Para audio en tiempo real
gcc -O3 -march=native -ffast-math -o audio_processor audio_processor.c -lm

# Para debugging
gcc -g -O0 -o audio_processor audio_processor.c -lm

# Para profiling
gcc -O2 -pg -o audio_processor audio_processor.c -lm
```

## Ejercicios Prácticos

### Ejercicio 1: Calculadora de Audio

```c
// Crear una función que calcule la frecuencia de una nota musical
float note_to_frequency(char note, int octave) {
    // Implementar la conversión de nota a frecuencia
    // A4 = 440 Hz, octava 4
}
```

### Ejercicio 2: Filtro de Audio

```c
// Implementar un filtro pasa-bajo de segundo orden
typedef struct {
    float a0, a1, a2;
    float b1, b2;
    float x1, x2, y1, y2;
} biquad_filter_t;

void init_biquad_lowpass(biquad_filter_t* filter, float freq, float q, int sample_rate);
float process_biquad(biquad_filter_t* filter, float input);
```

### Ejercicio 3: Generador de Tono

```c
// Crear un generador de tono sinusoidal
typedef struct {
    float frequency;
    float phase;
    float sample_rate;
} tone_generator_t;

float generate_tone(tone_generator_t* gen);
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Variables y Tipos**: Enteros, flotantes, tipos específicos para audio
2. **Operadores**: Aritméticos, lógicos, de comparación
3. **Estructuras de Control**: if-else, switch, for, while, do-while
4. **Funciones**: Definición, parámetros, funciones específicas para audio
5. **Punteros**: Conceptos básicos, aritmética, arrays
6. **Memoria**: Estática, dinámica, gestión
7. **Estructuras**: Definición, uso, aplicaciones en audio

## Próxima Clase

En la siguiente clase estudiaremos las **estructuras de datos y algoritmos**, fundamentales para el procesamiento eficiente de audio.

---

## Navegación
- **Anterior**: [Módulo 2 - Índice](../modulo-02-matematicas-audio/README.md)
- **Siguiente**: [Clase 3.2: Estructuras de Datos y Algoritmos](clase-3-2-estructuras-datos-algoritmos.md)
- **Volver al**: [Índice Principal](../../README.md)
