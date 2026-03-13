# Implementación del sistema de carga y preprocesamiento de la imagen

El sistema desarrollado para el procesamiento y transmisión de imágenes se divide en dos etapas principales:

* Carga y preparación de la imagen en MATLAB
* Preprocesamiento de la imagen dentro de Simulink

Estas etapas garantizan que la imagen cumpla con las condiciones necesarias para ser utilizada posteriormente en el sistema de comunicación implementado en el modelo.

---

# 1. Carga y preparación de la imagen en MATLAB

La primera etapa del sistema se ejecuta en el entorno de **MATLAB** y tiene como objetivo cargar una imagen desde el sistema de archivos, verificar su formato y preparar la estructura de datos necesaria para que pueda ser utilizada dentro del modelo de **Simulink**.

Inicialmente, la imagen es leída desde el almacenamiento local utilizando las herramientas de procesamiento de imágenes de MATLAB. Una vez cargada, se identifican sus dimensiones espaciales y el número de canales de color, lo cual permite verificar si la imagen corresponde a una imagen **RGB válida** o si requiere alguna adaptación previa para cumplir con el formato esperado por el sistema.

Durante esta etapa se realizan varias verificaciones y ajustes importantes:

* Se valida que la imagen tenga **tres canales de color (RGB)**.
* Si la imagen es originalmente **escala de grises**, el sistema replica el canal existente para generar tres canales equivalentes.
* Si la imagen contiene un **canal alfa (transparencia)**, este es descartado para conservar únicamente la información de color.

Posteriormente se verifica el **tipo de dato** utilizado para representar los valores de intensidad de los píxeles. Para garantizar la compatibilidad con los bloques de procesamiento de Simulink, la imagen se convierte al formato **uint8**, el cual es el formato estándar para imágenes digitales de **8 bits por canal**.

Esto asegura que los valores de intensidad de los píxeles se encuentren dentro del rango:

* **0 – 255**

Una vez validada y ajustada la estructura de la imagen, esta se organiza en un formato de datos compatible con el bloque **From Workspace** de Simulink.

Para ello, la imagen se encapsula dentro de una estructura temporal que incluye:

* Los valores de la señal
* El instante de tiempo asociado a la señal

Este formato permite que **Simulink interprete la imagen como una señal proveniente directamente del entorno de MATLAB**, facilitando así su integración dentro del modelo de simulación.

Finalmente, se realiza una verificación de la información cargada, mostrando:

* Dimensiones de la imagen
* Tipo de dato
* Rango de valores de intensidad

Esto permite confirmar que la imagen fue cargada correctamente antes de ejecutar la simulación.

<p align="center">
<img src="../../images/Valentina_color.jpeg" width="300">
</p>

---

# 2. Preprocesamiento de la imagen en Simulink

La segunda etapa del sistema se implementa dentro del entorno de **Simulink**, donde se realiza el preprocesamiento de la imagen previamente cargada desde MATLAB.

Esta etapa se ejecuta mediante un bloque **MATLAB Function**, el cual permite integrar directamente algoritmos escritos en MATLAB dentro del flujo de procesamiento del modelo de simulación.

El objetivo principal de esta etapa es transformar la imagen desde el espacio de color **RGB** hacia el espacio de color **YCbCr**, el cual es ampliamente utilizado en sistemas de:

* Compresión de imágenes
* Transmisión de video
* Procesamiento digital de señales

Este espacio de color separa la información de:

* **Luminancia (brillo)**
* **Crominancia (color)**

Esto permite optimizar los procesos de compresión al tratar de manera diferente cada tipo de información.

---

## Separación de canales

Inicialmente se separan los tres canales de la imagen:

* **R (Red)** – componente rojo  
* **G (Green)** – componente verde  
* **B (Blue)** – componente azul  

Estos canales se convierten temporalmente a un formato numérico de mayor precisión para permitir la realización de operaciones matemáticas durante la transformación de color.

---

## Transformación de color RGB → YCbCr

Posteriormente se aplica una transformación lineal basada en el estándar internacional:

* **ITU-R BT.601**

Este estándar define una relación matemática entre los componentes RGB y los componentes del espacio de color YCbCr.

A partir de esta transformación se obtienen tres nuevas componentes:

* **Y (Luminancia)**  
Representa la intensidad o brillo percibido de la imagen.

* **Cb (Crominancia azul)**  
Representa la diferencia del componente azul respecto a la luminancia.

* **Cr (Crominancia roja)**  
Representa la diferencia del componente rojo respecto a la luminancia.

La componente **Y** se obtiene mediante una combinación ponderada de los tres canales RGB, teniendo en cuenta la sensibilidad del ojo humano a cada color.

Las componentes **Cb y Cr** representan la información de color relativa respecto a la luminancia.

---

## Ajuste de rango y formato

Una vez calculadas las componentes Y, Cb y Cr:

* Se asegura que los valores se mantengan dentro del rango válido **0–255**
* Se evita saturación o desbordamiento de valores
* Los resultados se convierten nuevamente al formato **uint8**

Esto permite que las señales puedan seguir siendo procesadas eficientemente dentro del modelo de Simulink o ser visualizadas posteriormente en MATLAB.

