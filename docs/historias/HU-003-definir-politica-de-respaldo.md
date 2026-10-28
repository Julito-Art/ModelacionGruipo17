# HU-003 – Definir la política de respaldo de cada servidor

| | |
|---|---|
| **Prioridad (MoSCoW)** | Must |
| **Estimación** | 5 story points |
| **Sprint** | 1 |
| **Épica / Feature** | E1 Respaldo automatizado en la nube / F1.1 Conexión y configuración de respaldos |
| **Depende de** | HU-001 |
| **Origen en OAS** | Alcance del mandato (tiempo real o tiempos prolongados muy cortos); Solución Elegida (plazo mínimo de guardado entre 5 y 10 minutos). |
| **Documentos relacionados** | [06 – Clases](../06-diseno-de-clases.md), [07 – Estados y secuencia](../07-estados-y-secuencia.md), [Casos de prueba](../testing/casos-HU-003.md), [10 – Prototipo y heurísticas](../10-prototipo-y-heuristicas.md) |

## Historia

Como **Responsable de Respaldos** quiero definir para cada servidor el intervalo de respaldo (entre 5 y 10 minutos, por defecto 5), el esquema de tipos de respaldo y la retención para cumplir el objetivo de perder como máximo 10 minutos de datos.

## Contexto y reglas de negocio

- La política define cada cuánto se respalda un servidor y por cuánto tiempo se conservan sus puntos de restauración. Un servidor sin política no se respalda y queda marcado como «Sin protección».
- El valor por defecto de 5 minutos permite cumplir RNF-01: la antigüedad del último respaldo es el intervalo más la duración del respaldo y de su verificación. Con 10 minutos el objetivo puede no cumplirse (SM-03).
- El esquema de tipos de respaldo es fijo (SM-04): un completo inicial único, incrementales en cada intervalo y un diferencial diario fuera del horario laboral. En la pantalla solo se ajusta la hora del diferencial.

**Reglas que aplican** (documento maestro):

- **RN-03:** El intervalo es configurable entre 5 y 10 minutos; el valor por defecto es 5 y con más de 5 se avisa que RNF-01 puede no cumplirse.
- **RN-04:** Esquema de tipos: completo inicial único, incremental en cada intervalo y diferencial diario fuera del horario laboral.
- **RN-12:** Los puntos de restauración se conservan 30 días (retención por defecto).
- **RN-13:** El cambio de política es una acción relevante y se registra en la auditoría.
- **RN-16:** Solo el Responsable de Respaldos opera: define las políticas.

## Criterios de aceptación

**CA-1 – Valores por defecto y servidor sin protección** · _camino normal_

- **Dado** un servidor STRUMIS registrado que no tiene política y un usuario con rol Responsable de Respaldos autenticado
- **Cuando** abre «Política de respaldo» de STRUMIS
- **Entonces** el intervalo aparece precargado en 5 minutos y la retención en 30 días, y STRUMIS figura como «Sin protección» hasta que se guarde la política

**CA-2 – Guardar una política válida** · _camino normal_

- **Dado** el formulario de STRUMIS con intervalo 5, retención 30 y hora del diferencial 23:00
- **Cuando** el Responsable guarda
- **Entonces** la política queda vigente, STRUMIS deja de figurar como «Sin protección» y al reabrir el formulario se muestran los mismos valores

**CA-3 – Rechazar un intervalo fuera de rango** · _caso de error_

- **Dado** el formulario de STRUMIS con un intervalo de 11 minutos (o de 4)
- **Cuando** el Responsable intenta guardar
- **Entonces** no se guarda nada y, junto al campo, se muestra «El intervalo debe estar entre 5 y 10 minutos»; la política anterior no cambia

**CA-4 – Aviso por intervalo mayor a 5 minutos** · _camino normal_

- **Dado** el formulario de STRUMIS con un intervalo de 10 minutos
- **Cuando** el Responsable guarda
- **Entonces** la política se guarda y se muestra el aviso «Con más de 5 minutos no se garantiza perder como máximo 10 minutos de datos»

**CA-5 – Solo el Responsable define políticas** · _caso de error_

- **Dado** un usuario con rol Gerente de Administración (o Dirección) autenticado
- **Cuando** intenta abrir o guardar una política
- **Entonces** la opción no está disponible y, si lo intenta por otra vía, recibe «No tenés permiso para definir políticas» y la política no cambia

**CA-6 – La acción queda auditada** · _camino normal_

- **Dado** un guardado exitoso de la política de STRUMIS
- **Cuando** el guardado termina
- **Entonces** se registra en la auditoría la acción CAMBIO_DE_POLITICA con el usuario, la fecha y hora, el servidor y el resultado

## Qué queda fuera de esta historia

- Registrar los servidores (HU-001) y conectar con Acronis Cloud (HU-002).
- Lanzar los respaldos según la política (HU-004).
- Aplicar la misma política a los dos servidores de una sola vez.
- Editar el esquema de tipos de respaldo más allá de la hora del diferencial (SM-04).

## Notas de diseño

**Clases del diseño (documento 06) que toca:**

- `PoliticaDeRespaldo`: constantes `INTERVALO_MIN`, `INTERVALO_MAX` e `INTERVALO_POR_DEFECTO`; `validar()` y `cumpleObjetivoDePerdida()`.
- `PlanificadorDeRespaldos.definirPolitica(politica)` (Singleton): guarda la política vigente del servidor.
- `ServicioDeAuditoria.registrar(...)` con `AccionAuditada.CAMBIO_DE_POLITICA`.
- `Usuario.tieneRol(rol)` para el control de CA-5 y `ServidorOrigen` (`STRUMIS`, `TANGO`).

Esta historia usa el **Singleton**: `definirPolitica` se invoca siempre sobre la única instancia de `PlanificadorDeRespaldos`, de modo que el planificador y la pantalla comparten las mismas políticas.

**Capturas finales de las pantallas** (ya corregidas después de la evaluación heurística, ver el [documento 10](../10-prototipo-y-heuristicas.md#hu-003)):

- [Pantalla 1 – Política de respaldo (formulario)](../../prototipo/HU-003-p1-politica.png)
- [Pantalla 2 – Error de validación del intervalo](../../prototipo/HU-003-p2-error-intervalo.png)

## Datos de prueba

| Criterio | Datos de prueba |
|---|---|
| CA-1 | Servidor STRUMIS sin política; usuario resp.prueba@prometva.test (Responsable de Respaldos). Valores esperados: intervalo 5, retención 30, etiqueta «Sin protección». |
| CA-2 | Intervalo 5; retención 30; hora del diferencial 23:00 (ejemplo de horario fuera de jornada; la hora real se define con la empresa). |
| CA-3 | Intervalos inválidos 11 y 4; intervalos límite válidos 5 y 10; política previa de STRUMIS con intervalo 5 (debe quedar intacta). |
| CA-4 | Intervalo 10; retención 30. |
| CA-5 | gerente.prueba@prometva.test (Gerente de Administración) y direccion.prueba@prometva.test (Dirección). |
| CA-6 | Guardado de CA-2. Registro esperado: usuario resp.prueba@prometva.test, acción CAMBIO_DE_POLITICA, objetivo STRUMIS, resultado «exitoso». |

## Dependencias, estimación y Definition of Ready

- **Depende de:** HU-001 (el servidor debe estar registrado).
- **Estimación:** 5 SP, Sprint 1.

**Chequeo de Definition of Ready:**

- [x] Tiene rol, acción y beneficio, idénticos a los del backlog ([documento 01](../01-vision-y-backlog.md)).
- [x] Tiene 6 criterios Dado/Cuando/Entonces y 2 son casos de error.
- [x] Tiene estimación (5 SP) y prioridad (Must).
- [x] Sus dependencias están planificadas en sprints anteriores o en el mismo sprint (documento 02).
- [x] Indica qué queda fuera y los datos de prueba de cada criterio.
- [ ] Revisada por los cuatro integrantes del grupo.
