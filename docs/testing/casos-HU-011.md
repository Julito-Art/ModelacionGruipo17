# Casos de prueba de HU-011 – Recibir una alerta cuando un respaldo falla

| | |
|---|---|
| **Historia** | [HU-011](../historias/HU-011-alerta-por-respaldo-fallido.md) |
| **Plan de testing** | [plan-de-testing.md](plan-de-testing.md) |
| **Estado** | Diseñados (el SGR todavía no está implementado: no hay resultados de ejecución) |

## Casos

| ID | Criterio | Nivel | Tipo | Precondiciones | Pasos | Datos de entrada | Resultado esperado | RNF |
|---|---|---|---|---|---|---|---|---|
| CP-011-01 | CA-1 | Integración | normal | Tarea de TANGO FALLIDA a las 14:07:00 con el motivo indicado; correo simulado disponible. | Procesar el resultado de la tarea. | Datos de CA-1. | Alerta FALLA, CRITICA y ABIERTA con servidor TANGO, hora y motivo; aparece en el panel y se notifica al Responsable. | — |
| CP-011-02 | CA-1 | Integración | límite | Igual que CP-011-01. | Medir el tiempo entre la creación de la alerta y el envío del correo. | Envío a los 59 y a los 61 segundos. | A los 59 segundos cumple («en menos de 1 minuto»); a los 61 no cumple. | — |
| CP-011-03 | CA-1 | E2E | normal | Servidor SMTP de prueba; sesión de Responsable. | Provocar el fallo de TANGO y abrir el correo recibido y el panel. | Destinatario resp.prueba@prometva.test. | Correo con asunto «[SGR] CRITICA: el respaldo de TANGO falló», servidor, hora y motivo; la alerta figura ABIERTA en el panel. | — |
| CP-011-04 | CA-2 | Integración | negativo | Acronis simulado falla en el intento 1 de STRUMIS y funciona en el reintento 1. | Ejecutar el ciclo. | Intento 1 con error; reintento 1 exitoso. | Tarea COMPLETADA y 0 alertas. | — |
| CP-011-05 | CA-3 | Integración | negativo | `INotificador` simulado que lanza error de conexión. | Generar la alerta de CP-011-01. | Servidor de correo apagado. | La alerta queda ABIERTA y visible en el panel con «No se pudo enviar el correo»; no se pierde. | — |
| CP-011-06 | CA-4 | Integración | normal | Alerta ABIERTA de TANGO. | Completar un respaldo exitoso de TANGO a las 14:12 y llamar a `GestorDeAlertas.cerrarAlertas`. | Respaldo exitoso a las 14:12. | La alerta pasa a CERRADA con fecha de cierre 14:12 y no figura entre las abiertas. | — |
| CP-011-07 | CA-5 | Integración | negativo | Alerta FALLA de TANGO ya ABIERTA. | Hacer que otra tarea de TANGO quede FALLIDA a las 14:20. | Segunda falla de TANGO. | Sigue habiendo una sola alerta ABIERTA para TANGO y tipo FALLA. | — |
| CP-011-08 | CA-5 | Unitario | límite | `GestorDeAlertas` con 0 alertas abiertas. | Llamar a `alertarFalla` dos veces seguidas para el mismo servidor. | Dos fallas consecutivas. | Después de la primera hay 1 abierta; después de la segunda siguen siendo 1 (el máximo). | — |
| CP-011-09 | CA-6 | Integración | normal | Alerta generada en CP-011-01. | Consultar la auditoría. | — | Registro ALERTA_GENERADA con usuario «SGR», objetivo TANGO y resultado «CRITICA». | RNF-06 |

## Matriz de trazabilidad criterio ↔ caso de prueba

| Criterio de aceptación | Casos de prueba |
|---|---|
| CA-1 | CP-011-01, CP-011-02, CP-011-03 |
| CA-2 | CP-011-04 |
| CA-3 | CP-011-05 |
| CA-4 | CP-011-06 |
| CA-5 | CP-011-07, CP-011-08 |
| CA-6 | CP-011-09 |

## Cobertura

- Criterios de aceptación: 6; criterios con al menos un caso: 6.
- Casos negativos (error o dato inválido): CP-011-04, CP-011-05, CP-011-07.
- Casos de valor límite: CP-011-02, CP-011-08.
- Casos ligados a un RNF: CP-011-09 (RNF-06).
