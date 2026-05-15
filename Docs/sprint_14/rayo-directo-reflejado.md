# Canal Multitrayecto en Simulink/MATLAB

El bloque `canal_multitrayecto` implementa un modelo básico de canal multitrayecto utilizado en sistemas de comunicaciones digitales. Este modelo representa la propagación de una señal a través de dos trayectorias:

- Un rayo directo.
- Un rayo reflejado (eco).

El objetivo es simular el efecto de propagación múltiple presente en canales inalámbricos reales, donde la señal puede reflejarse en obstáculos como edificios, paredes o superficies metálicas.

---

# Modelo matemático

El canal implementado se describe mediante:

:contentReference[oaicite:0]{index=0}

donde:

- \(x[n]\): señal transmitida.
- \(y[n]\): señal recibida.
- \(\alpha\): ganancia o atenuación del rayo reflejado.
- \(D\): retardo del eco en número de muestras.

---

# Explicación paso a paso

## 1. Parámetro `alpha`

```matlab
alpha = 0.5;
```

Controla la amplitud del rayo reflejado.

- Si `alpha = 0`, no existe eco.
- Si `alpha` aumenta, el eco tiene mayor impacto.
- Valores altos generan mayor interferencia intersímbolo (ISI).

---

## 2. Parámetro `D`

```matlab
D = 3;
```

Representa el retardo del rayo reflejado en número de muestras.

Por ejemplo:

- `D = 1`: el eco llega una muestra después.
- `D = 3`: el eco llega tres muestras después.

A mayor valor de `D`, mayor separación temporal entre la señal directa y el reflejo.

---

## 3. Conversión a vector columna

```matlab
x = x(:);
```

Garantiza que la señal de entrada tenga formato de vector columna, evitando errores dimensionales durante la simulación.

---

## 4. Generación del eco retardado

```matlab
x_delay = [zeros(D,1); x(1:end-D)];
```

Esta línea genera una copia retardada de la señal original.

Ejemplo:

```text
x = [1 2 3 4]
D = 2
```

Resultado:

```text
x_delay = [0 0 1 2]
```

Los ceros iniciales representan el tiempo de propagación del eco.

---

## 5. Suma de trayectorias

```matlab
y = x + alpha*x_delay;
```

La señal final corresponde a la suma de:

- la señal directa,
- y la señal reflejada retardada y atenuada.


# Código MATLAB

```matlab
function y = canal_multitrayecto(x)

% Canal multitrayecto:
% rayo directo + un rayo reflejado

alpha = 0.5;   % Ganancia del rayo reflejado
D = 3;         % Retardo del reflejo en muestras

% Convertir a vector columna
x = x(:);

% Crear señal retardada
x_delay = [zeros(D,1); x(1:end-D)];

% Sumar señal directa y reflejada
y = x + alpha*x_delay;

end
