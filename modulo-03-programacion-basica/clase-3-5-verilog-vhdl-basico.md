# Clase 3.5: Verilog/VHDL Básico

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender los conceptos básicos de Verilog y VHDL
- Entender la descripción de hardware digital
- Conocer los elementos fundamentales para procesamiento de audio
- Implementar circuitos básicos para audio digital
- Entender la síntesis y simulación de hardware

## Introducción a los Lenguajes de Descripción de Hardware

### ¿Por qué Verilog/VHDL para Audio?

Los **lenguajes de descripción de hardware** son fundamentales para audio de ultra baja latencia porque:

1. **Paralelismo**: Procesamiento simultáneo de múltiples canales
2. **Latencia**: Procesamiento en hardware con latencia mínima
3. **Precisión**: Control exacto de timing y sincronización
4. **Eficiencia**: Optimización específica para algoritmos de audio
5. **Escalabilidad**: Fácil agregar más canales o funcionalidades

### Verilog vs VHDL

**Verilog**:
- Sintaxis similar a C
- Más fácil de aprender
- Ampliamente usado en la industria
- Mejor para diseño de bajo nivel

**VHDL**:
- Sintaxis más formal y estricta
- Mejor para sistemas complejos
- Más verboso pero más legible
- Mejor para verificación

## Fundamentos de Verilog

### Estructura Básica de un Módulo

```verilog
// Módulo básico para procesamiento de audio
module audio_processor (
    input wire clk,           // Reloj del sistema
    input wire rst,           // Reset
    input wire [15:0] audio_in,  // Entrada de audio 16-bit
    output reg [15:0] audio_out, // Salida de audio 16-bit
    input wire enable         // Habilitación
);

// Declaraciones internas
reg [15:0] audio_buffer;
reg [1:0] state;

// Lógica combinacional
always @(*) begin
    if (enable) begin
        audio_out = audio_buffer;
    end else begin
        audio_out = 16'b0;
    end
end

// Lógica secuencial
always @(posedge clk or posedge rst) begin
    if (rst) begin
        audio_buffer <= 16'b0;
        state <= 2'b00;
    end else begin
        audio_buffer <= audio_in;
        state <= state + 1;
    end
end

endmodule
```

### Tipos de Datos

```verilog
// Tipos de datos básicos
reg [7:0] byte_data;          // Registro de 8 bits
wire [15:0] audio_sample;     // Cable de 16 bits
reg [31:0] counter;           // Contador de 32 bits

// Tipos de datos para audio
reg [15:0] audio_input;       // Entrada de audio 16-bit
reg [23:0] audio_24bit;       // Audio 24-bit
reg [31:0] audio_32bit;       // Audio 32-bit

// Tipos de datos para filtros
reg [15:0] filter_coeff;      // Coeficiente de filtro
reg [31:0] accumulator;       // Acumulador para multiplicaciones
reg [15:0] delay_line[0:1023]; // Línea de retraso de 1024 muestras
```

### Operadores

```verilog
// Operadores aritméticos
reg [15:0] a, b, c;
c = a + b;        // Suma
c = a - b;        // Resta
c = a * b;        // Multiplicación
c = a / b;        // División

// Operadores lógicos
reg [15:0] result;
result = a & b;   // AND
result = a | b;   // OR
result = a ^ b;   // XOR
result = ~a;      // NOT

// Operadores de desplazamiento
result = a << 2;  // Desplazamiento izquierda
result = a >> 2;  // Desplazamiento derecha

// Operadores de comparación
reg flag;
flag = (a == b);  // Igual
flag = (a != b);  // Diferente
flag = (a > b);   // Mayor
flag = (a < b);   // Menor
```

## Circuitos Combinacionales

### Multiplexor

```verilog
// Multiplexor 4:1 para selección de entrada de audio
module audio_mux (
    input wire [15:0] input0,
    input wire [15:0] input1,
    input wire [15:0] input2,
    input wire [15:0] input3,
    input wire [1:0] select,
    output reg [15:0] output_audio
);

always @(*) begin
    case (select)
        2'b00: output_audio = input0;
        2'b01: output_audio = input1;
        2'b10: output_audio = input2;
        2'b11: output_audio = input3;
        default: output_audio = 16'b0;
    endcase
end

endmodule
```

### Decodificador

```verilog
// Decodificador para control de efectos
module effect_decoder (
    input wire [2:0] effect_select,
    output reg [7:0] effect_enable
);

always @(*) begin
    effect_enable = 8'b0;
    case (effect_select)
        3'b000: effect_enable[0] = 1'b1;  // Sin efecto
        3'b001: effect_enable[1] = 1'b1;  // Distorsión
        3'b010: effect_enable[2] = 1'b1;  // Eco
        3'b011: effect_enable[3] = 1'b1;  // Reverb
        3'b100: effect_enable[4] = 1'b1;  // Chorus
        3'b101: effect_enable[5] = 1'b1;  // Flanger
        3'b110: effect_enable[6] = 1'b1;  // Phaser
        3'b111: effect_enable[7] = 1'b1;  // Compresor
    endcase
end

endmodule
```

### Comparador

```verilog
// Comparador para detección de nivel de audio
module audio_level_detector (
    input wire [15:0] audio_input,
    input wire [15:0] threshold,
    output reg level_high,
    output reg level_low,
    output reg clipping
);

always @(*) begin
    level_high = (audio_input > threshold);
    level_low = (audio_input < -threshold);
    clipping = (audio_input > 16'h7FFF) || (audio_input < 16'h8000);
end

endmodule
```

## Circuitos Secuenciales

### Contador

```verilog
// Contador para generación de frecuencias
module frequency_counter (
    input wire clk,
    input wire rst,
    input wire [31:0] target_count,
    output reg [31:0] counter,
    output reg pulse
);

always @(posedge clk or posedge rst) begin
    if (rst) begin
        counter <= 32'b0;
        pulse <= 1'b0;
    end else begin
        if (counter >= target_count) begin
            counter <= 32'b0;
            pulse <= 1'b1;
        end else begin
            counter <= counter + 1;
            pulse <= 1'b0;
        end
    end
end

endmodule
```

### Registro de Desplazamiento

```verilog
// Registro de desplazamiento para línea de retraso
module delay_line (
    input wire clk,
    input wire rst,
    input wire [15:0] audio_in,
    output reg [15:0] audio_out,
    input wire [9:0] delay_length
);

reg [15:0] delay_buffer [0:1023];
integer i;

always @(posedge clk or posedge rst) begin
    if (rst) begin
        for (i = 0; i < 1024; i = i + 1) begin
            delay_buffer[i] <= 16'b0;
        end
        audio_out <= 16'b0;
    end else begin
        // Desplazar datos
        for (i = 1023; i > 0; i = i - 1) begin
            delay_buffer[i] <= delay_buffer[i-1];
        end
        delay_buffer[0] <= audio_in;
        
        // Salida con retraso
        audio_out <= delay_buffer[delay_length];
    end
end

endmodule
```

### Máquina de Estados

```verilog
// Máquina de estados para control de audio
module audio_state_machine (
    input wire clk,
    input wire rst,
    input wire start,
    input wire stop,
    input wire pause,
    output reg [2:0] state,
    output reg audio_enable
);

// Estados
parameter IDLE = 3'b000;
parameter PLAYING = 3'b001;
parameter PAUSED = 3'b010;
parameter STOPPED = 3'b011;

always @(posedge clk or posedge rst) begin
    if (rst) begin
        state <= IDLE;
        audio_enable <= 1'b0;
    end else begin
        case (state)
            IDLE: begin
                if (start) begin
                    state <= PLAYING;
                    audio_enable <= 1'b1;
                end
            end
            
            PLAYING: begin
                if (pause) begin
                    state <= PAUSED;
                    audio_enable <= 1'b0;
                end else if (stop) begin
                    state <= STOPPED;
                    audio_enable <= 1'b0;
                end
            end
            
            PAUSED: begin
                if (start) begin
                    state <= PLAYING;
                    audio_enable <= 1'b1;
                end else if (stop) begin
                    state <= STOPPED;
                end
            end
            
            STOPPED: begin
                if (start) begin
                    state <= PLAYING;
                    audio_enable <= 1'b1;
                end
            end
            
            default: begin
                state <= IDLE;
                audio_enable <= 1'b0;
            end
        endcase
    end
end

endmodule
```

## Procesamiento de Audio en Hardware

### Multiplicador para Audio

```verilog
// Multiplicador para aplicar ganancia
module audio_multiplier (
    input wire clk,
    input wire rst,
    input wire [15:0] audio_input,
    input wire [15:0] gain_factor,
    output reg [15:0] audio_output,
    output reg overflow
);

reg [31:0] product;
reg [15:0] result;

always @(posedge clk or posedge rst) begin
    if (rst) begin
        audio_output <= 16'b0;
        overflow <= 1'b0;
    end else begin
        // Multiplicación
        product = audio_input * gain_factor;
        
        // Verificar overflow
        if (product > 32'h7FFF) begin
            result = 16'h7FFF;
            overflow <= 1'b1;
        end else if (product < 32'h8000) begin
            result = 16'h8000;
            overflow <= 1'b1;
        end else begin
            result = product[15:0];
            overflow <= 1'b0;
        end
        
        audio_output <= result;
    end
end

endmodule
```

### Filtro Pasa-Bajo

```verilog
// Filtro pasa-bajo de primer orden
module lowpass_filter (
    input wire clk,
    input wire rst,
    input wire [15:0] audio_input,
    input wire [15:0] cutoff_freq,
    output reg [15:0] audio_output
);

reg [15:0] prev_output;
reg [31:0] alpha;
reg [31:0] temp_result;

always @(posedge clk or posedge rst) begin
    if (rst) begin
        prev_output <= 16'b0;
        audio_output <= 16'b0;
    end else begin
        // Calcular alpha (simplificado)
        alpha = cutoff_freq;
        
        // Filtro: y[n] = alpha * x[n] + (1-alpha) * y[n-1]
        temp_result = (alpha * audio_input) + ((16'hFFFF - alpha) * prev_output);
        audio_output <= temp_result[31:16];
        prev_output <= audio_output;
    end
end

endmodule
```

### Generador de Tono

```verilog
// Generador de tono sinusoidal
module tone_generator (
    input wire clk,
    input wire rst,
    input wire [31:0] frequency,
    input wire [15:0] amplitude,
    output reg [15:0] audio_output
);

reg [31:0] phase_accumulator;
reg [31:0] phase_increment;
reg [15:0] sine_value;

// Tabla de seno (simplificada)
reg [15:0] sine_table [0:255];

// Inicializar tabla de seno
initial begin
    integer i;
    for (i = 0; i < 256; i = i + 1) begin
        sine_table[i] = $signed(16'h7FFF * $sin(2.0 * 3.14159 * i / 256.0));
    end
end

always @(posedge clk or posedge rst) begin
    if (rst) begin
        phase_accumulator <= 32'b0;
        audio_output <= 16'b0;
    end else begin
        // Calcular incremento de fase
        phase_increment = (frequency * 256) / 44100;
        
        // Actualizar acumulador de fase
        phase_accumulator <= phase_accumulator + phase_increment;
        
        // Obtener valor de seno
        sine_value = sine_table[phase_accumulator[31:24]];
        
        // Aplicar amplitud
        audio_output <= (sine_value * amplitude) >> 15;
    end
end

endmodule
```

## Interfaces de Audio

### Interfaz I2S

```verilog
// Interfaz I2S para comunicación de audio
module i2s_interface (
    input wire clk,
    input wire rst,
    input wire [15:0] audio_data,
    input wire data_valid,
    output reg i2s_bclk,
    output reg i2s_lrclk,
    output reg i2s_data,
    output reg data_ready
);

reg [4:0] bit_counter;
reg [15:0] shift_register;
reg [1:0] state;

parameter IDLE = 2'b00;
parameter SEND_LEFT = 2'b01;
parameter SEND_RIGHT = 2'b10;

always @(posedge clk or posedge rst) begin
    if (rst) begin
        i2s_bclk <= 1'b0;
        i2s_lrclk <= 1'b0;
        i2s_data <= 1'b0;
        data_ready <= 1'b0;
        bit_counter <= 5'b0;
        state <= IDLE;
    end else begin
        // Generar BCLK (bit clock)
        i2s_bclk <= ~i2s_bclk;
        
        case (state)
            IDLE: begin
                if (data_valid) begin
                    shift_register <= audio_data;
                    state <= SEND_LEFT;
                    i2s_lrclk <= 1'b0;
                    bit_counter <= 5'b0;
                end
            end
            
            SEND_LEFT: begin
                if (i2s_bclk) begin
                    i2s_data <= shift_register[15];
                    shift_register <= {shift_register[14:0], 1'b0};
                    bit_counter <= bit_counter + 1;
                    
                    if (bit_counter == 15) begin
                        state <= SEND_RIGHT;
                        i2s_lrclk <= 1'b1;
                        bit_counter <= 5'b0;
                    end
                end
            end
            
            SEND_RIGHT: begin
                if (i2s_bclk) begin
                    i2s_data <= shift_register[15];
                    shift_register <= {shift_register[14:0], 1'b0};
                    bit_counter <= bit_counter + 1;
                    
                    if (bit_counter == 15) begin
                        state <= IDLE;
                        data_ready <= 1'b1;
                    end
                end
            end
        endcase
    end
end

endmodule
```

### Interfaz SPI

```verilog
// Interfaz SPI para control de audio
module spi_interface (
    input wire clk,
    input wire rst,
    input wire spi_clk,
    input wire spi_mosi,
    input wire spi_cs,
    output reg spi_miso,
    output reg [15:0] control_data,
    output reg data_valid
);

reg [15:0] shift_register;
reg [4:0] bit_counter;
reg [1:0] state;

parameter IDLE = 2'b00;
parameter RECEIVE = 2'b01;
parameter COMPLETE = 2'b10;

always @(posedge spi_clk or posedge rst) begin
    if (rst) begin
        shift_register <= 16'b0;
        bit_counter <= 5'b0;
        control_data <= 16'b0;
        data_valid <= 1'b0;
        state <= IDLE;
    end else begin
        case (state)
            IDLE: begin
                if (~spi_cs) begin
                    state <= RECEIVE;
                    bit_counter <= 5'b0;
                    data_valid <= 1'b0;
                end
            end
            
            RECEIVE: begin
                shift_register <= {shift_register[14:0], spi_mosi};
                bit_counter <= bit_counter + 1;
                
                if (bit_counter == 15) begin
                    state <= COMPLETE;
                    control_data <= shift_register;
                    data_valid <= 1'b1;
                end
            end
            
            COMPLETE: begin
                if (spi_cs) begin
                    state <= IDLE;
                    data_valid <= 1'b0;
                end
            end
        endcase
    end
end

endmodule
```

## Ejemplo Práctico: Procesador de Audio Completo

```verilog
// Procesador de audio completo
module audio_processor_top (
    input wire clk,
    input wire rst,
    input wire [15:0] audio_input,
    input wire [15:0] gain_control,
    input wire [15:0] filter_cutoff,
    input wire [2:0] effect_select,
    input wire enable,
    output reg [15:0] audio_output,
    output reg clipping,
    output reg data_ready
);

// Señales internas
wire [15:0] gain_output;
wire [15:0] filter_output;
wire [15:0] effect_output;
wire gain_overflow;
wire [7:0] effect_enable;

// Instanciar módulos
audio_multiplier gain_stage (
    .clk(clk),
    .rst(rst),
    .audio_input(audio_input),
    .gain_factor(gain_control),
    .audio_output(gain_output),
    .overflow(gain_overflow)
);

lowpass_filter filter_stage (
    .clk(clk),
    .rst(rst),
    .audio_input(gain_output),
    .cutoff_freq(filter_cutoff),
    .audio_output(filter_output)
);

effect_decoder effect_decoder_inst (
    .effect_select(effect_select),
    .effect_enable(effect_enable)
);

// Lógica de salida
always @(posedge clk or posedge rst) begin
    if (rst) begin
        audio_output <= 16'b0;
        clipping <= 1'b0;
        data_ready <= 1'b0;
    end else begin
        if (enable) begin
            audio_output <= filter_output;
            clipping <= gain_overflow;
            data_ready <= 1'b1;
        end else begin
            audio_output <= 16'b0;
            clipping <= 1'b0;
            data_ready <= 1'b0;
        end
    end
end

endmodule
```

## Simulación y Verificación

### Testbench Básico

```verilog
// Testbench para el procesador de audio
module audio_processor_tb;

// Señales de prueba
reg clk;
reg rst;
reg [15:0] audio_input;
reg [15:0] gain_control;
reg [15:0] filter_cutoff;
reg [2:0] effect_select;
reg enable;
wire [15:0] audio_output;
wire clipping;
wire data_ready;

// Instanciar módulo bajo prueba
audio_processor_top dut (
    .clk(clk),
    .rst(rst),
    .audio_input(audio_input),
    .gain_control(gain_control),
    .filter_cutoff(filter_cutoff),
    .effect_select(effect_select),
    .enable(enable),
    .audio_output(audio_output),
    .clipping(clipping),
    .data_ready(data_ready)
);

// Generar reloj
initial begin
    clk = 0;
    forever #5 clk = ~clk; // 100 MHz
end

// Proceso de prueba
initial begin
    // Inicializar señales
    rst = 1;
    audio_input = 16'b0;
    gain_control = 16'h8000; // Ganancia 1.0
    filter_cutoff = 16'h1000; // 1000 Hz
    effect_select = 3'b000;
    enable = 0;
    
    // Reset
    #20 rst = 0;
    
    // Habilitar procesador
    #10 enable = 1;
    
    // Probar con diferentes entradas
    #10 audio_input = 16'h1000; // Entrada pequeña
    #10 audio_input = 16'h4000; // Entrada media
    #10 audio_input = 16'h7000; // Entrada grande
    
    // Probar con diferentes ganancias
    #10 gain_control = 16'h4000; // Ganancia 0.5
    #10 gain_control = 16'hC000; // Ganancia 1.5
    
    // Probar con diferentes efectos
    #10 effect_select = 3'b001; // Distorsión
    #10 effect_select = 3'b010; // Eco
    
    // Finalizar simulación
    #100 $finish;
end

// Monitorear señales
initial begin
    $monitor("Time: %0t, Input: %h, Output: %h, Clipping: %b", 
             $time, audio_input, audio_output, clipping);
end

endmodule
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Fundamentos**: Estructura de módulos, tipos de datos, operadores
2. **Circuitos Combinacionales**: Multiplexores, decodificadores, comparadores
3. **Circuitos Secuenciales**: Contadores, registros, máquinas de estados
4. **Procesamiento de Audio**: Multiplicadores, filtros, generadores
5. **Interfaces**: I2S, SPI para comunicación de audio
6. **Sistema Completo**: Procesador de audio integrado
7. **Verificación**: Testbenches y simulación

## Próxima Clase

En la siguiente clase estudiaremos la **optimización de código**, que nos permitirá maximizar el rendimiento y minimizar la latencia.

---

## Navegación
- **Anterior**: [Clase 3.4: Python para Análisis de Audio](clase-3-4-python-analisis-audio.md)
- **Siguiente**: [Clase 3.6: Optimización de Código](clase-3-6-optimizacion-codigo.md)
- **Volver al**: [Índice Principal](../../README.md)
