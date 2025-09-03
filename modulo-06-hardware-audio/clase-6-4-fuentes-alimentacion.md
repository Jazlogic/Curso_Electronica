# Clase 6.4: Fuentes de Alimentación

## 🎯 Objetivos de la Clase

Al finalizar esta clase, serás capaz de:

1. **Diseñar fuentes de alimentación** estables y de bajo ruido para audio
2. **Implementar reguladores lineales** y conmutados
3. **Minimizar el ruido** en fuentes de alimentación
4. **Optimizar la eficiencia** y estabilidad
5. **Implementar protección** y monitoreo de fuentes

## 📚 Contenido Teórico

### 1. Fundamentos de Fuentes de Alimentación

#### 1.1 ¿Qué es una Fuente de Alimentación?

Una **fuente de alimentación** es un sistema que convierte la energía eléctrica de la red (AC) en voltajes DC estables y regulados para alimentar circuitos de audio. En aplicaciones de ultra-baja latencia, la fuente debe ser extremadamente estable y de bajo ruido.

**Características principales:**
- **Estabilidad**: Voltaje constante bajo diferentes cargas
- **Ruido**: Nivel mínimo de ruido y ripple
- **Eficiencia**: Máxima conversión de energía
- **Protección**: Protección contra sobrecarga y cortocircuito
- **Regulación**: Mantener voltaje constante

#### 1.2 Parámetros Críticos

**Ripple (Ondulación)**:
- Variación del voltaje de salida
- Se mide en mV pico a pico
- Objetivo: <1 mV para audio de alta calidad

**Regulación de línea**:
- Cambio de voltaje con variación de entrada
- Se expresa en porcentaje
- Objetivo: <0.01% para audio profesional

**Regulación de carga**:
- Cambio de voltaje con variación de carga
- Se expresa en porcentaje
- Objetivo: <0.01% para audio profesional

### 2. Tipos de Fuentes de Alimentación

#### 2.1 Fuentes Lineales

**Características:**
- Regulación por transistor en serie
- Bajo ruido y ripple
- Alta estabilidad
- Baja eficiencia (~50%)
- Alto consumo de energía

**Aplicaciones:**
- Audio de alta fidelidad
- Instrumentación de precisión
- Amplificadores de alta calidad
- Sistemas de referencia

**Circuito básico:**
```
AC Input ---> Transformer ---> Rectifier ---> Filter ---> Regulator ---> DC Output
```

#### 2.2 Fuentes Conmutadas (SMPS)

**Características:**
- Regulación por conmutación de alta frecuencia
- Alta eficiencia (~90%)
- Bajo consumo de energía
- Mayor ruido y ripple
- Menor tamaño y peso

**Aplicaciones:**
- Audio de consumo
- Sistemas portátiles
- Amplificadores de potencia
- Sistemas de bajo costo

**Circuito básico:**
```
AC Input ---> Rectifier ---> High-Freq Switch ---> Transformer ---> Filter ---> DC Output
```

### 3. Reguladores Lineales

#### 3.1 Regulador Serie

Un **regulador serie** usa un transistor en serie con la carga:

**Características:**
- Transistor en serie
- Retroalimentación negativa
- Alta estabilidad
- Baja eficiencia
- Bajo ruido

**Implementación:**
```
Vinput ---> Q1 (Series Transistor) ---> Voutput
              |
              R1
              |
              +---<---+
              |      |
              R2     R2
              |      |
             GND    GND
```

#### 3.2 Regulador Shunt

Un **regulador shunt** usa un transistor en paralelo con la carga:

**Características:**
- Transistor en paralelo
- Retroalimentación negativa
- Alta estabilidad
- Baja eficiencia
- Bajo ruido

**Implementación:**
```
Vinput ---> R1 ---> Voutput
              |
              Q1 (Shunt Transistor)
              |
             GND
```

### 4. Reguladores Conmutados

#### 4.1 Buck Converter

Un **conversor buck** reduce el voltaje de entrada:

**Características:**
- Reducción de voltaje
- Alta eficiencia
- Bajo ruido
- Fácil implementación

**Implementación:**
```
Vinput ---> L1 ---> D1 ---> Voutput
              |
              Q1 (Switch)
              |
             GND
```

#### 4.2 Boost Converter

Un **conversor boost** aumenta el voltaje de entrada:

**Características:**
- Aumento de voltaje
- Alta eficiencia
- Bajo ruido
- Fácil implementación

**Implementación:**
```
Vinput ---> L1 ---> Q1 ---> Voutput
              |
              D1
              |
             GND
```

### 5. Filtrado y Estabilización

#### 5.1 Filtros de Entrada

**Filtros EMI:**
- Eliminan interferencias de la red
- Protegen contra ruido
- Mejoran la compatibilidad
- Reducen emisiones

**Filtros de entrada:**
- Suavizan la entrada AC
- Reducen picos de voltaje
- Mejoran la estabilidad
- Protegen componentes

#### 5.2 Filtros de Salida

**Filtros LC:**
- Inductor y capacitor
- Filtrado de alta frecuencia
- Bajo ruido
- Alta estabilidad

**Filtros RC:**
- Resistor y capacitor
- Filtrado de baja frecuencia
- Bajo ruido
- Alta estabilidad

### 6. Protección y Monitoreo

#### 6.1 Protección contra Sobrecarga

**Limitador de corriente:**
- Detecta sobrecarga
- Limita corriente de salida
- Protege componentes
- Permite recuperación automática

**Protección térmica:**
- Detecta sobrecalentamiento
- Desconecta la fuente
- Protege componentes
- Permite recuperación automática

#### 6.2 Monitoreo de Estado

**Monitoreo de voltaje:**
- Detecta variaciones de voltaje
- Activa alarmas
- Registra eventos
- Permite diagnóstico

**Monitoreo de corriente:**
- Detecta variaciones de corriente
- Activa alarmas
- Registra eventos
- Permite diagnóstico

## 💻 Implementación Práctica

### 1. Regulador Lineal

```c
// Configuración de regulador
typedef struct {
    float output_voltage;
    float max_current;
    float ripple;
    float regulation;
    bool protection_active;
} regulator_config_t;

// Inicialización del regulador
int regulator_init(regulator_config_t *config) {
    // Configurar voltaje de salida
    // Configurar límites de corriente
    // Configurar protección
    // Calibrar parámetros
    return 0;
}

// Control del regulador
int regulator_control(regulator_config_t *config) {
    // Medir voltaje de salida
    // Medir corriente de salida
    // Ajustar regulación
    // Activar protección si es necesario
    return 0;
}
```

### 2. Regulador Conmutado

```c
// Configuración de SMPS
typedef struct {
    float output_voltage;
    float max_current;
    float switching_frequency;
    float efficiency;
    bool protection_active;
} smps_config_t;

// Inicialización del SMPS
int smps_init(smps_config_t *config) {
    // Configurar voltaje de salida
    // Configurar frecuencia de conmutación
    // Configurar límites de corriente
    // Calibrar parámetros
    return 0;
}

// Control del SMPS
int smps_control(smps_config_t *config) {
    // Medir voltaje de salida
    // Medir corriente de salida
    // Ajustar ciclo de trabajo
    // Activar protección si es necesario
    return 0;
}
```

### 3. Sistema de Protección

```c
// Sistema de protección
typedef struct {
    float max_voltage;
    float max_current;
    float max_temperature;
    bool protection_active;
    bool auto_recovery;
} protection_system_t;

// Monitoreo de protección
int monitor_protection(protection_system_t *prot) {
    // Medir voltaje
    // Medir corriente
    // Medir temperatura
    // Activar protección si es necesario
    return 0;
}

// Activación de protección
void activate_protection(protection_system_t *prot) {
    prot->protection_active = true;
    // Desconectar salida
    // Activar enfriamiento
    // Notificar al usuario
}
```

### 4. Monitoreo de Estado

```c
// Sistema de monitoreo
typedef struct {
    float output_voltage;
    float output_current;
    float input_voltage;
    float temperature;
    bool status_ok;
} monitoring_system_t;

// Monitoreo continuo
int continuous_monitoring(monitoring_system_t *monitor) {
    // Medir parámetros
    // Evaluar estado
    // Registrar eventos
    // Activar alarmas si es necesario
    return 0;
}

// Evaluación de estado
bool evaluate_status(monitoring_system_t *monitor) {
    // Verificar límites
    // Evaluar tendencias
    // Determinar estado
    return monitor->status_ok;
}
```

## 🔬 Laboratorio Práctico

### Experimento 1: Medición de Ripple

**Objetivo**: Medir el ripple en una fuente de alimentación

**Materiales**:
- Fuente de alimentación bajo prueba
- Osciloscopio
- Carga variable
- Multímetro

**Procedimiento**:
1. Conectar carga a la fuente
2. Medir ripple con osciloscopio
3. Variar la carga
4. Registrar mediciones
5. Analizar resultados

### Experimento 2: Regulación de Línea

**Objetivo**: Medir la regulación de línea de una fuente

**Materiales**:
- Fuente de alimentación bajo prueba
- Variac (variador de voltaje)
- Multímetro
- Carga fija

**Procedimiento**:
1. Conectar variac a la entrada
2. Conectar carga fija a la salida
3. Variar voltaje de entrada
4. Medir voltaje de salida
5. Calcular regulación

### Experimento 3: Eficiencia

**Objetivo**: Medir la eficiencia de una fuente de alimentación

**Materiales**:
- Fuente de alimentación bajo prueba
- Multímetro
- Carga variable
- Medidor de potencia

**Procedimiento**:
1. Medir potencia de entrada
2. Medir potencia de salida
3. Variar la carga
4. Calcular eficiencia
5. Analizar resultados

## 📊 Medición y Análisis

### 1. Parámetros de Medición

**Ripple:**
- Se mide con osciloscopio
- Se expresa en mV pico a pico
- Objetivo: <1 mV para audio de alta calidad

**Regulación de línea:**
- Se mide con variac
- Se expresa en porcentaje
- Objetivo: <0.01% para audio profesional

**Eficiencia:**
- Se mide con medidor de potencia
- Se expresa en porcentaje
- Objetivo: >90% para fuentes conmutadas

### 2. Herramientas de Medición

**Osciloscopio:**
- Medición de ripple
- Análisis de ruido
- Medición de transientes
- Verificación de señales

**Multímetro:**
- Medición de voltaje
- Medición de corriente
- Medición de resistencia
- Verificación de continuidad

**Analizador de Potencia:**
- Medición de eficiencia
- Análisis de armónicos
- Medición de factor de potencia
- Análisis de calidad de energía

## 🎯 Aplicaciones en Audio

### 1. Audio Profesional

- **Estudios de grabación**: Fuentes de alta estabilidad
- **Broadcasting**: Fuentes de alta confiabilidad
- **Post-producción**: Fuentes de alta precisión
- **Mastering**: Fuentes de referencia

### 2. Audio de Consumo

- **Hi-Fi**: Fuentes de alta calidad
- **Gaming**: Fuentes de alta eficiencia
- **Streaming**: Fuentes de bajo consumo
- **Móvil**: Fuentes de alta eficiencia

### 3. Audio de Ultra-Baja Latencia

- **Música en vivo**: Fuentes de alta estabilidad
- **Grabación en tiempo real**: Fuentes de alta precisión
- **Procesamiento en vivo**: Fuentes de alta eficiencia
- **Colaboración remota**: Fuentes de alta confiabilidad

## 🚀 Optimización para Ultra-Baja Latencia

### 1. Hardware

- **Fuentes de alta estabilidad**
- **Componentes de baja latencia**
- **Diseño optimizado**
- **Interconexión de alta velocidad**

### 2. Software

- **Control en tiempo real**
- **Monitoreo continuo**
- **Protección automática**
- **Optimización de eficiencia**

### 3. Sistema

- **Integración optimizada**
- **Monitoreo en tiempo real**
- **Control automático**
- **Protección inteligente**

## 📝 Ejercicios Prácticos

### Ejercicio 1: Diseño de Fuente Lineal
Diseña una fuente lineal de ±15V para audio profesional:
1. Selecciona la topología
2. Calcula los parámetros
3. Selecciona los componentes
4. Simula el rendimiento

### Ejercicio 2: Fuente Conmutada
Diseña una fuente conmutada de 5V para audio de consumo:
1. Selecciona el tipo de conversor
2. Calcula los parámetros
3. Implementa el circuito
4. Prueba el rendimiento

### Ejercicio 3: Sistema de Protección
Implementa un sistema de protección completo:
1. Diseña circuitos de protección
2. Implementa monitoreo
3. Desarrolla software de control
4. Prueba el sistema

## 🎯 Resumen de la Clase

En esta clase hemos cubierto:

1. **Fundamentos** de fuentes de alimentación
2. **Tipos de fuentes** y sus aplicaciones
3. **Reguladores lineales** y conmutados
4. **Filtrado y estabilización** de fuentes
5. **Protección y monitoreo** de fuentes
6. **Implementación práctica** con código
7. **Medición y análisis** de rendimiento
8. **Aplicaciones** en audio profesional

## 🧭 Navegación

- **Anterior**: [Clase 6.3: Circuitos de Interfaz](clase-6-3-circuitos-interfaz.md)
- **Siguiente**: [Clase 6.5: Protección y Control](clase-6-5-proteccion-control.md)
- **Módulo**: [Módulo 6: Hardware para Audio](README.md)
- **Inicio**: [Curso Completo](../../README.md)

---

*Duración estimada: 4-5 horas*
