# Sistema de Transmisión de Imágenes vía SDR con Modulación QPSK

## Introducción

La transmisión de imágenes a través de canales inalámbricos es un pilar fundamental de las telecomunicaciones modernas. Un sistema de este tipo convierte una imagen digital en una señal de radiofrecuencia apta para ser enviada por el aire, atravesando etapas de serialización, codificación binaria, modulación digital y filtrado de pulsos.

Técnicas como **QPSK** (*Quadrature Phase Shift Keying*) permiten transmitir múltiples bits por símbolo, mejorando la eficiencia espectral. El filtrado de **raíz de coseno alzado** reduce la interferencia entre símbolos (ISI) y limita el ancho de banda. Con plataformas de **radio definida por software (SDR)**, es posible implementar y validar estos sistemas experimentalmente, observando en tiempo real parámetros como el diagrama de constelación y la tasa de error de bits (BER).

---

## Descripción del Sistema (MATLAB/Simulink)

El sistema implementado sigue el siguiente flujo de procesamiento:

$$\text{Imagen RGB} \rightarrow \text{Serialización} \rightarrow \text{Bits} \rightarrow \text{QPSK} \rightarrow \text{Filtrado} \rightarrow \text{Transmisión RF}$$

### Bloques del sistema

| Bloque | Función | Dimensiones |
|---|---|---|
| **img_tx** | Fuente de imagen RGB de entrada | `92×94×3` |
| **Serialize Image** | Serializa la imagen en un vector de bytes | `25944×1` |
| **ByteToBits** | Convierte bytes a bits (×8) | `207552×1` |
| **QPSK** | Modula agrupando 2 bits por símbolo | `103776×1` |
| **Constellation Diagram** | Visualiza los 4 puntos de la constelación QPSK | — |
| **Square Root Filter** | Filtro de conformación de pulsos (raíz de coseno alzado) | `830208×1` |
| **SDRu Transmitter 30F4129** | Transmite la señal por radiofrecuencia vía hardware SDR | — |

### Flujo de datos

1. La imagen RGB (`92×94×3 px`) se serializa en **25,944 bytes**.
2. Cada byte se expande a 8 bits → **207,552 bits**.
3. El modulador QPSK agrupa 2 bits por símbolo → **103,776 símbolos complejos**.
4. El filtro de raíz de coseno alzado aplica sobremuestreo → **830,208 muestras**.
5. La señal es transmitida por el hardware SDR al canal inalámbrico.
