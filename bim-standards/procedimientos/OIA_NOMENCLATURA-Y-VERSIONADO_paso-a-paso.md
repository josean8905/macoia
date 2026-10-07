---
documento: Nomenclatura, estructura de carpetas y versionado de contenedores de información (CDE)
codigo: OIA-PRO-BIM-NOM
version: 1.0.0
fecha: 2026-10-07
estado: Vigente
norma: ISO 19650-1/-2 (adaptada a licencias de construcción en Colombia)
cde: Google Drive · G:\Mi unidad\00. Macoia\03_PROYECTOS
autor: Macoia S.A.S. (OIA)
---

# Nomenclatura y versionado OIA — paso a paso

Esta guía define cómo se nombran, dónde se guardan y cómo se versionan todos los archivos de un proyecto de licencia. Aplica a modelos, planos, imágenes, documentos legales, presupuestos e informes.

> Regla de oro: **el nombre dice QUÉ es el archivo; la carpeta dice en QUÉ ESTADO está; el registro dice en QUÉ REVISIÓN va.**

---

## 1. Estructura de carpetas de cada proyecto

```
XXX01_Nombre/
├── 00. ENTRADA/          ← bandeja: aquí llega todo sin clasificar
├── 0. INSUMOS/           ← lo que entrega el cliente o terceros (no se modifica)
│   ├── 01. LEGAL/        (tradición, escritura, predial, cédula/RUT, poder, licencias anteriores)
│   ├── 02. TOPOGRAFIA/
│   ├── 03. ESTUDIO_SUELOS/
│   └── 04. FOTOS_PREDIO/
├── 1. WIP/               ← trabajo en curso (estado S0)
│   ├── 0. DWG/  01. RVT/  02. IMG/  03. IFC/  04. CALC/
│   └── XXX01_DATOS_COMPLETAR.xlsx
├── 2. SHARED/            ← compartido para revisión (S1–S4)
├── 3. PUBLISHED/         ← expediente para radicar (A1, B1)
│   ├── 01. FUN/  02. DOC_LEGALES/  03. PLANOS_ARQ/
│   └── 04. ESTRUCTURAL/  05. GEOTECNIA/  06. VALLA_PAGOS/
├── 4. ARCHIVED/          ← versiones reemplazadas y radicadas (nunca se borran)
├── 5. CURADURIA/         ← radicado, actas, oficios, resolución
└── XXX01-OIA-ZZ-XX-REG-BIM-REGISTRO_DOCUMENTOS.xlsx
```

Cada carpeta tiene un `LEEME.txt` que explica qué va en ella.

---

## 2. Nombre del archivo

```
PROYECTO - ORIGINADOR - VOLUMEN - NIVEL - TIPO - FUNCIÓN - DESCRIPCIÓN . ext
  PRF01  -    OIA     -   ZZ    -  01   - PLN  -   ARQ   - PLANTA_PISO1 .pdf
```

| Campo | Regla | Ejemplos |
|---|---|---|
| **Proyecto** | 3 letras + 2 dígitos | `PRF01`, `CSC01`, `ESL01` |
| **Originador** | Quien produce el archivo. Macoia = `OIA` | `OIA` |
| **Volumen** | Parte del proyecto. `ZZ` = todo | `ZZ`, `T1` (torre 1) |
| **Nivel** | `XX` no aplica · `00` cimentación · `01`, `02` pisos · `RF` cubierta | `XX`, `01`, `RF` |
| **Tipo** | Qué es (3 letras) — **va antes de la función** | ver tabla 2.1 |
| **Función** | Disciplina o rol (3 letras) | ver tabla 2.2 |
| **Descripción** | MAYÚSCULAS, palabras separadas con `_`, sin tildes problemáticas | `CASA_SAN_CARLOS`, `LICENCIA_0808_2014` |

### 2.1 Códigos de TIPO

| Código | Significado | Código | Significado |
|---|---|---|---|
| `MOD` | Modelo 3D | `RES` | Resolución / acto administrativo |
| `PLN` | Plano | `ESC` | Escritura |
| `IMG` | Imagen / gráfico | `CTL` | Certificado de tradición y libertad |
| `ESQ` | Esquema / diagrama | `FUN` | Formulario Único Nacional |
| `INF` | Informe | `ACT` | Acta |
| `MEM` | Memoria de cálculo | `OFI` | Oficio / carta |
| `PRE` | Presupuesto | `REG` | Registro / listado |
| `OFE` | Oferta / cotización | `BEP` | Plan de ejecución BIM |
| `APU` | Análisis de precios unitarios | `FOT` | Fotografía |
| `EST` | Estudio (suelos, topografía…) | | |

### 2.2 Códigos de FUNCIÓN

| Código | Significado | Código | Significado |
|---|---|---|---|
| `ARQ` | Arquitectura | `ELE` | Eléctrico |
| `EST` | Estructura | `COS` | Costos y presupuesto |
| `GEO` | Geotecnia | `LEG` | Legal / jurídico |
| `TOP` | Topografía | `BIM` | Gestión de la información |
| `HID` | Hidrosanitario | `CLI` | Cliente / propietario |
| | | `CUR` | Curaduría / autoridad |

### 2.3 Ejemplos

| Archivo | Nombre |
|---|---|
| Modelo Revit | `PRF01-OIA-ZZ-XX-MOD-ARQ-CASA_PROFE.rvt` |
| Exportación IFC | `PRF01-OIA-ZZ-XX-MOD-ARQ-CASA_PROFE.ifc` |
| Planta piso 1 en DWG | `CSC01-OIA-ZZ-01-PLN-ARQ-PLANTA_PISO1.dwg` |
| Imagen de localización | `PRF01-OIA-ZZ-XX-IMG-ARQ-U05_LOCALIZACION_BN.png` |
| Resolución de licencia anterior | `ESL-OIA-ZZ-XX-RES-LEG-LICENCIA_0808_2014.pdf` |
| Certificado de tradición | `ESL-OIA-ZZ-XX-CTL-LEG-MATRICULA_470-70588.pdf` |
| Presupuesto | `CPO01-OIA-ZZ-XX-PRE-COS-PRESUPUESTO.xlsx` |
| Registro de documentos | `CSC01-OIA-ZZ-XX-REG-BIM-REGISTRO_DOCUMENTOS.xlsx` |

> Los documentos de terceros (escrituras, resoluciones, estudios) también llevan originador `OIA` porque Macoia es quien los registra en su CDE. El autor real se anota en la columna *Observaciones* del registro.

---

## 3. Estados y revisiones

| Estado | Significado | Carpeta | Revisión | Sufijo en el nombre |
|---|---|---|---|---|
| `S0` | Trabajo en curso | `1. WIP` | `P01.01`, `P01.02`… (solo en el registro) | **ninguno** |
| `S1` | Compartido para coordinación | `2. SHARED` | `P01`, `P02`… | `_S1_P01` |
| `S2` | Compartido para información | `2. SHARED` | `P01`… | `_S2_P01` |
| `S3` | Compartido para revisión y comentarios | `2. SHARED` | `P01`… | `_S3_P01` |
| `S4` | Compartido para aprobación | `2. SHARED` | `P01`… | `_S4_P01` |
| `A1` | Publicado — aprobado para radicar | `3. PUBLISHED` | `C01`, `C02`… | `_A1_C01` |
| `B1` | Publicado con observaciones (acta) | `3. PUBLISHED` | `C02`… | `_B1_C02` |
| `CR` | Registro final (licencia expedida) | `3. PUBLISHED` → `4. ARCHIVED` | `C0n` | `_CR_C0n` |

### Reglas
1. **En WIP el archivo no cambia de nombre.** Se guarda siempre encima; Google Drive conserva el historial de versiones (clic derecho › *Gestionar versiones*). Nada de `-v0.1`, `-V1`, `_final`, `_final2`.
2. **Cada vez que algo sale de WIP sube la revisión** y se agrega el sufijo de estado y revisión al nombre.
3. **La versión anterior se mueve a `4. ARCHIVED`**, nunca se borra.
4. **Toda entrada o cambio de estado se anota en el registro de documentos** (fecha, estado, revisión, observaciones).
5. **Los respaldos automáticos de Revit (`.0001`, `.0002`…) no son versiones.** Configurar en Revit *Opciones de guardado › Número máximo de copias de seguridad = 3*.

### Relación con las revisiones de planchas en Revit
La revisión **N.º n** de Revit (ver `OIA-PRO-ARQ-REV`) corresponde a la revisión **C0n** del CDE:

| Revit | CDE |
|---|---|
| 1 · RADICACIÓN EN LEGAL Y DEBIDA FORMA | `_A1_C01` |
| 2 · RESPUESTA ACTA DE OBSERVACIONES N.º 1 | `_B1_C02` |
| n · PLANOS APROBADOS – RES. XXXX DE AAAA | `_CR_C0n` |

---

## 4. Flujo de trabajo con la bandeja de entrada

1. Subir los documentos nuevos a `00. ENTRADA` del proyecto, con el nombre que traigan.
2. Pedir a Claude: *"organiza la entrada de XXX01"*.
3. Claude lee cada documento, le asigna tipo, función y descripción, lo renombra, lo mueve a su carpeta y lo agrega al registro.
4. La bandeja queda vacía (solo el `LEEME.txt`).

Para preguntas puntuales sobre un documento se puede adjuntar en el chat, pero **el archivo oficial siempre vive en Drive**.

---

## 5. Qué NO hacer

- ❌ Poner la versión en el nombre de un archivo de WIP (`-v0.1`, `-V1`, `_final`).
- ❌ Guardar respaldos de Revit (`.00NN.rvt`) como si fueran versiones.
- ❌ Borrar una versión anterior: se mueve a `4. ARCHIVED`.
- ❌ Usar el código de otro proyecto (ej. archivos `CSC01-…` dentro de BCS01).
- ❌ Dejar archivos sueltos en la raíz del proyecto o en `1. WIP` sin subcarpeta.
- ❌ Renombrar un `.rvt` desde el explorador mientras está abierto en Revit: usar *Guardar como*.

---

## Control de versiones

SemVer (`MAYOR.MENOR.PARCHE`): **MAYOR** cambia el formato del nombre o los códigos de estado; **MENOR** agrega códigos o carpetas; **PARCHE** corrige texto. Commit: `docs(nomenclatura): vX.Y.Z – <resumen>`.

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0.0 | 2026-10-07 | Versión inicial: orden TIPO antes de FUNCIÓN, estructura de carpetas 00–5, bandeja de entrada, estados S0–CR, revisiones P/C y registro de documentos. |
