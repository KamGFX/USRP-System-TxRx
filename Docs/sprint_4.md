# Selección del método de compresión: Transformada Wavelet de Haar

## Introducción

En los sistemas de transmisión digital de imágenes es común aplicar técnicas de compresión con el objetivo de reducir el volumen de datos que deben ser transmitidos a través del canal de comunicación. La compresión permite disminuir los requerimientos de ancho de banda, reducir el tiempo de transmisión y mejorar la eficiencia del sistema.

Existen dos tipos principales de compresión de imágenes:

- **Compresión sin pérdidas**, donde la imagen original puede recuperarse exactamente.
- **Compresión con pérdidas**, donde parte de la información se elimina para lograr una mayor reducción del tamaño de los datos.

Para este proyecto se selecciona un método de **compresión con pérdidas basado en la Transformada Wavelet de Haar**, debido a su simplicidad matemática, baja complejidad computacional y facilidad de implementación en entornos como MATLAB y Simulink.

---

# Fundamentos de la Transformada Wavelet de Haar

La transformada de Haar es una de las transformadas wavelet más simples y fue propuesta por el matemático **Alfred Haar en 1909**. Este método permite representar una señal o una imagen mediante un conjunto de funciones denominadas **wavelets**, que permiten analizar la información tanto en el dominio espacial como en el dominio de frecuencia.

A diferencia de métodos como la transformada de Fourier, que analizan las señales únicamente en términos de frecuencias globales, las wavelets permiten analizar simultáneamente la **localización espacial y la frecuencia de la señal**, lo cual resulta especialmente útil en el procesamiento de imágenes.

La transformada wavelet de Haar se basa en un proceso de descomposición de la señal mediante operaciones simples de:

- **Promedio**
- **Diferencia**

Estas operaciones permiten separar la información de la señal en componentes de baja frecuencia (información general) y componentes de alta frecuencia (detalles).

---

# Principio de funcionamiento

El algoritmo de la transformada de Haar opera tomando pares de valores consecutivos y calculando dos componentes:

- **Promedio (aproximación)**: representa la información general de la señal.
- **Diferencia (detalle)**: representa las variaciones locales o detalles.

Cuando la señal presenta correlación entre valores consecutivos —como ocurre en la mayoría de las imágenes— los valores promedio contienen la mayor parte de la información relevante, mientras que las diferencias tienden a ser pequeñas.

Este proceso puede repetirse de forma iterativa sobre los promedios obtenidos, generando una representación jerárquica de la señal.

Desde el punto de vista computacional, la transformada de Haar tiene una complejidad aproximada de:

O(N)

lo cual significa que el costo de cálculo crece de forma lineal con el tamaño de los datos, haciendo que el algoritmo sea muy eficiente.

---

# Aplicación de la transformada de Haar en imágenes

En el caso de imágenes, la transformada se aplica en **dos dimensiones**.

El procedimiento consiste en:

1. Aplicar la transformada a cada fila de la imagen.
2. Aplicar la transformada a cada columna del resultado.

Después de este proceso, la imagen se divide en cuatro subbandas:

- **LL (Low-Low)**: contiene la aproximación de la imagen.
- **LH (Low-High)**: contiene detalles horizontales.
- **HL (High-Low)**: contiene detalles verticales.
- **HH (High-High)**: contiene detalles diagonales.

La mayor parte de la energía de la imagen se concentra en la subbanda **LL**, mientras que las otras subbandas contienen información de detalles finos.

---

# Compresión utilizando la transformada de Haar

La compresión se logra eliminando o reduciendo los coeficientes de menor importancia obtenidos en la transformada.

El proceso general de compresión consiste en:

1. Aplicar la transformada wavelet a la imagen.
2. Identificar los coeficientes más significativos.
3. Eliminar o reducir los coeficientes de menor magnitud.
4. Almacenar o transmitir únicamente los coeficientes relevantes.

Posteriormente, para reconstruir la imagen, se aplica la **transformada inversa de Haar**.

Aunque la imagen reconstruida no es exactamente igual a la original, el resultado suele mantener una calidad visual aceptable.

---

# Justificación de la selección del método

La selección de la **Transformada Wavelet de Haar** como método de compresión resulta adecuada para los requerimientos del sistema de transmisión de imágenes desarrollado en este proyecto.

En primer lugar, el sistema requiere que la imagen sea **comprimida antes de ser serializada y transmitida**, con el fin de reducir la cantidad de información que debe enviarse a través del canal de comunicación. Al utilizar la transformada de Haar es posible concentrar la mayor parte de la información visual en un conjunto reducido de coeficientes, lo que permite disminuir significativamente el volumen de datos.

En segundo lugar, el sistema de transmisión implementado incluye procesos de **modulación digital y transmisión mediante hardware definido por software (USRP)**. En este contexto, reducir la cantidad de bits a transmitir facilita el proceso de modulación y disminuye el tiempo necesario para enviar la imagen completa.

Otro aspecto importante es que el proyecto requiere implementar el sistema utilizando **MATLAB y Simulink**, lo cual favorece el uso de algoritmos simples y de baja complejidad computacional. La transformada wavelet de Haar cumple con este requisito, ya que se basa únicamente en operaciones básicas de suma, resta y división, lo que permite integrarla fácilmente dentro del modelo de simulación.

Además, el sistema debe ser **modular**, separando las diferentes etapas del procesamiento en bloques independientes. La compresión mediante la transformada de Haar puede implementarse como un módulo dentro del flujo del sistema, ubicado entre la carga de la imagen y la etapa de serialización de los datos.

Finalmente, el objetivo del sistema no es conservar exactamente todos los píxeles originales de la imagen, sino **transmitir y reconstruir una imagen visualmente reconocible**. Por esta razón, el uso de compresión con pérdidas resulta adecuado, ya que permite reducir considerablemente el tamaño de los datos manteniendo una calidad visual aceptable.

---

# Conclusión

La transformada wavelet de Haar representa una técnica eficiente y sencilla para la compresión con pérdidas de imágenes. Su capacidad para concentrar la información relevante en un número reducido de coeficientes permite disminuir significativamente el volumen de datos que deben transmitirse.

Gracias a su baja complejidad computacional y a su facilidad de implementación en entornos como MATLAB y Simulink, este método resulta adecuado para el desarrollo de sistemas de transmisión digital de imágenes en entornos académicos y experimentales.

Por estas razones, la **Transformada Wavelet de Haar** fue seleccionada como método de compresión para el sistema implementado en este proyecto.
