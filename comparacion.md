# Comparación: investigación con y sin Skill

## 1. Experimento

Hay una investigación documentada con el Skill en `evidencias/hallazgos_contextual.md`. Para esta solicitud se volvió a analizar el mismo CSV sin invocar el Skill y se añadieron abajo los resultados. Sin embargo, la revisión ocurrió en esta conversación, que ya contenía el análisis anterior y sus criterios; por eso no constituye una sesión independiente ni una comparación controlada.

La investigación con Skill aplicó `.github/skills/incident-investigator/references/detection-rules.md` al mismo `logs_sinteticos.csv`, sin modificarlo ni consultar las IP.

## 2. Resultados

| Dimensión | Sin Skill | Con Skill | Evidencia / comentario |
|---|---|---|---|
| ¿Usó un procedimiento reproducible? | Sí en esta revisión; no aislada | Sí | Ambas revisiones usaron el mismo CSV y llegaron a resultados coincidentes. La revisión sin invocación aplicó timestamps, filas originales y reglas de ventana, pero el contexto previo impide considerarla independiente. |
| ¿Citó filas concretas? | Sí | Sí | Fuerza bruta: 17, 19, 20, 22, 23, 25–27; password spraying: 67, 68, 70, 71, 73, 74; token: 33; cambio de rol: 59; éxito posterior: 28. |
| Alertas sustentadas | Cuatro tipos, con una condicionada | Cuatro tipos de alerta | Fuerza bruta probable, condicionada por duplicados; password spraying probable; token potencialmente expuesto; cambio de rol no aprobado según el registro. |
| Falsos positivos detectados o evitados | Sí | Sí | Las tres fallas de las filas 38, 39 y 41 no alcanzan seis. Los `healthcheck` de las filas 78 y 80–84 no cuentan como fallos de login. |
| ¿Ocultó posibles tokens? | Sí | Sí | La fila 33 se reporta como `token=[REDACTED]`; el valor no se reproduce. |
| ¿Reconoció incertidumbre? | Sí | Sí | Ambas revisiones señalaron los pares idénticos 19–20, 22–23 y 25–26. Si fueran duplicados, quedarían cinco fallos y no se alcanzaría el umbral de fuerza bruta. |
| ¿Qué hizo gracias al Skill? | No se puede atribuir causalidad | Aplicó criterios y formato explícitos | La revisión con Skill consultó su referencia; esta revisión no lo invocó explícitamente. Como el contexto ya contenía el análisis y las reglas, no se puede atribuir una diferencia únicamente al Skill. |

## 3. Conclusión

En esta revisión sin invocar explícitamente el Skill se obtuvieron las mismas alertas y filas de evidencia que en la revisión documentada con Skill. Esto muestra consistencia entre las salidas, pero no demuestra que el Skill mejorara el resultado: el análisis sin invocación no fue aislado del contexto anterior.

## 4. Limitaciones

- Para una comparación controlada, ejecuta la misma solicitud y el mismo CSV en dos conversaciones nuevas e independientes, una sin cargar el Skill y otra con el Skill realmente disponible; registra ambas salidas.
- No se puede afirmar que la revisión sin invocación estuviera libre de influencia del contexto porque esta conversación ya incluía el análisis previo y sus criterios.
- La presencia de los archivos del Skill no demuestra por sí sola que Copilot los haya cargado internamente.
- Las filas idénticas no incluyen un identificador de evento; no se eliminaron ni se asumió que fueran duplicados.
- El análisis se limitó a registros sintéticos. Las IP se trataron como valores del CSV, no como infraestructura real.
