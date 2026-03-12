# Serialización de la Imagen y Validación del Flujo de Datos

## Descripción

En esta etapa del proyecto se implementó la **serialización de la imagen** y la **validación del flujo de datos** dentro del modelo desarrollado en Simulink.

El requerimiento inicial establece la necesidad de **implementar la serialización de una matriz RGB**. Sin embargo, en etapas previas del sistema la imagen fue convertida a escala de grises*con el objetivo de simplificar el procesamiento y reducir la cantidad de datos a transmitir.

Una imagen RGB tiene tres canales (R, G y B), lo que implica transmitir tres matrices de datos. Al convertir la imagen a escala de grises, el sistema trabaja únicamente con **una matriz bidimensional**, reduciendo el volumen de información y simplificando las operaciones posteriores del sistema de comunicación.

---

## Serialización de la Imagen

La serialización consiste en transformar la matriz de la imagen en un vector unidimensional, permitiendo que los datos puedan transmitirse de forma secuencial dentro del sistema de comunicaciones.

En Simulink, esta operación se implementa utilizando el bloque:

**Reshape**

Este bloque reorganiza los datos de la matriz sin modificar su contenido.

### Transformación realizada

La imagen en escala de grises se representa como una matriz:

```
m × n
```

Después de aplicar el bloque Reshape, la matriz se convierte en un vector:

```
(m × n) × 1
```

De esta forma, cada píxel de la imagen se transmite de manera secuencial dentro del flujo de datos del sistema.

---

## Validación de la Estructura del Flujo de Datos

Para verificar que la serialización se realiza correctamente, se implementó un bloque adicional de validación.

Se utilizó el bloque:

**Display**

Este bloque permite visualizar los valores y dimensiones de la señal generada después del proceso de serialización, facilitando la verificación de que la salida corresponde a un vector unidimensional con el número esperado de elementos.

### Flujo implementado en Simulink

El flujo de procesamiento en esta etapa del sistema es el siguiente:

```
Imagen en escala de grises
        ↓
Compresión Haar
        ↓
Reshape (Serialización)
        ↓
Display (Validación del flujo de datos)
```

