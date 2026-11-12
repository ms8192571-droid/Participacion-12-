# Hallazgos de investigación

**Archivo:** `logs_sinteticos.csv`

**Alcance:** 93 registros de datos; el encabezado es la fila 1 y los registros ocupan las filas 2–94. Los timestamps se interpretan con el desplazamiento `-05:00`.

**Reglas aplicadas:** [`.github/skills/incident-investigator/references/detection-rules.md`](.github/skills/incident-investigator/references/detection-rules.md). Se revisaron los eventos en orden cronológico, con coincidencia exacta de `event` y `result` y ventanas inclusivas de hasta cinco minutos. El CSV se leyó sin modificarlo. No se consultaron IP ni servicios externos.

## Alertas justificadas

| alerta | severidad orientativa | regla | timestamp | ip | usuario | filas/evidencia | limitaciones |
|---|---|---|---|---|---|---|---|
| Fuerza bruta probable, condicionada por duplicados | No determinada por estas reglas | 6 o más `login/failed` desde una IP hacia el mismo usuario en ≤5 minutos | 2026-11-05 01:14–01:18 (-05:00) | `198.51.100.77` | `admin` | Fallos: filas 17, 19, 20, 22, 23, 25, 26 y 27 (8 registros en 4 minutos). | Las filas 19–20, 22–23 y 25–26 son pares de registros idénticos. No hay identificador de evento para determinar si son duplicados. Contando las filas tal como aparecen se cumple el umbral; si cada par representara un único evento, quedarían 5 fallos y no se cumpliría. No se deduplicaron. |
| Password spraying probable | No determinada por estas reglas | `login/failed` desde una IP contra al menos 5 usuarios distintos en ≤5 minutos | 2026-11-05 02:01–02:03 (-05:00) | `203.0.113.89` | `ana`, `carlos`, `luisa`, `diego`, `maria`, `sofia` | Filas 67, 68, 70, 71, 73 y 74: seis usuarios distintos, todos con `login/failed`. | El patrón coincide con la regla; no demuestra intención ni atribuye culpabilidad. |
| Token potencialmente expuesto | No determinada por estas reglas | `detail` contiene `token=` | 2026-11-05 01:27 (-05:00) | `192.0.2.44` | `api_service` | Fila 33; el campo `detail` contiene `token=[REDACTED]`. | Se omite el valor del token. |
| Cambio de rol no aprobado según el registro | No determinada por estas reglas | `role_change/success` con `approved=false` | 2026-11-05 01:50 (-05:00) | `203.0.113.42` | `guest` | Fila 59; el registro indica `approved=false`. | Describe el registro; no identifica responsable ni infiere intención o culpabilidad. |

## Observación posterior a la secuencia de fuerza bruta

La fila 28 registra `login/success` para `admin` desde `198.51.100.77` a las 01:19 (-05:00), un minuto después del último fallo de la secuencia. Se anota aparte y no confirma que la cuenta haya sido comprometida.

## No coincidencias y limitaciones

- Las tres filas de `login/failed` para `maria` desde `192.0.2.25` (38, 39 y 41; 01:35–01:37) no alcanzan el umbral de fuerza bruta.
- Los eventos `healthcheck` de las filas 78 y 80–84 no son `login/failed` y no cuentan para las reglas de autenticación.
- Las IP se reportan únicamente como valores de los registros sintéticos; no se interpretan como infraestructura real ni se consultaron.
- Los resultados son alertas justificadas para revisión, no conclusiones forenses.
