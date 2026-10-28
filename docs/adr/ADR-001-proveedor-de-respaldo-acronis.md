# ADR-001 – Usar Acronis Cloud como proveedor de respaldo en la nube

| | |
|---|---|
| **Estado** | Aceptado |
| **Fecha** | 24/10/2026 |
| **Documentos relacionados** | [04 – RNF y arquitectura](../04-rnf-y-arquitectura.md), [00 – Documento maestro](../00-documento-maestro.md) (SM-01, SM-11) |

## Contexto

Prometva necesita copiar de forma automática las bases de datos de Strumis y Tango fuera de sus servidores privados (Mandato de OAS). Hay que decidir **con qué tecnología se obtiene el respaldo en la nube**, porque de eso dependen las capacidades del SGR (qué puede ordenar, supervisar y restaurar).

Los requisitos que condicionan la decisión:

- **RNF-01:** pérdida máxima de datos ≤ 10 minutos en el 99 % de las mediciones (la solución debe soportar respaldos cada 5 a 10 minutos).
- **RNF-02:** el 100 % de los respaldos cifrado con AES-256 en reposo y TLS 1.2 o superior en tránsito.
- **RNF-05:** restauración total de hasta 100 GB en ≤ 4 horas.
- **RNF-07:** usable por personal sin conocimientos técnicos avanzados.
- **RNF-08:** respaldar sin modificar las bases SQL de Strumis y Tango.
- Restricciones **RE-04** (calidad por encima de lo económico) y **RE-05** (sin área técnica especializada).

El trabajo de OAS ya comparó tres soluciones en su *Cuadro Comparativo*; este ADR deja registrada la decisión y su justificación a partir de los RNF.

## Alternativas consideradas

Los valores salen del Cuadro Comparativo del trabajo de OAS.

### Alternativa A – Acronis Cloud (Backup Empresarial, solución 1)

| Criterio | Valor (OAS) |
|---|---|
| Facilidad de implementación | Alta |
| Costo inicial / operativo | Medio / Medio (suscripción) |
| Seguridad | Alta (certificaciones) |
| Escalabilidad | Alta |
| Personalización | Baja |
| Dependencia externa | Alta |
| Tiempo de implementación | Rápido |

- **Ventajas:** cifrado AES-256 y TLS incluidos (RNF-02); respaldo incremental, diferencial y completo; restauración granular o total (RNF-05); panel y API listos; no requiere conocimientos avanzados (RNF-07, RE-05).
- **Desventajas:** dependencia alta de un tercero; costo recurrente por suscripción; poca personalización; necesita Internet estable.

### Alternativa B – NAS con sincronización a la nube (solución 2)

| Criterio | Valor (OAS) |
|---|---|
| Facilidad de implementación | Media |
| Costo inicial / operativo | Alto / Bajo-Medio |
| Seguridad | Media (depende de la configuración) |
| Escalabilidad | Media |
| Personalización | Media |
| Dependencia externa | Media |
| Tiempo de implementación | Medio |

- **Ventajas:** copia local rápida más copia remota (dos copias); menor dependencia externa; costo operativo bajo.
- **Desventajas:** requiere comprar y mantener hardware; al ser local queda más expuesto a ataques, lo que compromete RNF-02 (el incidente previo de la empresa fue sobre un servidor propio); su seguridad depende de la configuración que haga la empresa, sin personal especializado (RE-05).

### Alternativa C – Software propio de respaldo (solución 3)

| Criterio | Valor (OAS) |
|---|---|
| Facilidad de implementación | Baja |
| Costo inicial / operativo | Muy alto / Alto (mantenimiento interno) |
| Seguridad | Variable (depende del diseño) |
| Escalabilidad | Depende del diseño |
| Personalización | Alta |
| Dependencia externa | Baja |
| Tiempo de implementación | Lento |

- **Ventajas:** control total del código; máxima personalización; sin dependencia de terceros.
- **Desventajas:** exige equipo de desarrollo y mantenimiento permanente (contradice RE-05); implementación lenta y de costo muy alto; la seguridad (RNF-02) depende de que se diseñe y pruebe bien; sin certificaciones.

## Decisión

Se adopta la **Alternativa A: Acronis Cloud**. Es la única que satisface a la vez RNF-01, RNF-02, RNF-05 y RNF-07 sin exigir hardware propio ni un equipo técnico (RE-05). El SGR accede a Acronis a través del puerto `IProveedorNube` y su adaptador `AcronisCloudAdapter`, para acotar la dependencia.

Las alternativas B y C quedan **descartadas y fuera del modelo** (SM-11).

## Consecuencias

**Positivas**
- Cifrado y protocolos seguros resueltos por el proveedor (RNF-02).
- Implementación rápida y de bajo conocimiento técnico requerido (RNF-07).
- Restauración total o granular disponible sin desarrollo propio (RNF-05).
- El SGR se concentra en orquestar y controlar, no en implementar el respaldo.

**Negativas**
- **Dependencia alta de un proveedor externo:** si Acronis cambia su API, sus precios o tiene una caída, el SGR y los respaldos se ven afectados. Se mitiga con el puerto `IProveedorNube`, pero no se elimina.
- **Costo recurrente** por suscripción, que se suma al costo del servidor del SGR (ver ADR-003).
- **Personalización baja:** las funciones disponibles están limitadas a lo que ofrece Acronis; el SGR debe adaptarse a ellas.
- **Dependencia de Internet:** el RPO (RNF-01) y el RTO (RNF-05) dependen del ancho de banda supuesto en SM-08 **[A VALIDAR]**.
- Los datos de la empresa quedan alojados fuera de ella, lo que obliga a confiar en las certificaciones del proveedor.
