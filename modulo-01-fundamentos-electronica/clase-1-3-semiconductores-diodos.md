# Clase 1.3: Semiconductores y Diodos

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender qué son los semiconductores y cómo funcionan
- Entender el comportamiento de los diodos
- Conocer los tipos de diodos y sus aplicaciones
- Aplicar el modelo de diodo en circuitos
- Entender el papel de los diodos en circuitos de audio

## Introducción a los Semiconductores

### ¿Qué es un Semiconductor?

Un **semiconductor** es un material que tiene propiedades eléctricas intermedias entre un conductor y un aislante. Su conductividad puede ser controlada mediante la adición de impurezas o la aplicación de campos eléctricos.

### Propiedades de los Semiconductores

**Conductividad**:
- Conductores: 10⁶ - 10⁸ S/m
- Semiconductores: 10⁻⁶ - 10⁶ S/m
- Aislantes: 10⁻¹⁵ - 10⁻⁶ S/m

**Dependencia de la Temperatura**:
- Aumenta con la temperatura
- Comportamiento opuesto a los metales

### Materiales Semiconductores

**Elementales**:
- Silicio (Si): Más común, estable
- Germanio (Ge): Menos común, más sensible a la temperatura

**Compuestos**:
- Arseniuro de Galio (GaAs): Para alta frecuencia
- Nitruro de Galio (GaN): Para alta potencia

## Estructura Atómica del Silicio

### Estructura Cristalina

El silicio forma una estructura cristalina donde cada átomo comparte electrones con sus vecinos mediante enlaces covalentes.

**Configuración Electrónica**:
- Silicio: 1s² 2s² 2p⁶ 3s² 3p²
- 4 electrones de valencia
- Forma 4 enlaces covalentes

### Banda de Energía

**Conceptos Clave**:
- **Banda de Valencia**: Nivel de energía más alto ocupado
- **Banda de Conducción**: Nivel de energía más bajo desocupado
- **Gap de Energía**: Diferencia entre bandas (1.1 eV para Si)

## Doping de Semiconductores

### ¿Qué es el Doping?

El **doping** es el proceso de añadir impurezas controladas a un semiconductor para modificar sus propiedades eléctricas.

### Tipos de Doping

#### 1. Doping Tipo N (Negativo)

**Impurezas Donadoras**:
- Fósforo (P): 5 electrones de valencia
- Arsénico (As): 5 electrones de valencia
- Antimonio (Sb): 5 electrones de valencia

**Resultado**:
- Electrones libres adicionales
- Portadores mayoritarios: electrones
- Portadores minoritarios: huecos

#### 2. Doping Tipo P (Positivo)

**Impurezas Aceptoras**:
- Boro (B): 3 electrones de valencia
- Aluminio (Al): 3 electrones de valencia
- Galio (Ga): 3 electrones de valencia

**Resultado**:
- Huecos adicionales
- Portadores mayoritarios: huecos
- Portadores minoritarios: electrones

### Concentración de Portadores

**En equilibrio térmico**:
```
n × p = ni²
```

Donde:
- n = Concentración de electrones
- p = Concentración de huecos
- ni = Concentración intrínseca (1.5 × 10¹⁰ cm⁻³ para Si a 300K)

## Unión PN

### ¿Qué es una Unión PN?

Una **unión PN** se forma cuando se unen un semiconductor tipo P y uno tipo N. Esta unión es la base de todos los dispositivos semiconductores.

### Formación de la Unión

**Proceso**:
1. Los electrones del lado N se difunden al lado P
2. Los huecos del lado P se difunden al lado N
3. Se forma una región de agotamiento
4. Se establece un campo eléctrico interno

### Región de Agotamiento

**Características**:
- Sin portadores libres
- Campo eléctrico interno
- Diferencia de potencial (barreira de potencial)
- Ancho dependiente del dopaje

## Diodos

### ¿Qué es un Diodo?

Un **diodo** es un dispositivo semiconductor de dos terminales que permite el flujo de corriente en una sola dirección.

### Símbolo Esquemático
```
    ┌─|>|─┐
    │     │
    └─────┘
```

### Características del Diodo

#### Polarización Directa

**Condiciones**:
- Ánodo positivo respecto al cátodo
- Voltaje > 0.7V (Si) o 0.3V (Ge)

**Comportamiento**:
- Corriente alta
- Resistencia baja
- Diodo "encendido"

#### Polarización Inversa

**Condiciones**:
- Ánodo negativo respecto al cátodo
- Voltaje < 0V

**Comportamiento**:
- Corriente muy baja (corriente de fuga)
- Resistencia muy alta
- Diodo "apagado"

### Ecuación del Diodo

**Ecuación de Shockley**:
```
I = Is × (e^(V/Vt) - 1)
```

Donde:
- I = Corriente del diodo
- Is = Corriente de saturación inversa
- V = Voltaje del diodo
- Vt = Voltaje térmico (26mV a 300K)

### Modelo Simplificado

**Modelo de Voltaje Constante**:
- Polarización directa: V = 0.7V (Si)
- Polarización inversa: I = 0A

## Tipos de Diodos

### 1. Diodo Rectificador

**Características**:
- Alta corriente
- Baja frecuencia
- Voltaje de ruptura alto

**Aplicaciones**:
- Rectificación de corriente alterna
- Fuentes de alimentación

### 2. Diodo Zener

**Características**:
- Voltaje de ruptura controlado
- Operación en polarización inversa
- Regulación de voltaje

**Símbolo**:
```
    ┌─|>|─┐
    │     │
    └─────┘
```

**Aplicaciones**:
- Reguladores de voltaje
- Protección contra sobretensiones

### 3. Diodo LED

**Características**:
- Emite luz cuando está polarizado directamente
- Voltaje de polarización: 1.5V - 3.5V
- Corriente típica: 10mA - 20mA

**Símbolo**:
```
    ┌─|>|─┐
    │  ●  │
    └─────┘
```

**Aplicaciones**:
- Indicadores luminosos
- Iluminación
- Comunicaciones ópticas

### 4. Diodo Varactor

**Características**:
- Capacitancia variable con voltaje
- Operación en polarización inversa
- Sintonización de frecuencia

**Aplicaciones**:
- Osciladores controlados por voltaje (VCO)
- Sintonización de radiofrecuencia

## Circuitos con Diodos

### Rectificador de Media Onda

**Circuito**:
```
    Entrada AC ──[D]──[R]── Salida
```

**Funcionamiento**:
- Conduce durante el semiciclo positivo
- Bloquea durante el semiciclo negativo
- Salida: pulsos positivos

### Rectificador de Onda Completa

**Circuito con Transformador de Derivación Central**:
```
    Entrada AC ──[D1]──[R]── Salida
                │
                [D2]
```

**Funcionamiento**:
- D1 conduce durante el semiciclo positivo
- D2 conduce durante el semiciclo negativo
- Salida: pulsos positivos continuos

### Puente Rectificador

**Circuito**:
```
    Entrada AC ──[D1]──[D2]── Salida
                │     │
                [D3]──[D4]
```

**Ventajas**:
- No requiere transformador de derivación central
- Mayor eficiencia
- Menor costo

## Aplicaciones en Audio

### Protección de Polaridad

**Circuito**:
```
    Entrada ──[D1]──[D2]── Salida
```

**Funcionamiento**:
- Protege contra polaridad inversa
- Diodos en paralelo con polaridad opuesta
- Uno conduce, el otro bloquea

### Limitador de Voltaje

**Circuito**:
```
    Entrada ──[R]──[D1]── Salida
                │
                [D2]
```

**Funcionamiento**:
- Limita el voltaje de salida
- Diodos Zener en serie
- Protege circuitos sensibles

### Detector de Picos

**Circuito**:
```
    Entrada ──[D]──[C]── Salida
```

**Funcionamiento**:
- Detecta picos de voltaje
- Condensador mantiene el voltaje pico
- Diodo permite carga, bloquea descarga

## Ejercicios Prácticos

### Ejercicio 1: Análisis de Diodo

```
Datos:
- Voltaje de entrada: 5V
- Resistencia: 1kΩ
- Diodo de silicio

Pregunta: ¿Cuál es la corriente?

Solución:
Vd = 0.7V (modelo simplificado)
VR = 5V - 0.7V = 4.3V
I = VR/R = 4.3V/1kΩ = 4.3mA
```

### Ejercicio 2: Rectificador de Media Onda

```
Datos:
- Entrada: 12V RMS
- Resistencia de carga: 100Ω

Preguntas:
1. ¿Cuál es el voltaje pico de entrada?
2. ¿Cuál es el voltaje pico de salida?
3. ¿Cuál es el voltaje promedio de salida?

Solución:
1. Vp = 12V × √2 = 16.97V
2. Vp_salida = 16.97V - 0.7V = 16.27V
3. Vpromedio = Vp_salida/π = 16.27V/π = 5.18V
```

### Ejercicio 3: Diodo Zener

```
Datos:
- Voltaje de entrada: 15V
- Resistencia limitadora: 1kΩ
- Diodo Zener: 5.1V

Pregunta: ¿Cuál es el voltaje de salida?

Solución:
VZ = 5.1V (voltaje Zener)
Vout = 5.1V (regulado)
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Semiconductores**: Materiales con conductividad intermedia
2. **Doping**: Adición de impurezas para controlar propiedades
3. **Unión PN**: Base de dispositivos semiconductores
4. **Diodos**: Dispositivos de dos terminales, unidireccionales
5. **Tipos de Diodos**: Rectificador, Zener, LED, Varactor
6. **Aplicaciones**: Rectificación, protección, limitación

## Próxima Clase

En la siguiente clase estudiaremos los **transistores**, dispositivos fundamentales para amplificación y conmutación.

---

## Navegación
- **Anterior**: [Clase 1.2: Componentes Electrónicos Básicos](clase-1-2-componentes-electronicos-basicos.md)
- **Siguiente**: [Clase 1.3: Transistores](clase-1-3-transistores.md)
- **Volver al**: [Índice Principal](../../README.md)
