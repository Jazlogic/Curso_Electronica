# Clase 6.7: Medición y Calibración

## 🎯 Objetivos de la Clase

Al finalizar esta clase, serás capaz de:

1. **Realizar mediciones precisas** de parámetros de audio
2. **Calibrar sistemas** de audio para óptimo rendimiento
3. **Analizar THD, IMD y ruido** en sistemas de audio
4. **Medir respuesta en frecuencia** y fase
5. **Implementar sistemas de calibración** automática

## 📚 Contenido Teórico

### 1. Fundamentos de Medición

#### 1.1 ¿Por qué es Importante la Medición?

La **medición** en sistemas de audio es esencial para:
- **Verificar especificaciones** de rendimiento
- **Identificar problemas** de calidad
- **Optimizar parámetros** del sistema
- **Asegurar consistencia** de producción
- **Mantener calidad** a lo largo del tiempo

**Tipos de medición:**
- **Medición de distorsión**: THD, IMD, distorsión de fase
- **Medición de ruido**: SNR, ruido de fondo, ruido de cuantización
- **Medición de frecuencia**: Respuesta en frecuencia, fase, grupo delay
- **Medición de dinámica**: Rango dinámico, compresión, limitación

#### 1.2 Parámetros de Medición

**THD (Total Harmonic Distortion)**:
- Distorsión armónica total
- Se mide en porcentaje o dB
- Objetivo: <0.01% para audio de alta calidad

**IMD (Intermodulation Distortion)**:
- Distorsión por intermodulación
- Se mide en porcentaje o dB
- Objetivo: <0.01% para audio de alta calidad

**SNR (Signal-to-Noise Ratio)**:
- Relación señal-ruido
- Se mide en dB
- Objetivo: >100 dB para audio profesional

### 2. Instrumentos de Medición

#### 2.1 Analizador de Audio

Un **analizador de audio** es el instrumento principal para mediciones de audio:

**Características:**
- Medición de THD+N
- Análisis espectral
- Medición de IMD
- Análisis de ruido
- Medición de respuesta en frecuencia

**Aplicaciones:**
- Verificación de especificaciones
- Análisis de calidad
- Diagnóstico de problemas
- Optimización de rendimiento

#### 2.2 Osciloscopio

Un **osciloscopio** proporciona visualización temporal de señales:

**Características:**
- Visualización temporal
- Medición de voltaje
- Medición de tiempo
- Análisis de transientes
- Medición de jitter

**Aplicaciones:**
- Verificación de señales
- Análisis de transientes
- Medición de jitter
- Diagnóstico de problemas

#### 2.3 Analizador de Espectro

Un **analizador de espectro** proporciona análisis en el dominio de la frecuencia:

**Características:**
- Análisis espectral
- Medición de armónicos
- Análisis de ruido
- Medición de modulación
- Análisis de interferencias

**Aplicaciones:**
- Análisis de ruido
- Identificación de interferencias
- Medición de armónicos
- Análisis de modulación

### 3. Medición de Distorsión

#### 3.1 THD (Total Harmonic Distortion)

**Método de medición:**
1. Aplicar señal senoidal pura
2. Medir armónicos generados
3. Calcular THD total
4. Expresar en porcentaje o dB

**Cálculo:**
```
THD = √(H2² + H3² + H4² + ... + Hn²) / H1 × 100%
```

**Factores que afectan THD:**
- No linealidades del amplificador
- Saturación de componentes
- Distorsión de fase
- Ruido de cuantización

#### 3.2 IMD (Intermodulation Distortion)

**Método de medición:**
1. Aplicar dos tonos de frecuencia
2. Medir productos de intermodulación
3. Calcular IMD total
4. Expresar en porcentaje o dB

**Cálculo:**
```
IMD = √(IM2² + IM3² + IM4² + ... + IMn²) / (F1 + F2) × 100%
```

**Factores que afectan IMD:**
- No linealidades del amplificador
- Saturación de componentes
- Distorsión de fase
- Ruido de cuantización

### 4. Medición de Ruido

#### 4.1 SNR (Signal-to-Noise Ratio)

**Método de medición:**
1. Medir nivel de señal
2. Medir nivel de ruido
3. Calcular relación
4. Expresar en dB

**Cálculo:**
```
SNR = 20 × log10(Signal / Noise)
```

**Factores que afectan SNR:**
- Ruido térmico
- Ruido de cuantización
- Ruido de alimentación
- Interferencias electromagnéticas

#### 4.2 Ruido de Fondo

**Método de medición:**
1. Desconectar señal de entrada
2. Medir ruido de salida
3. Analizar espectro de ruido
4. Identificar fuentes de ruido

**Factores que afectan ruido de fondo:**
- Ruido térmico
- Ruido de alimentación
- Interferencias electromagnéticas
- Ruido de componentes

### 5. Medición de Respuesta en Frecuencia

#### 5.1 Respuesta en Frecuencia

**Método de medición:**
1. Aplicar barrido de frecuencia
2. Medir respuesta de amplitud
3. Medir respuesta de fase
4. Analizar resultados

**Parámetros importantes:**
- Frecuencia de corte
- Pendiente de rolloff
- Ripple en la banda de paso
- Rechazo de banda de parada

#### 5.2 Respuesta de Fase

**Método de medición:**
1. Aplicar barrido de frecuencia
2. Medir retraso de fase
3. Calcular grupo delay
4. Analizar resultados

**Parámetros importantes:**
- Retraso de fase
- Grupo delay
- Linealidad de fase
- Distorsión de fase

### 6. Calibración de Sistemas

#### 6.1 Calibración de Amplificadores

**Procedimiento:**
1. Verificar alimentación
2. Calibrar ganancia
3. Calibrar offset
4. Verificar especificaciones

**Parámetros de calibración:**
- Ganancia de voltaje
- Ganancia de corriente
- Offset de entrada
- Offset de salida

#### 6.2 Calibración de Convertidores

**Procedimiento:**
1. Verificar referencia de voltaje
2. Calibrar ganancia
3. Calibrar offset
4. Verificar especificaciones

**Parámetros de calibración:**
- Ganancia de conversión
- Offset de conversión
- Linealidad
- Distorsión

## 💻 Implementación Práctica

### 1. Sistema de Medición

```c
// Configuración de medición
typedef struct {
    float sample_rate;
    int num_samples;
    float reference_voltage;
    bool calibration_active;
} measurement_config_t;

// Inicialización de medición
int measurement_init(measurement_config_t *config) {
    // Configurar frecuencia de muestreo
    // Configurar número de muestras
    // Configurar referencia de voltaje
    // Inicializar estado
    return 0;
}

// Medición de THD
float measure_thd(measurement_config_t *config) {
    // Aplicar señal senoidal
    // Medir armónicos
    // Calcular THD
    // Retornar resultado
    return thd_result;
}
```

### 2. Sistema de Calibración

```c
// Configuración de calibración
typedef struct {
    float target_gain;
    float target_offset;
    float tolerance;
    bool auto_calibration;
} calibration_config_t;

// Inicialización de calibración
int calibration_init(calibration_config_t *config) {
    // Configurar objetivos
    // Configurar tolerancias
    // Configurar modo automático
    // Inicializar estado
    return 0;
}

// Calibración automática
int auto_calibration(calibration_config_t *config) {
    // Medir parámetros actuales
    // Comparar con objetivos
    // Ajustar parámetros
    // Verificar resultados
    return 0;
}
```

### 3. Análisis de Ruido

```c
// Configuración de análisis de ruido
typedef struct {
    float noise_floor;
    float signal_level;
    float snr;
    bool noise_analysis_active;
} noise_analysis_config_t;

// Inicialización de análisis de ruido
int noise_analysis_init(noise_analysis_config_t *config) {
    // Configurar piso de ruido
    // Configurar nivel de señal
    // Configurar análisis
    // Inicializar estado
    return 0;
}

// Análisis de ruido
float analyze_noise(noise_analysis_config_t *config) {
    // Medir ruido de fondo
    // Medir nivel de señal
    // Calcular SNR
    // Retornar resultado
    return snr_result;
}
```

### 4. Medición de Respuesta en Frecuencia

```c
// Configuración de respuesta en frecuencia
typedef struct {
    float start_frequency;
    float end_frequency;
    int num_points;
    bool phase_measurement;
} frequency_response_config_t;

// Inicialización de respuesta en frecuencia
int frequency_response_init(frequency_response_config_t *config) {
    // Configurar rango de frecuencia
    // Configurar número de puntos
    // Configurar medición de fase
    // Inicializar estado
    return 0;
}

// Medición de respuesta en frecuencia
int measure_frequency_response(frequency_response_config_t *config) {
    // Aplicar barrido de frecuencia
    // Medir respuesta de amplitud
    // Medir respuesta de fase
    // Analizar resultados
    return 0;
}
```

## 🔬 Laboratorio Práctico

### Experimento 1: Medición de THD

**Objetivo**: Medir la distorsión armónica total de un amplificador

**Materiales**:
- Amplificador bajo prueba
- Analizador de audio
- Generador de señales
- Carga resistiva

**Procedimiento**:
1. Conectar generador de señales a entrada
2. Conectar carga a salida
3. Aplicar señal senoidal de 1 kHz
4. Medir THD con analizador
5. Registrar resultados

### Experimento 2: Medición de SNR

**Objetivo**: Medir la relación señal-ruido de un sistema

**Materiales**:
- Sistema bajo prueba
- Analizador de audio
- Generador de señales
- Carga resistiva

**Procedimiento**:
1. Conectar generador de señales a entrada
2. Conectar carga a salida
3. Aplicar señal de referencia
4. Medir SNR con analizador
5. Registrar resultados

### Experimento 3: Respuesta en Frecuencia

**Objetivo**: Medir la respuesta en frecuencia de un sistema

**Materiales**:
- Sistema bajo prueba
- Analizador de red
- Generador de barrido
- Carga resistiva

**Procedimiento**:
1. Conectar generador de barrido a entrada
2. Conectar carga a salida
3. Configurar barrido de frecuencia
4. Medir respuesta con analizador
5. Analizar resultados

## 📊 Medición y Análisis

### 1. Parámetros de Medición

**THD:**
- Se mide con analizador de audio
- Se expresa en porcentaje o dB
- Objetivo: <0.01% para audio de alta calidad

**SNR:**
- Se mide con analizador de audio
- Se expresa en dB
- Objetivo: >100 dB para audio profesional

**Respuesta en frecuencia:**
- Se mide con analizador de red
- Se expresa en dB vs frecuencia
- Objetivo: ±0.1 dB de 20 Hz a 20 kHz

### 2. Herramientas de Medición

**Analizador de Audio:**
- Medición de THD
- Medición de SNR
- Análisis de ruido
- Medición de IMD

**Analizador de Red:**
- Medición de respuesta en frecuencia
- Medición de fase
- Medición de impedancia
- Análisis de estabilidad

**Osciloscopio:**
- Visualización temporal
- Medición de voltaje
- Medición de tiempo
- Análisis de transientes

## 🎯 Aplicaciones en Audio

### 1. Audio Profesional

- **Estudios de grabación**: Medición y calibración de alta precisión
- **Broadcasting**: Medición y calibración de alta confiabilidad
- **Post-producción**: Medición y calibración de alta precisión
- **Mastering**: Medición y calibración de referencia

### 2. Audio de Consumo

- **Hi-Fi**: Medición y calibración de alta calidad
- **Gaming**: Medición y calibración de baja latencia
- **Streaming**: Medición y calibración eficiente
- **Móvil**: Medición y calibración de bajo consumo

### 3. Audio de Ultra-Baja Latencia

- **Música en vivo**: Medición y calibración de respuesta rápida
- **Grabación en tiempo real**: Medición y calibración transparente
- **Procesamiento en vivo**: Medición y calibración de baja latencia
- **Colaboración remota**: Medición y calibración de alta velocidad

## 🚀 Optimización para Ultra-Baja Latencia

### 1. Hardware

- **Instrumentos de medición de alta velocidad**
- **Sistemas de calibración automática**
- **Diseño optimizado**
- **Interconexión de alta velocidad**

### 2. Software

- **Medición en tiempo real**
- **Calibración automática**
- **Análisis continuo**
- **Optimización de código**

### 3. Sistema

- **Integración optimizada**
- **Monitoreo en tiempo real**
- **Control automático**
- **Calibración inteligente**

## 📝 Ejercicios Prácticos

### Ejercicio 1: Medición de THD
Mide la distorsión armónica total de un amplificador:
1. Configura el equipo de medición
2. Aplica señal de prueba
3. Mide THD
4. Analiza resultados

### Ejercicio 2: Calibración de Sistema
Calibra un sistema de audio completo:
1. Identifica parámetros a calibrar
2. Configura el sistema de calibración
3. Ejecuta calibración automática
4. Verifica resultados

### Ejercicio 3: Análisis de Ruido
Analiza el ruido en un sistema de audio:
1. Configura el análisis de ruido
2. Mide ruido de fondo
3. Identifica fuentes de ruido
4. Implementa mejoras

## 🎯 Resumen de la Clase

En esta clase hemos cubierto:

1. **Fundamentos** de medición en audio
2. **Instrumentos de medición** y sus aplicaciones
3. **Medición de distorsión** (THD, IMD)
4. **Medición de ruido** (SNR, ruido de fondo)
5. **Medición de respuesta en frecuencia** y fase
6. **Calibración de sistemas** de audio
7. **Implementación práctica** con código
8. **Aplicaciones** en audio profesional

## 🧭 Navegación

- **Anterior**: [Clase 6.6: Diseño de PCB](clase-6-6-diseno-pcb.md)
- **Siguiente**: [Clase 6.8: Integración de Sistemas](clase-6-8-integracion-sistemas.md)
- **Módulo**: [Módulo 6: Hardware para Audio](README.md)
- **Inicio**: [Curso Completo](../../README.md)

---

*Duración estimada: 4-5 horas*
