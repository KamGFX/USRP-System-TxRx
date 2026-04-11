## *Bloque de Modulación y Conformación de Pulso*

Después del procesamiento FEC, la secuencia binaria es convertida a símbolos complejos mediante modulación digital y posteriormente se aplica un filtrado de conformación de pulso. Esta etapa es fundamental para adaptar la señal a un medio físico de transmisión, optimizando el uso del espectro y reduciendo interferencias entre símbolos.

El bloque está compuesto por dos etapas principales: modulación 16-QAM y filtrado Root Raised Cosine (RRC).

---

### *1. Modulador 16-QAM*

El modulador implementa una modulación digital de tipo *Quadrature Amplitude Modulation (QAM)* de orden 16. En este esquema, cada símbolo representa 4 bits, lo que permite transmitir múltiples bits por símbolo, aumentando la eficiencia espectral.

#### *Agrupación de bits*

La secuencia de entrada se agrupa en bloques de 4 bits. En caso de que la longitud de la secuencia no sea múltiplo de 4, se realiza un relleno con ceros para completar el último grupo.

Cada grupo de 4 bits se convierte a un valor decimal, el cual se utiliza para indexar la constelación.

#### *Mapeo de símbolos*

El sistema utiliza un mapeo tipo *Gray*, donde símbolos adyacentes difieren en un solo bit. Esto reduce la probabilidad de error en la demodulación.

Las componentes en fase (I) y cuadratura (Q) se asignan mediante tablas predefinidas:

- Valores posibles: \(\{-3, -1, 1, 3\}\)
- Normalización: los símbolos se escalan por \(1/\sqrt{10}\) para mantener potencia unitaria promedio

Cada símbolo complejo se construye como:

s = (I + jQ) / sqrt(10)


Este proceso genera una constelación 16-QAM normalizada en el plano complejo.

#### *Padding adicional*

Al final de la secuencia de símbolos se agregan muestras nulas (padding). Esto se realiza para evitar efectos transitorios en el filtrado posterior y garantizar una correcta salida del sistema.

---

### *2. Filtro Root Raised Cosine (RRC)*

Una vez obtenidos los símbolos modulados, se aplica un filtro de conformación de pulso tipo Root Raised Cosine (RRC). Este filtro tiene como objetivo limitar el ancho de banda de la señal y reducir la interferencia entre símbolos (ISI, *Inter-Symbol Interference*).

#### *Parámetros del filtro*

El filtro se define mediante los siguientes parámetros:

- Factor de roll-off: `alpha = 0.35`
- Span: `span = 10` (duración del filtro en símbolos)
- Samples per symbol: `sps = 2`

El número total de coeficientes del filtro es:

N = span * sps + 1


---

#### *Generación de la respuesta al impulso*

La respuesta al impulso del filtro se calcula a partir de la expresión analítica del filtro RRC, considerando tres casos:

1. En el origen (\(t = 0\))
2. En puntos singulares (\(t = \pm 1/(4\alpha)\))
3. En el resto de valores

Cada coeficiente se obtiene evaluando la función en el dominio del tiempo, y posteriormente el filtro se normaliza para tener energía unitaria.

---

#### *Upsampling*

Antes del filtrado, la señal se sobremuestrea insertando ceros entre símbolos:

sim_up(1:sps:end) = simbolos


Esto genera una señal discreta con mayor resolución temporal, necesaria para aplicar el filtrado de conformación.

---

#### *Filtrado*

La señal sobremuestreada se filtra mediante una operación de convolución discreta:

se_al_tx = filter(h, 1, sim_up)


El filtro se implementa con estado persistente para mantener continuidad entre bloques de datos, evitando discontinuidades en la señal de salida.

---

### *Función del filtro RRC en el sistema*

El filtro RRC cumple un rol fundamental en el sistema de comunicaciones:

- Limita el ancho de banda de la señal transmitida
- Reduce la interferencia entre símbolos
- Permite reconstrucción óptima en el receptor (cuando se usa un filtro RRC complementario)

En conjunto con un filtro RRC en el receptor, se obtiene una respuesta tipo Raised Cosine, cumpliendo la condición de cero interferencia intersimbólica en el instante de muestreo.

---

### *Resumen del bloque*

El procesamiento de modulación y filtrado sigue el siguiente flujo:

1. Agrupación de bits en bloques de 4  
2. Mapeo Gray a símbolos complejos 16-QAM  
3. Normalización de la constelación  
4. Inserción de padding  
5. Upsampling de la señal  
6. Filtrado mediante Root Raised Cosine  

Este bloque transforma la información digital en una señal lista para transmisión, optimizada tanto en eficiencia espectral como en robustez frente a interferencias.
