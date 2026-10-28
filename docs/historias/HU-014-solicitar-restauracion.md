# HU-014 – Solicitar una restauración

| | |
|---|---|
| **Prioridad (MoSCoW)** | Must |
| **Estimación** | 5 story points |
| **Sprint** | 2 |
| **Épica / Feature** | E3 Restauración de datos / F3.1 Solicitud y autorización de restauración |
| **Depende de** | HU-008 |
| **Origen en OAS** | Relevamiento (hackeo y pago para recuperar los datos); Backup Empresarial (restauración granular o total). |
| **Documentos relacionados** | [06 – Clases](../06-diseno-de-clases.md), [07 – Estados y secuencia](../07-estados-y-secuencia.md), [Casos de prueba](../testing/casos-HU-014.md), [10 – Prototipo y heurísticas](../10-prototipo-y-heuristicas.md) |

## Historia

Como **Responsable de Respaldos** quiero solicitar la restauración de STRUMIS o TANGO desde un punto de restauración, de forma total o granular para recuperar los datos tras una falla o un ataque.

## Contexto y reglas de negocio

- Ante una pérdida de datos o un ataque como el que sufrió la empresa, el Responsable pide recuperar un servidor desde un punto de restauración. Pedirlo no restaura nada: la solicitud queda PENDIENTE hasta que Dirección la autorice (HU-015).
- Todo punto de restauración existe solo si el respaldo pasó la verificación de integridad (HU-008), por lo que «verificado» se cumple siempre. Lo que se filtra en esta pantalla es la vigencia: un punto vence a los 30 días (RN-12).
- La restauración puede ser TOTAL (todo el servidor) o GRANULAR (solo los elementos que se indiquen, por ejemplo ciertas tablas o carpetas).

**Reglas que aplican** (documento maestro):

- **RN-10:** Toda restauración exige solicitud del Responsable y autorización de Dirección.
- **RN-12:** Los puntos de restauración se conservan 30 días.
- **RN-13:** Solicitar una restauración es una acción relevante y se audita.
- **RN-16:** Solo el Responsable de Respaldos solicita restauraciones.

## Criterios de aceptación

**CA-1 – Solo se ofrecen puntos vigentes** · _camino normal_

- **Dado** el servidor TANGO con tres puntos de restauración: uno de hace 2 días, uno de hace 29 días y uno de hace 31 días
- **Cuando** el Responsable abre «Nueva solicitud de restauración» y elige TANGO
- **Entonces** la lista ofrece únicamente los dos puntos vigentes; el de hace 31 días no aparece

**CA-2 – Solicitud total correcta** · _camino normal_

- **Dado** el punto de hace 2 días elegido, tipo TOTAL y el motivo «Pérdida de datos por ataque»
- **Cuando** el Responsable envía la solicitud
- **Entonces** se crea una solicitud en estado PENDIENTE con el solicitante, la fecha y el punto elegido, se muestra la confirmación y se registra RESTAURACION_SOLICITADA en la auditoría

**CA-3 – El motivo es obligatorio** · _caso de error_

- **Dado** el formulario con el motivo vacío (o solo con espacios)
- **Cuando** el Responsable envía
- **Entonces** se rechaza con «El motivo es obligatorio» junto al campo y no se crea ninguna solicitud

**CA-4 – Una restauración granular exige elementos** · _caso de error_

- **Dado** el tipo GRANULAR elegido y ningún elemento indicado
- **Cuando** el Responsable envía
- **Entonces** se rechaza con «Indicá al menos un elemento a restaurar» y no se crea ninguna solicitud

**CA-5 – Solicitud granular correcta** · _camino normal_

- **Dado** el tipo GRANULAR con los elementos «Facturas 2026» y «Clientes» y un motivo
- **Cuando** el Responsable envía
- **Entonces** se crea una solicitud PENDIENTE que conserva esos dos elementos

**CA-6 – Solo el Responsable solicita** · _caso de error_

- **Dado** un usuario con rol Dirección o Gerente de Administración autenticado
- **Cuando** intenta crear una solicitud
- **Entonces** la opción no está disponible y, si lo intenta por otra vía, recibe «No tenés permiso para solicitar restauraciones» y no se crea nada

**CA-7 – Servidor sin puntos vigentes** · _caso de error_

- **Dado** un servidor sin ningún punto de restauración vigente
- **Cuando** el Responsable abre «Nueva solicitud de restauración» y lo elige
- **Entonces** se muestra «No hay puntos de restauración vigentes para este servidor» y no se puede enviar la solicitud

## Qué queda fuera de esta historia

- Autorizar o rechazar la solicitud (HU-015).
- Ejecutar la restauración y el respaldo previo a restauración (HU-016).
- Los simulacros de restauración (HU-017).
- Elegir un destino de restauración distinto del servidor de origen.

## Notas de diseño

**Clases del diseño (documento 06) que toca:**

- `ServicioDeRestauracion.solicitar(punto, tipo, elementos, motivo, solicitante, ahora)`.
- `SolicitudDeRestauracion` (constructor, estado inicial PENDIENTE; ver el diagrama de estados del documento 07), con `TipoDeRestauracion` (`TOTAL`, `GRANULAR`) y `elementos: List<String>`.
- `PuntoDeRestauracion.estaVigente(ahora)` (vigente si `ahora < vigenteHasta`, con `vigenteHasta = fechaHora + 30 días`).
- `Usuario.tieneRol(rol)` para el control de CA-6 y `ServicioDeAuditoria.registrar` con `AccionAuditada.RESTAURACION_SOLICITADA`.

Esta historia **no** introduce un patrón nuevo; usa los puntos de restauración que genera `PlanificadorDeRespaldos` (Singleton) al completar los respaldos de HU-004. La historia de este grupo que usa patrones es HU-004.

**Capturas finales de las pantallas** (ya corregidas después de la evaluación heurística, ver el [documento 10](../10-prototipo-y-heuristicas.md#hu-014)):

- [Pantalla 1 – Nueva solicitud de restauración](../../prototipo/HU-014-p1-solicitud.png)
- [Pantalla 2 – Errores de validación](../../prototipo/HU-014-p2-errores.png)
- [Pantalla 3 – Solicitud enviada (PENDIENTE)](../../prototipo/HU-014-p3-confirmacion.png)

## Datos de prueba

| Criterio | Datos de prueba |
|---|---|
| CA-1 | Fecha actual simulada: 26/10/2026 23:00. Puntos de TANGO: PR-A (24/10/2026 23:00, DIFERENCIAL), PR-B (27/09/2026 23:00, INCREMENTAL) y PR-C (25/09/2026 23:00, INCREMENTAL). Esperado: se ofrecen PR-A y PR-B; PR-C no. |
| CA-2 | Punto PR-A; tipo TOTAL; motivo «Pérdida de datos por ataque»; usuario resp.prueba@prometva.test. Esperado: solicitud PENDIENTE; auditoría RESTAURACION_SOLICITADA. |
| CA-3 | Motivo «» y motivo «   » (tres espacios). Esperado: rechazo en ambos casos. |
| CA-4 | Tipo GRANULAR con 0 elementos (rechazo) y con 1 elemento «Clientes» (se acepta). |
| CA-5 | Elementos «Facturas 2026» y «Clientes»; motivo «Recuperar facturación eliminada por error». |
| CA-6 | direccion.prueba@prometva.test y gerente.prueba@prometva.test. |
| CA-7 | Servidor STRUMIS sin puntos vigentes (todos de hace más de 30 días). |

## Dependencias, estimación y Definition of Ready

- **Depende de:** HU-008 (los puntos de restauración se generan al verificar los respaldos).
- **Estimación:** 5 SP, Sprint 2.

**Chequeo de Definition of Ready:**

- [x] Tiene rol, acción y beneficio, idénticos a los del backlog ([documento 01](../01-vision-y-backlog.md)).
- [x] Tiene 7 criterios Dado/Cuando/Entonces y 4 son casos de error.
- [x] Tiene estimación (5 SP) y prioridad (Must).
- [x] Sus dependencias están planificadas en sprints anteriores o en el mismo sprint (documento 02).
- [x] Indica qué queda fuera y los datos de prueba de cada criterio.
- [ ] Revisada por los cuatro integrantes del grupo.
