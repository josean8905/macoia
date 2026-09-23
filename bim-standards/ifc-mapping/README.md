# Tablas de mapeo IFC4 / ISO 19650

- **`IFC+SG Property Sets.txt`** — definición de los Property Sets IFC-SG (`SGPset_*`) por clase IFC, con el tipo de dato de cada propiedad. Base para lo que se audita en tiempo real contra POT/NSR-10.
- **`IFC-SG Property Mapping Export.txt`** — exportación del mapeo de clase/tipo IFC a Property Set (formato de exportación de Revit *File → Import/Export Settings → IFC Options*), usada para configurar el exportador nativo de IFC de Revit con el esquema IFC-SG/CORENET-X.

Estos archivos son jurisdiction-neutral (heredados del estándar CORENET X de Singapur) y son la base sobre la que se valida el cumplimiento normativo local (POT Yopal / NSR-10) — ver los reportes de validación específicos de cada proyecto en `projects/<proyecto>/reportes-validacion/`.
