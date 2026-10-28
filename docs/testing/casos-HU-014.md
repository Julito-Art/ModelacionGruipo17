# Casos de prueba de HU-014 – Solicitar una restauración

| | |
|---|---|
| **Historia** | [HU-014](../historias/HU-014-solicitar-restauracion.md) |
| **Plan de testing** | [plan-de-testing.md](plan-de-testing.md) |
| **Estado** | Diseñados (el SGR todavía no está implementado: no hay resultados de ejecución) |

## Casos

| ID | Criterio | Nivel | Tipo | Precondiciones | Pasos | Datos de entrada | Resultado esperado | RNF |
|---|---|---|---|---|---|---|---|---|
| CP-014-01 | CA-1 | Unitario | normal | Puntos de TANGO de hace 2, 29 y 31 días. | Filtrar con `PuntoDeRestauracion.estaVigente(ahora)`. | PR-A, PR-B y PR-C; ahora = 26/10/2026 23:00. | Quedan PR-A y PR-B; PR-C se descarta. | — |
| CP-014-02 | CA-1 | Unitario | límite | Un punto con `vigenteHasta = fechaHora + 30 días`. | Evaluar `estaVigente` con distintas antigüedades. | 29 días 23 h 59 min y 30 días 0 h 1 min. | El primero es vigente; el segundo está vencido. | — |
| CP-014-03 | CA-2 | Integración | normal | Sesión de Responsable; punto PR-A. | Llamar a `ServicioDeRestauracion.solicitar` con tipo TOTAL y el motivo indicado. | PR-A; TOTAL; «Pérdida de datos por ataque». | Solicitud PENDIENTE con solicitante, fecha y punto elegido. | — |
| CP-014-04 | CA-2 | Integración | normal | Solicitud creada en CP-014-03. | Consultar la auditoría. | — | Registro RESTAURACION_SOLICITADA con usuario resp.prueba@prometva.test. | RNF-06 |
| CP-014-05 | CA-3 | Unitario | negativo | Ninguna. | Llamar a `solicitar` con motivo vacío y con motivo de tres espacios. | Motivos «» y «   ». | Ambos se rechazan con «El motivo es obligatorio» y no se crea solicitud. | — |
| CP-014-06 | CA-4 | Unitario | negativo | Tipo GRANULAR. | Llamar a `solicitar` sin elementos. | Lista de elementos vacía. | Se rechaza con «Indicá al menos un elemento a restaurar» y no se crea solicitud. | — |
| CP-014-07 | CA-4 | Unitario | límite | Tipo GRANULAR. | Llamar a `solicitar` con 0 y con 1 elemento. | 0 elementos y «Clientes». | Con 0 se rechaza; con 1 se acepta. | — |
| CP-014-08 | CA-5 | Integración | normal | Sesión de Responsable. | Solicitar GRANULAR con dos elementos. | «Facturas 2026» y «Clientes». | Solicitud PENDIENTE que conserva los dos elementos. | — |
| CP-014-09 | CA-6 | Integración | negativo | Sesiones de Dirección y de Gerente de Administración. | Intentar `solicitar` con cada usuario. | direccion.prueba@prometva.test y gerente.prueba@prometva.test. | Ambas se rechazan con «No tenés permiso para solicitar restauraciones» y no se crea nada. | — |
| CP-014-10 | CA-7 | Integración | negativo | STRUMIS sin puntos vigentes. | Abrir «Nueva solicitud» y elegir STRUMIS. | Puntos de más de 30 días. | Se muestra «No hay puntos de restauración vigentes para este servidor» y no se puede enviar. | — |
| CP-014-11 | CA-1 | E2E | normal | Sesión de Responsable; TANGO con PR-A y PR-B. | Abrir la solicitud, elegir PR-A, TOTAL, escribir el motivo y enviar. | Datos de CA-2. | Se recorren las pantallas 1 y 3 y la solicitud queda PENDIENTE. | — |

## Matriz de trazabilidad criterio ↔ caso de prueba

| Criterio de aceptación | Casos de prueba |
|---|---|
| CA-1 | CP-014-01, CP-014-02, CP-014-11 |
| CA-2 | CP-014-03, CP-014-04 |
| CA-3 | CP-014-05 |
| CA-4 | CP-014-06, CP-014-07 |
| CA-5 | CP-014-08 |
| CA-6 | CP-014-09 |
| CA-7 | CP-014-10 |

## Cobertura

- Criterios de aceptación: 7; criterios con al menos un caso: 7.
- Casos negativos (error o dato inválido): CP-014-05, CP-014-06, CP-014-09, CP-014-10.
- Casos de valor límite: CP-014-02, CP-014-07.
- Casos ligados a un RNF: CP-014-04 (RNF-06).
