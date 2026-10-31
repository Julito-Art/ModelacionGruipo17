# Sistema de Gestión de Respaldos de Prometva (SGR)

Trabajo integrador de **Modelación y Diseño de Sistemas** (Parciales 2 y 3). Modelado y documentación de la solución elegida en el trabajo de campo de OAS sobre **Prometva** (respaldo automático en la nube con Acronis Cloud de las bases de Strumis y Tango). No se programa: se modela y se documenta.

## Datos del grupo

|||
|-|-|
|**Grupo**|N.º 17|
|**Integrantes**|Julián Álvarez García, Mauro Flores, Gabriela Ruiz y Marcos Valor|
|**Caso de OAS**|Prometva (pyme de estructuras metálicas), trabajo de campo del Grupo 2 de OAS (Alvarez Julián, Altamirano Nicolás y Carrizo Agustín).|
|**Mail del interlocutor**|**julian.alvarez.423@mi.unc.edu.ar**|
|**Board del proyecto**|**https://github.com/users/Julito-Art/projects/4**|
|**Repositorio**|**https://github.com/Julito-Art/Proyecto-ModelacionGrupo17**|

Uso de GitHub: por indicación y con autorización del docente, el trabajo se entrega en GitHub en lugar de Azure DevOps. El repositorio es privado y el docente está invitado al repositorio y al tablero. El backlog son issues con las etiquetas `épica`, `feature` e `historia`, enlazadas como sub-issues, y el tablero tiene los campos Sprint, Puntos y Prioridad

## Índice de la entrega

|Punto|Entregable|Archivo|Pendiente|
|-|-|-|-|
|—|Documento maestro (nombres, reglas y supuestos)|[docs/00-documento-maestro.md](docs/00-documento-maestro.md)|—|
|1|Visión y backlog ágil|[docs/01-vision-y-backlog.md](docs/01-vision-y-backlog.md)|—|
|2|Plan de sprints|[docs/02-plan-de-sprints.md](docs/02-plan-de-sprints.md)|—|
|3|Contexto y casos de uso|[docs/03-contexto-y-casos-de-uso.md](docs/03-contexto-y-casos-de-uso.md)|—|
|4|RNF y arquitectura|[docs/04-rnf-y-arquitectura.md](docs/04-rnf-y-arquitectura.md)|—|
|5|ADR|[ADR-001](docs/adr/ADR-001-proveedor-de-respaldo-acronis.md), [ADR-002](docs/adr/ADR-002-estilo-monolito-modular.md), [ADR-003](docs/adr/ADR-003-servidor-dedicado-para-el-sgr.md)|—|
|6|Diseño de clases y patrones|[docs/06-diseno-de-clases.md](docs/06-diseno-de-clases.md)|—|
|7|Estados y secuencia|[docs/07-estados-y-secuencia.md](docs/07-estados-y-secuencia.md)|—|
|8|Historias de usuario completas (4)|[HU-003](docs/historias/HU-003-definir-politica-de-respaldo.md), [HU-004](docs/historias/HU-004-ejecutar-respaldos-automaticos.md), [HU-011](docs/historias/HU-011-alerta-por-respaldo-fallido.md), [HU-014](docs/historias/HU-014-solicitar-restauracion.md)|—|
|9|Plan de testing y casos de prueba|[plan-de-testing](docs/testing/plan-de-testing.md), [casos-HU-003](docs/testing/casos-HU-003.md), [casos-HU-004](docs/testing/casos-HU-004.md), [casos-HU-011](docs/testing/casos-HU-011.md), [casos-HU-014](docs/testing/casos-HU-014.md)|—|
|10|Prototipo y evaluación heurística|[docs/10-prototipo-y-heuristicas.md](docs/10-prototipo-y-heuristicas.md), capturas en [`prototipo/`](prototipo/)|—|
|11|Proyecto en GitHub (Board y repo)|[Board](https://github.com/users/Julito-Art/projects/4)|—|
|—|Trabajo de patrones GoF (Singleton y Multiton)|[Patrones Creacionales: Singleton y Multiton (PDF)](<docs/Patrones Creacionales_ Singleton y Multiton (Modelacion) (2).pdf>)|—|

Los diagramas están en [`diagramas/`](diagramas/), cada uno como imagen `.png` con su fuente PlantUML `.puml`.

