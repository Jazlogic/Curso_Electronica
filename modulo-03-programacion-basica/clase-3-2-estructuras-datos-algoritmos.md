# Clase 3.2: Estructuras de Datos y Algoritmos

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender las estructuras de datos fundamentales
- Entender los algoritmos de ordenamiento y búsqueda
- Conocer las estructuras de datos específicas para audio
- Aplicar algoritmos eficientes en el procesamiento de audio
- Entender la complejidad computacional y su impacto en la latencia

## Introducción a las Estructuras de Datos

### ¿Por qué son importantes para el audio?

Las **estructuras de datos** son fundamentales en el procesamiento de audio porque:

1. **Eficiencia**: Algoritmos optimizados reducen la latencia
2. **Organización**: Datos estructurados facilitan el procesamiento
3. **Memoria**: Gestión eficiente de buffers y caches
4. **Tiempo Real**: Estructuras que garantizan tiempo constante

### Complejidad Computacional

**Notación Big O**:
- **O(1)**: Tiempo constante (ideal para tiempo real)
- **O(log n)**: Tiempo logarítmico (muy eficiente)
- **O(n)**: Tiempo lineal (aceptable para buffers pequeños)
- **O(n log n)**: Tiempo cuasi-lineal (FFT, ordenamiento)
- **O(n²)**: Tiempo cuadrático (evitar en tiempo real)

## Arrays y Vectores

### Arrays Estáticos

```c
// Buffer de audio estático
#define BUFFER_SIZE 1024
float audio_buffer[BUFFER_SIZE];

// Inicializar buffer
void init_buffer(float* buffer, int size) {
    for (int i = 0; i < size; i++) {
        buffer[i] = 0.0f;
    }
}

// Copiar buffer
void copy_buffer(float* src, float* dst, int size) {
    for (int i = 0; i < size; i++) {
        dst[i] = src[i];
    }
}
```

### Arrays Dinámicos

```c
// Crear array dinámico
float* create_audio_buffer(int size) {
    float* buffer = (float*)malloc(size * sizeof(float));
    if (buffer == NULL) {
        printf("Error: No se pudo asignar memoria\n");
        return NULL;
    }
    return buffer;
}

// Redimensionar array
float* resize_audio_buffer(float* buffer, int old_size, int new_size) {
    float* new_buffer = (float*)realloc(buffer, new_size * sizeof(float));
    if (new_buffer == NULL) {
        printf("Error: No se pudo redimensionar\n");
        return buffer; // Mantener buffer original
    }
    return new_buffer;
}
```

### Operaciones con Arrays

```c
// Sumar dos buffers
void add_buffers(float* a, float* b, float* result, int size) {
    for (int i = 0; i < size; i++) {
        result[i] = a[i] + b[i];
    }
}

// Multiplicar buffer por escalar
void scale_buffer(float* buffer, int size, float scale) {
    for (int i = 0; i < size; i++) {
        buffer[i] *= scale;
    }
}

// Encontrar máximo valor
float find_max(float* buffer, int size) {
    float max_val = buffer[0];
    for (int i = 1; i < size; i++) {
        if (buffer[i] > max_val) {
            max_val = buffer[i];
        }
    }
    return max_val;
}
```

## Listas Enlazadas

### Lista Enlazada Simple

```c
// Nodo de la lista
typedef struct node {
    float data;
    struct node* next;
} node_t;

// Lista enlazada
typedef struct {
    node_t* head;
    int size;
} linked_list_t;

// Crear nodo
node_t* create_node(float data) {
    node_t* node = (node_t*)malloc(sizeof(node_t));
    if (node == NULL) return NULL;
    
    node->data = data;
    node->next = NULL;
    return node;
}

// Insertar al inicio
void insert_front(linked_list_t* list, float data) {
    node_t* new_node = create_node(data);
    if (new_node == NULL) return;
    
    new_node->next = list->head;
    list->head = new_node;
    list->size++;
}

// Eliminar del inicio
float remove_front(linked_list_t* list) {
    if (list->head == NULL) return 0.0f;
    
    node_t* temp = list->head;
    float data = temp->data;
    list->head = temp->next;
    free(temp);
    list->size--;
    
    return data;
}
```

### Lista Circular para Audio

```c
// Buffer circular para audio
typedef struct {
    float* buffer;
    int size;
    int head;
    int tail;
    int count;
} circular_buffer_t;

// Crear buffer circular
circular_buffer_t* create_circular_buffer(int size) {
    circular_buffer_t* cb = (circular_buffer_t*)malloc(sizeof(circular_buffer_t));
    if (cb == NULL) return NULL;
    
    cb->buffer = (float*)malloc(size * sizeof(float));
    if (cb->buffer == NULL) {
        free(cb);
        return NULL;
    }
    
    cb->size = size;
    cb->head = 0;
    cb->tail = 0;
    cb->count = 0;
    
    return cb;
}

// Insertar en buffer circular
int circular_buffer_push(circular_buffer_t* cb, float data) {
    if (cb->count >= cb->size) {
        return 0; // Buffer lleno
    }
    
    cb->buffer[cb->head] = data;
    cb->head = (cb->head + 1) % cb->size;
    cb->count++;
    
    return 1;
}

// Extraer de buffer circular
int circular_buffer_pop(circular_buffer_t* cb, float* data) {
    if (cb->count <= 0) {
        return 0; // Buffer vacío
    }
    
    *data = cb->buffer[cb->tail];
    cb->tail = (cb->tail + 1) % cb->size;
    cb->count--;
    
    return 1;
}
```

## Pilas y Colas

### Pila (Stack)

```c
// Pila para procesamiento de audio
typedef struct {
    float* data;
    int top;
    int capacity;
} stack_t;

// Crear pila
stack_t* create_stack(int capacity) {
    stack_t* stack = (stack_t*)malloc(sizeof(stack_t));
    if (stack == NULL) return NULL;
    
    stack->data = (float*)malloc(capacity * sizeof(float));
    if (stack->data == NULL) {
        free(stack);
        return NULL;
    }
    
    stack->top = -1;
    stack->capacity = capacity;
    
    return stack;
}

// Apilar (push)
int stack_push(stack_t* stack, float value) {
    if (stack->top >= stack->capacity - 1) {
        return 0; // Pila llena
    }
    
    stack->data[++stack->top] = value;
    return 1;
}

// Desapilar (pop)
int stack_pop(stack_t* stack, float* value) {
    if (stack->top < 0) {
        return 0; // Pila vacía
    }
    
    *value = stack->data[stack->top--];
    return 1;
}
```

### Cola (Queue)

```c
// Cola para procesamiento de audio
typedef struct {
    float* data;
    int front;
    int rear;
    int size;
    int capacity;
} queue_t;

// Crear cola
queue_t* create_queue(int capacity) {
    queue_t* queue = (queue_t*)malloc(sizeof(queue_t));
    if (queue == NULL) return NULL;
    
    queue->data = (float*)malloc(capacity * sizeof(float));
    if (queue->data == NULL) {
        free(queue);
        return NULL;
    }
    
    queue->front = 0;
    queue->rear = -1;
    queue->size = 0;
    queue->capacity = capacity;
    
    return queue;
}

// Encolar (enqueue)
int queue_enqueue(queue_t* queue, float value) {
    if (queue->size >= queue->capacity) {
        return 0; // Cola llena
    }
    
    queue->rear = (queue->rear + 1) % queue->capacity;
    queue->data[queue->rear] = value;
    queue->size++;
    
    return 1;
}

// Desencolar (dequeue)
int queue_dequeue(queue_t* queue, float* value) {
    if (queue->size <= 0) {
        return 0; // Cola vacía
    }
    
    *value = queue->data[queue->front];
    queue->front = (queue->front + 1) % queue->capacity;
    queue->size--;
    
    return 1;
}
```

## Árboles

### Árbol Binario de Búsqueda

```c
// Nodo del árbol
typedef struct tree_node {
    float frequency;
    float amplitude;
    struct tree_node* left;
    struct tree_node* right;
} tree_node_t;

// Crear nodo
tree_node_t* create_tree_node(float freq, float amp) {
    tree_node_t* node = (tree_node_t*)malloc(sizeof(tree_node_t));
    if (node == NULL) return NULL;
    
    node->frequency = freq;
    node->amplitude = amp;
    node->left = NULL;
    node->right = NULL;
    
    return node;
}

// Insertar en árbol
tree_node_t* insert_tree_node(tree_node_t* root, float freq, float amp) {
    if (root == NULL) {
        return create_tree_node(freq, amp);
    }
    
    if (freq < root->frequency) {
        root->left = insert_tree_node(root->left, freq, amp);
    } else if (freq > root->frequency) {
        root->right = insert_tree_node(root->right, freq, amp);
    }
    
    return root;
}

// Buscar en árbol
tree_node_t* search_tree(tree_node_t* root, float freq) {
    if (root == NULL || root->frequency == freq) {
        return root;
    }
    
    if (freq < root->frequency) {
        return search_tree(root->left, freq);
    } else {
        return search_tree(root->right, freq);
    }
}
```

## Algoritmos de Ordenamiento

### Ordenamiento por Inserción

```c
// Ordenamiento por inserción (O(n²))
void insertion_sort(float* array, int size) {
    for (int i = 1; i < size; i++) {
        float key = array[i];
        int j = i - 1;
        
        while (j >= 0 && array[j] > key) {
            array[j + 1] = array[j];
            j--;
        }
        array[j + 1] = key;
    }
}
```

### Ordenamiento Rápido (Quicksort)

```c
// Función auxiliar para particionar
int partition(float* array, int low, int high) {
    float pivot = array[high];
    int i = low - 1;
    
    for (int j = low; j < high; j++) {
        if (array[j] <= pivot) {
            i++;
            // Intercambiar
            float temp = array[i];
            array[i] = array[j];
            array[j] = temp;
        }
    }
    
    // Intercambiar pivot
    float temp = array[i + 1];
    array[i + 1] = array[high];
    array[high] = temp;
    
    return i + 1;
}

// Quicksort (O(n log n) promedio)
void quicksort(float* array, int low, int high) {
    if (low < high) {
        int pi = partition(array, low, high);
        quicksort(array, low, pi - 1);
        quicksort(array, pi + 1, high);
    }
}
```

### Ordenamiento por Conteo (para audio)

```c
// Ordenamiento por conteo para valores enteros (O(n + k))
void counting_sort(int* array, int size, int max_val) {
    int* count = (int*)calloc(max_val + 1, sizeof(int));
    int* output = (int*)malloc(size * sizeof(int));
    
    // Contar ocurrencias
    for (int i = 0; i < size; i++) {
        count[array[i]]++;
    }
    
    // Acumular conteos
    for (int i = 1; i <= max_val; i++) {
        count[i] += count[i - 1];
    }
    
    // Construir array ordenado
    for (int i = size - 1; i >= 0; i--) {
        output[count[array[i]] - 1] = array[i];
        count[array[i]]--;
    }
    
    // Copiar de vuelta
    for (int i = 0; i < size; i++) {
        array[i] = output[i];
    }
    
    free(count);
    free(output);
}
```

## Algoritmos de Búsqueda

### Búsqueda Lineal

```c
// Búsqueda lineal (O(n))
int linear_search(float* array, int size, float target) {
    for (int i = 0; i < size; i++) {
        if (array[i] == target) {
            return i;
        }
    }
    return -1; // No encontrado
}
```

### Búsqueda Binaria

```c
// Búsqueda binaria (O(log n))
int binary_search(float* array, int size, float target) {
    int left = 0;
    int right = size - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (array[mid] == target) {
            return mid;
        } else if (array[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return -1; // No encontrado
}
```

## Estructuras de Datos Específicas para Audio

### Buffer de Audio con Overlap

```c
// Buffer con solapamiento para FFT
typedef struct {
    float* buffer;
    int size;
    int overlap;
    int hop_size;
    int position;
} overlap_buffer_t;

// Crear buffer con solapamiento
overlap_buffer_t* create_overlap_buffer(int size, int overlap) {
    overlap_buffer_t* ob = (overlap_buffer_t*)malloc(sizeof(overlap_buffer_t));
    if (ob == NULL) return NULL;
    
    ob->buffer = (float*)calloc(size, sizeof(float));
    if (ob->buffer == NULL) {
        free(ob);
        return NULL;
    }
    
    ob->size = size;
    ob->overlap = overlap;
    ob->hop_size = size - overlap;
    ob->position = 0;
    
    return ob;
}

// Procesar con solapamiento
void process_overlap_buffer(overlap_buffer_t* ob, float* input, int input_size) {
    for (int i = 0; i < input_size; i++) {
        ob->buffer[ob->position] = input[i];
        ob->position++;
        
        if (ob->position >= ob->size) {
            // Procesar buffer completo
            process_audio_block(ob->buffer, ob->size);
            
            // Desplazar para solapamiento
            memmove(ob->buffer, ob->buffer + ob->hop_size, ob->overlap * sizeof(float));
            ob->position = ob->overlap;
        }
    }
}
```

### Tabla de Búsqueda (Lookup Table)

```c
// Tabla de búsqueda para seno
typedef struct {
    float* table;
    int size;
    float phase_step;
} lookup_table_t;

// Crear tabla de seno
lookup_table_t* create_sine_table(int size) {
    lookup_table_t* lt = (lookup_table_t*)malloc(sizeof(lookup_table_t));
    if (lt == NULL) return NULL;
    
    lt->table = (float*)malloc(size * sizeof(float));
    if (lt->table == NULL) {
        free(lt);
        return NULL;
    }
    
    lt->size = size;
    lt->phase_step = 2.0f * M_PI / size;
    
    // Llenar tabla
    for (int i = 0; i < size; i++) {
        lt->table[i] = sin(i * lt->phase_step);
    }
    
    return lt;
}

// Obtener valor de la tabla
float lookup_sine(lookup_table_t* lt, float phase) {
    // Normalizar fase
    while (phase < 0) phase += 2.0f * M_PI;
    while (phase >= 2.0f * M_PI) phase -= 2.0f * M_PI;
    
    // Convertir a índice
    int index = (int)(phase / lt->phase_step);
    
    // Interpolación lineal
    float t = (phase - index * lt->phase_step) / lt->phase_step;
    int next_index = (index + 1) % lt->size;
    
    return lt->table[index] + t * (lt->table[next_index] - lt->table[index]);
}
```

## Algoritmos de Procesamiento de Audio

### Filtro de Mediana

```c
// Filtro de mediana para reducir ruido
float median_filter(float* buffer, int size) {
    // Crear copia para ordenar
    float* temp = (float*)malloc(size * sizeof(float));
    for (int i = 0; i < size; i++) {
        temp[i] = buffer[i];
    }
    
    // Ordenar
    insertion_sort(temp, size);
    
    // Obtener mediana
    float median = temp[size / 2];
    
    free(temp);
    return median;
}
```

### Detección de Picos

```c
// Detectar picos en señal de audio
typedef struct {
    int* peaks;
    int count;
    int capacity;
} peak_detector_t;

// Detectar picos
peak_detector_t* detect_peaks(float* signal, int size, float threshold) {
    peak_detector_t* pd = (peak_detector_t*)malloc(sizeof(peak_detector_t));
    if (pd == NULL) return NULL;
    
    pd->peaks = (int*)malloc(size * sizeof(int));
    if (pd->peaks == NULL) {
        free(pd);
        return NULL;
    }
    
    pd->count = 0;
    pd->capacity = size;
    
    for (int i = 1; i < size - 1; i++) {
        if (signal[i] > threshold && 
            signal[i] > signal[i-1] && 
            signal[i] > signal[i+1]) {
            pd->peaks[pd->count++] = i;
        }
    }
    
    return pd;
}
```

## Ejemplo Práctico: Procesador de Audio con Estructuras de Datos

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>

// Estructura principal del procesador
typedef struct {
    circular_buffer_t* input_buffer;
    circular_buffer_t* output_buffer;
    lookup_table_t* sine_table;
    float gain;
    float frequency;
    float phase;
} audio_processor_t;

// Inicializar procesador
audio_processor_t* create_audio_processor(int buffer_size) {
    audio_processor_t* proc = (audio_processor_t*)malloc(sizeof(audio_processor_t));
    if (proc == NULL) return NULL;
    
    proc->input_buffer = create_circular_buffer(buffer_size);
    proc->output_buffer = create_circular_buffer(buffer_size);
    proc->sine_table = create_sine_table(1024);
    
    if (proc->input_buffer == NULL || proc->output_buffer == NULL || proc->sine_table == NULL) {
        // Limpiar recursos
        if (proc->input_buffer) free(proc->input_buffer);
        if (proc->output_buffer) free(proc->output_buffer);
        if (proc->sine_table) free(proc->sine_table);
        free(proc);
        return NULL;
    }
    
    proc->gain = 1.0f;
    proc->frequency = 440.0f;
    proc->phase = 0.0f;
    
    return proc;
}

// Procesar audio
void process_audio(audio_processor_t* proc, float* input, float* output, int size) {
    for (int i = 0; i < size; i++) {
        // Agregar a buffer de entrada
        circular_buffer_push(proc->input_buffer, input[i]);
        
        // Procesar (ejemplo: modulación de amplitud)
        float carrier = lookup_sine(proc->sine_table, proc->phase);
        float modulated = input[i] * carrier * proc->gain;
        
        // Actualizar fase
        proc->phase += 2.0f * M_PI * proc->frequency / 44100.0f;
        if (proc->phase >= 2.0f * M_PI) {
            proc->phase -= 2.0f * M_PI;
        }
        
        // Agregar a buffer de salida
        circular_buffer_push(proc->output_buffer, modulated);
        
        // Extraer de buffer de salida
        float output_sample;
        if (circular_buffer_pop(proc->output_buffer, &output_sample)) {
            output[i] = output_sample;
        } else {
            output[i] = 0.0f;
        }
    }
}

int main() {
    // Crear procesador
    audio_processor_t* processor = create_audio_processor(1024);
    if (processor == NULL) {
        printf("Error: No se pudo crear el procesador\n");
        return 1;
    }
    
    // Crear buffers de prueba
    const int buffer_size = 1024;
    float* input_buffer = (float*)malloc(buffer_size * sizeof(float));
    float* output_buffer = (float*)malloc(buffer_size * sizeof(float));
    
    // Llenar con señal de prueba
    for (int i = 0; i < buffer_size; i++) {
        input_buffer[i] = 0.5f * sin(2.0f * M_PI * 1000.0f * i / 44100.0f);
    }
    
    // Procesar
    process_audio(processor, input_buffer, output_buffer, buffer_size);
    
    // Mostrar resultados
    printf("Procesamiento completado\n");
    printf("Primeras 10 muestras de salida:\n");
    for (int i = 0; i < 10; i++) {
        printf("%.4f\n", output_buffer[i]);
    }
    
    // Limpiar
    free(input_buffer);
    free(output_buffer);
    // Nota: En una aplicación real, se debería limpiar el procesador
    
    return 0;
}
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Arrays**: Estáticos, dinámicos, operaciones
2. **Listas Enlazadas**: Simples, circulares, aplicaciones
3. **Pilas y Colas**: Implementación, uso en audio
4. **Árboles**: Búsqueda binaria, aplicaciones
5. **Algoritmos**: Ordenamiento, búsqueda, complejidad
6. **Estructuras Específicas**: Buffers de audio, tablas de búsqueda
7. **Aplicaciones**: Filtros, detección de picos, procesamiento

## Próxima Clase

En la siguiente clase estudiaremos la **programación orientada a objetos**, que nos permitirá crear sistemas de audio más modulares y mantenibles.

---

## Navegación
- **Anterior**: [Clase 3.1: Fundamentos de C/C++](clase-3-1-fundamentos-c-cpp.md)
- **Siguiente**: [Clase 3.3: Programación Orientada a Objetos](clase-3-3-programacion-orientada-objetos.md)
- **Volver al**: [Índice Principal](../../README.md)
