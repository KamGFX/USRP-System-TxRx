# Implementación de un sistema de carga y procesamiento de imágenes en MATLAB–Simulink

El procesamiento digital de imágenes es una herramienta fundamental en diversas aplicaciones de ingeniería, como visión por computador, análisis de imágenes médicas, robótica y sistemas de monitoreo. MATLAB y Simulink ofrecen un entorno robusto para el manejo y procesamiento de imágenes.

En este trabajo se implementa un sistema que permite cargar una imagen seleccionada por el usuario con una resolución superior a **740 × 492 píxeles**, procesarla en MATLAB y posteriormente enviarla a un modelo de Simulink para aplicar un algoritmo de procesamiento básico. El procesamiento consiste en convertir la imagen de formato RGB a escala de grises utilizando un modelo de luminancia.

---

# Descripción general del sistema

## 1. Etapa de preparación en MATLAB

- Carga de la imagen desde un archivo.
- Verificación de su tamaño mínimo.
- Conversión del tipo de datos para procesamiento.
- Preparación de la estructura necesaria para enviar la imagen a Simulink.

## 2. Etapa de procesamiento en Simulink

- Recepción de la imagen mediante un bloque From Workspace.
- Procesamiento de la imagen mediante un bloque MATLAB Function.
- Conversión de la imagen RGB a escala de grises.
- Visualización o almacenamiento de la imagen procesada.

---

# Implementación y procesamiento de la imagen en MATLAB

La primera etapa del sistema se desarrolla en MATLAB y tiene como propósito cargar la imagen seleccionada por el usuario, verificar que cumpla con los requisitos de resolución mínima y prepararla para su utilización dentro del modelo de Simulink. Inicialmente, la imagen se carga desde el almacenamiento local mediante la función `imread`, la cual permite leer archivos de distintos formatos de imagen y almacenarlos en una matriz tridimensional que contiene la información de los canales de color.

Una vez cargada la imagen, se obtienen sus dimensiones utilizando la función `size`, lo cual permite identificar su altura, ancho y número de canales. Esta información se utiliza para verificar que la resolución de la imagen sea superior a **740 × 492 píxeles**, condición establecida como requisito del sistema. En caso de que la imagen no cumpla con esta condición, el sistema genera un mensaje de error que detiene la ejecución del programa, evitando que se procese una imagen que no cumpla con las especificaciones requeridas.

Posteriormente, la imagen se convierte al tipo de dato `double` con el fin de facilitar la realización de operaciones matemáticas durante el procesamiento. Esta conversión permite trabajar con mayor precisión en los cálculos. Después de esta conversión se realiza una operación de saturación para asegurar que los valores de intensidad de los píxeles no superen el valor máximo permitido para imágenes de 8 bits, que corresponde a **255**. Una vez aplicado este control, la imagen se convierte nuevamente al formato `uint8`, el cual es el formato estándar utilizado para la visualización de imágenes en MATLAB y Simulink.

Finalmente, la imagen procesada se organiza en una estructura de datos compatible con el bloque **From Workspace** de Simulink. En esta estructura se especifica el instante de tiempo en el que la imagen será entregada al modelo de simulación, los valores de la señal correspondientes a la matriz de la imagen y las dimensiones de la misma. Esto permite que Simulink interprete correctamente la imagen como una señal proveniente del workspace de MATLAB. Adicionalmente, se incluye una visualización opcional de la imagen mediante la función `imshow`, lo cual permite verificar visualmente que la imagen fue cargada y preparada correctamente antes de ejecutar la simulación.

---

# Implementación y procesamiento de la imagen en Simulink

La segunda etapa del sistema se desarrolla dentro del entorno de Simulink, donde se realiza el procesamiento de la imagen cargada previamente en MATLAB. Para llevar a cabo esta tarea se utiliza un bloque **MATLAB Function**, el cual permite ejecutar código MATLAB directamente dentro del modelo de simulación. Este bloque recibe como entrada la imagen proveniente del bloque **From Workspace** y aplica un procesamiento básico que consiste en convertir la imagen desde el formato RGB a una imagen en escala de grises.

Dentro del bloque MATLAB Function, la imagen recibida se convierte inicialmente al tipo de dato `double`, lo cual permite realizar las operaciones matemáticas necesarias para el procesamiento de la señal. Posteriormente se verifica que la imagen de entrada sea efectivamente una imagen RGB, es decir, que posea tres canales de color. Esta verificación es importante para garantizar el correcto funcionamiento del algoritmo, ya que el cálculo de la escala de grises se basa en la combinación de los tres canales de color presentes en la imagen original.

Una vez verificada la estructura de la imagen, se realiza la conversión a escala de grises utilizando el modelo de luminancia perceptual. Este modelo pondera cada uno de los canales de color de acuerdo con la sensibilidad del ojo humano a diferentes longitudes de onda. En particular, el canal verde tiene mayor contribución a la percepción de brillo, seguido por el canal rojo y finalmente por el canal azul. La intensidad de cada píxel en escala de grises se obtiene mediante una combinación lineal de los tres canales utilizando coeficientes específicos que representan esta ponderación.

Finalmente, la imagen resultante se convierte nuevamente al tipo de dato `uint8`, permitiendo que pueda ser visualizada correctamente dentro del entorno de Simulink o exportada nuevamente a MATLAB para su análisis. El resultado final es una imagen de un solo canal que conserva las dimensiones espaciales de la imagen original, pero representa únicamente la información de luminancia, lo que permite simplificar su análisis o procesamiento posterior.

## Referencias

- [Importing Images into MATLAB Workspace – MathWorks](https://la.mathworks.com/help/matlab/import_export/importing-images.html)

- [Read Image Data into Workspace – MathWorks](https://es.mathworks.com/help/images/read-image-data-into-the-workspace.html)
