# Clase 1.5: Amplificadores Operacionales

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué son los amplificadores operacionales y cómo funcionan
- Entender las características ideales y reales de los op-amps
- Conocer las configuraciones básicas de amplificadores
- Aplicar el análisis de circuitos con op-amps
- Entender el papel de los op-amps en circuitos de audio

## Introducción a los Amplificadores Operacionales

### ¿Qué es un Amplificador Operacional?

Un **amplificador operacional (op-amp)** es un circuito integrado que amplifica la diferencia de voltaje entre sus dos entradas. Es uno de los componentes más versátiles en electrónica.

### Historia del Op-Amp

**Desarrollo**:
- 1941: Primer amplificador operacional (Bell Labs)
- 1963: Primer op-amp integrado (Fairchild μA702)
- 1968: Op-amp estándar (Fairchild μA741)
- 1970s: Op-amps de alta velocidad y baja distorsión

### Símbolo Esquemático

```
        V+
        │
    ┌───┴───┐
    │       │
    +       │
    │       │
    -       │
    │       │
    └───┬───┘
        │
        V-
```

**Terminales**:
- **+**: Entrada no inversora
- **-**: Entrada inversora
- **V+**: Alimentación positiva
- **V-**: Alimentación negativa
- **Salida**: Voltaje de salida

## Características Ideales del Op-Amp

### Modelo Ideal

**Características**:
1. **Ganancia infinita**: A = ∞
2. **Impedancia de entrada infinita**: Rin = ∞
3. **Impedancia de salida cero**: Rout = 0
4. **Ancho de banda infinito**: BW = ∞
5. **Tiempo de respuesta cero**: tr = 0
6. **Ruido cero**: Vn = 0
7. **Deriva cero**: Vdrift = 0

### Ecuación del Op-Amp Ideal

```
Vout = A × (V+ - V-)
```

Donde:
- Vout = Voltaje de salida
- A = Ganancia de lazo abierto
- V+ = Voltaje de entrada no inversora
- V- = Voltaje de entrada inversora

### Reglas del Op-Amp Ideal

**Regla 1**: La diferencia de voltaje entre las entradas es cero
```
V+ = V-
```

**Regla 2**: No fluye corriente hacia las entradas
```
I+ = I- = 0
```

## Características Reales del Op-Amp

### Parámetros Importantes

#### 1. Ganancia de Lazo Abierto (AOL)

**Definición**: Ganancia del op-amp sin retroalimentación
- **Valor típico**: 100,000 - 1,000,000
- **Dependencia**: Disminuye con la frecuencia

#### 2. Impedancia de Entrada

**Definición**: Resistencia vista desde las entradas
- **Valor típico**: 1MΩ - 100MΩ
- **Diferencial**: Entre las dos entradas
- **Común**: Entre cada entrada y tierra

#### 3. Impedancia de Salida

**Definición**: Resistencia interna de la salida
- **Valor típico**: 10Ω - 100Ω
- **Efecto**: División de voltaje con la carga

#### 4. Ancho de Banda

**Definición**: Rango de frecuencias donde la ganancia es útil
- **Valor típico**: 1MHz - 100MHz
- **Producto ganancia-ancho de banda**: Constante

#### 5. Slew Rate

**Definición**: Velocidad máxima de cambio del voltaje de salida
- **Valor típico**: 0.5V/μs - 100V/μs
- **Efecto**: Limitación en señales de alta frecuencia

#### 6. Voltaje de Offset

**Definición**: Voltaje de salida cuando las entradas son iguales
- **Valor típico**: 1mV - 10mV
- **Efecto**: Error en mediciones de precisión

## Configuraciones Básicas

### 1. Amplificador Inversor

**Circuito**:
```
    Entrada ──[R1]──┬──[R2]── Salida
                    │
                    └── Entrada -
                    │
                    Entrada + ── GND
```

**Características**:
- Ganancia: Av = -R2/R1
- Impedancia de entrada: Rin = R1
- Inversión de fase

**Análisis**:
```
V- = 0 (tierra virtual)
I1 = Vin/R1
I2 = -Vout/R2
I1 = I2 (no hay corriente en la entrada)
Vin/R1 = -Vout/R2
Vout = -(R2/R1) × Vin
```

### 2. Amplificador No Inversor

**Circuito**:
```
    Entrada + ── Entrada
                    │
    GND ──[R1]──┬──[R2]── Salida
                │
                └── Entrada -
```

**Características**:
- Ganancia: Av = 1 + R2/R1
- Impedancia de entrada: Rin = ∞
- Sin inversión de fase

**Análisis**:
```
V+ = Vin
V- = Vout × R1/(R1 + R2)
V+ = V- (regla del op-amp)
Vin = Vout × R1/(R1 + R2)
Vout = Vin × (R1 + R2)/R1
Vout = Vin × (1 + R2/R1)
```

### 3. Seguidor de Voltaje

**Circuito**:
```
    Entrada + ── Entrada
                    │
                    └── Salida
                    │
                    Entrada - ── Salida
```

**Características**:
- Ganancia: Av = 1
- Impedancia de entrada: Rin = ∞
- Impedancia de salida: Rout = 0
- Sin inversión de fase

### 4. Amplificador Diferencial

**Circuito**:
```
    V1 ──[R1]──┬──[R2]── Salida
               │
               └── Entrada -
               │
    V2 ──[R3]──┬── Entrada +
               │
               └──[R4]── GND
```

**Características**:
- Ganancia: Av = R2/R1 (si R1 = R3 y R2 = R4)
- Amplifica la diferencia entre V1 y V2
- Rechaza señales comunes

## Aplicaciones en Audio

### 1. Preamplificador de Micrófono

**Circuito**:
```
    Micrófono ──[R1]──┬──[R2]── Salida
                      │
                      └── Entrada -
                      │
                      Entrada + ── GND
```

**Características**:
- Ganancia ajustable
- Impedancia de entrada alta
- Ruido bajo

### 2. Filtro Activo

**Filtro Pasa-Bajo**:
```
    Entrada ──[R]──┬──[C]── GND
                   │
                   └── Entrada -
                   │
                   Entrada + ── GND
                   │
                   Salida
```

**Frecuencia de Corte**:
```
fc = 1/(2πRC)
```

### 3. Sumador de Audio

**Circuito**:
```
    V1 ──[R1]──┬──[Rf]── Salida
    V2 ──[R2]──┤
    V3 ──[R3]──┤
               │
               └── Entrada -
               │
               Entrada + ── GND
```

**Salida**:
```
Vout = -Rf × (V1/R1 + V2/R2 + V3/R3)
```

### 4. Amplificador de Instrumentación

**Circuito**:
```
    V1 ──[R1]──┬──[R2]── Salida
               │
               └── Entrada -
               │
    V2 ──[R1]──┬── Entrada +
               │
               └──[R2]── GND
```

**Características**:
- Alta impedancia de entrada
- Rechazo de modo común
- Ganancia ajustable

## Ejercicios Prácticos

### Ejercicio 1: Amplificador Inversor

```
Datos:
- R1 = 1kΩ
- R2 = 10kΩ
- Vin = 0.5V

Pregunta: ¿Cuál es el voltaje de salida?

Solución:
Av = -R2/R1 = -10kΩ/1kΩ = -10
Vout = Av × Vin = -10 × 0.5V = -5V
```

### Ejercicio 2: Amplificador No Inversor

```
Datos:
- R1 = 2kΩ
- R2 = 8kΩ
- Vin = 1V

Pregunta: ¿Cuál es el voltaje de salida?

Solución:
Av = 1 + R2/R1 = 1 + 8kΩ/2kΩ = 1 + 4 = 5
Vout = Av × Vin = 5 × 1V = 5V
```

### Ejercicio 3: Filtro Activo

```
Datos:
- R = 10kΩ
- C = 0.1μF

Pregunta: ¿Cuál es la frecuencia de corte?

Solución:
fc = 1/(2πRC) = 1/(2π × 10kΩ × 0.1μF)
fc = 1/(2π × 10,000 × 0.0000001) = 159.2 Hz
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Amplificadores Operacionales**: Circuitos integrados versátiles
2. **Características Ideales**: Modelo simplificado para análisis
3. **Características Reales**: Parámetros que afectan el rendimiento
4. **Configuraciones Básicas**: Inversor, no inversor, seguidor, diferencial
5. **Aplicaciones en Audio**: Preamplificadores, filtros, sumadores

## Próxima Clase

En la siguiente clase del Módulo 2 estudiaremos las **matemáticas fundamentales para audio**, incluyendo números complejos y análisis de Fourier.

---

## Navegación
- **Anterior**: [Clase 1.4: Transistores](clase-1-4-transistores.md)
- **Siguiente**: [Módulo 2 - Índice](../modulo-02-matematicas-audio/README.md)
- **Volver al**: [Índice Principal](../../README.md)
