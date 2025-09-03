# Clase 6.1: Convertidores ADC/DAC

## 🎯 Objetivos de la Clase

Al finalizar esta clase, serás capaz de:

1. **Comprender los principios fundamentales** de la conversión analógico-digital y digital-analógico
2. **Analizar las diferentes arquitecturas** de convertidores ADC/DAC
3. **Evaluar parámetros críticos** como resolución, frecuencia de muestreo y jitter
4. **Diseñar circuitos de acondicionamiento** para convertidores
5. **Implementar soluciones de ultra-baja latencia** para aplicaciones de audio

## 📚 Contenido Teórico

### 1. Fundamentos de la Conversión

#### 1.1 ¿Qué es un Convertidor ADC?

Un **Convertidor Analógico-Digital (ADC)** es un dispositivo que transforma una señal analógica continua en una representación digital discreta. En audio, esto significa convertir las ondas sonoras en números que una computadora puede procesar.

**Proceso de conversión:**
1. **Muestreo (Sampling)**: Tomar muestras de la señal analógica a intervalos regulares
2. **Cuantización (Quantization)**: Asignar cada muestra a un valor digital discreto
3. **Codificación (Encoding)**: Representar el valor cuantizado en formato binario

#### 1.2 ¿Qué es un Convertidor DAC?

Un **Convertidor Digital-Analógico (DAC)** realiza el proceso inverso: convierte datos digitales en señales analógicas continuas.

**Proceso de conversión:**
1. **Decodificación**: Interpretar los datos digitales
2. **Reconstrucción**: Generar la señal analógica correspondiente
3. **Filtrado**: Suavizar la señal para eliminar artefactos de muestreo

### 2. Parámetros Críticos

#### 2.1 Resolución

La **resolución** determina la precisión de la conversión:

- **Bits**: Número de bits utilizados para representar cada muestra
- **Niveles**: 2^n niveles de cuantización (donde n = número de bits)
- **Rango dinámico**: 6.02 × n + 1.76 dB (teórico)

**Ejemplos de resolución:**
- 16-bit: 65,536 niveles, ~96 dB de rango dinámico
- 24-bit: 16,777,216 niveles, ~144 dB de rango dinámico
- 32-bit: 4,294,967,296 niveles, ~192 dB de rango dinámico

#### 2.2 Frecuencia de Muestreo

La **frecuencia de muestreo (fs)** determina qué frecuencias pueden ser capturadas:

- **Teorema de Nyquist**: fs ≥ 2 × fmax
- **Frecuencias estándar**: 44.1 kHz, 48 kHz, 96 kHz, 192 kHz
- **Oversampling**: Muestreo a frecuencias superiores para mejorar la calidad

#### 2.3 Jitter

El **jitter** es la variación temporal en el momento de muestreo:

- **Efectos**: Distorsión, ruido, pérdida de claridad
- **Fuentes**: Osciladores inestables, ruido en el reloj
- **Especificaciones**: Picosegundos (ps) de jitter RMS

### 3. Arquitecturas de Convertidores

#### 3.1 ADC - Arquitecturas Comunes

**ADC Delta-Sigma (ΣΔ):**
- Alta resolución (24-32 bits)
- Bajo ruido
- Requiere filtrado digital
- Ideal para audio de alta calidad

**ADC SAR (Successive Approximation):**
- Velocidad media
- Resolución media (12-18 bits)
- Bajo consumo
- Bueno para control y medición

**ADC Pipeline:**
- Alta velocidad
- Resolución media
- Alto consumo
- Usado en aplicaciones de alta velocidad

#### 3.2 DAC - Arquitecturas Comunes

**DAC R-2R:**
- Arquitectura clásica
- Buena linealidad
- Requiere componentes precisos
- Usado en aplicaciones de alta precisión

**DAC Delta-Sigma:**
- Alta resolución
- Bajo ruido
- Requiere filtrado
- Estándar en audio profesional

**DAC Current-Steering:**
- Alta velocidad
- Buena linealidad
- Alto consumo
- Usado en aplicaciones de alta velocidad

### 4. Circuitos de Acondicionamiento

#### 4.1 Buffer de Entrada

Un **buffer** proporciona impedancia de entrada alta y baja impedancia de salida:

```c
// Ejemplo de buffer con op-amp
// Entrada: señal analógica
// Salida: señal acondicionada para ADC
```

**Características importantes:**
- Impedancia de entrada: >1 MΩ
- Impedancia de salida: <100 Ω
- Ancho de banda: >fs/2
- Distorsión: <0.001%

#### 4.2 Filtro Anti-Aliasing

El **filtro anti-aliasing** elimina frecuencias superiores a fs/2:

- **Tipo**: Filtro paso-bajo
- **Frecuencia de corte**: fs/2
- **Pendiente**: >60 dB/octava
- **Implementación**: Activo o pasivo

#### 4.3 Amplificador de Salida

El **amplificador de salida** del DAC debe:

- Proporcionar suficiente corriente
- Mantener baja distorsión
- Tener ancho de banda adecuado
- Ser estable con cargas capacitivas

## 💻 Implementación Práctica

### 1. Diseño de un ADC de 24-bit

```c
// Configuración de ADC de 24-bit
#include <stdint.h>

typedef struct {
    uint32_t sample_rate;
    uint8_t resolution;
    uint8_t channels;
    float reference_voltage;
} adc_config_t;

// Función de inicialización
int adc_init(adc_config_t *config) {
    // Configurar reloj de muestreo
    // Configurar resolución
    // Configurar canales
    // Configurar referencia de voltaje
    return 0;
}

// Función de lectura
int32_t adc_read_channel(uint8_t channel) {
    // Iniciar conversión
    // Esperar conversión completa
    // Leer resultado
    // Retornar valor de 24-bit
    return 0;
}
```

### 2. Diseño de un DAC de 24-bit

```c
// Configuración de DAC de 24-bit
typedef struct {
    uint32_t sample_rate;
    uint8_t resolution;
    uint8_t channels;
    float reference_voltage;
} dac_config_t;

// Función de inicialización
int dac_init(dac_config_t *config) {
    // Configurar reloj de muestreo
    // Configurar resolución
    // Configurar canales
    // Configurar referencia de voltaje
    return 0;
}

// Función de escritura
int dac_write_channel(uint8_t channel, int32_t value) {
    // Validar valor
    // Escribir al DAC
    // Actualizar salida
    return 0;
}
```

### 3. Sistema de Control de Latencia

```c
// Control de latencia ultra-baja
typedef struct {
    uint32_t buffer_size;
    uint32_t sample_rate;
    float latency_ms;
} latency_config_t;

// Calcular latencia
float calculate_latency(latency_config_t *config) {
    return (float)config->buffer_size / config->sample_rate * 1000.0;
}

// Optimizar para latencia mínima
int optimize_latency(latency_config_t *config) {
    // Minimizar buffer
    // Maximizar frecuencia de muestreo
    // Optimizar procesamiento
    return 0;
}
```

## 🔬 Laboratorio Práctico

### Experimento 1: Análisis de Resolución

**Objetivo**: Comparar diferentes resoluciones de ADC

**Materiales**:
- ADC de 16-bit
- ADC de 24-bit
- Generador de señales
- Osciloscopio
- Analizador de espectro

**Procedimiento**:
1. Generar señal senoidal de 1 kHz
2. Convertir con ADC de 16-bit
3. Convertir con ADC de 24-bit
4. Comparar ruido de cuantización
5. Medir THD+N

### Experimento 2: Análisis de Jitter

**Objetivo**: Medir y analizar el jitter en convertidores

**Materiales**:
- Convertidor con jitter controlable
- Analizador de espectro
- Oscilador de referencia

**Procedimiento**:
1. Configurar convertidor con jitter mínimo
2. Medir espectro de salida
3. Introducir jitter controlado
4. Observar cambios en el espectro
5. Cuantificar degradación

## 📊 Medición y Análisis

### 1. Parámetros de Medición

**THD+N (Total Harmonic Distortion + Noise)**:
- Distorsión armónica total + ruido
- Especificación clave para audio
- Se mide en dB o porcentaje

**SNR (Signal-to-Noise Ratio)**:
- Relación señal-ruido
- Determina rango dinámico
- Se mide en dB

**IMD (Intermodulation Distortion)**:
- Distorsión por intermodulación
- Resultado de no linealidades
- Se mide con tonos múltiples

### 2. Herramientas de Medición

**Analizador de Audio**:
- Medición de THD+N
- Análisis espectral
- Medición de IMD
- Análisis de jitter

**Osciloscopio**:
- Visualización temporal
- Medición de jitter
- Análisis de ruido
- Verificación de señales

## 🎯 Aplicaciones en Audio

### 1. Audio Profesional

- **Estudios de grabación**: 24-bit/192 kHz
- **Broadcasting**: 24-bit/48 kHz
- **Post-producción**: 32-bit/192 kHz
- **Mastering**: 32-bit/384 kHz

### 2. Audio de Consumo

- **Hi-Fi**: 24-bit/192 kHz
- **Gaming**: 24-bit/96 kHz
- **Streaming**: 16-bit/44.1 kHz
- **Móvil**: 24-bit/48 kHz

### 3. Audio de Ultra-Baja Latencia

- **Música en vivo**: <1 ms de latencia
- **Grabación en tiempo real**: <0.5 ms
- **Procesamiento en vivo**: <0.1 ms
- **Colaboración remota**: <5 ms total

## 🚀 Optimización para Ultra-Baja Latencia

### 1. Hardware

- **Convertidores de alta velocidad**
- **Buffers mínimos**
- **Procesamiento paralelo**
- **Interconexión de alta velocidad**

### 2. Software

- **Kernels de tiempo real**
- **Prioridad de procesos**
- **Memoria dedicada**
- **Optimización de código**

### 3. Red

- **Protocolos de baja latencia**
- **Buffering mínimo**
- **Compresión eficiente**
- **Sincronización precisa**

## 📝 Ejercicios Prácticos

### Ejercicio 1: Diseño de ADC
Diseña un ADC de 24-bit/192 kHz para audio profesional:
1. Selecciona la arquitectura
2. Calcula los parámetros críticos
3. Diseña el circuito de acondicionamiento
4. Especifica los componentes

### Ejercicio 2: Análisis de Rendimiento
Analiza el rendimiento de un convertidor existente:
1. Mide THD+N
2. Analiza el espectro de ruido
3. Evalúa la respuesta en frecuencia
4. Identifica limitaciones

### Ejercicio 3: Optimización de Latencia
Optimiza un sistema para latencia mínima:
1. Identifica cuellos de botella
2. Minimiza buffers
3. Optimiza procesamiento
4. Mide latencia resultante

## 🎯 Resumen de la Clase

En esta clase hemos cubierto:

1. **Fundamentos** de convertidores ADC/DAC
2. **Parámetros críticos** para audio de alta calidad
3. **Arquitecturas** de convertidores
4. **Circuitos de acondicionamiento**
5. **Implementación práctica** con código
6. **Medición y análisis** de rendimiento
7. **Aplicaciones** en audio profesional
8. **Optimización** para ultra-baja latencia

## 🧭 Navegación

- **Anterior**: [Clase 5.8: Implementación en Hardware](../modulo-05-procesamiento-digital-senales/clase-5-8-implementacion-hardware.md)
- **Siguiente**: [Clase 6.2: Amplificadores de Audio](clase-6-2-amplificadores-audio.md)
- **Módulo**: [Módulo 6: Hardware para Audio](README.md)
- **Inicio**: [Curso Completo](../../README.md)

---

*Duración estimada: 4-5 horas*
