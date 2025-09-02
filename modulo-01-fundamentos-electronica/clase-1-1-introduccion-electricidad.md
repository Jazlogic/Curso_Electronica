# Clase 1.1: Introducción a la Electricidad

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué es la electricidad y cómo se manifiesta
- Entender los conceptos básicos de carga eléctrica, corriente y voltaje
- Conocer las unidades fundamentales del sistema eléctrico
- Aplicar la ley de Ohm en circuitos simples
- Identificar los tipos básicos de circuitos eléctricos

## ¿Qué es la Electricidad?

La **electricidad** es un fenómeno físico que se manifiesta a través de la presencia y flujo de cargas eléctricas. Es una forma de energía que puede ser generada, transmitida y utilizada para realizar trabajo.

### Definición Técnica
La electricidad es el conjunto de fenómenos físicos relacionados con la presencia y flujo de cargas eléctricas. Estas cargas pueden ser positivas o negativas, y su movimiento genera campos eléctricos y magnéticos.

### Manifestaciones de la Electricidad
1. **Electricidad estática**: Cargas en reposo (como la que sientes al tocar una puerta después de caminar en una alfombra)
2. **Corriente eléctrica**: Cargas en movimiento (como la electricidad que fluye por los cables de tu casa)
3. **Campos eléctricos**: Regiones del espacio donde las cargas experimentan fuerzas
4. **Campos magnéticos**: Regiones del espacio donde las cargas en movimiento experimentan fuerzas

## Conceptos Fundamentales

### 1. Carga Eléctrica (Q)

**Definición**: La carga eléctrica es una propiedad fundamental de la materia que determina cómo interactúa con campos eléctricos y magnéticos.

**Características**:
- Se mide en **Culombios (C)**
- Existen dos tipos: positiva (+) y negativa (-)
- Las cargas del mismo signo se repelen, las de signo opuesto se atraen
- La carga está cuantizada (múltiplos de la carga del electrón: 1.6 × 10⁻¹⁹ C)

**Ejemplo Práctico**:
```
Carga del electrón: -1.6 × 10⁻¹⁹ C
Carga del protón: +1.6 × 10⁻¹⁹ C
Carga de 1 Culombio: 6.24 × 10¹⁸ electrones
```

### 2. Corriente Eléctrica (I)

**Definición**: La corriente eléctrica es el flujo de cargas eléctricas a través de un conductor.

**Fórmula**:
```
I = Q/t
```
Donde:
- I = Corriente (Amperios, A)
- Q = Carga (Culombios, C)
- t = Tiempo (segundos, s)

**Unidades**:
- **Amperio (A)**: Unidad básica de corriente
- **Miliamperio (mA)**: 1 mA = 0.001 A
- **Microamperio (μA)**: 1 μA = 0.000001 A

**Ejemplo Práctico**:
```
Si 2 Culombios de carga pasan por un punto en 4 segundos:
I = 2 C / 4 s = 0.5 A = 500 mA
```

### 3. Voltaje o Tensión (V)

**Definición**: El voltaje es la diferencia de potencial eléctrico entre dos puntos, que representa la energía por unidad de carga.

**Fórmula**:
```
V = W/Q
```
Donde:
- V = Voltaje (Voltios, V)
- W = Energía (Joules, J)
- Q = Carga (Culombios, C)

**Unidades**:
- **Voltio (V)**: Unidad básica de voltaje
- **Kilovoltio (kV)**: 1 kV = 1000 V
- **Milivoltio (mV)**: 1 mV = 0.001 V

**Ejemplo Práctico**:
```
Si se requiere 12 Joules para mover 2 Culombios:
V = 12 J / 2 C = 6 V
```

### 4. Resistencia (R)

**Definición**: La resistencia es la oposición que presenta un material al flujo de corriente eléctrica.

**Fórmula**:
```
R = V/I
```
Donde:
- R = Resistencia (Ohmios, Ω)
- V = Voltaje (Voltios, V)
- I = Corriente (Amperios, A)

**Unidades**:
- **Ohmio (Ω)**: Unidad básica de resistencia
- **Kiloohmio (kΩ)**: 1 kΩ = 1000 Ω
- **Megaohmio (MΩ)**: 1 MΩ = 1,000,000 Ω

## Ley de Ohm

La **Ley de Ohm** es una de las leyes fundamentales de la electricidad que relaciona voltaje, corriente y resistencia.

### Fórmula Principal
```
V = I × R
```

### Fórmulas Derivadas
```
I = V/R
R = V/I
```

### Ejemplo Práctico
```
Datos:
- Voltaje: 12 V
- Resistencia: 4 Ω

Cálculo de corriente:
I = V/R = 12 V / 4 Ω = 3 A
```

## Tipos de Circuitos Eléctricos

### 1. Circuito en Serie

**Características**:
- Los componentes están conectados uno después del otro
- La corriente es la misma en todos los componentes
- El voltaje se divide entre los componentes
- Si un componente falla, todo el circuito se interrumpe

**Fórmulas**:
```
Resistencia total: R_total = R₁ + R₂ + R₃ + ...
Voltaje total: V_total = V₁ + V₂ + V₃ + ...
Corriente: I = V_total / R_total
```

**Ejemplo**:
```
Resistencias en serie: 2Ω, 3Ω, 5Ω
R_total = 2 + 3 + 5 = 10Ω
Si V = 20V, entonces I = 20V / 10Ω = 2A
```

### 2. Circuito en Paralelo

**Características**:
- Los componentes están conectados entre los mismos dos puntos
- El voltaje es el mismo en todos los componentes
- La corriente se divide entre los componentes
- Si un componente falla, los demás siguen funcionando

**Fórmulas**:
```
Resistencia total: 1/R_total = 1/R₁ + 1/R₂ + 1/R₃ + ...
Voltaje: V = V₁ = V₂ = V₃ = ...
Corriente total: I_total = I₁ + I₂ + I₃ + ...
```

**Ejemplo**:
```
Resistencias en paralelo: 2Ω, 3Ω, 6Ω
1/R_total = 1/2 + 1/3 + 1/6 = 3/6 + 2/6 + 1/6 = 6/6 = 1
R_total = 1Ω
Si V = 12V, entonces I_total = 12V / 1Ω = 12A
```

### 3. Circuito Mixto

**Características**:
- Combina conexiones en serie y en paralelo
- Requiere análisis paso a paso
- Muy común en circuitos reales

## Potencia Eléctrica

**Definición**: La potencia eléctrica es la tasa a la cual se consume o produce energía eléctrica.

**Fórmula**:
```
P = V × I
```

**Fórmulas Derivadas**:
```
P = I² × R
P = V² / R
```

**Unidades**:
- **Vatio (W)**: Unidad básica de potencia
- **Kilovatio (kW)**: 1 kW = 1000 W
- **Milivatio (mW)**: 1 mW = 0.001 W

**Ejemplo Práctico**:
```
Si V = 12V e I = 2A:
P = 12V × 2A = 24W
```

## Ejercicios Prácticos

### Ejercicio 1: Cálculo de Corriente
```
Datos:
- Voltaje: 9V
- Resistencia: 3Ω

Pregunta: ¿Cuál es la corriente?
Solución: I = V/R = 9V/3Ω = 3A
```

### Ejercicio 2: Cálculo de Resistencia
```
Datos:
- Voltaje: 24V
- Corriente: 0.5A

Pregunta: ¿Cuál es la resistencia?
Solución: R = V/I = 24V/0.5A = 48Ω
```

### Ejercicio 3: Cálculo de Potencia
```
Datos:
- Voltaje: 15V
- Corriente: 2A

Pregunta: ¿Cuál es la potencia?
Solución: P = V × I = 15V × 2A = 30W
```

## Aplicaciones en Audio

### ¿Por qué es importante la electricidad en audio?

1. **Amplificación**: Los amplificadores de audio convierten señales de bajo voltaje en señales de alto voltaje
2. **Conversión**: Los convertidores ADC/DAC transforman señales analógicas en digitales y viceversa
3. **Filtrado**: Los filtros electrónicos eliminan frecuencias no deseadas
4. **Modulación**: Las señales de audio se modulan para transmisión

### Ejemplo Práctico: Amplificador de Audio
```
Entrada: 0.1V (señal de micrófono)
Ganancia: 100
Salida: 0.1V × 100 = 10V (señal amplificada)
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Electricidad**: Fenómeno físico relacionado con cargas eléctricas
2. **Carga (Q)**: Propiedad fundamental de la materia (Culombios)
3. **Corriente (I)**: Flujo de cargas (Amperios)
4. **Voltaje (V)**: Diferencia de potencial (Voltios)
5. **Resistencia (R)**: Oposición al flujo (Ohmios)
6. **Ley de Ohm**: V = I × R
7. **Circuitos**: Serie, paralelo y mixto
8. **Potencia**: P = V × I

## Próxima Clase

En la siguiente clase estudiaremos los **componentes electrónicos básicos** y cómo se comportan en circuitos reales.

---

## Navegación
- **Anterior**: [Módulo 1 - Índice](../modulo-01-fundamentos-electronica/README.md)
- **Siguiente**: [Clase 1.2: Componentes Electrónicos Básicos](clase-1-2-componentes-electronicos-basicos.md)
- **Volver al**: [Índice Principal](../../README.md)