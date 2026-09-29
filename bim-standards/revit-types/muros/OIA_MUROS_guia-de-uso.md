---
documento: Guía de uso de tipos de muro OIA_MUR
codigo: OIA-STD-ARQ-MUR
version: 1.0.0
fecha: 2026-09-29
estado: Vigente
plantilla: OIA-TEM-ARQ_2026-V1.rte
base_de_datos: OIA_BD_Auditoria_APU_Casanare_2026-1.xlsx (v0.2)
fuente_precios: Gobernación de Casanare — Listado de precios de referencia 2026-1 (20/04/26)
autor: Macoia S.A.S. (OIA)
---

# Tipos de muro OIA_MUR — intención y uso en Revit

Esta guía explica **para qué existe cada tipo de muro** de la plantilla `OIA-TEM-ARQ_2026-V1.rte` y **cómo colocarlo** en un modelo para que el mismo archivo sirva a la vez para:

1. **Radicar ante la curaduría** (planos físicos 1:50, cuadro de áreas y aislamientos correctos).
2. **Presupuestar** con los APU de la Gobernación de Casanare (cada capa tiene un código APU).
3. **Exportar a IFC-SG / CORENET X** (entidad, tipo predefinido y propiedades del muro).

> Regla de oro: **se modela solo lo que cambia cotas o paramento** (mampostería + pañetes). Pintura, estuco y enchape **no son capas**: se cuantifican por área de cara con el código APU que ya trae el tipo.

---

## 1. Resumen de tipos

| ID | Nombre en Revit | Intención | Espesor | Función | IFC |
|---|---|---|---|---|---|
| MUR-01 | `OIA_MUR-01 EXT Pañetado · Bloque No.5 12 · e=15.5` | Fachada estándar pañetada y pintada | 0.155 | Exterior | IfcWall · STANDARD |
| MUR-02 | `OIA_MUR-02 EXT Ladrillo a la vista · Tolete 12 · e=13.5` | Fachada en ladrillo a la vista | 0.135 | Exterior | IfcWall · STANDARD |
| MUR-03 | `OIA_MUR-03 INT Divisorio · Bloque No.5 12 · e=15.0` | Divisorio interior estándar | 0.150 | Interior | IfcWall · PARTITIONING |
| MUR-04 | `OIA_MUR-04 INT Divisorio liviano · Bloque No.4 9 · e=12.0` | Divisorio delgado sin función estructural | 0.120 | Interior | IfcWall · PARTITIONING |
| MUR-05 | `OIA_MUR-05 INT Zona húmeda · Bloque No.5 12 · e=15.0` | Baños, cocina, zona de ropas | 0.150 | Interior | IfcWall · PARTITIONING |
| MUR-06 | `OIA_MUR-06 MED Medianero · Bloque No.5 12 · e=15.5` | Muro sobre lindero / culata | 0.155 | Exterior | IfcWall · STANDARD |

Todos: **envolver en inserciones = ambos**, **envolver en extremos = exterior**, núcleo = capa de mampostería (función *Estructura [1]*).

---

## 2. Intención de cada tipo

### MUR-01 · Fachada pañetada (el tipo por defecto de fachada)
- **Para qué:** cualquier muro que da a la calle, al antejardín, al patio o al aislamiento posterior y que se termina con pañete y pintura. Es la fachada más común en vivienda de Yopal.
- **Capas (exterior → interior):** pañete impermeabilizado 1:3 · 2.0 cm (APU 4923) · bloque No.5 arcilla 33×12×23 · 12 cm (APU 6456) · pañete liso 1:4 · 1.5 cm (APU 279).
- **Acabados (no modelados):** exterior pintura koraza 2 manos (APU 6372) · interior estuco y vinilo 3 manos (APU 5947).
- **Por qué el pañete exterior es impermeabilizado:** es la cara expuesta a lluvia; la regla R18 exige cara exterior impermeable.
- **No usar para:** muros sobre lindero (usar MUR-06) ni fachadas en ladrillo a la vista (usar MUR-02).

### MUR-02 · Fachada en ladrillo a la vista
- **Para qué:** fachadas donde la mampostería queda expuesta como acabado.
- **Capas:** ladrillo tolete común 12×24.5×6 · 12 cm (APU 89) · pañete liso interior 1.5 cm (APU 279).
- **Acabados:** exterior barniz transparente (APU 6410) · interior estuco y vinilo (APU 5947).
- **Ojo:** como no hay pañete exterior, **la cara exterior del tipo es el propio ladrillo**: con línea de ubicación en cara de acabado exterior, el paramento es la cara del ladrillo.

### MUR-03 · Divisorio interior estándar
- **Para qué:** separaciones entre espacios interiores (alcobas, sala, circulaciones).
- **Capas:** pañete liso 1.5 · bloque No.5 12 · pañete liso 1.5 (APU 279 + 6456 + 279).
- **Acabados:** estuco y vinilo en ambas caras (APU 5947).
- **Estructural:** depende del sistema del proyecto (ver §4.3).

### MUR-04 · Divisorio liviano
- **Para qué:** closets, cierres de ductos, antepechos interiores y divisiones que **nunca** tendrán función estructural.
- **Capas:** pañete liso 1.5 · bloque No.4 arcilla 33×9×23 · 9 cm (APU 6350) · pañete liso 1.5.
- **No usar:** como muro confinado en mampostería confinada (Título E); 9 cm no es el espesor del sistema.

### MUR-05 · Zona húmeda
- **Para qué:** muros de baños, cocina y zona de ropas.
- **Capas:** pañete **impermeabilizado** 1:4 · 1.5 cm del lado húmedo (APU 6007) · bloque No.5 12 · pañete liso 1.5 del lado seco.
- **Acabados:** lado húmedo enchape cerámico 30×45 (APU 6804); lado seco estuco y vinilo (APU 5947). El enchape **no es capa**: se cuantifica por área de cara enchapada.
- **Clave de uso:** la **cara Exterior del tipo** es la **cara húmeda**. Voltear el muro para que quede hacia el baño o la cocina.
- **Muro entre dos baños:** usar MUR-05 y aceptar pañete liso en una cara; el costo del impermeabilizado faltante es menor. Si se vuelve frecuente, se creará un MUR-07 con doble cara húmeda (nueva versión de esta guía).

### MUR-06 · Medianero
- **Para qué:** muros sobre el lindero con el predio vecino y culatas.
- **Capas:** pañete de culata impermeabilizado 1:6 · 2.0 cm (APU 5225) · bloque No.5 12 · pañete liso 1.5.
- **Clave de uso:** la **cara Exterior del tipo** mira **al predio vecino**. Marcar `IsPartyWall = Sí` en cada instancia.
- **Por qué es un tipo aparte:** CORENET X lo pide explícito (`SGPset_Wall.IsPartyWall`) y la curaduría revisa linderos y culatas.

---

## 3. Cómo elegir el tipo (árbol rápido)

```
¿El muro está sobre el lindero con el vecino?          → MUR-06
¿Da al exterior (calle, patio, antejardín, aislamiento)?
    ├── ¿Ladrillo a la vista?                          → MUR-02
    └── No                                             → MUR-01
¿Es interior?
    ├── ¿Una cara da a baño / cocina / ropas?          → MUR-05 (cara Exterior hacia lo húmedo)
    ├── ¿Closet, ducto o división sin carga, e ≤ 12?   → MUR-04
    └── Resto                                          → MUR-03
```

---

## 4. Cómo modelarlos en Revit

### 4.1 Línea de ubicación (paramento)
| Tipo | Línea de ubicación al dibujar | Por qué |
|---|---|---|
| MUR-01, MUR-02, MUR-06 | **Cara de acabado: exterior** | Aislamientos, antejardín y área construida se miden al paramento terminado. Se dibuja sobre la línea del paramento aprobado. |
| MUR-03, MUR-04, MUR-05 | **Eje del núcleo** | Las cotas interiores se amarran a ejes de mampostería. |

La línea de ubicación es de instancia: fijarla en la barra de opciones **antes** de dibujar. Regla de auditoría **R16**.

### 4.2 Orientación (flecha de volteo)
- Dibujando en sentido horario, la cara Exterior del tipo queda hacia afuera. Verificar siempre con la flecha de volteo (barra espaciadora para invertir).
- MUR-01/02: cara Exterior hacia afuera del edificio.
- MUR-05: cara Exterior hacia el baño/cocina.
- MUR-06: cara Exterior hacia el predio vecino.

### 4.3 Estructural o divisorio (`LoadBearing`)
Depende del **sistema estructural del proyecto**, no del tipo:

| Sistema del proyecto | Muros de mampostería | `LoadBearing` | Uso estructural Revit |
|---|---|---|---|
| Mampostería confinada (NSR-10 Título E, casas de 1–2 pisos) | Los muros confinados son el sistema resistente | **Sí** en muros confinados | Portante |
| Pórticos de concreto | La mampostería es divisoria | **No** | No portante |

Debe coincidir con el diseño estructural que se radica. Regla **R17** (por validar con el ingeniero estructural). MUR-04 siempre `LoadBearing = No`.

### 4.4 Alturas
- Restricción de base y superior **a niveles** (`00_CIMENTACION`, `01_PISO 01`, `02_CUBIERTA`…), sin desfases manuales salvo antepechos.
- En pórticos, el muro llega a la cara inferior de la viga (unir geometría muro–viga; la viga corta al muro).

### 4.5 Uniones con estructura
- Columnas y vigas de concreto (`OIA_COL-…`, `OIA_VA-…`) **cortan** al muro: *Unir geometría* con la columna/viga como elemento de corte. Así el m² de mampostería descuenta el concreto, como lo mide el APU.
- No modelar columnetas ni dovelas como parte del muro.

### 4.6 Vanos
- Puertas y ventanas se insertan normalmente; el pañete envuelve en ambas caras del vano (configurado en el tipo).
- El área del muro que entrega Revit ya descuenta vanos → es la cantidad para APU de mampostería y pañetes.

---

## 5. Parámetros que se llenan por instancia

La plantilla tiene `IsExternal`, `IsPartyWall` y `LoadBearing` como **parámetros de instancia** (CORENET X). Mientras no exista el botón pyRevit que los complete, se llenan así:

| Tipo | IsExternal | IsPartyWall | LoadBearing |
|---|---|---|---|
| MUR-01 | Sí | No | Según §4.3 |
| MUR-02 | Sí | No | Según §4.3 |
| MUR-03 | No | No | Según §4.3 |
| MUR-04 | No | No | No |
| MUR-05 | No | No | Según §4.3 |
| MUR-06 | Sí | **Sí** | Según §4.3 |

Parámetros de **tipo** ya cargados (no editar en el modelo): `OIA_APU_Codigo` (núcleo), `OIA_APU_Descripcion` (capas y acabados), `OIA_APU_UM` = m², `OIA_APU_ParametroCantidad` = Area, `OIA_APU_ValorUnitario` y `Costo` (costo compuesto COP/m²), `Export Type to IFC As` y `Type IFC Predefined Type`.

---

## 6. Cantidades y costo

| Tipo | Capas (APU) | Acabados (APU) | COP/m² de muro* |
|---|---|---|---|
| MUR-01 | 4923 + 6456 + 279 | 6372 / 5947 | 220.995 |
| MUR-02 | 89 + 279 | 6410 / 5947 | 208.731 |
| MUR-03 | 279 + 6456 + 279 | 5947 / 5947 | 228.960 |
| MUR-04 | 279 + 6350 + 279 | 5947 / 5947 | 224.367 |
| MUR-05 | 6007 + 6456 + 279 | 6804 / 5947 | 281.820 |
| MUR-06 | 5225 + 6456 + 279 | — / 5947 | 204.559 |

\* Listado 2026-1. Supone dos caras de igual área; no incluye filos, dilataciones ni enchape parcial. El valor vigente siempre es el de la base de datos (hoja `Muros_Estructura`).

- **Cantidad:** `Área` del muro (m², ya descuenta vanos) para mampostería y cada pañete.
- **Espesor nominal vs. APU:** los APU de pañete consumen más mortero que su espesor nominal (p. ej. 4923: 2.0 cm nominal, 2.4 cm equivalente) porque incluyen el relleno de irregularidades del bloque. Se modela el nominal; el costo sale del APU.
- **Acero de refuerzo:** no aplica al muro de arquitectura (grafil, dovelas y columnetas van en estructura).

---

## 7. Reglas de auditoría que aplican

| ID | Qué se verifica |
|---|---|
| R01–R03 | El tipo tiene código APU válido del listado vigente y la UM (m²) coincide con `Area`. |
| R06–R07 | Costo vigente y exportación IFC correcta. |
| R15 | Espesor del tipo = suma de capas de la base de datos (±1 mm). |
| R16 | Fachadas y medianeros con línea de ubicación en cara de acabado exterior. |
| R17 | `LoadBearing` coherente con el sistema estructural (por validar ingeniero). |
| R18 | Cara exterior impermeable en muros exteriores. |
| R19 | `IsPartyWall = Sí` en muros sobre lindero. |
| R20 | Cada capa con espesor tiene APU con UM m². |

Detalle completo en la hoja `Reglas_Auditoria` de la base de datos.

---

## 8. Qué NO hacer

- ❌ Modelar pintura, estuco o enchape como capa del muro.
- ❌ Crear tipos nuevos duplicando y cambiando espesores "a mano": toda variación entra por una nueva versión de esta guía y de la base de datos.
- ❌ Usar los tipos núcleo `OIA_MAM …` para modelar proyectos propios: existen solo para auditar modelos de terceros que modelan únicamente la mampostería.
- ❌ Dibujar fachadas con línea de ubicación en el eje (desplaza el paramento 7.75 cm y altera aislamientos y área construida).
- ❌ Usar MUR-04 como muro estructural.

---

## 9. Pendientes conocidos

- Confirmar con el POT de Yopal que el área construida se mide al paramento terminado (supuesto que justifica modelar pañetes).
- Botón pyRevit que complete `IsExternal`, `IsPartyWall` y `LoadBearing` desde el tipo y el sistema estructural.
- Valores numéricos de las reglas R11 y R12; validación de R17 con ingeniero estructural.

---

## Control de versiones

Esta guía se versiona con **SemVer** (`MAYOR.MENOR.PARCHE`):

- **MAYOR:** cambia la estructura de capas o el espesor de un tipo existente (afecta modelos ya hechos).
- **MENOR:** se agrega un tipo nuevo o una regla nueva.
- **PARCHE:** correcciones de texto, precios del listado o aclaraciones.

Cada cambio: se actualiza `version` y `fecha` en el encabezado, se agrega una fila abajo y se hace commit en GitHub con el mensaje `docs(muros): vX.Y.Z – <resumen>`.

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0.0 | 2026-09-29 | Versión inicial: MUR-01 a MUR-06 creados en `OIA-TEM-ARQ_2026-V1.rte`, base de datos v0.2, reglas R15–R20. |
