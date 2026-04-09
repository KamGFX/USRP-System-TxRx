### *Bloque Modulador 16-QAM*

El bloque modulador tiene como función convertir la secuencia de bits codificados en símbolos complejos correspondientes a una constelación **16-QAM**. Esta etapa permite la transición del dominio digital (bits) al dominio de señal (símbolos), siendo fundamental para la transmisión a través del canal.

En este sistema, la modulación se realiza agrupando los bits en bloques de 4, donde cada grupo se asigna a un símbolo complejo. Dado que `log2(16) = 4`, cada símbolo representa 4 bits, lo que incrementa la eficiencia espectral.

---

### *1. Agrupación de bits*

El modulador asegura que la longitud de la secuencia sea múltiplo de 4. En caso contrario, se agregan ceros al final de la secuencia para completar el último símbolo:

    resto = mod(N_bits, 4);
    if resto ~= 0
        bits_cod = [bits_cod; zeros(4-resto,1)];
    end

Posteriormente, los bits se procesan en grupos de 4, donde cada grupo constituye un símbolo.

---

### *2. Mapeo binario a símbolo (Gray Coding)*

Cada grupo de 4 bits se convierte a un valor decimal:

    decimal = b(1)*8 + b(2)*4 + b(3)*2 + b(4);

Luego, se aplica un mapeo Gray mediante el vector:

    gray_map = [0 1 3 2 4 5 7 6 12 13 15 14 8 9 11 10];

El uso de codificación Gray permite que símbolos adyacentes en la constelación difieran en un solo bit, reduciendo la probabilidad de error en presencia de ruido.

---

### *3. Asignación en la constelación I/Q*

Cada índice obtenido se asocia a un punto de la constelación mediante dos tablas que representan los ejes en fase (I) y cuadratura (Q):

    tabla_I = [-3 -3 -3 -3 -1 -1 -1 -1 1 1 1 1 3 3 3 3];
    tabla_Q = [ 3  1 -1 -3  3  1 -1 -3 3 1 -1 -3 3 1 -1 -3];

Los símbolos complejos se generan como:

    simbolo = I + 1j*Q;

De esta manera, cada grupo de 4 bits se representa como un punto en el plano complejo.

---

### *4. Generación de símbolos complejos*

Para cada grupo de bits, se obtiene el símbolo correspondiente mediante:

    simbolos(k) = tabla_I(sym_idx) + 1j*tabla_Q(sym_idx);

El resultado es una secuencia de símbolos complejos que representan la señal modulada en banda base.

---

### *5. Normalización de potencia*

Finalmente, los símbolos generados se normalizan dividiendo por `sqrt(10)`:

    simbolos = simbolos / sqrt(10);

Esta normalización garantiza que la potencia promedio de la señal sea unitaria, lo cual es fundamental para análisis posteriores como la estimación de BER y la comparación de desempeño en distintos esquemas de modulación.


---

### *Descripción conceptual*

El modulador 16-QAM toma grupos de 4 bits y los convierte en símbolos complejos ubicados en una constelación bidimensional. Cada símbolo contiene simultáneamente una componente en fase (I) y una componente en cuadratura (Q). Gracias al uso de codificación Gray y normalización de potencia, el sistema logra un equilibrio entre eficiencia espectral y robustez frente al ruido.

