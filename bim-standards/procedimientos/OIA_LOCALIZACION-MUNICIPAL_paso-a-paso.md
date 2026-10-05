---
documento: Paso a paso para el recuadro "Localización municipal" (plancha de portada y localización)
codigo: OIA-PRO-ARQ-LOC
version: 1.0.0
fecha: 2026-10-05
estado: Vigente
herramientas: Affinity 3.3 (SDK JavaScript vía MCP) · Revit 2027 (rvt-mcp)
proyecto_piloto: PRF01 Casa Ofelia — plancha A-000, recuadro 3
autor: Macoia S.A.S. (OIA)
---

# Localización municipal — paso a paso

Este procedimiento explica cómo armar el **recuadro 3 · Localización municipal** del esquema de localización (1 Nacional → 2 Departamental → 3 Municipal → 4 Específica → 5 Implantación). El recuadro debe permitir que la curaduría ubique **de un vistazo** la zona del POT donde está el predio y, dentro de un círculo de referencia, **la manzana con sus calles y nomenclatura**.

> Regla de oro: **el mapa completo del municipio, limpio; el detalle va solo dentro de la lupa.** Nada de vías, lotes ni curvas de nivel fuera del círculo.

---

## 1. Resultado esperado

| Elemento | Cómo se ve | Por qué |
|---|---|---|
| **Mapa** | Todo el casco urbano, solo límites de zonas de tratamiento, números de zona, perímetro urbano y río. | Lectura rápida; sin ruido gráfico. |
| **Zona del predio** | Rellena con el color de su tratamiento en el POT (ej. zona 35, *Consolidación – Densificación moderada*, RGB 224·133·124). | Es la única zona con color. |
| **Demás zonas** | Sin color (blanco), solo contorno. | Contraste con la zona del predio. |
| **Lupa** | Círculo azul (borde RGB 30·110·230, 6 pt) de ≈17 mm en la plancha, centrado en la manzana. Dentro: ampliación ≈2,6× con manzanas, calles y nomenclatura. | Muestra el entorno inmediato con rótulos legibles. |
| **Manzana del predio** | Rojo sólido (RGB 215·25·28) dentro de la lupa. | Se referencia la **manzana completa**, no el lote. |
| **Efectos 3D** | Ninguno (extrusiones, biseles y sombras fueron descartados). | Decisión de diseño 2026-10-05. |

Ejemplo aprobado: `PRF01-OIA-ZZ-XX-ARQ-IMG-POT_Yopal_LocMunicipal_v5_lupa.png`.

---

## 2. Insumos

| Insumo | Fuente | Nota |
|---|---|---|
| Plano POT de tratamientos urbanísticos (PDF vectorial) | `02_COM\MAESTROS\U05_TRATAMIENTOSURBANISTICOS 1.pdf` (Yopal) | Se abre en Affinity; capas por grupo (Paramento, Vías, Sector_Tratamientos, Labels…). |
| Zona de tratamiento del predio | Certificado de uso del suelo / POT | Número y color de la leyenda. |
| Manzana y lote | Plano predial / boletín catastral | Identificar la manzana (ej. Bloque N) y las calles que la rodean. |
| Nomenclatura de las vías | Rótulos del POT (capa `MallaVialPerimetral Anno`) y boletín de nomenclatura | Confirmar con el certificado de nomenclatura. |
| Plancha destino | Revit, A-000 `PORTADA Y LOCALIZACIÓN GENERAL`, recuadro 3 | Marco ≈17,4 × 17,7 cm. |

---

## 3. Paso a paso en Affinity

> Todo se hace sobre el PDF del POT abierto en Affinity. **No se borra nada del original:** las capas se ocultan y lo nuevo va en capas con prefijo `R3`. El PDF no se sobrescribe.

### Paso 1 · Dejar el mapa limpio
Ocultar (visibilidad, no borrar) dentro del contenedor `Layers`:
- `Image` (teselas ráster con los colores de todas las zonas)
- `Paramento` (manzanas/lotes), `Vias Municipales`, `Curvas de Nivel`, `Pista Aeropuerto`
- `MallaVialPerimetral Anno` (nombres de calles), `Measured Grid`, `Other` (marco)
- En `Labels`: `Curvas de Nivel - Default`, `Drenaje Doble - Default`, `Toponimia - Default`

Ocultar también los contenedores superiores del rótulo del POT (`Other 2`, `Other 6`, `CASANARE`, `YOPAL`, `COLOMBIA`) y cualquier grupo de trabajo anterior del predio.

Quedan visibles: `Perimetro`, `Sector_Tratamientos` y `Labels › Sector_Tratamientos - Default` (números de zona).

> Comando SDK: `doc.executeCommand(DocumentCommand.createSetVisibility(Selection.create(doc, nodos), false))`.

### Paso 2 · Rellenar la zona del predio
Los colores de zona del PDF son **ráster** (teselas en `Image`); los contornos de `Sector_Tratamientos` son segmentos sueltos, no polígonos. Por eso:
1. Obtener el polígono de la zona como **trazo vectorial** (calcado sobre el ráster o reutilizando una máscara existente).
2. Crear un `PolyCurveNode` con relleno sólido del color de la leyenda y **sin borde**.
3. Insertarlo **detrás** de `Sector_Tratamientos` para que los contornos y el número de zona queden encima:
   `builder.setInsertionTargetSelection(Selection.create(doc, sectorTratamientos)); builder.setInsertionMode(InsertionMode.Behind)`.
4. Nombre: `R3 Zona NN relleno`.

### Paso 3 · Ubicar la manzana del predio
1. Buscar en `Paramento` el polígono que contiene el lote (comparar `getSpreadBaseBox(true)` con la posición del lote).
2. Su centro es el **centro de la lupa** `C`.
3. Identificar las calles que la rodean (rótulos de `MallaVialPerimetral Anno` dentro de un radio de ~450 u alrededor de `C`).

### Paso 4 · Construir la lupa
Contenedor superior `R3 LOCALIZACION MUNICIPAL`, de atrás hacia adelante:

| Capa | Geometría | Estilo |
|---|---|---|
| `Lupa (fondo calles, recorte)` | Círculo centro `C`, radio **R = 500 u** (≈17 mm en la plancha) | Relleno RGB 205·222·247 (azul claro = calles), sin borde. **Es el padre de recorte**: todo lo que va adentro se corta al círculo. |
| ↳ `Lupa manzana` (×n) | Manzanas de `Paramento` cuyo contorno queda a menos de `R/k` de `C`, ampliadas con `Transform.createScale(k,k).around(C.x, C.y)`, **k = 2,6** | Relleno RGB 232·160·152, borde RGB 110·70·65 a 1,2 pt. |
| ↳ `Lupa Manzana Bloque N` | La manzana del predio, misma transformación | Relleno RGB 215·25·28, borde RGB 120·0·0. |
| ↳ `Lupa rotulo …` | Rótulos de calle duplicados (ver Paso 5) | Texto negro con contorno blanco (efecto *Outline*, radio 7). |
| `Circulo borde` | Mismo círculo | Sin relleno, borde RGB 30·110·230 a 6 pt. |

Geometría de `Paramento`: las coordenadas de `polyCurve` son **locales**; pasar a coordenadas del pliego antes de ampliar:
```js
const pc = nodo.polyCurve.clone();
pc.transform(nodo.baseToSpreadTransform); // local → pliego
pc.transform(Transform.createScale(k, k).around(C[0], C[1])); // ampliación de la lupa
```

### Paso 5 · Nomenclatura dentro de la lupa
1. Para cada calle que bordea la manzana (ej. `Cl 45`, `Cl 46`, `Kr 6`, `Kr 7`), elegir un rótulo existente del POT con el mismo texto y orientación.
2. Calcular el punto destino en la lupa: `T = C + k·(P − C)`, donde `P` es un punto sobre el eje de la calle junto a la manzana.
3. Duplicar el rótulo trasladándolo a `T` (`DocumentCommand.createTransform(sel, Transform.createTranslate(dx,dy), {duplicateNodes:true})`).
4. Escalarlo alrededor de `T` con factor **4,7** (texto ≈1,3 mm en la plancha).
5. Moverlo dentro del círculo de la lupa (`createMoveNodes(..., lupa, NodeMoveType.Inside, ...)`).
6. Agregar contorno blanco (`OutlineLayerEffect`, radio 7) para que se lea sobre las manzanas.

### Paso 6 · Exportar la imagen
1. Crear un rectángulo blanco `R3 Fondo/area exportacion` al fondo de `Layers`, con márgenes de 150 u alrededor del contenido visible (en Yopal: 145,159 → 11427,11997).
2. Seleccionarlo y exportar con `FileExportArea.createForSelectionArea()` a **PNG 2400 × 2518 px** (proporción del rectángulo).
3. El SDK de Affinity solo escribe en el **Escritorio**: exportar ahí y mover la imagen con Revit (Paso 7).
4. Nombre: `{Proyecto}-OIA-ZZ-XX-ARQ-IMG-POT_{Municipio}_LocMunicipal_vN.png`.

---

## 4. Paso a paso en Revit

### Paso 7 · Reemplazar la imagen del recuadro 3
1. Copiar el PNG del Escritorio a `1. WIP\02. IMG\` del proyecto.
2. En A-000 leer la caja de la imagen actual del recuadro 3 (centro y alto).
3. `ImageType.Create(doc, new ImageTypeOptions(ruta, false, ImageTypeSource.Import))` → imagen **incrustada**.
4. `ImageInstance.Create(...)` en el mismo centro (`BoxPlacement.Center`), `Height` igual al anterior, recentrar.
5. Borrar la instancia anterior. Todo en **una transacción** (se deshace con Ctrl+Z).
6. Verificar exportando la plancha a PNG y revisando el recuadro.

> `ImageInstance.ChangeTypeId` **no** sirve para cambiar de imagen (error "type typeId is not valid"); hay que crear la instancia nueva y borrar la vieja.

### Paso 8 · Rótulo del recuadro
Debajo del marco: `3. LOCALIZACIÓN MUNICIPAL` (Arial 3 mm negrita) y `{Municipio}, {Departamento} · Sin escala` (Arial 2,4 mm). La lupa es una **ampliación sin escala**.

---

## 5. Qué NO hacer

- ❌ Mostrar vías, lotes o nomenclatura a escala real en todo el mapa: a esta escala los rótulos del POT quedan de ≈0,3 mm (ilegibles).
- ❌ Marcar solo el lote: en el municipal se referencia la **manzana**.
- ❌ Dejar el círculo azul semitransparente encima de las calles de la lupa: tapa la nomenclatura.
- ❌ Aplicar efectos 3D (extrusiones, biseles, sombras): fueron descartados.
- ❌ Borrar capas del PDF del POT o guardarlo encima del original.
- ❌ Usar `ChangeTypeId` en la imagen de Revit (ver Paso 7).

---

## 6. Parámetros de referencia (PRF01, Yopal)

| Parámetro | Valor |
|---|---|
| Unidades del pliego Affinity | px a 400 dpi (≈0,0149 mm de plancha por unidad tras exportar) |
| Centro de la lupa `C` | Centro de la manzana Bloque N (4493, 8737) |
| Radio de la lupa `R` | 500 u ≈ 17 mm en la plancha |
| Ampliación `k` | 2,6 (radio de origen ≈192 u ≈ una manzana a cada lado) |
| Escala de rótulos | 4,7 sobre el rótulo original del POT |
| PNG | 2400 × 2518 px |
| Caja en A-000 | (0,0567; 0,356) – (0,2253; 0,5329) m |

---

## 7. Pendientes conocidos

- Si la curaduría exige texto ≥ 2 mm, agrandar la lupa a ≈25 mm (R ≈ 750 u) manteniendo k.
- Confirmar el nombre de la vía al occidente de la manzana (`Kr 6`) con el certificado de nomenclatura; el POT no la rotula en ese tramo.
- Guardar el script de Affinity en la biblioteca de scripts y parametrizarlo (zona, manzana, k, R) para reutilizarlo en otros municipios.

---

## Control de versiones

SemVer (`MAYOR.MENOR.PARCHE`):
- **MAYOR:** cambia el criterio gráfico del recuadro (qué se muestra o cómo se referencia el predio).
- **MENOR:** se agrega un paso, un caso o una automatización.
- **PARCHE:** correcciones de texto o de parámetros.

Commit: `docs(localizacion): vX.Y.Z – <resumen>`.

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0.0 | 2026-10-05 | Versión inicial: mapa POT limpio, zona del predio con color, lupa ampliada con manzanas, calles y nomenclatura, manzana en rojo sólido; flujo Affinity → Revit. |
