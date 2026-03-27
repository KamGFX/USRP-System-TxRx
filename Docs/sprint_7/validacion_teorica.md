## Bloque FEC (Forward Error Correction)

El bloque de corrección de errores hacia adelante (FEC) implementado en el sistema tiene como objetivo
aumentar la robustez de la transmisión digital frente a errores introducidos por el canal. Este bloque
está compuesto por cuatro etapas principales: generación de CRC, interleaving, scrambling y codificación
convolucional. Cada una cumple una función específica dentro del proceso global de protección de la
información.

---

### 1. General CRC Generator

En la primera etapa se realiza la generación de un código de redundancia cíclica (CRC), utilizando el
polinomio generador:

$$G(z) = z^{16} + z^{12} + z^5 + 1$$

Este bloque toma como entrada la secuencia de bits original y calcula un residuo mediante una división
polinómica en el dominio binario. Dicho residuo se agrega al final del mensaje, formando una nueva
secuencia que contiene tanto los datos como los bits de verificación. La función principal del CRC es la
detección de errores en el receptor — no los corrige, únicamente los detecta.

---

### 2. Bloque Interleaver

Posteriormente, la secuencia de bits (incluyendo el CRC) es procesada por un interleaver matricial,
donde los datos se escriben por filas y se leen por columnas. Este proceso no altera el contenido de
los bits, sino únicamente su orden, dispersando posibles errores en ráfaga del canal en errores más
aislados. Esta transformación es fundamental para mejorar el desempeño del codificador convolucional,
el cual es más eficiente corrigiendo errores dispersos que errores agrupados.

---

### 3. Scrambler

En la tercera etapa se aplica un scrambler basado en un registro de desplazamiento con realimentación
lineal (LFSR), definido por el polinomio:

$$1 + z^{-3} + z^{-7}$$

El scrambler combina la secuencia de entrada con una secuencia pseudoaleatoria generada internamente,
produciendo una señal con características estadísticas más uniformes, evitando largas secuencias de
ceros o unos que puedan afectar la sincronización o el espectro de la señal. El scrambler no introduce
redundancia ni permite corrección o detección de errores.

---

### 4. Codificador Convolucional

Finalmente, la secuencia es procesada por un codificador convolucional de tasa `1/2` y longitud de
restricción `7`, utilizando los polinomios generadores `[171 133]` en notación octal. Este codificador
introduce redundancia estructurada generando dos bits de salida por cada bit de entrada, mediante
operaciones XOR entre el estado actual del registro y los polinomios generadores, permitiendo la
corrección de errores en el receptor mediante el algoritmo de Viterbi.

#### Validación teórica

Los resultados experimentales obtenidos son consistentes con el comportamiento teórico documentado
para este código. La reducción del BER en varios órdenes de magnitud tras el Viterbi confirma la
ganancia de codificación esperada, y la degradación progresiva al aumentar la probabilidad de error
es coherente con los límites teóricos del decodificador:

| Probabilidad de error canal | BER antes de Viterbi | BER después de Viterbi | Eficiencia |
|:---:|:---:|:---:|:---:|
| 0.01 | 0.009995 | 3.244×10⁻⁷ | ~100% |
| 0.05 | 0.05001 | 0.001493 | 98.51% |

Para la validación teórica completa del par codificador/decodificador con estos parámetros exactos
(K=7, `[171 133]`), los siguientes recursos documentan el comportamiento esperado y su comparación
contra curvas BER teóricas:

- **BER para Viterbi hard/soft decision — MathWorks (usa exactamente K=7, [171 133]):**  
  https://www.mathworks.com/help/comm/ug/estimate-ber-for-hard-and-soft-decision-viterbi-decoding.html

- **Curva BER simulada vs cota teórica — MathWorks:**  
  https://www.mathworks.com/help/comm/ug/punctured-convolutional-coding-1.html

- **Documentación oficial bloque Viterbi Decoder + BERTool:**  
  https://www.mathworks.com/help/comm/ref/viterbidecoder.html

- **Función `vitdec` con ejemplo de BER codificado vs no codificado:**  
  https://www.mathworks.com/help/comm/ref/vitdec.html

- **Referencia teórica — Wikipedia Convolutional code:**  
  https://en.wikipedia.org/wiki/Convolutional_code

---

### Resumen del flujo FEC

El procesamiento completo del bloque FEC sigue el siguiente orden:

1. **Generación de CRC** — detección de errores
2. **Interleaving** — dispersión de errores en ráfaga
3. **Scrambling** — mejora de propiedades estadísticas
4. **Codificación convolucional** — corrección de errores

Este esquema permite detectar errores residuales mediante el CRC y corregir errores introducidos por
el canal gracias a la redundancia del codificador convolucional, mejorada por el efecto del interleaver.
