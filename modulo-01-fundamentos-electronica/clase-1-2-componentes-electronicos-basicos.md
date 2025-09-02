# Clase 1.2: Componentes Electrónicos Básicos

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Identificar y entender los componentes electrónicos básicos
- Comprender el funcionamiento de resistencias, condensadores e inductores
- Conocer los símbolos esquemáticos de cada componente
- Aplicar las leyes de Kirchhoff en circuitos simples
- Entender el comportamiento de los componentes en circuitos de audio

## Introducción a los Componentes Electrónicos

Los **componentes electrónicos** son elementos físicos que se utilizan para construir circuitos electrónicos. Cada componente tiene una función específica y se comporta de manera predecible bajo ciertas condiciones.

### Clasificación de Componentes

1. **Componentes Pasivos**: No requieren fuente de alimentación externa
   - Resistencias
   - Condensadores
   - Inductores

2. **Componentes Activos**: Requieren fuente de alimentación para funcionar
   - Transistores
   - Amplificadores operacionales
   - Diodos

## 1. Resistencias

### ¿Qué es una Resistencia?

Una **resistencia** es un componente pasivo que se opone al flujo de corriente eléctrica, convirtiendo la energía eléctrica en calor.

### Características Principales

**Valor de Resistencia (R)**:
- Se mide en Ohmios (Ω)
- Valores comunes: 1Ω, 10Ω, 100Ω, 1kΩ, 10kΩ, 100kΩ, 1MΩ

**Tolerancia**:
- Indica la precisión del valor nominal
- Común: ±5%, ±1%, ±0.1%

**Potencia Máxima**:
- Cantidad de calor que puede disipar
- Común: 1/4W, 1/2W, 1W, 2W

### Código de Colores

Las resistencias utilizan un código de colores para indicar su valor:

```
Color    Dígito    Multiplicador    Tolerancia
Negro    0         10⁰             -
Marrón   1         10¹             ±1%
Rojo     2         10²             ±2%
Naranja  3         10³             -
Amarillo 4         10⁴             -
Verde    5         10⁵             ±0.5%
Azul     6         10⁶             ±0.25%
Violeta  7         10⁷             ±0.1%
Gris     8         10⁸             ±0.05%
Blanco   9         10⁹             -
Dorado   -         10⁻¹            ±5%
Plateado -         10⁻²            ±10%
```

### Ejemplo de Lectura
```
Resistencia con colores: Marrón, Negro, Rojo, Dorado
- Marrón (1): Primer dígito = 1
- Negro (0): Segundo dígito = 0
- Rojo (10²): Multiplicador = 100
- Dorado (±5%): Tolerancia = ±5%

Valor: 10 × 100 = 1000Ω = 1kΩ ±5%
```

### Símbolo Esquemático
```
    ┌─────┐
    │     │
    └─────┘
```

### Aplicaciones en Audio
- **Divisores de voltaje**: Para ajustar niveles de señal
- **Carga de salida**: Para adaptar impedancias
- **Filtros**: En combinación con condensadores

## 2. Condensadores

### ¿Qué es un Condensador?

Un **condensador** es un componente pasivo que almacena energía eléctrica en un campo eléctrico.

### Características Principales

**Capacitancia (C)**:
- Se mide en Faradios (F)
- Valores comunes: pF, nF, μF, mF

**Tensión de Trabajo**:
- Voltaje máximo que puede soportar
- Común: 16V, 25V, 50V, 100V, 400V

**Tipo de Dieléctrico**:
- Cerámico: Para altas frecuencias
- Electrolítico: Para altos valores
- Poliéster: Para audio

### Conversión de Unidades
```
1 Faradio (F) = 1,000,000 μF
1 μF = 1,000 nF
1 nF = 1,000 pF
```

### Comportamiento en Circuitos

**Carga del Condensador**:
```
V(t) = V₀(1 - e^(-t/RC))
```

**Descarga del Condensador**:
```
V(t) = V₀ × e^(-t/RC)
```

Donde:
- V(t) = Voltaje en el tiempo t
- V₀ = Voltaje inicial
- R = Resistencia
- C = Capacitancia
- τ = RC (constante de tiempo)

### Símbolo Esquemático
```
    ┌─| |─┐
    │     │
    └─────┘
```

### Aplicaciones en Audio
- **Filtros pasa-alto**: Eliminan componentes DC
- **Filtros pasa-bajo**: Eliminan frecuencias altas
- **Acoplamiento**: Conectan etapas sin DC
- **Desacoplamiento**: Filtran ruido de alimentación

## 3. Inductores

### ¿Qué es un Inductor?

Un **inductor** es un componente pasivo que almacena energía en un campo magnético.

### Características Principales

**Inductancia (L)**:
- Se mide en Henrios (H)
- Valores comunes: μH, mH, H

**Resistencia Interna**:
- Resistencia del alambre (DCR)
- Afecta la eficiencia

**Corriente Máxima**:
- Corriente que puede soportar sin saturarse

### Comportamiento en Circuitos

**Energía Almacenada**:
```
E = ½ × L × I²
```

**Reactancia Inductiva**:
```
XL = 2πfL
```

Donde:
- XL = Reactancia inductiva (Ω)
- f = Frecuencia (Hz)
- L = Inductancia (H)

### Símbolo Esquemático
```
    ┌─────┐
    │  ═══│
    └─────┘
```

### Aplicaciones en Audio
- **Filtros**: En combinación con condensadores
- **Transformadores**: Para acoplamiento de impedancias
- **Filtros de alimentación**: Eliminan ruido de alta frecuencia

## Leyes de Kirchhoff

### Primera Ley (Ley de Corrientes)

**Enunciado**: La suma algebraica de las corrientes que entran y salen de un nodo es igual a cero.

```
Σ I_entrada = Σ I_salida
```

### Segunda Ley (Ley de Voltajes)

**Enunciado**: La suma algebraica de las diferencias de potencial en un lazo cerrado es igual a cero.

```
Σ V = 0
```

### Ejemplo Práctico

**Circuito con dos resistencias en serie**:
```
    V1 ──[R1]──[R2]── GND
```

**Aplicando la Segunda Ley**:
```
V1 - VR1 - VR2 = 0
V1 = VR1 + VR2
```

## Análisis de Circuitos

### Método de Análisis Nodal

1. Identificar todos los nodos
2. Elegir un nodo de referencia (tierra)
3. Aplicar la Primera Ley de Kirchhoff
4. Resolver el sistema de ecuaciones

### Método de Análisis de Mallas

1. Identificar todas las mallas
2. Asignar corrientes de malla
3. Aplicar la Segunda Ley de Kirchhoff
4. Resolver el sistema de ecuaciones

## Ejercicios Prácticos

### Ejercicio 1: Circuito en Serie
```
Datos:
- V1 = 12V
- R1 = 2Ω
- R2 = 4Ω

Preguntas:
1. ¿Cuál es la resistencia total?
2. ¿Cuál es la corriente?
3. ¿Cuál es el voltaje en cada resistencia?

Solución:
1. R_total = 2Ω + 4Ω = 6Ω
2. I = V/R = 12V/6Ω = 2A
3. VR1 = I × R1 = 2A × 2Ω = 4V
   VR2 = I × R2 = 2A × 4Ω = 8V
```

### Ejercicio 2: Circuito en Paralelo
```
Datos:
- V = 9V
- R1 = 3Ω
- R2 = 6Ω

Preguntas:
1. ¿Cuál es la resistencia total?
2. ¿Cuál es la corriente total?
3. ¿Cuál es la corriente en cada resistencia?

Solución:
1. 1/R_total = 1/3 + 1/6 = 2/6 + 1/6 = 3/6 = 1/2
   R_total = 2Ω
2. I_total = V/R = 9V/2Ω = 4.5A
3. I1 = V/R1 = 9V/3Ω = 3A
   I2 = V/R2 = 9V/6Ω = 1.5A
```

### Ejercicio 3: Circuito RC
```
Datos:
- V = 10V
- R = 1kΩ
- C = 1μF

Pregunta: ¿Cuál es la constante de tiempo?

Solución:
τ = RC = 1000Ω × 0.000001F = 0.001s = 1ms
```

## Aplicaciones en Audio

### Filtros de Audio

**Filtro Pasa-Alto (HPF)**:
```
    Entrada ──[C]──[R]── Salida
```

**Filtro Pasa-Bajo (LPF)**:
```
    Entrada ──[R]──[C]── Salida
```

**Frecuencia de Corte**:
```
fc = 1/(2πRC)
```

### Ejemplo: Filtro Pasa-Alto
```
Datos:
- C = 10μF
- R = 1kΩ

Cálculo de frecuencia de corte:
fc = 1/(2π × 1000 × 0.00001) = 15.9 Hz
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Resistencias**: Oponen al flujo de corriente, código de colores
2. **Condensadores**: Almacenan energía eléctrica, filtros
3. **Inductores**: Almacenan energía magnética, reactancia
4. **Leyes de Kirchhoff**: Análisis de circuitos
5. **Aplicaciones en Audio**: Filtros y acoplamiento

## Próxima Clase

En la siguiente clase estudiaremos los **semiconductores y diodos**, fundamentales para entender los componentes activos.

---

## Navegación
- **Anterior**: [Clase 1.1: Introducción a la Electricidad](clase-1-1-introduccion-electricidad.md)
- **Siguiente**: [Clase 1.3: Semiconductores y Diodos](clase-1-3-semiconductores-diodos.md)
- **Volver al**: [Índice Principal](../../README.md)
