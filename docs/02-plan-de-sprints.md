# 02 – Plan de sprints

| | |
|---|---|
| **Entregable** | Punto 2 de la consigna (Parcial 3) |
| **Estado** | Borrador v3 – 28/10/2026 |
| **Backlog de origen** | [01 – Visión y backlog](01-vision-y-backlog.md) |

## 1. Duración y capacidad

- **Duración de los sprints:** 3 sprints cortos dentro de una única semana (del 24/10/2026 al 30/10/2026). El Sprint 1 dura 3 días corridos y los Sprints 2 y 3 duran 2 días cada uno.
- **Equipo supuesto:** 4 integrantes con **dedicación completa** durante toda la semana (incluido el fin de semana del 24 y 25/10).
- **Capacidad supuesta:** **10 story points por día** de equipo = 4 personas × 2,5 SP por persona y día. Con esa velocidad, la capacidad es de 30 SP en el Sprint 1 (3 días) y de 20 SP en cada uno de los Sprints 2 y 3 (2 días).
- **Criterio:** 1 SP equivale a unas 3 horas de trabajo efectivo de una persona. Esta calibración reemplaza a la de la versión anterior (dedicación parcial, 5 SP por persona cada 2 semanas), que con una sola semana habría dado apenas unos 10 SP en total. Como no hay historial de velocidad, el valor es una **suposición a recalibrar** al cierre del Sprint 1. Ningún sprint se carga al 100 % para dejar margen a reuniones y retrabajo.
- **Alcance:** este plan cubre solo los Sprints 1 a 3 del plan original (22 HU originales → 11 HU, 53 SP). Las 11 HU restantes (42 SP) quedan en el backlog sin sprint asignado (ver sección 6).
- **Regla de planificación:** ningún sprint supera la capacidad; las dependencias se respetan (una HU nunca va antes de lo que necesita).

## 2. Escala de estimación

Secuencia de Fibonacci acotada (1, 2, 3, 5, 8). **HU de referencia:** HU-001 (registrar servidores) = 3 SP: un formulario con validaciones y persistencia. Una HU de 8 SP implica integración con sistemas externos o varios estados.

## 3. Resumen de sprints

| Sprint | Fechas | Objetivo (sprint goal) | HU | Puntos | Capacidad |
|---|---|---|---|---|---|
| 1 | 24/10/2026 – 26/10/2026 | Dejar listos el acceso por rol, el registro de auditoría, los servidores STRUMIS y TANGO y la política de respaldo de cada uno. | HU-018, HU-023, HU-001, HU-003 | 16 | 30 |
| 2 | 27/10/2026 – 28/10/2026 | Lograr que ambos servidores se respalden automáticamente en Acronis Cloud, con reintentos y con la opción de lanzar un respaldo manual. | HU-002, HU-004, HU-005, HU-006 | 19 | 20 |
| 3 | 29/10/2026 – 30/10/2026 | Garantizar que cada respaldo sea íntegro, que no se pierda ante cortes de Internet y que las fallas y los atrasos generen alertas. | HU-008, HU-011, HU-007, HU-012 | 18 | 20 |

Total planificado: **53 SP** en 3 sprints (capacidad total 70 SP). Las fechas pertenecen al proyecto hipotético de implementación y se pueden mover; lo importante es el orden.

## 4. Detalle por sprint

### Sprint 1 (24/10 – 26/10)

**Objetivo:** Dejar listos el acceso por rol, el registro de auditoría, los servidores STRUMIS y TANGO y la política de respaldo de cada uno.

| Orden | HU | Título | MoSCoW | SP | Depende de |
|---|---|---|---|---|---|
| 1 | HU-018 | Autenticarse y acceder según el rol | Must | 5 | — |
| 2 | HU-023 | Registrar automáticamente las acciones en auditoría | Must | 3 | HU-018 |
| 3 | HU-001 | Registrar los servidores de origen | Must | 3 | HU-018 |
| 4 | HU-003 | Definir la política de respaldo de cada servidor | Must | 5 | HU-001 |

Puntos del sprint: **16** de 30 (holgura 14).

### Sprint 2 (27/10 – 28/10)

**Objetivo:** Lograr que ambos servidores se respalden automáticamente en Acronis Cloud, con reintentos y con la opción de lanzar un respaldo manual.

| Orden | HU | Título | MoSCoW | SP | Depende de |
|---|---|---|---|---|---|
| 1 | HU-002 | Conectar el sistema con Acronis Cloud | Must | 5 | HU-018 |
| 2 | HU-004 | Ejecutar respaldos automáticos según la política | Must | 8 | HU-002, HU-003 |
| 3 | HU-005 | Reintentar automáticamente un respaldo fallido | Must | 3 | HU-004 |
| 4 | HU-006 | Lanzar un respaldo manual a demanda | Should | 3 | HU-004, HU-023 |

Puntos del sprint: **19** de 20 (holgura 1).

### Sprint 3 (29/10 – 30/10)

**Objetivo:** Garantizar que cada respaldo sea íntegro, que no se pierda ante cortes de Internet y que las fallas y los atrasos generen alertas.

| Orden | HU | Título | MoSCoW | SP | Depende de |
|---|---|---|---|---|---|
| 1 | HU-008 | Verificar la integridad de cada respaldo | Must | 5 | HU-004 |
| 2 | HU-011 | Recibir una alerta cuando un respaldo falla | Must | 5 | HU-005 |
| 3 | HU-007 | Encolar respaldos cuando no hay conexión a Internet | Should | 5 | HU-004 |
| 4 | HU-012 | Recibir una alerta por atraso de respaldos | Should | 3 | HU-011 |

Puntos del sprint: **18** de 20 (holgura 2).

## 5. Verificación de dependencias

| HU | Sprint | Depende de (sprint) | ¿Se respeta? |
|---|---|---|---|
| HU-023 | 1 | HU-018 (S1) | Sí |
| HU-001 | 1 | HU-018 (S1) | Sí |
| HU-003 | 1 | HU-001 (S1) | Sí |
| HU-002 | 2 | HU-018 (S1) | Sí |
| HU-004 | 2 | HU-002 (S2), HU-003 (S1) | Sí |
| HU-005 | 2 | HU-004 (S2) | Sí |
| HU-006 | 2 | HU-004 (S2), HU-023 (S1) | Sí |
| HU-008 | 3 | HU-004 (S2) | Sí |
| HU-011 | 3 | HU-005 (S2) | Sí |
| HU-007 | 3 | HU-004 (S2) | Sí |
| HU-012 | 3 | HU-011 (S3) | Sí |

Dentro de un mismo sprint, el orden de la tabla del punto 4 es el orden de trabajo, de modo que cada HU se hace después de la que necesita. Las HU sin dependencias declaradas (HU-018) son las primeras del Sprint 1; todas las demás funciones requieren iniciar sesión, por lo que HU-018 va primero.

## 6. Hitos de producto y backlog pendiente

- **Cierre del Sprint 1:** acceso por rol, auditoría, servidores registrados y política de respaldo definida.
- **Cierre del Sprint 2:** respaldo automático en Acronis Cloud con reintentos y respaldo manual.
- **Cierre del Sprint 3:** el núcleo de protección funciona: respaldo automático (HU-004), reintentos (HU-005), verificación de integridad (HU-008), cola ante cortes de Internet (HU-007) y alertas de falla y atraso (HU-011, HU-012).

**Backlog pendiente (sin sprint asignado, 42 SP):** HU-009, HU-014, HU-015, HU-013, HU-010, HU-016, HU-017, HU-020, HU-019, HU-021 y HU-022. Entre ellas hay HU de prioridad **Must** (HU-009, HU-014, HU-015 y HU-016: panel, solicitud y autorización de restauraciones, y restauración), por lo que con este plan **no quedan entregadas todas las HU Must**; la restauración de datos queda fuera del alcance de estos 3 sprints.

## 7. Carga en el tablero de GitHub (Sprint, Puntos y Prioridad)

Por indicación del docente se usa GitHub en lugar de Azure DevOps. El backlog vive en **issues del repositorio** y en un **tablero (GitHub Project)** con tres campos personalizados, con estos nombres exactos:

| Campo del tablero | Tipo | Valores |
|---|---|---|
| **Sprint** | Iteration | Tres iteraciones (tabla de abajo) |
| **Puntos** | Number | Story points de cada HU (escala 1, 2, 3, 5, 8) |
| **Prioridad** | Single select | Must, Should, Could, Won't (MoSCoW) |

Las tres iteraciones del campo **Sprint**:

| Iteración | Inicio | Fin | Duración |
|---|---|---|---|
| Sprint 1 | 2026-10-24 | 2026-10-26 | 3 días |
| Sprint 2 | 2026-10-27 | 2026-10-28 | 2 días |
| Sprint 3 | 2026-10-29 | 2026-10-30 | 2 días |

Las iteraciones son consecutivas, sin receso entre ellas. Las HU del backlog pendiente (sección 6) se cargan con el campo Sprint vacío.

Las épicas, features e historias se cargan como issues con las etiquetas `épica`, `feature` e `historia`, enlazadas como sub-issues (feature dentro de épica, historia dentro de feature). La creación de etiquetas e issues se puede automatizar con el script que acompaña este trabajo; el detalle está en `_interno/guia-github.md`. Los valores de Sprint, Puntos y Prioridad de cada HU son los de las secciones 3 y 4 de este documento y los del backlog.

## 8. Riesgos y supuestos del plan

- La velocidad de 10 SP por día de equipo exige dedicación completa de las 4 personas, incluido el fin de semana. Es una suposición sin historial; si en el Sprint 1 se completan menos puntos, se mueven HU Should (HU-006, HU-007, HU-012) fuera del plan.
- Los Sprints 2 y 3 quedan con muy poca holgura (1 y 2 SP) y duran solo 2 días cada uno, por lo que cualquier retraso se traslada al sprint siguiente.
- HU-004 (8 SP) concentra la mayor complejidad (ejecución automática con integración a Acronis); si se atrasa, arrastra a HU-005, HU-006, HU-008 y a todo el Sprint 3. Está prevista su división en dos si se desborda.
- Las HU de los Sprints 2 y 3 asumen la integración con Acronis Cloud definida en HU-002; un cambio de proveedor impactaría el plan (ver ADR-001).
- Quedan fuera de este plan las HU de restauración (HU-014 a HU-017); si el alcance debe incluirlas, hay que ampliar el plazo o reducir el resto.
