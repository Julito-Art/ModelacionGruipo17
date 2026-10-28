# Casos de prueba de HU-004 – Ejecutar respaldos automáticos según la política

| | |
|---|---|
| **Historia** | [HU-004](../historias/HU-004-ejecutar-respaldos-automaticos.md) |
| **Plan de testing** | [plan-de-testing.md](plan-de-testing.md) |
| **Estado** | Diseñados (el SGR todavía no está implementado: no hay resultados de ejecución) |

## Casos

| ID | Criterio | Nivel | Tipo | Precondiciones | Pasos | Datos de entrada | Resultado esperado | RNF |
|---|---|---|---|---|---|---|---|---|
| CP-004-01 | CA-1 | Integración | normal | Reloj simulado 14:00; STRUMIS 5 min y TANGO 10 min; Acronis simulado exitoso e íntegro. | Avanzar el reloj hasta las 14:05. | Datos de CA-1. | Tarea de STRUMIS con disparador PROGRAMADO que llega a COMPLETADA, punto de restauración vigente hasta 25/11/2026 y ninguna tarea de TANGO. | — |
| CP-004-02 | CA-1 | Integración | límite | Igual que CP-004-01. | Medir el tiempo entre dos ciclos consecutivos de STRUMIS. | Intervalos de 3:59, 4:00, 5:00, 6:00 y 6:01 minutos. | Se aceptan 4:00, 5:00 y 6:00 (5 ± 1 minuto); se rechazan 3:59 y 6:01. | — |
| CP-004-03 | CA-1 | Integración | normal | Igual que CP-004-01; Acronis simulado falla solo para TANGO. | Avanzar el reloj hasta las 14:10. | Falla simulada en TANGO. | La tarea de STRUMIS no se ve afectada: sigue sus ciclos y llega a COMPLETADA. | — |
| CP-004-04 | CA-2 | Unitario | normal | Agentes A (sin respaldos previos) y B (con completo previo a las 02:00). | Llamar a `PoliticaDeRespaldo.tipoParaCiclo(agente, ahora)` con las 14:05. | Agentes A y B; hora 14:05. | A: COMPLETO. B: INCREMENTAL. | — |
| CP-004-05 | CA-2 | Unitario | límite | Agente B sin diferencial del día; hora del diferencial 23:00. | Llamar a `tipoParaCiclo` a las 22:59, a las 23:00 y a las 23:30 (con diferencial ya hecho). | Horas 22:59, 23:00 y 23:30. | INCREMENTAL, DIFERENCIAL e INCREMENTAL. | — |
| CP-004-06 | CA-3 | Unitario | negativo | Agente de STRUMIS con `hayRespaldoEnCurso()` = verdadero. | Llamar a `PlanificadorDeRespaldos.ejecutarCiclo(STRUMIS, ahora)`. | Respaldo en EN_EJECUCION. | Devuelve nulo, no se crea ninguna tarea y se registra CICLO_OMITIDO. | — |
| CP-004-07 | CA-3 | Integración | negativo | Respaldo de STRUMIS en cada estado en curso: EN_EJECUCION, VERIFICANDO y EN_REINTENTO. | Hacer que llegue un ciclo en cada caso. | Los tres estados. | En los tres casos: 0 tareas nuevas y 1 registro CICLO_OMITIDO. | — |
| CP-004-08 | CA-4 | Integración | negativo | Acronis simulado devuelve exitoso = falso con «Acronis: error de autenticación». | Ejecutar el ciclo de STRUMIS. | Motivo «Acronis: error de autenticación». | Tarea en EN_REINTENTO, reintentos = 1, motivo guardado y ningún punto de restauración. | — |
| CP-004-09 | CA-5 | Integración | negativo | Acronis simulado lanza «sin conexión». | Ejecutar el ciclo de TANGO. | Conexión caída. | Tarea en EN_COLA, reintentos = 0 y ninguna alerta. | — |
| CP-004-10 | CA-6 | Integración | normal | Respaldo de CP-004-01. | Consultar la auditoría. | — | Registros RESPALDO_INICIADO y RESPALDO_FINALIZADO (STRUMIS, COMPLETADA) con el usuario «SGR». | RNF-06 |
| CP-004-11 | CA-1 | Integración | normal | Reloj simulado; STRUMIS con intervalo 5; respaldo más verificación de 90 segundos. | Simular 24 horas y medir cada minuto la antigüedad del último respaldo exitoso. | Intervalo 5 min; duración 90 s. | En al menos el 99 % de las mediciones la antigüedad es ≤ 10 minutos. | RNF-01 |

## Matriz de trazabilidad criterio ↔ caso de prueba

| Criterio de aceptación | Casos de prueba |
|---|---|
| CA-1 | CP-004-01, CP-004-02, CP-004-03, CP-004-11 |
| CA-2 | CP-004-04, CP-004-05 |
| CA-3 | CP-004-06, CP-004-07 |
| CA-4 | CP-004-08 |
| CA-5 | CP-004-09 |
| CA-6 | CP-004-10 |

## Cobertura

- Criterios de aceptación: 6; criterios con al menos un caso: 6.
- Casos negativos (error o dato inválido): CP-004-06, CP-004-07, CP-004-08, CP-004-09.
- Casos de valor límite: CP-004-02, CP-004-05.
- Casos ligados a un RNF: CP-004-10 (RNF-06), CP-004-11 (RNF-01).
