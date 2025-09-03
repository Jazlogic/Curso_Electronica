# Clase 6.6: Diseño de PCB

## 🎯 Objetivos de la Clase

Al finalizar esta clase, serás capaz de:

1. **Diseñar placas de circuito impreso** para aplicaciones de audio de alta calidad
2. **Implementar técnicas de layout** para minimizar ruido y interferencias
3. **Optimizar el routing** y distribución de señales
4. **Diseñar ground planes** y sistemas de blindaje
5. **Seleccionar componentes** y técnicas de manufactura

## 📚 Contenido Teórico

### 1. Fundamentos del Diseño de PCB

#### 1.1 ¿Qué es una PCB?

Una **Placa de Circuito Impreso (PCB)** es una placa base que conecta componentes electrónicos mediante pistas conductoras. En audio de alta calidad, el diseño de PCB es crítico para mantener la integridad de la señal y minimizar el ruido.

**Características principales:**
- **Material base**: FR4, Rogers, cerámica
- **Capas**: Simple, doble, multicapa
- **Espesor**: 0.8mm a 3.2mm
- **Acabado**: HASL, ENIG, OSP
- **Máscara**: Verde, azul, roja, negra

#### 1.2 Parámetros Críticos para Audio

**Impedancia característica**:
- Control de impedancia de pistas
- Minimización de reflexiones
- Mejora de integridad de señal
- Reducción de ruido

**Capacitancia parásita**:
- Minimización de capacitancia
- Reducción de acoplamiento
- Mejora de respuesta en frecuencia
- Reducción de distorsión

**Inductancia parásita**:
- Minimización de inductancia
- Reducción de ruido
- Mejora de estabilidad
- Reducción de interferencias

### 2. Técnicas de Layout

#### 2.1 Separación de Señales

**Separación analógica/digital**:
- Áreas separadas en la PCB
- Ground planes separados
- Alimentación separada
- Blindaje entre secciones

**Separación de frecuencias**:
- Señales de alta frecuencia
- Señales de baja frecuencia
- Señales de control
- Señales de alimentación

#### 2.2 Routing de Señales

**Pistas de señal**:
- Ancho apropiado
- Longitud mínima
- Separación adecuada
- Impedancia controlada

**Pistas de alimentación**:
- Ancho suficiente
- Baja impedancia
- Separación de señales
- Filtrado adecuado

**Pistas de control**:
- Separación de señales
- Blindaje adecuado
- Longitud mínima
- Impedancia controlada

### 3. Ground Planes y Blindaje

#### 3.1 Ground Planes

**Ground plane sólido**:
- Área de cobre continua
- Baja impedancia
- Reducción de ruido
- Mejora de estabilidad

**Ground plane dividido**:
- Separación de secciones
- Reducción de acoplamiento
- Mejora de aislamiento
- Control de ruido

**Ground plane multicapa**:
- Múltiples capas de tierra
- Reducción de impedancia
- Mejora de blindaje
- Control de ruido

#### 3.2 Blindaje Electromagnético

**Blindaje de pistas**:
- Pistas de guarda
- Reducción de acoplamiento
- Mejora de aislamiento
- Control de ruido

**Blindaje de componentes**:
- Jaulas de Faraday
- Reducción de interferencias
- Mejora de aislamiento
- Control de ruido

**Blindaje de secciones**:
- Separación física
- Reducción de acoplamiento
- Mejora de aislamiento
- Control de ruido

### 4. Componentes SMD vs Through-Hole

#### 4.1 Componentes SMD

**Ventajas:**
- Menor tamaño
- Menor inductancia
- Mejor rendimiento de alta frecuencia
- Automatización de montaje

**Desventajas:**
- Mayor costo de prototipado
- Requiere equipos especializados
- Difícil reparación manual
- Limitaciones de potencia

#### 4.2 Componentes Through-Hole

**Ventajas:**
- Fácil prototipado
- Fácil reparación
- Mayor robustez mecánica
- Menor costo de prototipado

**Desventajas:**
- Mayor tamaño
- Mayor inductancia
- Limitaciones de alta frecuencia
- Montaje manual

### 5. Técnicas de Manufactura

#### 5.1 Procesos de Manufactura

**Fabricación estándar**:
- Laminado de capas
- Perforado de vías
- Metalización de vías
- Aplicación de máscara

**Fabricación avanzada**:
- Vías ciegas
- Vías enterradas
- Microvías
- Acabados especiales

#### 5.2 Control de Calidad

**Inspección visual**:
- Verificación de pistas
- Verificación de componentes
- Verificación de soldadura
- Verificación de acabado

**Pruebas eléctricas**:
- Continuidad de pistas
- Aislamiento entre pistas
- Impedancia de pistas
- Funcionalidad del circuito

## 💻 Implementación Práctica

### 1. Diseño de Layout

```c
// Configuración de layout
typedef struct {
    float board_width;
    float board_height;
    int num_layers;
    float trace_width;
    float trace_spacing;
    float via_diameter;
} layout_config_t;

// Inicialización de layout
int layout_init(layout_config_t *config) {
    // Configurar dimensiones
    // Configurar capas
    // Configurar pistas
    // Configurar vías
    return 0;
}

// Diseño de pistas
int design_traces(layout_config_t *config) {
    // Calcular ancho de pistas
    // Calcular separación
    // Calcular impedancia
    // Optimizar routing
    return 0;
}
```

### 2. Ground Plane

```c
// Configuración de ground plane
typedef struct {
    bool solid_ground;
    bool split_ground;
    int num_ground_layers;
    float ground_thickness;
    bool ground_vias;
} ground_plane_config_t;

// Inicialización de ground plane
int ground_plane_init(ground_plane_config_t *config) {
    // Configurar tipo de ground
    // Configurar capas
    // Configurar espesor
    // Configurar vías
    return 0;
}

// Diseño de ground plane
int design_ground_plane(ground_plane_config_t *config) {
    // Diseñar áreas de ground
    // Conectar ground planes
    // Optimizar impedancia
    // Minimizar ruido
    return 0;
}
```

### 3. Blindaje

```c
// Configuración de blindaje
typedef struct {
    bool trace_shielding;
    bool component_shielding;
    bool section_shielding;
    float shield_thickness;
    bool shield_vias;
} shielding_config_t;

// Inicialización de blindaje
int shielding_init(shielding_config_t *config) {
    // Configurar tipo de blindaje
    // Configurar espesor
    // Configurar vías
    // Configurar materiales
    return 0;
}

// Diseño de blindaje
int design_shielding(shielding_config_t *config) {
    // Diseñar blindaje de pistas
    // Diseñar blindaje de componentes
    // Diseñar blindaje de secciones
    // Optimizar efectividad
    return 0;
}
```

### 4. Optimización

```c
// Configuración de optimización
typedef struct {
    bool minimize_noise;
    bool minimize_crosstalk;
    bool optimize_impedance;
    bool optimize_thermal;
} optimization_config_t;

// Inicialización de optimización
int optimization_init(optimization_config_t *config) {
    // Configurar objetivos
    // Configurar parámetros
    // Configurar límites
    // Inicializar estado
    return 0;
}

// Optimización de diseño
int optimize_design(optimization_config_t *config) {
    // Optimizar ruido
    // Optimizar crosstalk
    // Optimizar impedancia
    // Optimizar térmico
    return 0;
}
```

## 🔬 Laboratorio Práctico

### Experimento 1: Diseño de Layout

**Objetivo**: Diseñar el layout de una PCB para audio

**Materiales**:
- Software de diseño de PCB
- Esquemático del circuito
- Especificaciones de componentes
- Herramientas de diseño

**Procedimiento**:
1. Importar esquemático
2. Configurar reglas de diseño
3. Colocar componentes
4. Routing de pistas
5. Verificar diseño

### Experimento 2: Ground Plane

**Objetivo**: Diseñar un ground plane optimizado

**Materiales**:
- Software de diseño de PCB
- Herramientas de análisis
- Especificaciones de impedancia
- Herramientas de simulación

**Procedimiento**:
1. Diseñar ground plane
2. Configurar impedancia
3. Simular rendimiento
4. Optimizar diseño
5. Verificar resultados

### Experimento 3: Blindaje

**Objetivo**: Implementar blindaje electromagnético

**Materiales**:
- Software de diseño de PCB
- Herramientas de análisis
- Especificaciones de blindaje
- Herramientas de simulación

**Procedimiento**:
1. Diseñar blindaje
2. Configurar parámetros
3. Simular efectividad
4. Optimizar diseño
5. Verificar resultados

## 📊 Medición y Análisis

### 1. Parámetros de Medición

**Impedancia de pistas:**
- Se mide con TDR
- Se expresa en ohmios
- Objetivo: 50Ω para señales de alta frecuencia

**Capacitancia parásita:**
- Se mide con LCR
- Se expresa en picofaradios
- Objetivo: <1pF para audio de alta calidad

**Inductancia parásita:**
- Se mide con LCR
- Se expresa en nanohenrios
- Objetivo: <1nH para audio de alta calidad

### 2. Herramientas de Medición

**TDR (Time Domain Reflectometer):**
- Medición de impedancia
- Análisis de pistas
- Detección de fallas
- Verificación de diseño

**LCR Meter:**
- Medición de capacitancia
- Medición de inductancia
- Medición de resistencia
- Análisis de componentes

**Analizador de Red:**
- Medición de impedancia
- Análisis de frecuencia
- Medición de pérdidas
- Verificación de diseño

## 🎯 Aplicaciones en Audio

### 1. Audio Profesional

- **Estudios de grabación**: PCBs de alta calidad
- **Broadcasting**: PCBs de alta confiabilidad
- **Post-producción**: PCBs de alta precisión
- **Mastering**: PCBs de referencia

### 2. Audio de Consumo

- **Hi-Fi**: PCBs de alta calidad
- **Gaming**: PCBs de baja latencia
- **Streaming**: PCBs eficientes
- **Móvil**: PCBs de bajo consumo

### 3. Audio de Ultra-Baja Latencia

- **Música en vivo**: PCBs de alta velocidad
- **Grabación en tiempo real**: PCBs transparentes
- **Procesamiento en vivo**: PCBs de baja latencia
- **Colaboración remota**: PCBs de alta velocidad

## 🚀 Optimización para Ultra-Baja Latencia

### 1. Hardware

- **PCBs de alta velocidad**
- **Componentes de baja latencia**
- **Diseño optimizado**
- **Interconexión de alta velocidad**

### 2. Software

- **Herramientas de diseño avanzadas**
- **Simulación en tiempo real**
- **Optimización automática**
- **Verificación de diseño**

### 3. Sistema

- **Integración optimizada**
- **Monitoreo en tiempo real**
- **Control automático**
- **Protección inteligente**

## 📝 Ejercicios Prácticos

### Ejercicio 1: Diseño de PCB
Diseña una PCB para un amplificador de audio:
1. Crea el esquemático
2. Diseña el layout
3. Optimiza el routing
4. Verifica el diseño

### Ejercicio 2: Ground Plane
Diseña un ground plane optimizado:
1. Calcula la impedancia
2. Diseña las áreas
3. Simula el rendimiento
4. Optimiza el diseño

### Ejercicio 3: Blindaje
Implementa blindaje electromagnético:
1. Identifica fuentes de ruido
2. Diseña el blindaje
3. Simula la efectividad
4. Optimiza el diseño

## 🎯 Resumen de la Clase

En esta clase hemos cubierto:

1. **Fundamentos** del diseño de PCB
2. **Técnicas de layout** para audio
3. **Ground planes y blindaje** electromagnético
4. **Componentes SMD vs Through-Hole**
5. **Técnicas de manufactura** y control de calidad
6. **Implementación práctica** con código
7. **Medición y análisis** de rendimiento
8. **Aplicaciones** en audio profesional

## 🧭 Navegación

- **Anterior**: [Clase 6.5: Protección y Control](clase-6-5-proteccion-control.md)
- **Siguiente**: [Clase 6.7: Medición y Calibración](clase-6-7-medicion-calibracion.md)
- **Módulo**: [Módulo 6: Hardware para Audio](README.md)
- **Inicio**: [Curso Completo](../../README.md)

---

*Duración estimada: 4-5 horas*
