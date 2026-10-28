# ADR-003 – Desplegar el SGR en un servidor dedicado dentro de la red de Prometva

| | |
|---|---|
| **Estado** | Propuesto (a validar con la empresa: implica comprar o asignar un equipo) |
| **Fecha** | 24/10/2026 |
| **Documentos relacionados** | [04 – RNF y arquitectura](../04-rnf-y-arquitectura.md), [ADR-002](ADR-002-estilo-monolito-modular.md) |

## Contexto

Hay que decidir **dónde se ejecuta el SGR** (aplicación y su base de datos). El sistema debe convivir con los servidores existentes de Strumis y Tango sin alterarlos.

Los requisitos que condicionan la decisión:

- **RNF-04:** durante un respaldo, el tiempo de respuesta de Strumis no puede empeorar más de un 10 % con 30 usuarios concurrentes.
- **RNF-03:** disponibilidad mensual del SGR ≥ 99 %.
- **RNF-02:** comunicaciones cifradas y superficie de ataque reducida; la empresa ya sufrió un ataque a su servidor privado.
- **RNF-08:** las bases de Strumis y Tango no se modifican.
- Restricciones **RE-01** (no alterar lo existente), **RE-04** (calidad por encima de lo económico) y **RE-05** (sin área técnica especializada). El trabajo de OAS pide a la vez calidad por encima de lo económico (Diagnóstico) y un software de bajo costo (La Solución Elegida), y su Límite del mandato pide no alterar el flujo de procesos actual.

## Alternativas consideradas

### Alternativa A – Instalar el SGR en los servidores existentes (Strumis o Tango)

- **Ventajas:** no hay que comprar equipo; menos infraestructura.
- **Desventajas:** compite por recursos con las bases en producción (riesgo para RNF-04); un fallo o ataque sobre ese servidor se lleva también al SGR, justo cuando más se lo necesita; obliga a modificar los servidores existentes, contra RE-01.

### Alternativa B – Alojar el SGR en una máquina virtual en la nube

- **Ventajas:** sin hardware propio; buena disponibilidad según el proveedor (RNF-03); aislado de un ataque a la red interna.
- **Desventajas:** requiere administrar una cuenta y una máquina en la nube, conocimiento que la empresa no tiene (RE-05); suma un segundo proveedor externo, además de Acronis; agrega costo mensual recurrente y exposición a Internet de la interfaz del SGR.

## Decisión

Se despliega el SGR en un **servidor dedicado, nuevo, dentro de la red de Prometva y separado de los servidores de producción**: aplicación modular, interfaz web y base de datos SQL propia en el mismo equipo (ver diagrama de despliegue). Solo se instala el agente de Acronis en los servidores existentes.

Se prioriza la separación de la producción (RNF-04) y la ausencia de nuevos proveedores (RE-05), aceptando el costo de un equipo extra, que se limita a un **equipo estándar** (no un servidor de alta gama) para conciliar RE-04 con el pedido de bajo costo de OAS.

## Consecuencias

**Positivas**
- Los servidores de Strumis y Tango no cargan con el SGR ni se ven alterados (RNF-04, RNF-08, RE-01).
- Un ataque o falla en un servidor de producción no se lleva al SGR.
- No se introduce un segundo proveedor externo ni se expone la interfaz del SGR a Internet.

**Negativas**
- **Choca en parte con el «bajo costo» de OAS** y agrega un equipo en la empresa. No altera el flujo de trabajo del resto del personal (Límite del mandato), pero suma hardware y el rol de Responsable de Respaldos; se defiende porque evita cargar los servidores de producción (RNF-04).
- **Costo de hardware** y de su mantenimiento, en una empresa sin personal de informática (RE-05). Se puede reducir usando un equipo estándar y con las alertas del propio SGR.
- **Punto único de falla físico:** si se rompe el equipo, no se lanzan respaldos hasta repararlo; la disponibilidad de RNF-03 depende de ese hardware.
- **Sigue estando en la red de la empresa:** si la red se compromete, el SGR puede verse alcanzado. Se mitiga restringiendo los puertos abiertos y el acceso a la base `sgr-db` solo desde el propio equipo.
- **El SGR no respalda su propia base de datos** (RN-02 define solo dos orígenes): una falla del disco pierde la auditoría y la configuración. Queda como riesgo abierto **[A VALIDAR]**.
