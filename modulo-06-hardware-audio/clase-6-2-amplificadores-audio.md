# Clase 6.2: Amplificadores de Audio

## 🎯 Objetivos de la Clase

Al finalizar esta clase, serás capaz de:

1. **Comprender las diferentes clases** de amplificación y sus aplicaciones
2. **Diseñar amplificadores de alta fidelidad** con baja distorsión
3. **Analizar y minimizar el ruido** en amplificadores
4. **Implementar sistemas de protección** para amplificadores
5. **Optimizar la estabilidad** y respuesta en frecuencia

## 📚 Contenido Teórico

### 1. Fundamentos de Amplificación

#### 1.1 ¿Qué es un Amplificador de Audio?

Un **amplificador de audio** es un dispositivo que aumenta la amplitud de una señal de audio manteniendo su forma de onda. En aplicaciones de ultra-baja latencia, el amplificador debe ser transparente y no introducir retrasos significativos.

**Características principales:**
- **Ganancia**: Factor de amplificación (dB o veces)
- **Ancho de banda**: Rango de frecuencias que puede amplificar
- **Distorsión**: Alteración de la forma de onda
- **Ruido**: Señales no deseadas añadidas
- **Eficiencia**: Relación entre potencia de salida y consumo

#### 1.2 Parámetros Críticos

**THD (Total Harmonic Distortion)**:
- Distorsión armónica total
- Se mide en porcentaje o dB
- Objetivo: <0.01% para audio de alta calidad

**SNR (Signal-to-Noise Ratio)**:
- Relación señal-ruido
- Se mide en dB
- Objetivo: >100 dB para audio profesional

**Slew Rate**:
- Velocidad de cambio de voltaje
- Se mide en V/μs
- Crítico para señales de alta frecuencia

### 2. Clases de Amplificación

#### 2.1 Clase A

**Características:**
- El transistor conduce durante todo el ciclo
- Distorsión muy baja
- Eficiencia baja (~25%)
- Alto consumo de energía
- Calentamiento significativo

**Aplicaciones:**
- Audio de alta fidelidad
- Preamplificadores
- Amplificadores de instrumentos

**Circuito básico:**
```
Vcc
 |
 R1
 |
 +---> Salida
 |
 Q1 (NPN)
 |
 R2
 |
 GND
```

#### 2.2 Clase B

**Características:**
- Dos transistores (push-pull)
- Cada transistor conduce medio ciclo
- Eficiencia media (~50%)
- Distorsión de cruce (crossover)
- Menor consumo que Clase A

**Aplicaciones:**
- Amplificadores de potencia
- Sistemas de audio de consumo
- Amplificadores de bajo costo

#### 2.3 Clase AB

**Características:**
- Combinación de Clase A y B
- Pequeña corriente de polarización
- Distorsión de cruce mínima
- Eficiencia media-alta (~60%)
- Balance entre calidad y eficiencia

**Aplicaciones:**
- Amplificadores de audio profesionales
- Sistemas de sonido en vivo
- Amplificadores de instrumentos

#### 2.4 Clase D

**Características:**
- Modulación por ancho de pulso (PWM)
- Eficiencia muy alta (~90%)
- Requiere filtrado de salida
- Bajo consumo de energía
- Menor tamaño y peso

**Aplicaciones:**
- Amplificadores de potencia modernos
- Sistemas de audio portátiles
- Amplificadores de subwoofer

### 3. Diseño de Amplificadores

#### 3.1 Amplificador Operacional

Los **amplificadores operacionales (op-amps)** son la base de muchos amplificadores de audio:

**Parámetros importantes:**
- **Ganancia de lazo abierto**: >100,000
- **Ancho de banda**: >1 MHz
- **Slew rate**: >10 V/μs
- **Ruido**: <10 nV/√Hz
- **THD**: <0.001%

**Configuraciones comunes:**

**Amplificador Inversor:**
```
Rf
 |
 +---> Salida
 |
 R1
 |
 +---> Entrada
 |
 GND
```

**Amplificador No Inversor:**
```
+---> Entrada
 |
 R1
 |
 +---> Salida
 |
 Rf
 |
 GND
```

#### 3.2 Amplificador de Potencia

**Etapas de un amplificador de potencia:**

1. **Etapa de entrada**: Amplificación de voltaje
2. **Etapa de ganancia**: Amplificación adicional
3. **Etapa de salida**: Amplificación de corriente
4. **Protección**: Circuitos de seguridad

**Diseño de etapa de salida:**
```
Vcc
 |
 Q1 (NPN)
 |
 +---> Salida
 |
 Q2 (PNP)
 |
 -Vcc
```

### 4. Minimización de Ruido

#### 4.1 Fuentes de Ruido

**Ruido térmico (Johnson)**:
- Causado por movimiento térmico de electrones
- Proporcional a la temperatura
- Inversamente proporcional a la resistencia

**Ruido de disparo (Shot)**:
- Causado por naturaleza discreta de la corriente
- Proporcional a la corriente
- Independiente de la frecuencia

**Ruido 1/f (Flicker)**:
- Aumenta a bajas frecuencias
- Causado por imperfecciones en semiconductores
- Dependiente del material

#### 4.2 Técnicas de Reducción de Ruido

**Selección de componentes:**
- Resistencias de bajo ruido
- Transistores de bajo ruido
- Op-amps especializados

**Diseño de circuito:**
- Minimizar resistencias
- Usar retroalimentación negativa
- Blindaje electromagnético
- Filtrado de alimentación

### 5. Sistemas de Protección

#### 5.1 Protección contra Sobrecarga

**Limitador de corriente:**
```c
// Protección de corriente
if (output_current > max_current) {
    reduce_gain();
    activate_protection();
}
```

**Protección térmica:**
```c
// Protección térmica
if (temperature > max_temperature) {
    shutdown_amplifier();
    activate_cooling();
}
```

#### 5.2 Protección de Altavoces

**Limitador de potencia:**
- Detecta sobrecarga
- Reduce ganancia automáticamente
- Protege altavoces de daño

**Protección DC:**
- Detecta voltaje DC en salida
- Desconecta altavoces
- Previene daño por corriente continua

## 💻 Implementación Práctica

### 1. Amplificador de Audio con Op-Amp

```c
// Configuración de amplificador
typedef struct {
    float gain;
    float bandwidth;
    float input_impedance;
    float output_impedance;
    float thd;
    float noise_level;
} amplifier_config_t;

// Inicialización del amplificador
int amplifier_init(amplifier_config_t *config) {
    // Configurar ganancia
    // Configurar ancho de banda
    // Configurar impedancias
    // Calibrar parámetros
    return 0;
}

// Procesamiento de señal
float amplifier_process(float input_sample) {
    // Aplicar ganancia
    // Aplicar filtros
    // Limitar salida
    return output_sample;
}
```

### 2. Sistema de Protección

```c
// Sistema de protección
typedef struct {
    float max_current;
    float max_temperature;
    float max_voltage;
    bool protection_active;
} protection_system_t;

// Monitoreo de protección
int monitor_protection(protection_system_t *prot) {
    // Medir corriente
    // Medir temperatura
    // Medir voltaje
    // Activar protección si es necesario
    return 0;
}

// Activación de protección
void activate_protection(protection_system_t *prot) {
    prot->protection_active = true;
    // Reducir ganancia
    // Activar enfriamiento
    // Notificar al usuario
}
```

### 3. Control de Ganancia Digital

```c
// Control de ganancia
typedef struct {
    float gain_db;
    float gain_linear;
    bool mute;
    bool bypass;
} gain_control_t;

// Aplicar ganancia
float apply_gain(float input, gain_control_t *control) {
    if (control->mute) return 0.0;
    if (control->bypass) return input;
    
    return input * control->gain_linear;
}

// Cambiar ganancia
int set_gain(gain_control_t *control, float gain_db) {
    control->gain_db = gain_db;
    control->gain_linear = pow(10.0, gain_db / 20.0);
    return 0;
}
```

## 🔬 Laboratorio Práctico

### Experimento 1: Medición de THD

**Objetivo**: Medir la distorsión armónica total de un amplificador

**Materiales**:
- Amplificador bajo prueba
- Generador de señales
- Analizador de distorsión
- Carga resistiva

**Procedimiento**:
1. Conectar generador de señales a entrada
2. Conectar carga a salida
3. Aplicar señal senoidal de 1 kHz
4. Medir THD a diferentes niveles
5. Registrar resultados

### Experimento 2: Análisis de Ruido

**Objetivo**: Analizar el ruido en un amplificador

**Materiales**:
- Amplificador bajo prueba
- Analizador de espectro
- Carga resistiva
- Blindaje

**Procedimiento**:
1. Conectar entrada a tierra
2. Conectar carga a salida
3. Medir espectro de ruido
4. Identificar fuentes de ruido
5. Implementar mejoras

### Experimento 3: Respuesta en Frecuencia

**Objetivo**: Medir la respuesta en frecuencia del amplificador

**Materiales**:
- Amplificador bajo prueba
- Generador de barrido
- Analizador de red
- Carga resistiva

**Procedimiento**:
1. Configurar barrido de frecuencia
2. Medir respuesta en frecuencia
3. Identificar resonancias
4. Optimizar respuesta
5. Documentar resultados

## 📊 Medición y Análisis

### 1. Parámetros de Medición

**THD (Total Harmonic Distortion)**:
- Se mide con analizador de distorsión
- Se expresa en porcentaje o dB
- Objetivo: <0.01% para audio de alta calidad

**SNR (Signal-to-Noise Ratio)**:
- Se mide con analizador de espectro
- Se expresa en dB
- Objetivo: >100 dB para audio profesional

**Respuesta en Frecuencia**:
- Se mide con analizador de red
- Se expresa en dB vs frecuencia
- Objetivo: ±0.1 dB de 20 Hz a 20 kHz

### 2. Herramientas de Medición

**Analizador de Audio**:
- Medición de THD
- Análisis de ruido
- Medición de IMD
- Análisis de respuesta

**Osciloscopio**:
- Visualización temporal
- Medición de slew rate
- Análisis de distorsión
- Verificación de señales

**Analizador de Espectro**:
- Análisis de ruido
- Identificación de interferencias
- Medición de armónicos
- Análisis de modulación

## 🎯 Aplicaciones en Audio

### 1. Audio Profesional

- **Estudios de grabación**: Amplificadores de alta fidelidad
- **Broadcasting**: Amplificadores de alta confiabilidad
- **Post-producción**: Amplificadores de precisión
- **Mastering**: Amplificadores de referencia

### 2. Audio de Consumo

- **Hi-Fi**: Amplificadores de alta calidad
- **Gaming**: Amplificadores de baja latencia
- **Streaming**: Amplificadores eficientes
- **Móvil**: Amplificadores de bajo consumo

### 3. Audio de Ultra-Baja Latencia

- **Música en vivo**: Amplificadores de respuesta rápida
- **Grabación en tiempo real**: Amplificadores transparentes
- **Procesamiento en vivo**: Amplificadores de baja latencia
- **Colaboración remota**: Amplificadores de alta velocidad

## 🚀 Optimización para Ultra-Baja Latencia

### 1. Hardware

- **Amplificadores de alta velocidad**
- **Componentes de baja latencia**
- **Diseño optimizado**
- **Interconexión de alta velocidad**

### 2. Software

- **Procesamiento en tiempo real**
- **Buffering mínimo**
- **Prioridad de procesos**
- **Optimización de código**

### 3. Sistema

- **Integración optimizada**
- **Monitoreo en tiempo real**
- **Control automático**
- **Protección inteligente**

## 📝 Ejercicios Prácticos

### Ejercicio 1: Diseño de Amplificador
Diseña un amplificador de audio de 100W con THD <0.01%:
1. Selecciona la clase de amplificación
2. Calcula los parámetros del circuito
3. Selecciona los componentes
4. Simula el rendimiento

### Ejercicio 2: Análisis de Ruido
Analiza y minimiza el ruido en un amplificador:
1. Identifica fuentes de ruido
2. Implementa técnicas de reducción
3. Mide la mejora
4. Documenta los resultados

### Ejercicio 3: Sistema de Protección
Implementa un sistema de protección completo:
1. Diseña circuitos de protección
2. Implementa monitoreo
3. Desarrolla software de control
4. Prueba el sistema

## 🎯 Resumen de la Clase

En esta clase hemos cubierto:

1. **Fundamentos** de amplificación de audio
2. **Clases de amplificación** y sus aplicaciones
3. **Diseño de amplificadores** de alta fidelidad
4. **Minimización de ruido** y distorsión
5. **Sistemas de protección** para amplificadores
6. **Implementación práctica** con código
7. **Medición y análisis** de rendimiento
8. **Aplicaciones** en audio profesional

## 🧭 Navegación

- **Anterior**: [Clase 6.1: Convertidores ADC/DAC](clase-6-1-convertidores-adc-dac.md)
- **Siguiente**: [Clase 6.3: Circuitos de Interfaz](clase-6-3-circuitos-interfaz.md)
- **Módulo**: [Módulo 6: Hardware para Audio](README.md)
- **Inicio**: [Curso Completo](../../README.md)

---

*Duración estimada: 4-5 horas*
