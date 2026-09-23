# Macoia S.A.S.

**Macoia** (sigla originadora: **OIA**) es una empresa ConTech enfocada en la transformación digital del sector de la edificación e infraestructura en Colombia. Su estrategia de entrada al mercado ("Caballo de Troya") es ofrecer gestión y tramitación exprés de licencias de construcción, apalancándose en metodología BIM (ISO 19650), un Entorno Común de Datos (CDE) estricto y automatizaciones paramétricas en Autodesk Revit / OpenBIM (IFC4) para auditar en tiempo real el cumplimiento del POT local y la NSR-10.

El objetivo de fondo: erradicar reprocesos, garantizar expedientes con cero rechazos ante las curadurías, y capturar la base de datos maestra del activo desde su origen.

Punto de entrada actual: presentaciones a curadurías en **Yopal (Casanare)**, construidas sobre una plantilla arquitectónica que se está diseñando desde el inicio para ser jurisdiction-neutral y escalar a presentaciones en **CORENET X** (Singapur) más adelante.

Emprendedor / Gerente de Grupo Macoya: **José Antonio Bermúdez Montañez** — Ingeniero Civil, Especialista en gerencia empresarial, Especialista en modelado y coordinación de obras civiles bajo metodología BIM.

## Estructura del repositorio

```
macoia/
├── assets/            Recursos visuales globales (logo, guía de marca)
├── marketing/          Estrategia comercial: landing page, piezas, manuales de envío
├── scripts/            Automatizaciones (pyRevit, Dynamo)
│   ├── pyrevit/        Extensiones y rutinas personalizadas de pyRevit
│   └── dyp/             Scripts de Dynamo para validación
├── bim-standards/      Estándares, normativas y parámetros compartidos
│   ├── shared-parameters/  Archivos de parámetros compartidos IFC-SG / CORENET-X
│   ├── ifc-mapping/         Tablas de mapeo IFC4 / ISO 19650
│   └── templates-bep/       Guías y plantillas del Plan de Ejecución BIM (BEP)
└── projects/            Documentación y reportes de proyectos específicos
    └── csc01-casa-sancarlos/   Proyecto piloto usado para construir la plantilla maestra
```

## Estado actual (ver detalle en cada carpeta)

- La plantilla maestra de arquitectura se está construyendo inyectando el sistema de parámetros IFC-SG / CORENET-X en la plantilla existente del estudio (`OIA-TEM-ARQ_2026-V1.rte`), no desde un archivo nuevo.
- Orden de trabajo decidido: primero el parámetro `Mark` (esquema letra+número por categoría), después una convención de nombres de Familia/Tipo neutral de jurisdicción.
- El proyecto **CSC01 Casa San Carlos** es el piloto base para validar la plantilla y el mecanismo de auditoría de modelos de terceros.
