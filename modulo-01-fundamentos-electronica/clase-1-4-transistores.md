# Clase 1.4: Transistores

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué son los transistores y cómo funcionan
- Entender los tipos básicos de transistores
- Conocer las configuraciones de amplificadores
- Aplicar el modelo de transistor en circuitos
- Entender el papel de los transistores en circuitos de audio

## Introducción a los Transistores

### ¿Qué es un Transistor?

Un **transistor** es un dispositivo semiconductor de tres terminales que puede funcionar como amplificador o como interruptor. Es el componente fundamental de la electrónica moderna.

### Historia del Transistor

**Desarrollo**:
- 1947: Primer transistor de punto de contacto (Bell Labs)
- 1948: Transistor de unión bipolar (BJT)
- 1960: Transistor de efecto de campo (FET)
- 1970: Transistor de efecto de campo de metal-óxido-semiconductor (MOSFET)

### Importancia en la Electrónica

**Aplicaciones**:
- Amplificación de señales
- Conmutación digital
- Regulación de voltaje
- Osciladores
- Circuitos integrados

## Transistor de Unión Bipolar (BJT)

### Estructura del BJT

**Tipos**:
- **NPN**: Dos regiones N separadas por una región P
- **PNP**: Dos regiones P separadas por una región N

**Terminales**:
- **Emisor (E)**: Inyecta portadores
- **Base (B)**: Controla el flujo de portadores
- **Colector (C)**: Recoge los portadores

### Símbolos Esquemáticos

**Transistor NPN**:
```
        C
        │
    ┌───┴───┐
    │       │
    B       E
    │       │
    └───────┘
```

**Transistor PNP**:
```
        C
        │
    ┌───┴───┐
    │       │
    B       E
    │       │
    └───────┘
```

### Funcionamiento del BJT

#### Transistor NPN

**Polarización**:
- Base-Emisor: Polarización directa (VBE > 0.7V)
- Base-Colector: Polarización inversa (VBC < 0V)

**Proceso**:
1. Los electrones se inyectan del emisor a la base
2. La base es muy delgada, pocos electrones se recombinan
3. La mayoría de electrones llegan al colector
4. La corriente de base controla la corriente de colector

#### Transistor PNP

**Polarización**:
- Base-Emisor: Polarización directa (VBE < -0.7V)
- Base-Colector: Polarización inversa (VBC > 0V)

**Proceso**:
1. Los huecos se inyectan del emisor a la base
2. La base es muy delgada, pocos huecos se recombinan
3. La mayoría de huecos llegan al colector
4. La corriente de base controla la corriente de colector

### Características del BJT

#### Corrientes

**Relaciones**:
```
IE = IB + IC
IC = β × IB
IE = (β + 1) × IB
```

Donde:
- IE = Corriente de emisor
- IB = Corriente de base
- IC = Corriente de colector
- β = Ganancia de corriente (hFE)

#### Voltajes

**Relaciones**:
```
VCE = VCC - IC × RC
VBE ≈ 0.7V (Si) o 0.3V (Ge)
```

### Configuraciones de Amplificadores

#### 1. Emisor Común

**Características**:
- Alta ganancia de voltaje
- Alta ganancia de corriente
- Inversión de fase
- Impedancia de entrada media
- Impedancia de salida alta

**Circuito**:
```
    VCC ──[RC]── C
                │
                B ── Entrada
                │
                E ──[RE]── GND
```

**Ganancia de Voltaje**:
```
Av = -RC/RE
```

#### 2. Colector Común (Seguidor de Emisor)

**Características**:
- Ganancia de voltaje ≈ 1
- Alta ganancia de corriente
- Sin inversión de fase
- Alta impedancia de entrada
- Baja impedancia de salida

**Circuito**:
```
    VCC ── C
           │
           B ── Entrada
           │
           E ──[RE]── GND
           │
           Salida
```

#### 3. Base Común

**Características**:
- Alta ganancia de voltaje
- Ganancia de corriente ≈ 1
- Sin inversión de fase
- Baja impedancia de entrada
- Alta impedancia de salida

**Circuito**:
```
    VCC ──[RC]── C
                │
                B ──[CB]── GND
                │
                E ── Entrada
```

## Transistor de Efecto de Campo (FET)

### ¿Qué es un FET?

Un **FET** es un transistor que controla la corriente mediante un campo eléctrico, en lugar de una corriente de base.

### Tipos de FET

#### 1. JFET (Junction Field Effect Transistor)

**Estructura**:
- Canal de material semiconductor
- Compuerta (Gate) de material opuesto
- Drenaje (Drain) y Fuente (Source)

**Símbolo N-Channel**:
```
        D
        │
    ┌───┴───┐
    │       │
    G       S
    │       │
    └───────┘
```

**Características**:
- Control por voltaje
- Alta impedancia de entrada
- Bajo ruido
- Operación unidireccional

#### 2. MOSFET (Metal-Oxide-Semiconductor FET)

**Estructura**:
- Canal de material semiconductor
- Compuerta aislada por óxido
- Drenaje y Fuente

**Símbolo N-Channel**:
```
        D
        │
    ┌───┴───┐
    │       │
    G       S
    │       │
    └───────┘
```

**Tipos**:
- **Depletion**: Canal presente sin voltaje de compuerta
- **Enhancement**: Canal creado por voltaje de compuerta

### Características del FET

#### JFET

**Región de Saturación**:
```
ID = IDSS × (1 - VGS/VGS(off))²
```

Donde:
- ID = Corriente de drenaje
- IDSS = Corriente de saturación
- VGS = Voltaje compuerta-fuente
- VGS(off) = Voltaje de corte

#### MOSFET

**Región de Saturación**:
```
ID = K × (VGS - Vth)²
```

Donde:
- K = Parámetro del dispositivo
- Vth = Voltaje umbral

## Aplicaciones en Audio

### Amplificador de Audio

**Circuito Básico**:
```
    VCC ──[RC]── C
                │
                B ──[C1]── Entrada
                │
                E ──[RE]──[CE]── GND
                │
                [C2]── Salida
```

**Componentes**:
- C1: Acoplamiento de entrada
- C2: Acoplamiento de salida
- CE: Desacoplamiento del emisor
- RE: Estabilización de polarización

### Amplificador Diferencial

**Características**:
- Amplifica la diferencia entre dos señales
- Rechaza señales comunes
- Base de amplificadores operacionales

**Circuito**:
```
    VCC ──[RC1]── C1
                │
                B1 ── Entrada 1
                │
                E1 ──[RE]── E2
                │           │
                B2 ── Entrada 2
                │
                C2 ──[RC2]── VCC
```

### Amplificador Push-Pull

**Características**:
- Dos transistores complementarios
- Alta eficiencia
- Baja distorsión
- Amplificadores de potencia

**Circuito**:
```
    VCC ──[Q1]── Salida
           │
           Entrada
           │
    GND ──[Q2]── Salida
```

## Ejercicios Prácticos

### Ejercicio 1: Análisis de Transistor

```
Datos:
- VCC = 12V
- RC = 2kΩ
- RE = 1kΩ
- β = 100
- VBE = 0.7V

Preguntas:
1. ¿Cuál es la corriente de colector?
2. ¿Cuál es el voltaje de colector?
3. ¿Cuál es la ganancia de voltaje?

Solución:
1. IB = (VCC - VBE)/(RC + (β+1)×RE) = (12-0.7)/(2000+101×1000) = 11.3V/103kΩ = 0.11mA
   IC = β×IB = 100×0.11mA = 11mA
2. VC = VCC - IC×RC = 12V - 11mA×2kΩ = 12V - 22V = -10V (saturación)
3. Av = -RC/RE = -2000/1000 = -2
```

### Ejercicio 2: Amplificador con Carga

```
Datos:
- VCC = 15V
- RC = 3kΩ
- RL = 6kΩ
- β = 150
- VBE = 0.7V

Pregunta: ¿Cuál es la ganancia de voltaje con carga?

Solución:
RAC = RC || RL = 3kΩ || 6kΩ = 2kΩ
Av = -RAC/RE = -2000/1000 = -2
```

### Ejercicio 3: FET como Amplificador

```
Datos:
- VDD = 20V
- RD = 5kΩ
- IDSS = 10mA
- VGS(off) = -4V

Pregunta: ¿Cuál es la corriente de drenaje?

Solución:
ID = IDSS × (1 - VGS/VGS(off))²
ID = 10mA × (1 - 0/(-4))² = 10mA × 1² = 10mA
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Transistores**: Dispositivos de tres terminales para amplificación
2. **BJT**: Transistor de unión bipolar, control por corriente
3. **FET**: Transistor de efecto de campo, control por voltaje
4. **Configuraciones**: Emisor común, colector común, base común
5. **Aplicaciones**: Amplificadores de audio, circuitos de potencia

## Próxima Clase

En la siguiente clase estudiaremos los **amplificadores operacionales**, componentes fundamentales para circuitos de audio de alta calidad.

---

## Navegación
- **Anterior**: [Clase 1.3: Semiconductores y Diodos](clase-1-3-semiconductores-diodos.md)
- **Siguiente**: [Clase 1.5: Amplificadores Operacionales](clase-1-5-amplificadores-operacionales.md)
- **Volver al**: [Índice Principal](../../README.md)
