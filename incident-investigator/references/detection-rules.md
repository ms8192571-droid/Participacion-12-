# Detection Rules

## 1. Fuerza bruta probable

Se considera una alerta cuando existen 6 o más eventos `login/failed` desde la misma IP hacia el mismo usuario dentro de una ventana de 5 minutos.

Si aparece posteriormente un `login/success` en los siguientes 5 minutos, registrarlo por separado.

## 2. Password spraying probable

Se considera una alerta cuando una misma IP realiza `login/failed` contra al menos 5 usuarios distintos dentro de una ventana de 5 minutos.

## 3. Token potencialmente expuesto

Si un campo `detail` contiene `token=`, registrar una alerta de posible exposición.

Nunca mostrar el valor completo del token. Sustituirlo por `[REDACTED]`.

## 4. Cambio de rol no aprobado

Si existe `role_change/success` con `approved=false`, registrar una alerta de cambio de rol no aprobado.

No inferir culpabilidad.

## 5. Falsos positivos

- 3 fallos aislados no alcanzan el umbral de fuerza bruta.
- Un `healthcheck` no es un `login/failed`.

## Convenciones de análisis

- Ordenar los registros por `timestamp`.
- La ventana temporal es de máximo 5 minutos, incluyendo ambos extremos.
- Contar únicamente eventos cuyos `event` y `result` coincidan exactamente.
- Citar números de fila del CSV original, considerando el encabezado como fila 1.
- Si no se pueden identificar duplicados de manera inequívoca, indicar la limitación.
- Los resultados son alertas justificadas, no conclusiones forenses.
