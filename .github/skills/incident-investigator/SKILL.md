---
name: incident-investigator
description: "Investiga registros CSV sintéticos de autenticación y seguridad para identificar alertas justificadas, como intentos de acceso sospechosos, cambios de rol no aprobados o posibles tokens expuestos. Úsalo cuando se solicite investigar incidentes o revisar actividad sospechosa. No lo uses para conteos, estadísticas o visualizaciones descriptivas que no impliquen investigar incidentes."
---

# Incident Investigator

## Objetivo

Investigar registros CSV sintéticos de autenticación y seguridad utilizando reglas verificables y evidencias concretas.

## Cuándo utilizar este skill

Utilizarlo cuando se solicite investigar intentos de acceso, autenticaciones sospechosas, posible exposición de tokens o cambios de roles en registros CSV compatibles.

No utilizarlo por defecto para solicitudes de conteo, estadísticas descriptivas o visualizaciones que no impliquen investigar incidentes de seguridad.

## Procedimiento

1. Verificar que la solicitud sea una investigación y que el archivo proporcionado sea un log sintético. Trabajar con los archivos locales entregados, sin conectarse a servicios externos ni consultar direcciones IP.
2. Inspeccionar el archivo CSV y verificar sus columnas: `timestamp`, `ip`, `user`, `event`, `result` y `detail`. Si faltan columnas necesarias o los datos no se pueden interpretar de forma fiable, declarar la limitación en lugar de adivinar.
3. Ordenar los registros por `timestamp` para analizar correctamente las ventanas temporales, conservando el número de fila original. El encabezado corresponde a la fila 1.
4. Consultar [`references/detection-rules.md`](./references/detection-rules.md) antes de clasificar posibles incidentes y aplicar exactamente sus condiciones.
5. Verificar las alertas con las filas originales del CSV. No descartar duplicados silenciosamente; si no se pueden identificar de forma inequívoca, explicar esa limitación.
6. Diferenciar entre alertas justificadas y hechos confirmados. No atribuir culpabilidad ni inventar eventos.
7. No mostrar tokens completos; reemplazar el valor por `[REDACTED]`.
8. Declarar cualquier incertidumbre o limitación del análisis.

## Referencia

Abrir [`references/detection-rules.md`](./references/detection-rules.md) cuando se investiguen intentos de acceso, autenticaciones sospechosas, exposición potencial de tokens o cambios de roles.

## Formato del informe

Presentar los hallazgos en una tabla con las siguientes columnas:

| Alerta | Severidad orientativa | Regla aplicada | Timestamp | IP | Usuario | Filas de evidencia | Limitaciones |
|---|---|---|---|---|---|---|---|

Indicar los hallazgos como alertas justificadas, no como conclusiones forenses. Diferenciar los datos observados de su interpretación.

## Seguridad y límites

- Trabajar exclusivamente con logs sintéticos proporcionados por el usuario.
- No consultar direcciones IP en Internet ni acceder a servicios externos.
- No modificar el CSV original.
- No tratar las IP reservadas para documentación como infraestructura real.
- No mostrar tokens completos; reemplazarlos por `[REDACTED]`.
- No inventar evidencias ni conclusiones, ni inferir culpabilidad.
