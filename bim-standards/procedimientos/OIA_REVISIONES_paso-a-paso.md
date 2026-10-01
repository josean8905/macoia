---
documento: Paso a paso para revisiones de planchas (radicación y actas de observaciones)
codigo: OIA-PRO-ARQ-REV
version: 1.0.0
fecha: 2026-10-01
estado: Vigente
plantilla: OIA-TEM-ARQ_2026-V1.rte
rotulos: OIA_Rotulo_Pliego (100×70) · OIA_Rotulo_MedioPliego (70×50)
autor: Macoia S.A.S. (OIA)
---

# Revisiones de planchas en Revit — paso a paso

Este procedimiento explica cómo registrar en Revit cada **emisión** del juego de planos (radicación, respuestas a actas de observaciones, planos aprobados), para que la **tabla de revisiones del rótulo** se llene sola y quede trazado, plancha por plancha, qué se cambió y por qué.

> Regla de oro: **la tabla del rótulo nunca se escribe a mano.** Se alimenta de las revisiones del proyecto. Si una fila no aparece, falta asignar la revisión a esa plancha.

> Los nombres de menús van en español con el equivalente en inglés entre paréntesis, porque varían según el idioma instalado de Revit.

---

## 1. Conceptos

| Elemento | Qué es | Dónde vive |
|---|---|---|
| **Revisión** | Una emisión del juego de planos con número, fecha y descripción (ej. "Respuesta acta N.º 1"). | Lista de revisiones del proyecto. |
| **Nube de revisión** | Dibujo que encierra lo que cambió en una vista; queda ligada a una revisión. | En la vista (planta, corte, fachada…). |
| **Etiqueta de nube** | Muestra el número de la revisión junto a la nube. | En la vista. |
| **Tabla de revisiones** | Cuadro "Nº · Descripción · Fecha" del rótulo; lista solo las revisiones asignadas a esa plancha. | Dentro de la familia del rótulo (esquina inferior derecha del área de dibujo). |

Una plancha muestra una revisión cuando ocurre **cualquiera** de estas dos cosas:
1. Alguna vista colocada en la plancha tiene una nube de esa revisión.
2. La revisión está marcada a mano en la plancha (*Revisiones en plancha*).

---

## 2. Convención OIA de revisiones para curaduría

Numeración **por proyecto** (no por plancha) y en el mismo orden del trámite:

| N.º | Descripción (texto exacto) | Fecha | Emitida para | Cuándo |
|---|---|---|---|---|
| 1 | `RADICACIÓN EN LEGAL Y DEBIDA FORMA` | Fecha del radicado | Curaduría Urbana de Yopal | Juego completo entregado al radicar. |
| 2 | `RESPUESTA ACTA DE OBSERVACIONES N.º 1` | Fecha de entrega de correcciones | Curaduría Urbana de Yopal | Cuando la curaduría expide el acta y se corrigen planos. |
| 3 | `RESPUESTA ACTA DE OBSERVACIONES N.º 2` | … | … | Solo si hay segunda acta. |
| n | `PLANOS APROBADOS – RES. XXXX DE AAAA` | Fecha de la resolución | Titular | Cuando sale la licencia; es la versión que se archiva y se lleva a obra. |

- **Emitida por:** iniciales del coordinador BIM que revisa (ej. `JABM`).
- La descripción va en **mayúsculas** y sin abreviaturas distintas a "N.º" y "RES.", para que todas las planchas se lean igual.
- Una revisión cubre **una sola entrega**. No se reutiliza una revisión para dos entregas distintas.

---

## 3. Paso a paso

### Paso 1 · Configurar la numeración (una sola vez por proyecto)
1. **Vista › Composición de plano › Revisiones** (*View › Sheet Composition › Revisions*).
2. En **Numeración** (*Numbering*) elegir **Por proyecto** (*Per Project*).
3. En **Opciones de numeración** dejar la secuencia numérica empezando en **1**, sin prefijo.
4. Aceptar.

### Paso 2 · Crear la revisión de radicación
1. Abrir de nuevo **Revisiones** y pulsar **Añadir** (*Add*).
2. Llenar la fila:
   - **Fecha:** fecha del radicado.
   - **Descripción:** `RADICACIÓN EN LEGAL Y DEBIDA FORMA`.
   - **Emitida para:** `Curaduría Urbana de Yopal`.
   - **Emitida por:** iniciales del coordinador.
   - **Emitida:** sin marcar todavía.
   - **Mostrar:** *Nube y etiqueta*.
3. Aceptar.

### Paso 3 · Asignar la radicación a todas las planchas
La radicación no lleva nubes porque todo es nuevo; se marca directamente en cada plancha.
1. En el Navegador de proyectos, seleccionar **todas** las planchas A-000…A-401 (clic en la primera, Shift + clic en la última).
2. En Propiedades, junto a **Revisiones en plancha** (*Revisions on Sheet*), pulsar **Editar**.
3. Marcar la revisión **1** y aceptar.
4. Abrir cualquier plancha: la tabla del rótulo ya muestra `1 · RADICACIÓN… · fecha`.

### Paso 4 · Emitir la radicación
1. Verificar el juego completo (rótulo lleno, botón **Cuadro FUN** en CUMPLE, planchas revisadas).
2. En **Revisiones**, marcar **Emitida** (*Issued*) en la fila 1.
   - Al emitirla, Revit **bloquea** la revisión: ya no se pueden agregar ni mover nubes de esa revisión. Así la radicación queda congelada tal como se entregó.
3. Imprimir o exportar los PDF (ver Paso 9).

### Paso 5 · Llega un acta de observaciones: crear la nueva revisión
1. **Revisiones › Añadir.**
2. **Descripción:** `RESPUESTA ACTA DE OBSERVACIONES N.º 1`; **Fecha:** la de la entrega prevista; **Emitida para:** `Curaduría Urbana de Yopal`.
3. Aceptar. Las revisiones nuevas quedan **sin emitir** mientras se corrige.

### Paso 6 · Corregir y marcar cada cambio con una nube
Por cada observación del acta:
1. Hacer la corrección en el modelo.
2. Abrir la **vista** donde se ve el cambio (no la plancha).
3. **Anotar › Nube de revisión** (*Annotate › Revision Cloud*).
4. En Propiedades, antes de terminar el boceto, elegir **Revisión = 2 – RESPUESTA ACTA…**.
5. Dibujar la nube alrededor del cambio y terminar (✔).
6. **Anotar › Etiquetar por categoría** (*Tag by Category*) y hacer clic sobre la nube: aparece el número **2**.
7. Opcional pero recomendado: en el parámetro **Comentarios** de la nube anotar el número del ítem del acta (ej. `Acta 1 – ítem 4: aislamiento posterior`).

> Si la corrección es solo de texto del rótulo o del cuadro de áreas y no hay una vista que encerrar, asignar la revisión a mano en esa plancha (Paso 3, solo para esa plancha).

### Paso 7 · Verificar qué planchas cambiaron
1. Abrir cada plancha: las que tienen nubes de la revisión 2 muestran la fila `2 · RESPUESTA ACTA… · fecha` debajo de la fila 1.
2. Las planchas sin cambios **no** muestran la revisión 2. Es correcto: solo se reimprimen las que cambiaron, salvo que la curaduría pida el juego completo.
3. Para tener la lista completa, crear una **Lista de planos** (*Sheet List*) con los campos *Número de plano*, *Nombre de plano*, *Revisión actual* y *Fecha de revisión actual*.

### Paso 8 · Emitir la respuesta
1. Revisar que cada ítem del acta tenga su nube (o su asignación manual).
2. En **Revisiones**, marcar **Emitida** en la fila 2.
3. Si el acta pedía cambios visuales de entrega, ocultar las nubes en la próxima revisión cambiando **Mostrar** de la revisión 2 a **Ninguno** (la fila sigue en la tabla; solo se ocultan los dibujos de las nubes).

### Paso 9 · Exportar los PDF y llevarlos al CDE
1. **Archivo › Exportar › PDF**, seleccionando las planchas de la entrega.
2. Nombre de cada archivo: `{ISO_Codigo_Documento}_R{N.º revisión con 2 dígitos}.pdf`
   - Ejemplo: `PRF01-OIA-ZZ-XX-DR-A-0101_R02.pdf`
3. Guardar en el CDE:
   - Mientras se corrige → `01_WIP`.
   - Una vez emitida → `03_PUBLISHED` (el archivo ya no se edita; una corrección nueva es una revisión nueva).

### Paso 10 · Planos aprobados
Cuando la curaduría expide la resolución de licencia:
1. Crear la revisión `PLANOS APROBADOS – RES. XXXX DE AAAA` con la fecha de la resolución.
2. Asignarla a todas las planchas (Paso 3) y emitirla.
3. Exportar el juego completo a `03_PUBLISHED` y copiar el modelo a `04_ARCHIVED` como versión aprobada.

---

## 4. Ajuste del cuadro en el rótulo

- El cuadro está dentro de la familia del rótulo, alineado al borde inferior del marco y a la franja del rótulo (corregido el 2026-10-01 en ambos formatos).
- Hoy muestra muchas filas vacías y mide ≈16 cm de alto. Para que muestre **solo las revisiones que existan**:
  1. Abrir la plancha, seleccionar el rótulo y **Editar familia**.
  2. Seleccionar la tabla de revisiones y en sus Propiedades cambiar **Altura** (*Height*) a **Variable**.
  3. **Cargar en proyecto** con *Sobrescribir la versión existente*.
- Si se cambia la familia, actualizar también los `.rfa` en `02_COM\MAESTROS\BIM\WIP\Plantillas_RVT\Familias` y recargarlos en `OIA-TEM-ARQ_2026-V1.rte`.

---

## 5. Qué NO hacer

- ❌ Escribir filas a mano en la tabla del rótulo o tapar la tabla con texto.
- ❌ Dibujar la nube en la plancha en vez de en la vista: no queda ligada al modelo y se pierde si se mueve la vista a otra plancha.
- ❌ Desmarcar **Emitida** para "arreglar algo rápido" en una revisión ya entregada: se crea una revisión nueva.
- ❌ Borrar revisiones antiguas para limpiar la tabla: es la trazabilidad del expediente ante la curaduría.
- ❌ Numerar por plancha: con varias actas, las planchas quedan con números distintos para la misma entrega.

---

## 6. Pendientes conocidos

- Confirmar con la curaduría si en las respuestas a actas piden el juego completo o solo las planchas modificadas (cambia el Paso 7).
- Crear la *Lista de planos* con revisión actual dentro de la plantilla.
- Evaluar un botón pyRevit que exporte los PDF con el nombre `{ISO_Codigo_Documento}_RNN.pdf` directo a la carpeta del CDE.

---

## Control de versiones

Esta guía se versiona con **SemVer** (`MAYOR.MENOR.PARCHE`):

- **MAYOR:** cambia la convención de numeración o de descripciones (afecta expedientes en curso).
- **MENOR:** se agrega un paso, un caso nuevo o una automatización.
- **PARCHE:** correcciones de texto o aclaraciones.

Cada cambio: se actualiza `version` y `fecha` en el encabezado, se agrega una fila abajo y se hace commit en GitHub con el mensaje `docs(revisiones): vX.Y.Z – <resumen>`.

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0.0 | 2026-10-01 | Versión inicial: convención de revisiones por proyecto para radicación, actas de observaciones y planos aprobados; nombre de PDF y flujo CDE. |
