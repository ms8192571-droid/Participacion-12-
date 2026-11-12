# Hallazgos de investigación

**Archivo analizado:** `logs_sinteticos.csv`

**Alcance:** 93 registros de datos, filas 2–94; encabezado en la fila 1. Los timestamps son ISO 8601 con desplazamiento `-05:00`.

**Método:** se consultó y aplicó `.github/skills/incident-investigator/references/detection-rules.md`. Se revisaron los eventos en orden cronológico, con coincidencia exacta de `event` y `result` y ventanas inclusivas de hasta 5 minutos. El CSV se leyó sin modificarlo. No se consultaron las IP ni servicios externos.

## Alertas justificadas

| Alerta | Severidad orientativa | Regla aplicada | Timestamp | IP | Usuario | Filas de evidencia | Limitaciones |
|---|---|---|---|---|---|---|---|
| Fuerza bruta probable, condicionada por posibles duplicados | No determinada por estas reglas | 6 o más `login/failed` desde una misma IP hacia el mismo usuario en ≤5 minutos | 2026-11-05 01:14–01:18 (-05:00) | `198.51.100.77` | `admin` | Filas 17, 19, 20, 22, 23, 25, 26 y 27: ocho fallos en cuatro minutos. | Las filas 19–20, 22–23 y 25–26 son pares idénticos. El CSV no tiene identificador de evento para decidir si son duplicados. Contando cada fila se cumple el umbral; si cada par fuera un duplicado, quedarían cinco fallos y no se cumpliría. No se deduplicaron. |
| Password spraying probable | No determinada por estas reglas | `login/failed` desde una misma IP contra al menos cinco usuarios distintos en ≤5 minutos | 2026-11-05 02:01–02:03 (-05:00) | `203.0.113.89` | `ana`, `carlos`, `luisa`, `diego`, `maria`, `sofia` | Filas 67, 68, 70, 71, 73 y 74: seis usuarios distintos. | Coincide con la regla; no demuestra intención ni atribuye culpabilidad. |
| Token potencialmente expuesto | No determinada por estas reglas | `detail` contiene `token=` | 2026-11-05 01:27 (-05:00) | `192.0.2.44` | `api_service` | Fila 33; `detail` contiene `token=[REDACTED]`. | El valor del token se omitió. |
| Cambio de rol no aprobado según el registro | No determinada por estas reglas | `role_change/success` con `approved=false` | 2026-11-05 01:50 (-05:00) | `203.0.113.42` | `guest` | Fila 59; el registro indica `approved=false`. | Solo describe lo que indica el registro; no identifica responsable ni infiere intención o culpabilidad. |

## Éxito posterior a la secuencia de fallos

La fila 28 registra `login/success` para `admin` desde `198.51.100.77` a las 01:19 (-05:00), un minuto después del último fallo registrado. Se informa por separado y no confirma que la cuenta haya sido comprometida.

## No coincidencias y limitaciones

- Las tres filas de `login/failed` para `maria` desde `192.0.2.25` (38, 39 y 41; 01:35–01:37) no alcanzan el umbral de seis para fuerza bruta.
- Los eventos `healthcheck` de las filas 78 y 80–84 no son `login/failed` y no cuentan para las reglas de autenticación.
- Las IP se muestran solo como valores registrados en logs sintéticos; no se consultaron ni se trataron como infraestructura real.
- Estos resultados son alertas justificadas para revisión, no conclusiones forenses.
