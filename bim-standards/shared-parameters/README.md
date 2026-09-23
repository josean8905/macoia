# Shared Parameters (IFC-SG / CORENET-X)

Archivos `.txt` de parámetros compartidos de Revit (formato nativo de Revit, prefijo `GMY`/`SG` heredado del estándar de Singapur — CORENET X), base para inyectar el sistema de parámetros IFC-SG/CORENET-X en la plantilla arquitectónica de Macoia (`OIA-TEM-ARQ_2026-V1.rte`).

- **`OIA_IFC+SG_SharedParameters_R2024.txt`** — archivo de parámetros compartidos ya preparado y con GUIDs propios de OIA/Macoia (revisión 2024). Es el archivo que se importa en Revit vía *Manage → Shared Parameters*.
- **`IFC Shared Parameters-RevitIFCBuiltIn_ALL.txt`** — listado completo de los parámetros integrados (built-in) de instancia del exportador nativo de IFC de Revit, exportado como referencia.
- **`IFC Shared Parameters-RevitIFCBuiltIn-Type_ALL.txt`** — el mismo listado a nivel de tipo (Type).

Estos dos últimos existen para poder cotejar, parámetro por parámetro, cuál viene nativo de Revit/Autodesk (`RevitIFCBuiltIn`) contra cuál hay que añadir vía `SGPset_*` (ver `bim-standards/ifc-mapping/`), y así evitar duplicar parámetros al inyectar el esquema en la plantilla.

## Orden de trabajo decidido

1. Parámetro `Mark` — esquema letra+número (W, C, B, F, S por categoría) por elemento. *(en curso — el script `Populate_IFC_SG.py` no logró aún el objetivo buscado, ver `projects/csc01-casa-sancarlos/pdt-plan-de-trabajo.md`)*.
2. Convención de nombres de Familia/Tipo, profesional y neutral de jurisdicción, para la biblioteca.
