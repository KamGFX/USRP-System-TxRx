# Sistema de Recepción de Imágenes mediante USRP y Modulación QPSK

## Descripción General

El sistema de recepción tiene como objetivo recuperar una imagen transmitida inalámbricamente a través de una plataforma USRP utilizando modulación QPSK. Se trata de una arquitectura básica que permite reconstruir la imagen original a partir de los símbolos recibidos, sin implementar mecanismos avanzados de sincronización o detección de tramas.

Inicialmente, la señal de radiofrecuencia es capturada por la USRP receptora, la cual realiza la conversión de la señal analógica a muestras digitales complejas. Estas muestras son procesadas por un filtro Root Raised Cosine (RRC), cuya función es reducir la interferencia entre símbolos (ISI) y mejorar la calidad de la señal recibida.

Posteriormente, la señal filtrada es entregada al demodulador QPSK, encargado de convertir los símbolos complejos recibidos en una secuencia binaria equivalente. Cada símbolo QPSK representa dos bits de información, permitiendo recuperar el flujo binario transmitido.

La secuencia de bits obtenida es procesada por el bloque **BitsToBytes**, el cual agrupa cada conjunto de ocho bits para reconstruir los bytes originales enviados desde el transmisor.

Finalmente, los bytes recuperados son enviados al bloque **RebuildImage**, encargado de reorganizar la información en una matriz tridimensional correspondiente a la imagen original. Para ello se utilizan dimensiones previamente conocidas (92 × 94 × 3), donde 92 corresponde al número de filas, 94 al número de columnas y 3 a los canales de color RGB. Una vez reconstruida la matriz de la imagen, esta es visualizada mediante un bloque **Video Viewer**, permitiendo observar el resultado de la transmisión.

## Arquitectura del Sistema

```text
USRP Receiver
      ↓
Filtro Root Raised Cosine (RRC)
      ↓
Demodulador QPSK
      ↓
BitsToBytes
      ↓
RebuildImage
      ↓
Video Viewer
```

## Funcionamiento de los Bloques

### 1. USRP Receiver

La USRP receptora captura la señal transmitida a través del canal inalámbrico y realiza la conversión de la señal analógica a digital. El resultado es una secuencia de muestras complejas en banda base que serán procesadas por el resto de la cadena de recepción.

### 2. Filtro Root Raised Cosine (RRC)

El filtro Root Raised Cosine se emplea para reducir la interferencia entre símbolos (ISI), la cual puede aparecer debido a las características del canal de transmisión y al proceso de modulación.

Su principal función es mejorar la calidad de los símbolos recibidos y preparar la señal para la etapa de demodulación.

### 3. Demodulador QPSK

El demodulador QPSK recibe las muestras complejas provenientes del filtro RRC y determina el símbolo transmitido más probable.

Cada símbolo QPSK representa dos bits de información, por lo que esta etapa convierte la señal modulada nuevamente en una secuencia binaria.

```text
Símbolos QPSK → Bits
```

### 4. BitsToBytes

La salida del demodulador corresponde a un flujo continuo de bits. El bloque **BitsToBytes** agrupa cada conjunto de ocho bits para reconstruir los bytes originales generados durante la transmisión.

```text
Bits → Bytes
```

Esta etapa permite recuperar la representación digital original de la imagen.

### 5. RebuildImage

El bloque **RebuildImage** recibe el vector de bytes reconstruido y reorganiza la información para formar nuevamente la imagen.

En esta implementación se utilizan dimensiones fijas:

- Filas: 92
- Columnas: 94
- Canales: 3 (RGB)

La reconstrucción se realiza mediante una operación de reorganización de datos (`reshape`), permitiendo recuperar la estructura matricial original de la imagen.

```text
Vector de Bytes → Imagen RGB
```

### 6. Video Viewer

Finalmente, la imagen reconstruida es enviada al bloque **Video Viewer**, el cual permite visualizar el resultado de la transmisión en tiempo real.

## Características del Sistema

- Recepción inalámbrica utilizando dispositivos USRP.
- Demodulación QPSK.
- Filtrado mediante Root Raised Cosine.
- Reconstrucción de imágenes RGB.
- Visualización en tiempo real de la imagen recibida.
- Implementación sencilla para validación de la cadena de comunicación.

## Limitaciones

Al tratarse de una implementación básica, el sistema no incorpora mecanismos avanzados de comunicación digital, entre ellos:

- Sincronización temporal de símbolos.
- Sincronización de frecuencia y fase de portadora.
- Detección automática de tramas.
- Verificación de integridad mediante CRC.
- Corrección de errores (FEC).
- Interleaving o técnicas de protección contra errores.

Debido a estas limitaciones, el sistema puede presentar degradaciones en la imagen recibida cuando existen errores de sincronización, ruido en el canal o pérdidas de muestras durante la transmisión.

## Aplicación

Esta arquitectura constituye una implementación inicial para la transmisión y recepción de imágenes mediante radios definidas por software (SDR). Su objetivo principal es validar el funcionamiento de la cadena de comunicación digital, permitiendo posteriormente incorporar mecanismos más avanzados de sincronización, detección de tramas y corrección de errores para mejorar la robustez del sistema.
