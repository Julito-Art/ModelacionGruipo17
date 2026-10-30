# Casos de prueba de HU-003 – Definir la política de respaldo de cada servidor

| | |
|---|---|
| **Historia** | [HU-003](../historias/HU-003-definir-politica-de-respaldo.md) |
| **Plan de testing** | [plan-de-testing.md](plan-de-testing.md) |
| **Estado** | Diseñados (el SGR todavía no está implementado: no hay resultados de ejecución) |

## Casos

| ID | Criterio | Nivel | Tipo | Precondiciones | Pasos | Datos de entrada | Resultado esperado | RNF |
|---|---|---|---|---|---|---|---|---|
| CP-003-01 | CA-1 | Unitario | normal | Ninguna. | Crear `PoliticaDeRespaldo(STRUMIS)` y consultar sus valores. | Servidor STRUMIS. | `intervaloMinutos()` = 5, `retencionDias()` = 30 y `validar()` = verdadero. | — |
| CP-003-02 | CA-1 | E2E | normal | STRUMIS registrado sin política; sesión iniciada como Responsable. | Abrir «Política de respaldo» de STRUMIS. | Usuario resp.prueba@prometva.test. | Intervalo 5, retención 30 y la etiqueta «Sin protección» visibles. | — |
| CP-003-03 | CA-2 | Integración | normal | Sesión de Responsable. | Llamar a `PlanificadorDeRespaldos.definirPolitica` con intervalo 5, retención 30 y hora 23:00; reabrir la política. | Intervalo 5; retención 30; hora 23:00. | La política queda guardada, STRUMIS deja de figurar «Sin protección» y al reabrir se ven 5, 30 y 23:00. | — |
| CP-003-04 | CA-3 | Unitario | negativo | Ninguna. | Crear una política con intervalo 11 y llamar a `validar()`. | Intervalo 11. | `validar()` = falso. | — |
| CP-003-05 | CA-3 | Unitario | límite | Ninguna. | Llamar a `validar()` con intervalos 4, 5, 10 y 11. | Intervalos 4, 5, 10, 11. | Falso, verdadero, verdadero, falso. | — |
| CP-003-06 | CA-3 | E2E | negativo | Sesión de Responsable; política previa de STRUMIS con intervalo 5. | Escribir 11 en el intervalo y guardar. | Intervalo 11. | Mensaje «El intervalo debe estar entre 5 y 10 minutos» junto al campo; la política previa (5) no cambia. | — |
| CP-003-07 | CA-4 | Unitario | normal | Ninguna. | Crear una política con intervalo 10; llamar a `validar()` y a `cumpleObjetivoDePerdida()`. | Intervalo 10. | `validar()` = verdadero y `cumpleObjetivoDePerdida()` = falso. | — |
| CP-003-08 | CA-4 | E2E | normal | Sesión de Responsable. | Guardar una política con intervalo 10. | Intervalo 10. | La política se guarda y aparece el aviso «Con más de 5 minutos no se garantiza perder como máximo 10 minutos de datos». | — |
| CP-003-09 | CA-5 | Integración | negativo | Sesiones de Gerente de Administración y de Dirección. | Con cada usuario, llamar a `definirPolitica`. | gerente.prueba@prometva.test y direccion.prueba@prometva.test. | Ambas llamadas se rechazan con «No tenés permiso para definir políticas» y la política no cambia. | — |
| CP-003-10 | CA-6 | Integración | normal | Guardado exitoso de CP-003-03. | Consultar el registro de auditoría. | — | Existe un registro con usuario resp.prueba@prometva.test, acción CAMBIO_DE_POLITICA, objetivo STRUMIS y resultado «exitoso». | RNF-06 |
| CP-003-11 | CA-2 | Aceptación | normal | Al menos 5 usuarios de prueba sin conocimientos técnicos avanzados (recomendado: 10). | Pedir a cada uno que defina una política de respaldo sin ayuda, cronometrando. | Servidor STRUMIS; valores por defecto. | Al menos el 90 % de los usuarios la define en 3 minutos o menos (con 5 usuarios, los 5). | RNF-07 |
| CP-003-12 | CA-7 | Unitario | límite | Ninguna. | Llamar a `errores()` con retención 6, 7, 90 y 91 (y 0, -1 y 400). | Retenciones 6, 7, 90, 91, 0, -1 y 400. | 7 y 90 sin error; 6, 91, 0, -1 y 400 con «La retención debe estar entre 7 y 90 días». | — |
| CP-003-13 | CA-7 | Unitario | límite | Ninguna. | Llamar a `PoliticaDeRespaldo.esHoraLaboral` y a `errores()` con 07:59, 08:00, 17:59 y 18:00. | Horas 07:59, 08:00, 17:59 y 18:00. | 07:59 y 18:00 sin error; 08:00 y 17:59 con «La hora del diferencial debe estar fuera del horario laboral (08:00 a 18:00)». | — |
| CP-003-14 | CA-7 | E2E | negativo | Sesión de Responsable; política previa de STRUMIS (5, 30, 23:00). | Dejar el intervalo vacío y guardar; repetir con 5,5, con retención 400 y con hora 10:00. | Intervalo «» y 5,5; retención 400; hora 10:00. | Cada caso muestra su mensaje junto al campo («Ingresá el intervalo como un número entero de minutos», «La retención debe estar entre 7 y 90 días», «La hora del diferencial debe estar fuera del horario laboral (08:00 a 18:00)») y la política previa no cambia. | — |

## Matriz de trazabilidad criterio ↔ caso de prueba

| Criterio de aceptación | Casos de prueba |
|---|---|
| CA-1 | CP-003-01, CP-003-02 |
| CA-2 | CP-003-03, CP-003-11 |
| CA-3 | CP-003-04, CP-003-05, CP-003-06 |
| CA-4 | CP-003-07, CP-003-08 |
| CA-5 | CP-003-09 |
| CA-6 | CP-003-10 |
| CA-7 | CP-003-12, CP-003-13, CP-003-14 |

## Cobertura

- Criterios de aceptación: 7; criterios con al menos un caso: 7.
- Casos negativos (error o dato inválido): CP-003-04, CP-003-06, CP-003-09, CP-003-14.
- Casos de valor límite: CP-003-05, CP-003-12, CP-003-13.
- Casos ligados a un RNF: CP-003-10 (RNF-06), CP-003-11 (RNF-07).
