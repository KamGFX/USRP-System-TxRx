## *Bloque FEC (Forward Error Correction)*

El bloque de corrección de errores hacia adelante (FEC) implementado en el sistema tiene como objetivo aumentar la robustez de la transmisión digital frente a errores introducidos por el canal. Este bloque está compuesto por cuatro etapas principales: generación de CRC, interleaving, scrambling y codificación convolucional. Cada una cumple una función específica dentro del proceso global de protección de la información.

---

### *1. General CRC Generator*

En la primera etapa se realiza la generación de un código de redundancia cíclica (CRC), utilizando el polinomio generador:

G(z) = z^16 + z^12 + z^5 + 1

Este bloque toma como entrada la secuencia de bits original y calcula un residuo mediante una división polinómica en el dominio binario. Dicho residuo se agrega al final del mensaje, formando una nueva secuencia que contiene tanto los datos como los bits de verificación.

La función principal del CRC es la detección de errores en el receptor. Si durante la transmisión ocurre alguna alteración en los bits, el residuo calculado en recepción no coincidirá con el esperado, lo que permitirá identificar la presencia de errores. Es importante destacar que el CRC no corrige errores, únicamente los detecta.

---

### *2. Bloque Interleaver*

Posteriormente, la secuencia de bits (incluyendo el CRC) es procesada por un interleaver matricial. En este bloque, los datos se reorganizan mediante una operación de reestructuración en una matriz de dimensiones definidas (filas y columnas), donde los bits se escriben por filas y posteriormente se leen por columnas.

Este proceso no altera el contenido de los bits, sino únicamente su orden. Su propósito es dispersar posibles errores en ráfaga que puedan ocurrir en el canal de transmisión. De esta manera, un conjunto de errores consecutivos se distribuye a lo largo de la secuencia, convirtiéndose en errores más aislados.

Esta transformación es fundamental para mejorar el desempeño del codificador convolucional, el cual es más eficiente corrigiendo errores dispersos que errores agrupados.

---

### *3. Scrambler*

En la tercera etapa se aplica un scrambler basado en un registro de desplazamiento con realimentación lineal (LFSR), definido por el polinomio:

1 + z^-3 + z^-7


El scrambler combina la secuencia de entrada con una secuencia pseudoaleatoria generada internamente, produciendo una señal con características estadísticas más uniformes. Esto evita la presencia de largas secuencias de ceros o unos, las cuales pueden afectar negativamente la transmisión, por ejemplo, dificultando la sincronización o generando componentes no deseadas en el espectro.

Es importante señalar que el scrambler no introduce redundancia ni permite la corrección o detección de errores; su función es exclusivamente mejorar las propiedades de la señal transmitida.

---

### *4. Codificador Convolucional*

Finalmente, la secuencia es procesada por un codificador convolucional de tasa `1/2` y longitud de restricción `7`, utilizando los polinomios generadores `[171 133]` en notación octal (estándar ampliamente utilizado, por ejemplo, en sistemas espaciales).

Este codificador introduce redundancia estructurada en la señal mediante el uso de un registro de desplazamiento. Cada bit de entrada se combina con bits anteriores almacenados en memoria, generando dos bits de salida por cada bit de entrada.

El proceso de codificación se basa en operaciones módulo 2 (XOR) entre el estado actual del registro y los polinomios generadores. Como resultado, se obtiene una secuencia codificada que contiene información redundante suficiente para permitir la corrección de errores en el receptor, típicamente mediante algoritmos como el de Viterbi.

---

### *Resumen del flujo FEC*

El procesamiento completo del bloque FEC sigue el siguiente orden:

1. Generación de CRC (detección de errores)  
2. Interleaving (dispersión de errores en ráfaga)  
3. Scrambling (mejora de propiedades estadísticas)  
4. Codificación convolucional (corrección de errores)  

Este esquema permite detectar errores residuales mediante el CRC y corregir errores introducidos por el canal gracias a la redundancia del codificador convolucional, mejorada por el efecto del interleaver.
