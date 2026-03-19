# Serialización de una Matriz (Imagen) para Transmisión hacia una USRP

La serialización de una matriz, en el contexto del procesamiento de señales y la comunicación
con una USRP (*Universal Software Radio Peripheral*), es el proceso mediante el cual una
estructura de datos bidimensional —como una imagen o una matriz de valores complejos— se
convierte en una secuencia lineal (unidimensional) de muestras que puede ser transmitida de
forma continua a través de una interfaz de datos hacia el hardware de radio.

---

## ¿En qué consiste el proceso?

Una imagen o matriz está organizada en filas y columnas, formando una estructura 2D. Sin
embargo, la USRP espera recibir los datos como un flujo continuo y ordenado de muestras,
generalmente en formato de números complejos **(I/Q)**. La serialización toma esa matriz y
"desenrolla" sus elementos —fila por fila o columna por columna— generando un vector plano.

Este vector luego se **normaliza**, se **escala** al rango de amplitud adecuado y se envía
muestra a muestra al dispositivo mediante protocolos como:

- **UHD** (*USRP Hardware Driver*)
- **GNU Radio**

---

## ¿Por qué se hace?

| Razón | Descripción |
|---|---|
| **Compatibilidad con el hardware** | La USRP opera sobre un flujo de datos en tiempo real; no puede interpretar estructuras multidimensionales directamente. |
| **Continuidad del flujo** | La transmisión de RF exige que las muestras lleguen de forma continua y sin interrupciones para evitar *underflows* (subdesbordamientos del buffer). |
| **Mapeo a señal analógica** | Cada muestra serializada representa un punto I/Q en el tiempo, lo que permite que el DAC de la USRP lo convierta en una señal de radiofrecuencia real. |
| **Control del orden de transmisión** | La serialización permite definir explícitamente el orden en que la información es enviada, lo cual es crítico para la correcta reconstrucción o interpretación de la señal en el receptor. |

---

## Conclusión

> La serialización es el puente entre la representación lógica de la información (una imagen o
> matriz) y la representación física requerida por el hardware de transmisión.
