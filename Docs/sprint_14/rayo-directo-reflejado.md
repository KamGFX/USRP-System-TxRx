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
