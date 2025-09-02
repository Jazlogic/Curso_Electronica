# Clase 3.3: Programación Orientada a Objetos

## Objetivos de la Clase
Al finalizar esta clase, serás capaz de:
- Comprender los conceptos fundamentales de la programación orientada a objetos
- Entender las clases, objetos, herencia y polimorfismo
- Aplicar la POO en el diseño de sistemas de audio
- Crear interfaces y abstracciones para componentes de audio
- Entender el patrón de diseño para aplicaciones de audio

## Introducción a la Programación Orientada a Objetos

### ¿Por qué POO para Audio?

La **Programación Orientada a Objetos** es ideal para sistemas de audio porque:

1. **Modularidad**: Cada componente de audio es un objeto independiente
2. **Reutilización**: Los filtros, efectos y procesadores se pueden reutilizar
3. **Mantenibilidad**: Código más fácil de mantener y extender
4. **Abstracción**: Ocultar la complejidad de implementación
5. **Escalabilidad**: Fácil agregar nuevos componentes

### Conceptos Fundamentales

- **Clase**: Plantilla para crear objetos
- **Objeto**: Instancia de una clase
- **Encapsulación**: Ocultar datos internos
- **Herencia**: Reutilizar código de clases padre
- **Polimorfismo**: Mismo interfaz, diferentes implementaciones

## Clases y Objetos

### Definición de Clases

```cpp
// Clase básica para una muestra de audio
class AudioSample {
private:
    float value;
    int sample_rate;
    
public:
    // Constructor
    AudioSample(float val = 0.0f, int sr = 44100) : value(val), sample_rate(sr) {}
    
    // Destructor
    ~AudioSample() {}
    
    // Getters
    float getValue() const { return value; }
    int getSampleRate() const { return sample_rate; }
    
    // Setters
    void setValue(float val) { value = val; }
    void setSampleRate(int sr) { sample_rate = sr; }
    
    // Métodos
    void normalize() {
        if (value > 1.0f) value = 1.0f;
        if (value < -1.0f) value = -1.0f;
    }
    
    void applyGain(float gain) {
        value *= gain;
        normalize();
    }
};
```

### Uso de Objetos

```cpp
int main() {
    // Crear objetos
    AudioSample sample1(0.5f, 44100);
    AudioSample sample2; // Usa valores por defecto
    
    // Usar métodos
    sample1.applyGain(2.0f);
    sample2.setValue(0.8f);
    
    // Acceder a datos
    std::cout << "Sample 1: " << sample1.getValue() << std::endl;
    std::cout << "Sample 2: " << sample2.getValue() << std::endl;
    
    return 0;
}
```

## Encapsulación

### Modificadores de Acceso

```cpp
class AudioBuffer {
private:
    float* data;
    int size;
    int capacity;
    
protected:
    void resize(int new_capacity);
    
public:
    AudioBuffer(int cap = 1024);
    ~AudioBuffer();
    
    // Interfaz pública
    void push(float value);
    float pop();
    float get(int index) const;
    void set(int index, float value);
    int getSize() const { return size; }
    bool isEmpty() const { return size == 0; }
    bool isFull() const { return size == capacity; }
};

// Implementación
AudioBuffer::AudioBuffer(int cap) : capacity(cap), size(0) {
    data = new float[capacity];
}

AudioBuffer::~AudioBuffer() {
    delete[] data;
}

void AudioBuffer::push(float value) {
    if (size < capacity) {
        data[size++] = value;
    }
}

float AudioBuffer::pop() {
    if (size > 0) {
        return data[--size];
    }
    return 0.0f;
}

void AudioBuffer::resize(int new_capacity) {
    if (new_capacity > capacity) {
        float* new_data = new float[new_capacity];
        for (int i = 0; i < size; i++) {
            new_data[i] = data[i];
        }
        delete[] data;
        data = new_data;
        capacity = new_capacity;
    }
}
```

## Herencia

### Clase Base para Filtros

```cpp
// Clase base abstracta para filtros
class AudioFilter {
protected:
    float sample_rate;
    bool enabled;
    
public:
    AudioFilter(float sr = 44100.0f) : sample_rate(sr), enabled(true) {}
    virtual ~AudioFilter() {}
    
    // Método virtual puro (debe ser implementado por clases derivadas)
    virtual float process(float input) = 0;
    
    // Métodos comunes
    void setEnabled(bool en) { enabled = en; }
    bool isEnabled() const { return enabled; }
    void setSampleRate(float sr) { sample_rate = sr; }
    float getSampleRate() const { return sample_rate; }
    
    // Método virtual (puede ser sobrescrito)
    virtual void reset() {}
};
```

### Filtro Pasa-Bajo

```cpp
class LowPassFilter : public AudioFilter {
private:
    float cutoff_frequency;
    float alpha;
    float prev_output;
    
public:
    LowPassFilter(float sr = 44100.0f, float cutoff = 1000.0f) 
        : AudioFilter(sr), cutoff_frequency(cutoff), prev_output(0.0f) {
        updateAlpha();
    }
    
    // Implementar método virtual puro
    float process(float input) override {
        if (!enabled) return input;
        
        float output = alpha * input + (1.0f - alpha) * prev_output;
        prev_output = output;
        return output;
    }
    
    // Sobrescribir método virtual
    void reset() override {
        prev_output = 0.0f;
    }
    
    // Métodos específicos
    void setCutoffFrequency(float cutoff) {
        cutoff_frequency = cutoff;
        updateAlpha();
    }
    
    float getCutoffFrequency() const { return cutoff_frequency; }
    
private:
    void updateAlpha() {
        float rc = 1.0f / (2.0f * M_PI * cutoff_frequency);
        alpha = 1.0f / (1.0f + rc * sample_rate);
    }
};
```

### Filtro Pasa-Alto

```cpp
class HighPassFilter : public AudioFilter {
private:
    float cutoff_frequency;
    float alpha;
    float prev_input;
    float prev_output;
    
public:
    HighPassFilter(float sr = 44100.0f, float cutoff = 1000.0f) 
        : AudioFilter(sr), cutoff_frequency(cutoff), 
          prev_input(0.0f), prev_output(0.0f) {
        updateAlpha();
    }
    
    float process(float input) override {
        if (!enabled) return input;
        
        float output = alpha * (prev_output + input - prev_input);
        prev_input = input;
        prev_output = output;
        return output;
    }
    
    void reset() override {
        prev_input = 0.0f;
        prev_output = 0.0f;
    }
    
    void setCutoffFrequency(float cutoff) {
        cutoff_frequency = cutoff;
        updateAlpha();
    }
    
    float getCutoffFrequency() const { return cutoff_frequency; }
    
private:
    void updateAlpha() {
        float rc = 1.0f / (2.0f * M_PI * cutoff_frequency);
        alpha = rc / (rc + 1.0f / sample_rate);
    }
};
```

## Polimorfismo

### Uso de Polimorfismo

```cpp
// Función que procesa con cualquier filtro
void processWithFilter(AudioFilter* filter, float* buffer, int size) {
    for (int i = 0; i < size; i++) {
        buffer[i] = filter->process(buffer[i]);
    }
}

// Ejemplo de uso
int main() {
    const int buffer_size = 1024;
    float* audio_buffer = new float[buffer_size];
    
    // Llenar buffer con señal de prueba
    for (int i = 0; i < buffer_size; i++) {
        audio_buffer[i] = 0.5f * sin(2.0f * M_PI * 1000.0f * i / 44100.0f);
    }
    
    // Crear diferentes filtros
    LowPassFilter lowpass(44100.0f, 500.0f);
    HighPassFilter highpass(44100.0f, 2000.0f);
    
    // Usar polimorfismo
    AudioFilter* filter = &lowpass;
    processWithFilter(filter, audio_buffer, buffer_size);
    
    // Cambiar filtro
    filter = &highpass;
    processWithFilter(filter, audio_buffer, buffer_size);
    
    delete[] audio_buffer;
    return 0;
}
```

## Interfaces y Clases Abstractas

### Interfaz para Efectos de Audio

```cpp
// Interfaz para efectos de audio
class AudioEffect {
public:
    virtual ~AudioEffect() {}
    virtual float process(float input) = 0;
    virtual void reset() = 0;
    virtual void setParameter(int param, float value) = 0;
    virtual float getParameter(int param) const = 0;
};
```

### Implementación de Efectos

```cpp
// Efecto de distorsión
class DistortionEffect : public AudioEffect {
private:
    float gain;
    float threshold;
    
public:
    DistortionEffect(float g = 2.0f, float th = 0.7f) 
        : gain(g), threshold(th) {}
    
    float process(float input) override {
        float output = input * gain;
        
        // Distorsión suave
        if (output > threshold) {
            output = threshold + (output - threshold) / (1.0f + (output - threshold));
        } else if (output < -threshold) {
            output = -threshold + (output + threshold) / (1.0f - (output + threshold));
        }
        
        return output;
    }
    
    void reset() override {
        // No hay estado que resetear
    }
    
    void setParameter(int param, float value) override {
        switch (param) {
            case 0: gain = value; break;
            case 1: threshold = value; break;
        }
    }
    
    float getParameter(int param) const override {
        switch (param) {
            case 0: return gain;
            case 1: return threshold;
            default: return 0.0f;
        }
    }
};

// Efecto de eco
class EchoEffect : public AudioEffect {
private:
    float* delay_buffer;
    int buffer_size;
    int write_pos;
    float feedback;
    float mix;
    
public:
    EchoEffect(int delay_samples = 4410, float fb = 0.3f, float m = 0.5f) 
        : buffer_size(delay_samples), write_pos(0), feedback(fb), mix(m) {
        delay_buffer = new float[buffer_size];
        for (int i = 0; i < buffer_size; i++) {
            delay_buffer[i] = 0.0f;
        }
    }
    
    ~EchoEffect() {
        delete[] delay_buffer;
    }
    
    float process(float input) override {
        int read_pos = (write_pos + 1) % buffer_size;
        float delayed = delay_buffer[read_pos];
        
        float output = input + delayed * feedback;
        delay_buffer[write_pos] = output;
        
        write_pos = (write_pos + 1) % buffer_size;
        
        return input * (1.0f - mix) + output * mix;
    }
    
    void reset() override {
        for (int i = 0; i < buffer_size; i++) {
            delay_buffer[i] = 0.0f;
        }
        write_pos = 0;
    }
    
    void setParameter(int param, float value) override {
        switch (param) {
            case 0: feedback = value; break;
            case 1: mix = value; break;
        }
    }
    
    float getParameter(int param) const override {
        switch (param) {
            case 0: return feedback;
            case 1: return mix;
            default: return 0.0f;
        }
    }
};
```

## Composición y Agregación

### Procesador de Audio Completo

```cpp
class AudioProcessor {
private:
    std::vector<AudioEffect*> effects;
    std::vector<AudioFilter*> filters;
    float gain;
    bool enabled;
    
public:
    AudioProcessor() : gain(1.0f), enabled(true) {}
    
    ~AudioProcessor() {
        // Limpiar efectos
        for (auto effect : effects) {
            delete effect;
        }
        // Limpiar filtros
        for (auto filter : filters) {
            delete filter;
        }
    }
    
    // Agregar efectos
    void addEffect(AudioEffect* effect) {
        effects.push_back(effect);
    }
    
    void addFilter(AudioFilter* filter) {
        filters.push_back(filter);
    }
    
    // Procesar audio
    float process(float input) {
        if (!enabled) return input;
        
        float output = input;
        
        // Aplicar filtros
        for (auto filter : filters) {
            output = filter->process(output);
        }
        
        // Aplicar efectos
        for (auto effect : effects) {
            output = effect->process(output);
        }
        
        // Aplicar ganancia
        output *= gain;
        
        return output;
    }
    
    // Configuración
    void setGain(float g) { gain = g; }
    float getGain() const { return gain; }
    void setEnabled(bool en) { enabled = en; }
    bool isEnabled() const { return enabled; }
    
    // Resetear todo
    void reset() {
        for (auto effect : effects) {
            effect->reset();
        }
        for (auto filter : filters) {
            filter->reset();
        }
    }
};
```

## Patrones de Diseño

### Patrón Singleton para Configuración

```cpp
class AudioConfig {
private:
    static AudioConfig* instance;
    int sample_rate;
    int buffer_size;
    int channels;
    
    AudioConfig() : sample_rate(44100), buffer_size(1024), channels(2) {}
    
public:
    static AudioConfig* getInstance() {
        if (instance == nullptr) {
            instance = new AudioConfig();
        }
        return instance;
    }
    
    // Getters
    int getSampleRate() const { return sample_rate; }
    int getBufferSize() const { return buffer_size; }
    int getChannels() const { return channels; }
    
    // Setters
    void setSampleRate(int sr) { sample_rate = sr; }
    void setBufferSize(int bs) { buffer_size = bs; }
    void setChannels(int ch) { channels = ch; }
};

// Inicializar instancia estática
AudioConfig* AudioConfig::instance = nullptr;
```

### Patrón Factory para Crear Efectos

```cpp
class EffectFactory {
public:
    enum EffectType {
        DISTORTION,
        ECHO,
        REVERB,
        CHORUS
    };
    
    static AudioEffect* createEffect(EffectType type) {
        switch (type) {
            case DISTORTION:
                return new DistortionEffect();
            case ECHO:
                return new EchoEffect();
            case REVERB:
                return new ReverbEffect();
            case CHORUS:
                return new ChorusEffect();
            default:
                return nullptr;
        }
    }
};
```

### Patrón Observer para Eventos

```cpp
// Interfaz para observadores
class AudioObserver {
public:
    virtual ~AudioObserver() {}
    virtual void onAudioLevelChanged(float level) = 0;
    virtual void onAudioClipping() = 0;
};

// Sujeto observable
class AudioMonitor {
private:
    std::vector<AudioObserver*> observers;
    float current_level;
    bool clipping;
    
public:
    void addObserver(AudioObserver* observer) {
        observers.push_back(observer);
    }
    
    void removeObserver(AudioObserver* observer) {
        observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
    }
    
    void setAudioLevel(float level) {
        current_level = level;
        notifyLevelChanged(level);
        
        if (level > 1.0f && !clipping) {
            clipping = true;
            notifyClipping();
        } else if (level <= 1.0f) {
            clipping = false;
        }
    }
    
private:
    void notifyLevelChanged(float level) {
        for (auto observer : observers) {
            observer->onAudioLevelChanged(level);
        }
    }
    
    void notifyClipping() {
        for (auto observer : observers) {
            observer->onAudioClipping();
        }
    }
};
```

## Ejemplo Práctico: Sistema de Audio Completo

```cpp
#include <iostream>
#include <vector>
#include <memory>

// Implementación completa del sistema
class AudioSystem {
private:
    AudioProcessor processor;
    AudioMonitor monitor;
    AudioConfig* config;
    
public:
    AudioSystem() {
        config = AudioConfig::getInstance();
        
        // Configurar procesador
        processor.addFilter(new LowPassFilter(config->getSampleRate(), 1000.0f));
        processor.addEffect(EffectFactory::createEffect(EffectFactory::ECHO));
        processor.setGain(0.8f);
    }
    
    void processAudio(float* buffer, int size) {
        for (int i = 0; i < size; i++) {
            float input = buffer[i];
            float output = processor.process(input);
            buffer[i] = output;
            
            // Monitorear nivel
            float level = abs(output);
            monitor.setAudioLevel(level);
        }
    }
    
    void reset() {
        processor.reset();
    }
    
    void setGain(float gain) {
        processor.setGain(gain);
    }
    
    void addObserver(AudioObserver* observer) {
        monitor.addObserver(observer);
    }
};

// Observador para logging
class AudioLogger : public AudioObserver {
public:
    void onAudioLevelChanged(float level) override {
        if (level > 0.8f) {
            std::cout << "Audio level high: " << level << std::endl;
        }
    }
    
    void onAudioClipping() override {
        std::cout << "WARNING: Audio clipping detected!" << std::endl;
    }
};

int main() {
    // Crear sistema de audio
    AudioSystem audioSystem;
    
    // Agregar observador
    AudioLogger logger;
    audioSystem.addObserver(&logger);
    
    // Crear buffer de prueba
    const int buffer_size = 1024;
    float* audio_buffer = new float[buffer_size];
    
    // Llenar con señal de prueba
    for (int i = 0; i < buffer_size; i++) {
        audio_buffer[i] = 0.8f * sin(2.0f * M_PI * 1000.0f * i / 44100.0f);
    }
    
    // Procesar audio
    audioSystem.processAudio(audio_buffer, buffer_size);
    
    // Mostrar resultados
    std::cout << "Procesamiento completado" << std::endl;
    std::cout << "Primeras 10 muestras:" << std::endl;
    for (int i = 0; i < 10; i++) {
        std::cout << audio_buffer[i] << std::endl;
    }
    
    delete[] audio_buffer;
    return 0;
}
```

## Resumen de la Clase

En esta clase hemos aprendido:

1. **Clases y Objetos**: Definición, constructores, destructores
2. **Encapsulación**: Modificadores de acceso, interfaz pública
3. **Herencia**: Clases base, clases derivadas, métodos virtuales
4. **Polimorfismo**: Mismo interfaz, diferentes implementaciones
5. **Interfaces**: Clases abstractas, implementaciones
6. **Composición**: Agregar objetos a otros objetos
7. **Patrones de Diseño**: Singleton, Factory, Observer

## Próxima Clase

En la siguiente clase estudiaremos **Python para análisis de audio**, que nos permitirá crear herramientas de análisis y prototipado rápido.

---

## Navegación
- **Anterior**: [Clase 3.2: Estructuras de Datos y Algoritmos](clase-3-2-estructuras-datos-algoritmos.md)
- **Siguiente**: [Clase 3.4: Python para Análisis de Audio](clase-3-4-python-analisis-audio.md)
- **Volver al**: [Índice Principal](../../README.md)
