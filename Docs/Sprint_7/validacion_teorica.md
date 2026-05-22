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
restricción `7`, utilizando los polinomios generadores `[171 133]` en notación octal, estándar
ampliamente utilizado en sistemas espaciales y de comunicaciones digitales (NASA, DVB).

A diferencia de los códigos de bloque, el codificador convolucional posee **memoria** — cada bit de
salida depende no solo del bit de entrada actual, sino también de los `K-1 = 6` bits anteriores
almacenados en el registro de desplazamiento. Esto le permite generar redundancia estructurada
que captura el historial de la señal.

Por cada bit de entrada se generan **dos bits de salida**, calculados mediante operaciones XOR
entre el estado actual del registro y cada uno de los dos polinomios generadores:

- `g1 = 1111001` (171 octal) — observa las posiciones D₀, D₁, D₂, D₃, D₆
- `g2 = 1011011` (133 octal) — observa las posiciones D₀, D₂, D₃, D₅, D₆

Cada polinomio actúa como un observador independiente del mismo registro, generando dos
perspectivas complementarias del estado interno del codificador. Esta doble observación es
precisamente lo que le entrega al decodificador Viterbi la información suficiente para
reconstruir el camino más probable a través del trellis y corregir errores introducidos por
el canal.

El trellis del código tiene `2^(K-1) = 64` estados posibles. El decodificador Viterbi recorre
este trellis hacia atrás con una profundidad de traceback de `35` pasos — valor que cumple con
la regla empírica de `5 × (K-1) = 30` mínimo recomendado para K=7 — acumulando métricas de
distancia de Hamming entre los bits recibidos y los esperados en cada transición, para
seleccionar el camino de menor error y recuperar los bits originales.

#### Validación teórica

Los resultados experimentales obtenidos son consistentes con el comportamiento teórico documentado
para este código. La reducción del BER en varios órdenes de magnitud tras el Viterbi confirma la
ganancia de codificación esperada, y la degradación progresiva al aumentar la probabilidad de error
es coherente con los límites teóricos del decodificador:

| Probabilidad de error canal | BER antes de Viterbi | BER después de Viterbi | Eficiencia |
|:---:|:---:|:---:|:---:|
| 0.01 | 0.009995 | 3.244×10⁻⁷ | ~100% |
| 0.05 | 0.05001 | 0.001493 | 98.51% |

Con una probabilidad de error de canal del 1%, el decodificador Viterbi corrigió prácticamente
la totalidad de los errores introducidos, dejando un BER residual del orden de 10⁻⁷. Al aumentar
la probabilidad al 5%, la eficiencia de corrección se mantiene por encima del 98%, resultado
coherente con el comportamiento teórico esperado para este código en canal BSC con decisión
hard decision.

---

### Resumen del flujo FEC

El procesamiento completo del bloque FEC sigue el siguiente orden:

1. **Generación de CRC** — detección de errores
2. **Interleaving** — dispersión de errores en ráfaga
3. **Scrambling** — mejora de propiedades estadísticas
4. **Codificación convolucional** — corrección de errores

Este esquema permite detectar errores residuales mediante el CRC y corregir errores introducidos por
el canal gracias a la redundancia del codificador convolucional, mejorada por el efecto del interleaver.

---

### Referencias y recursos de validación

Los siguientes recursos documentan el comportamiento teórico esperado para el par codificador
convolucional / decodificador Viterbi con los parámetros utilizados en este sistema (K=7, `[171 133]`,
tasa 1/2), y permiten contrastar los resultados experimentales obtenidos contra curvas BER teóricas
y ejemplos de implementación en MATLAB/Simulink:

#### Documentación oficial MathWorks

- **BER para Viterbi hard/soft decision — usa exactamente K=7, `[171 133]`**  
  Ejemplo oficial que estima y compara el BER para ambos tipos de decisión sobre canal AWGN.  
  https://www.mathworks.com/help/comm/ug/estimate-ber-for-hard-and-soft-decision-viterbi-decoding.html

- **Curva BER simulada vs cota teórica**  
  Ejemplo que obtiene curvas BER de simulación y las compara contra la cota teórica del código.  
  https://www.mathworks.com/help/comm/ug/punctured-convolutional-coding-1.html

- **Documentación bloque Viterbi Decoder en Simulink + uso de BERTool**  
  Explica la configuración del bloque y cómo usar la aplicación `bertool` para validación teórica.  
  https://www.mathworks.com/help/comm/ref/viterbidecoder.html

- **Documentación bloque Convolutional Encoder en Simulink**  
  Referencia completa del bloque codificador con ejemplos de configuración del trellis.  
  https://www.mathworks.com/help/comm/ref/convolutionalencoder.html

- **Función `vitdec` — BER codificado vs no codificado**  
  Ejemplo en MATLAB que calcula y compara el BER con y sin codificación convolucional.  
  https://www.mathworks.com/help/comm/ref/vitdec.html

#### Referencias teóricas

- **Wikipedia — Convolutional code**  
  Descripción del código convolucional K=7 tasa 1/2, su uso en sistemas NASA/Voyager y su
  relación con el algoritmo de Viterbi.  
  https://en.wikipedia.org/wiki/Convolutional_code

- **Wikipedia — Viterbi decoder**  
  Explicación del algoritmo de Viterbi, regla del traceback depth y comparación hard vs
  soft decision.  
  https://en.wikipedia.org/wiki/Viterbi_decoder
