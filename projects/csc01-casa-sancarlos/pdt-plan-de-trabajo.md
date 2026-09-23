# Plan de trabajo — CSC01 Casa San Carlos

**Proyecto:** CSC01 Casa San Carlos (`CSC01-OIA-ZZ-XX-ARQ-MOD-CASA_SAN_CARLOS-v0.1.rvt`)
**Tipo de cliente:** constructora/desarrolladora (no persona natural).
**Rol del proyecto:** piloto base para construir la plantilla maestra de licencias de construcción de vivienda unifamiliar y para validar el mecanismo de auditoría de modelos de terceros.

## Objetivo

Macoia necesita dos entregables distintos a partir de este mismo trabajo:

1. **Plantilla maestra** — para cuando Macoia gestiona licencias de construcción propias desde cero.
2. **Mecanismo/herramientas de auditoría** — para cuando llegan modelos de otros arquitectos o consultores (terceros) y hay que validar su cumplimiento antes de radicar.

## Enfoque de construcción

La plantilla maestra se está construyendo **inyectando** el sistema de parámetros IFC-SG/CORENET-X (ver `bim-standards/shared-parameters/` y `bim-standards/ifc-mapping/`) en la plantilla ya existente del estudio, `OIA-TEM-ARQ_2026-V1.rte` (ubicada en `G:\Mi unidad\00. Macoia\02_COM\MAESTROS\BIM\WIP\Plantillas_RVT`), en vez de crear un archivo nuevo desde cero.

## Orden de trabajo decidido

1. **Parámetro `Mark`** — esquema letra+número (W, C, B, F, S por categoría) por elemento. *(en curso, ver estado abajo)*
2. **Convención de nombres de Familia/Tipo** — profesional y neutral de jurisdicción, para la biblioteca. *(pendiente, arranca después del punto 1)*

## Estado actual

- El script `Populate_IFC_SG.py`, actualizado con la asignación de `Mark`, **no logró aún el objetivo buscado**.
- Trabajo pausado en este punto por decisión explícita, para reorganizar los flujos de trabajo y tener claridad sobre los próximos pasos antes de seguir iterando el script.

## Contexto normativo relevante

- En Yopal la curaduría **aún no maneja formatos digitales como IFC**; la radicación de licencias de construcción se hace en planos físicos. Esto no cambia el valor de construir la plantilla con datos IFC desde el origen — sigue siendo la base para capturar la base de datos maestra del activo y para escalar a jurisdicciones donde sí se radica digital (CORENET X).
- Ver `reportes-validacion/diagnostico-ifc-predefinedtype-53elementos.md` para el estado detallado de la auditoría de exportación IFC de este mismo modelo (parámetros `IFC Predefined Type` / `Export to IFC`).

## Próximos pasos

1. Retomar el punto 1 (`Mark`) con un enfoque distinto al de `Populate_IFC_SG.py`, dado que no logró el objetivo.
2. No avanzar a la convención de nombres de Familia/Tipo hasta cerrar el punto 1.
3. Resolver las decisiones pendientes acumuladas en el diagnóstico de exportación IFC (ver reporte de validación) antes de considerar cualquier IFC de este modelo como representativo del estado real de cumplimiento.
