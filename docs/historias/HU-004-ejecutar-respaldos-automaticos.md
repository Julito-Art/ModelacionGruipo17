# HU-004 – Ejecutar respaldos automáticos según la política

| | |
|---|---|
| **Prioridad (MoSCoW)** | Must |
| **Estimación** | 8 story points |
| **Sprint** | 2 |
| **Épica / Feature** | E1 Respaldo automatizado en la nube / F1.2 Ejecución automática y a demanda |
| **Depende de** | HU-002, HU-003 |
| **Origen en OAS** | Alcance del mandato (de forma automatizada); Diagnóstico (que no se tenga que hacer manualmente); Flujograma de Soluciones (Respaldo Automatizado). |
| **Documentos relacionados** | [06 – Clases](../06-diseno-de-clases.md), [07 – Estados y secuencia](../07-estados-y-secuencia.md), [Casos de prueba](../testing/casos-HU-004.md), [10 – Prototipo y heurísticas](../10-prototipo-y-heuristicas.md) |

## Historia

Como **Responsable de Respaldos** quiero que los respaldos de STRUMIS y TANGO se ejecuten automáticamente según la política de cada servidor para no depender de copias manuales ni de que alguien se acuerde de hacerlas.

## Contexto y reglas de negocio

- Es el corazón del SGR: sin intervención manual, cada servidor se respalda en Acronis Cloud según su política. Cada ejecución es una `TareaDeRespaldo` que recorre los estados definidos en el documento 07.
- La primera ejecución de cada servidor es un respaldo COMPLETO; luego hay incrementales en cada intervalo y un diferencial por día (SM-04). El Planificador elige el tipo en cada ciclo.
- STRUMIS y TANGO se respaldan de forma independiente: cada uno tiene su propio agente (Multiton) y su propia política.

**Reglas que aplican** (documento maestro):

- **RN-01:** El respaldo no detiene ni modifica el uso normal de Strumis ni de Tango.
- **RN-04:** Tipos de respaldo: completo inicial único, incremental en cada intervalo y diferencial diario.
- **RN-05:** Un solo respaldo en ejecución por servidor; si llega un ciclo y hay uno en curso, ese ciclo se omite y se registra.
- **RN-06:** Un respaldo que falla por un error propio se reintenta hasta 3 veces (el detalle es de HU-005).
- **RN-13:** Se registran el inicio y el resultado de cada respaldo, y los ciclos omitidos.

## Criterios de aceptación

**CA-1 – Ejecución automática e independiente por servidor** · _camino normal_

- **Dado** STRUMIS con política de 5 minutos, TANGO con política de 10 minutos, conexión con Acronis disponible y el último ciclo de ambos a las 14:00
- **Cuando** son las 14:05 (con una tolerancia de ±1 minuto)
- **Entonces** se crea una tarea de STRUMIS con disparador PROGRAMADO que pasa por EN_EJECUCION y VERIFICANDO hasta COMPLETADA, se genera un punto de restauración vigente por 30 días y no se crea ninguna tarea de TANGO hasta las 14:10

**CA-2 – Tipo de respaldo de cada ciclo** · _camino normal_

- **Dado** un agente sin respaldos previos, y otro con un completo previo
- **Cuando** corre el siguiente ciclo
- **Entonces** el primero hace un respaldo COMPLETO; el segundo hace un INCREMENTAL, salvo que ya sea la hora del diferencial (23:00) y ese día no se haya hecho uno, en cuyo caso hace un DIFERENCIAL

**CA-3 – Ciclo omitido si hay un respaldo en curso** · _caso de error_

- **Dado** un respaldo de STRUMIS que sigue ejecutándose, verificándose o reintentándose
- **Cuando** llega el siguiente ciclo de STRUMIS
- **Entonces** no se crea otra tarea ni se lanza un segundo respaldo de ese servidor, y queda registrado CICLO_OMITIDO en la auditoría

**CA-4 – Falla del respaldo: la tarea pasa a reintento** · _caso de error_

- **Dado** que Acronis informa un error en el primer intento de un respaldo de STRUMIS
- **Cuando** termina ese intento
- **Entonces** la tarea pasa a EN_REINTENTO con reintentos = 1 y el motivo registrado, y no se genera punto de restauración (los reintentos restantes y la alerta corresponden a HU-005 y HU-011)

**CA-5 – Sin conexión a Internet: la tarea queda en cola** · _caso de error_

- **Dado** que no hay conexión a Internet
- **Cuando** se ejecuta el ciclo de TANGO
- **Entonces** la tarea queda EN_COLA sin consumir reintentos y no se genera alerta de falla (el reenvío ordenado al recuperar la conexión corresponde a HU-007)

**CA-6 – Inicio y resultado auditados** · _camino normal_

- **Dado** un respaldo que se inicia y termina
- **Cuando** el planificador procesa su resultado
- **Entonces** se registran RESPALDO_INICIADO al comenzar y RESPALDO_FINALIZADO al terminar, con el servidor y el resultado

## Qué queda fuera de esta historia

- Los reintentos completos, uno por minuto, hasta agotarlos (HU-005).
- Reenviar en orden los respaldos en cola al recuperar la conexión (HU-007).
- La verificación de integridad propiamente dicha (HU-008); acá solo se recorre el estado VERIFICANDO.
- Alertas por falla o atraso (HU-011 y HU-012).
- Lanzar un respaldo a mano (HU-006): usa el mismo flujo pero con disparador MANUAL.

## Notas de diseño

**Clases del diseño (documento 06) que toca:**

- `PlanificadorDeRespaldos` (Singleton): `obtenerInstancia()`, `ejecutarCiclo`, `lanzar` y `procesarResultado`.
- `AgenteDeRespaldo` (Multiton, clave `ServidorOrigen`): `obtenerInstancia(servidor)`, `hayRespaldoEnCurso`, `ejecutarRespaldo` y `registrarExito`.
- `PoliticaDeRespaldo.tipoParaCiclo(agente, ahora)`.
- `IEstrategiaDeRespaldo` con `RespaldoCompleto`, `RespaldoIncremental` y `RespaldoDiferencial` (Strategy).
- `IProveedorNube` y `AcronisCloudAdapter` (Adapter) sobre `ApiAcronis`; `SinConexionException`.
- `TareaDeRespaldo` (estados y transiciones del documento 07), `PuntoDeRestauracion`, `ServicioDeAuditoria` y `GestorDeAlertas.cerrarAlertas`.

Esta historia usa **los cuatro patrones**: Singleton (`PlanificadorDeRespaldos`), Multiton (`AgenteDeRespaldo`), Strategy (los tres tipos de respaldo) y Adapter (`AcronisCloudAdapter`). Su secuencia está en el [documento 07](../07-estados-y-secuencia.md).

**Capturas finales de las pantallas** (ya corregidas después de la evaluación heurística, ver el [documento 10](../10-prototipo-y-heuristicas.md#hu-004)):

- [Pantalla 1 – Panel de respaldos](../../prototipo/HU-004-p1-panel.png)
- [Pantalla 2 – Detalle de una tarea con error y reintento](../../prototipo/HU-004-p2-detalle-tarea.png)

## Datos de prueba

| Criterio | Datos de prueba |
|---|---|
| CA-1 | Reloj simulado 26/10/2026 14:00:00. STRUMIS: intervalo 5 min; TANGO: intervalo 10 min. Acronis simulado responde «exitoso» y «íntegro» en 90 segundos. Tolerancia: se acepta un ciclo entre 14:04:00 y 14:06:00. Punto de restauración esperado: vigente hasta 25/11/2026. |
| CA-2 | Agente A: sin respaldos previos → COMPLETO. Agente B: completo previo a las 02:00; ciclos a las 14:05 → INCREMENTAL; a las 22:59 → INCREMENTAL; a las 23:00 sin diferencial del día → DIFERENCIAL; a las 23:30 con diferencial ya hecho → INCREMENTAL. |
| CA-3 | Respaldo de STRUMIS en cada estado en curso (EN_EJECUCION, VERIFICANDO, EN_REINTENTO) cuando llega un ciclo. Esperado: 0 tareas nuevas y 1 registro CICLO_OMITIDO. |
| CA-4 | Acronis simulado devuelve exitoso = falso con motivo «Acronis: error de autenticación». Esperado: estado EN_REINTENTO, reintentos 1, motivo guardado, 0 puntos de restauración. |
| CA-5 | Acronis simulado lanza «sin conexión» en el ciclo de TANGO. Esperado: estado EN_COLA, reintentos 0, 0 alertas. |
| CA-6 | Respaldo de CA-1. Registros esperados: RESPALDO_INICIADO (STRUMIS, «iniciado») y RESPALDO_FINALIZADO (STRUMIS, «COMPLETADA»), ambos con el usuario «SGR». |

## Dependencias, estimación y Definition of Ready

- **Depende de:** HU-002 (conexión con Acronis) y HU-003 (política del servidor).
- **Estimación:** 8 SP, Sprint 2.

**Chequeo de Definition of Ready:**

- [x] Tiene rol, acción y beneficio, idénticos a los del backlog ([documento 01](../01-vision-y-backlog.md)).
- [x] Tiene 6 criterios Dado/Cuando/Entonces y 3 son casos de error.
- [x] Tiene estimación (8 SP) y prioridad (Must).
- [x] Sus dependencias están planificadas en sprints anteriores o en el mismo sprint (documento 02).
- [x] Indica qué queda fuera y los datos de prueba de cada criterio.
- [ ] Revisada por los cuatro integrantes del grupo.
