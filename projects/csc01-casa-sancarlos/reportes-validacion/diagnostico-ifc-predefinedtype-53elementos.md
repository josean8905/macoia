# Diagnóstico IFC PredefinedType — 53 elementos sin validar en bimeco.io

## 🟢 CORRIDA 2026-09-17 (05:07 UTC, ejecución programada) — cuarta verificación en vivo: cero cambios, confirmación adicional de que los parámetros leídos son los correctos

**Método:** rvt-mcp conectado (único target, Revit 2027, pid 27172), verificado por Title/PathName (`CSC01-OIA-ZZ-XX-ARQ-MOD-CASA_SAN_CARLOS-v0.1.rvt`, misma ruta de siempre). Solo lectura: se releyeron en vivo los 209 elementos físicos de las 14 categorías relevantes (agrupados por categoría + tipo + estado de export + subtype). **No se aplicó ningún cambio.**

### Resultado: idéntico a la corrida de esta madrugada (04:10 UTC, hace ~1h) — sin drift

Se comparó cada grupo resultante contra la corrida anterior de hoy mismo. Coincide exactamente: Muros con `STANDARD`/`PARTITIONING` intactos, 8 Puertas vacías (mismos 4 tipos), Puerta corrediza (714518) en `NOTDEFINED`, Floors "Acera 19cm" (715316) y "Con pavimento flotante" (753454) en `USERDEFINED`, los 3 Contrapisos con los mismos 3 valores por instancia (706281 `BASESLAB`, 755382 `FLOOR`, 760059 `ROOF`), 30 Columnas / 66 Vigas / 6 Ventanas / 1 Escalera sin subtype a nivel tipo. **Ningún elemento cambió desde la corrida de esta madrugada.**

### Verificación metodológica adicional (importante, valida la corrección del 17-sep sobre el "Grupo A")

En esta corrida, antes de releer los 209 elementos, se verificó con `revit_get_element_parameters` / `revit_get_type_parameters` (lectura directa de parámetro por elemento, no inferencia) la identidad exacta de los parámetros usados en todas las corridas de este diagnóstico, tomando el Muro 708896 como referencia:

- **`IFC Predefined Type`** (instancia) = `BuiltInParameter.IFC_EXPORT_PREDEFINEDTYPE` — confirmado, es el parámetro correcto que se ha venido leyendo.
- **`Type IFC Predefined Type`** (tipo) — parámetro de texto correctamente identificado por nombre (no es un BuiltInParameter estándar con GUID fijo entre categorías, se confirma por nombre vía `LookupParameter`).
- **`Export to IFC`** (instancia) = `BuiltInParameter.IFC_EXPORT_ELEMENT`, **`Export Type to IFC`** (tipo) = `BuiltInParameter.IFC_EXPORT_ELEMENT_TYPE` — ambos confirmados, y se reconfirma que `raw=0` → `display="By Type"` (instancia) / `"Default"` (tipo), **no "No"**. Esto refuerza la corrección de esta madrugada (17-sep): el "Grupo A" (194/209 instancias, 208/209 tipos en valor "0") sigue sin evidencia de ser un bloqueo real — es el estado neutro heredado, no una exclusión explícita.
- Se identificó además que existe un parámetro separado `Export to IFC As` / `Export Type to IFC As` (ej. "IfcWall", "IfcWallType") que es la **clase IFC de destino**, no el PredefinedType — distinto del `IFC Predefined Type` / `Type IFC Predefined Type`. No afecta ninguna decisión pendiente, pero es una precisión útil si se automatiza esto más adelante: hay 4 parámetros IFC relacionados por elemento/tipo (Export on/off, Export As [clase], Predefined Type [subtype], y a veces un ObjectType/IfcObjectType libre) y no deben confundirse entre sí.

**No hay nada nuevo que reportar respecto a la corrida de esta madrugada.** Las 10 decisiones pendientes acumuladas (ver corrida de 04:10 UTC abajo, encabezada por la reclasificación del Grupo A) siguen exactamente igual, sin confirmación todavía. No se generó notificación para esta corrida porque no hay cambio de estado ni hallazgo nuevo que amerite interrumpir — es una reconfirmación limpia.

---

## 🟡🔴 CORRIDA 2026-09-17 (04:10 UTC) — CORRECCIÓN IMPORTANTE: el "Grupo A" (bloqueo total) de las corridas anteriores parece un error de lectura del parámetro, no un problema real del modelo

**Método:** rvt-mcp conectado (único target, Revit 2027, pid 27172), verificado por Title/PathName (`CSC01-OIA-ZZ-XX-ARQ-MOD-CASA_SAN_CARLOS-v0.1.rvt`). Solo lectura: se releyeron en vivo los 209 elementos físicos de las 14 categorías relevantes vía C# sin transacción, **y adicionalmente se inspeccionó la identidad real del parámetro** (`Definition.Name`, `BuiltInParameter`, `StorageType`, valor entero crudo) para reconciliar contra lo documentado en las corridas de ayer. **No se aplicó ningún cambio.**

### El hallazgo: "No" nunca fue "No"

Las corridas del 16-sep reportaron `Export to IFC` (instancia) = **No** en 194/209 elementos y `Export Type to IFC` (tipo) = **No** en 208/209 tipos, y lo marcaron como el bloqueante crítico de máxima prioridad ("el modelo completo no exportaría nada a IFC").

Al inspeccionar hoy la identidad exacta del parámetro (`BuiltInParameter.IFC_EXPORT_ELEMENT` a nivel instancia, `IFC_EXPORT_ELEMENT_TYPE` a nivel tipo — ambos `StorageType.Integer`, tri-estado), se confirma:

- **Valor entero `0` → `AsValueString() = "By Type"`** (instancia) / **`"Default"`** (tipo). Esto **no significa "no exportar"** — significa "sin override, heredar el comportamiento normal/de categoría". Es el estado neutro y esperado para la gran mayoría de un modelo sano.
- **Valor entero `1` → `"Yes"`** (confirmado en la Puerta 713492 y en el tipo de Escalera 731317) — override explícito para forzar exportación.
- Ningún elemento de los 209 revisados hoy tiene el parámetro en un valor que corresponda a exclusión explícita — es decir, **no hay evidencia de que el modelo tenga elementos marcados a propósito como "no exportar"**.

Es decir: los mismos 208/209 tipos y 194/209 instancias que ayer se reportaron como "No" (bloqueados) hoy se leen exactamente igual en cuanto a datos crudos (`AsInteger() = 0`), pero el valor `0` corresponde a `"By Type"`/`"Default"`, no a `"No"`. Todo indica que las corridas anteriores asumieron `0 == "No"` sin verificar el mapeo real del enum, y esa asunción se propagó y se amplificó (de 65 elementos "apagados" el 11-sep a "prácticamente todo el modelo" el 16-sep) probablemente por el mismo error de lectura aplicado a más categorías, no por un cambio real en el modelo.

**Esto no confirma que el modelo exportaría todo correctamente** — el valor "By Type"/"Default" depende de la configuración de exportación IFC a nivel de categoría (la configuración de exportación guardada / IFC Export Setup que se elige al momento de exportar), que **no es un parámetro por elemento** y no se puede leer con esta consulta. Sigue siendo cierto que la forma definitiva de confirmar qué sale en el IFC es un export de prueba real + `ifcopenshell` (decisión #10 de la lista acumulada, nunca ejecutada). Pero la severidad de "Grupo A" baja de "bloqueante confirmado, prioridad máxima" a "sin evidencia de bloqueo — pendiente de verificación con export real".

### Los 53 elementos: sin cambios de datos desde anoche

Se releyeron los mismos 209 elementos y los subtypes/objectType/nivel siguen **idénticos** a la corrida de anoche (16-sep, ejecución #2): mismos 8 tipos de Puerta sin subtype, misma Puerta corrediza (714518) en `NOTDEFINED`, mismos 2 Floors `USERDEFINED` sin `ObjectType` (Acera 19cm 715316, Pavimento flotante 753454), mismos 3 Contrapisos con 3 valores distintos (706281 `BASESLAB` ✅, 755382 `FLOOR`, 760059 `ROOF` ⚠️), mismos Muros ya corregidos (`STANDARD`/`PARTITIONING`) sin revertir. La tabla de grupos A–G de la corrida anterior (más abajo) sigue vigente tal cual, **salvo que el Grupo A ya no debe tratarse como confirmado** — ver arriba.

### Decisión que hace falta ahora (reemplaza la decisión #1 anterior)

1. 🟡 **Grupo A reclasificado:** no se encontró evidencia de que el modelo esté realmente bloqueado para exportar. Antes de invertir esfuerzo "revirtiendo" un apagado masivo, recomiendo confirmar con un export de prueba real (vista pequeña) + revisión con `ifcopenshell`, tal como estaba sugerido desde el 16-sep (decisión #10) y nunca ejecutado. Solo José Antonio puede autorizar generar ese archivo de prueba — no se hizo en esta corrida por mantenerse en solo lectura/diagnóstico según la instrucción.
2. El resto de las decisiones pendientes (Grupos B–G: puertas sin subtype, puerta corrediza, floors USERDEFINED, contrapisos inconsistentes, mobiliario/Rooms sin subtype) **siguen exactamente igual que anoche** — ver tabla completa abajo. Nada de esto se ha aplicado todavía.

---

## 🟢 CORRIDA 2026-09-16 (noche, ejecución programada #2) — tercera verificación en vivo: cero cambios, mismas 10 decisiones pendientes

**Método:** rvt-mcp conectado (único target, Revit 2027, pid 27172), verificado por Title/PathName (`CSC01-OIA-ZZ-XX-ARQ-MOD-CASA_SAN_CARLOS-v0.1.rvt`). Solo lectura: se releyeron en vivo los 209 elementos físicos de las 14 categorías relevantes (agrupados por categoría + tipo + estado de export + subtype, vía C# sin transacción). **No se aplicó ningún cambio.**

### Resultado: idéntico byte a byte a la corrida anterior (hoy, tarde/noche) — sin drift, ninguna decisión atendida todavía

Se comparó cada uno de los 37 grupos resultantes contra la corrida previa documentada abajo. Coinciden exactamente:

- `Export Type to IFC` (tipo) sigue en No en 208/209 tipos (único Sí: tipo de Escalera "Concreto para escaleras 21MPa").
- `Export to IFC` (instancia) sigue en No en 194/209 (únicos Sí: 9 Puertas + 6 Pisos).
- Muros: los dos tipos corregidos hoy (`MU_CERRAMIENTO_LOTE...`→`STANDARD`, `MU_INTERIOR_Divisorio_Mampostería...`→`PARTITIONING`) siguen intactos y sin revertir, pero ambos con `Export Type to IFC = No` — siguen sin poder salir en un IFC.
- Puerta corrediza (714518) sigue en `NOTDEFINED`. Las 8 Puertas vacías (100x210×4, 80x210×2, 90x210×1, 1810x2400×1) siguen vacías.
- Los 3 Contrapisos del mismo tipo (`S1_LOSA_CONTRAPISO...`) siguen con 3 valores distintos: 706281=`BASESLAB` ✅, 755382=`FLOOR`, 760059=`ROOF` ⚠️ (sigue siendo la asignación más claramente equivocada — losa de piso en nivel "CUBIERTA").
- Floors "Acera 19cm" (715316) y "Con pavimento flotante" (753454) siguen en `USERDEFINED` sin `ObjectType`.

**No hay nada nuevo que reportar respecto a la corrida anterior.** Las 10 decisiones pendientes acumuladas (ver corrida anterior abajo, encabezada por el Grupo A — el apagado masivo de `Export Type to IFC`/`Export to IFC`, que sigue siendo el bloqueante real) continúan exactamente igual, sin confirmación todavía. No se generó notificación para esta corrida porque no hay cambio de estado que amerite interrumpir — el hallazgo crítico ya fue reportado en la corrida anterior del mismo día.

**Nota (17-sep):** ver corrida de hoy arriba — el "bloqueante real" mencionado en este párrafo probablemente fue un error de lectura del parámetro tri-estado, no un problema real.

---

## 🟢 CORRIDA 2026-09-16 (tarde/noche, ejecución programada) — verificación en vivo: cero cambios de estado, bloqueo principal sigue vigente

**Método:** rvt-mcp conectado (único target disponible: Revit 2027, pid 27172), verificado por Title/PathName (`CSC01-OIA-ZZ-XX-ARQ-MOD-CASA_SAN_CARLOS-v0.1.rvt`, ruta `G:\Mi unidad\00. Macoia\02_COM\MAESTROS\BIM\WIP\Plantillas_RVT\`). Solo lectura: se recolectaron los 209 elementos físicos de las 14 categorías relevantes (Muros, Pisos, Puertas, Ventanas, Columnas, Estructura metálica, Escalera, Barandas, Rooms, Casework, Furniture, Furniture Systems, Plumbing Fixtures, Entourage) vía C# sin transacción y se agruparon por tipo + estado de export + subtype. **No se aplicó ningún cambio.**

### Resultado: el estado es idéntico al reportado hace unas horas (corrida de hoy 17:15) — sin drift

Se confirmó dato por dato contra el diagnóstico anterior de hoy mismo:

- **`Export Type to IFC` (nivel TIPO) sigue en No en 208/209 tipos.** Único tipo en Sí: el tipo de Escalera "Concreto para escaleras 21MPa".
- **`Export to IFC` (nivel INSTANCIA) sigue en No en 194/209 instancias.** Los únicos 15 en Sí siguen siendo: 9 Puertas + 6 Pisos.
- **La corrección de Muros mencionada como referencia ("mismo patrón que se corrigió hoy") se confirma vigente y estable:** el tipo "MU_CERRAMIENTO_LOTE..." tiene `Type IFC Predefined Type = STANDARD` y "MU_INTERIOR_Divisorio_Mampostería..." tiene `PARTITIONING` — ya no aparecen como `USERDEFINED` vacío. Esta parte del trabajo de hoy no se ha revertido ni corrompido. **Pero ambos tipos de Muro siguen con `Export Type to IFC = No`, así que aunque el subtype ya es correcto, ningún muro saldría hoy en un IFC.**

**Conclusión de esta corrida: el hallazgo crítico de la corrida anterior sigue siendo el bloqueante real y no ha sido atendido.** Mientras `Export Type to IFC` (tipo) y `Export to IFC` (instancia) sigan apagados para prácticamente todo el modelo, corregir subtypes —incluido el trabajo ya bien hecho en Muros— no tiene ningún efecto en lo que bimeco.io vería, porque esos elementos simplemente no estarían en el archivo IFC.

**Nota (17-sep):** ver corrida de hoy arriba — "No" en este párrafo probablemente debería leerse como "By Type"/"Default" (heredar), no como exclusión confirmada. Pendiente de verificar con export real.

### Los 53 elementos (27 sin subtype + 26 subtype inválido), agrupados por causa probable y decisión pendiente

Nota de honestidad: el conteo original "53" de bimeco.io no se puede re-derivar con precisión exacta desde Revit solo —depende de qué .ifc se subió y cuándo—, pero cruza casi perfecto con los grupos ya identificados en corridas anteriores (11-sep, .ifc real) más lo verificado hoy en vivo. Los grupos marcados "✅ confirmado" se verificaron contra un .ifc real exportado; los marcados "🟡 inferido" son el comportamiento documentado de Revit pero no re-confirmados contra un .ifc nuevo en esta corrida (no se generó ningún archivo, por instrucción de solo-lectura).

| Grupo | Elementos | Causa probable | Estado del subtype hoy | Decisión pendiente |
|---|---|---|---|---|
| A — Bloqueo total (afecta a los 53 y a 156 más) — **reclasificado 17-sep, ver arriba: probable error de lectura, no confirmado como bloqueo real** | 194 instancias / 208 tipos | `Export to IFC` / `Export Type to IFC` en valor "By Type"/"Default" (antes leído como "No") | N/A | Confirmar con export de prueba real + ifcopenshell si esto realmente bloquea algo antes de "revertir" nada |
| B — Puertas sin subtype, exportador resuelve a DOOR por defecto | 8 (tipos "100x210"×4, "80x210"×2, "90x210"×1, "1810x2400"×1) | Parámetro vacío | ✅ confirmado contra .ifc real (11-sep) que resuelve a `DOOR` | Confiar en el default, o asignar `DOOR` explícito por robustez |
| C — Puerta corrediza con subtype explícito inválido | 1 (id 714518, "V_CORREDIZA_1.20x2.40") | `NOTDEFINED` puesto a mano | confirmado inválido | Asignar `DOOR` |
| D — Pisos tipo "USERDEFINED sin ObjectType" (mismo patrón ya corregido en Muros) | 2 (Acera 19cm id 715316, Pavimento flotante id 753454) | `USERDEFINED` sin `ObjectType` | confirmado hoy en vivo, sin cambios | Asignar `SIDEWALK` y `FLOOR` respectivamente |
| E — Contrapisos mismo tipo con 3 valores distintos por instancia, uno probablemente por nombre de nivel y no por función | 3 (706281 `BASESLAB` ✅, 755382 `FLOOR` inconsistente, 760059 `ROOF` ⚠️ probablemente mal — está en nivel "CUBIERTA" pero es losa de piso, no cubierta) | override manual de instancia | confirmado hoy en vivo, sin cambios | Unificar los 3 a `BASESLAB`; prioridad especial en corregir 760059 |
| F — Vigas / Columnas / Ventanas / Escalera sin subtype, exportador resuelve bien por defecto | 66 vigas + 30 columnas + 6 ventanas + 1 escalera = 103 | Parámetro vacío a nivel tipo e instancia | 🟡 inferido — confirmado contra .ifc real del 11-sep para estas categorías puntuales, no re-testeado con export nuevo | Ninguna acción necesaria sobre subtype **una vez resuelto el Grupo A**; si se quiere blindar contra cambios de exportador, se puede asignar explícito |
| G — Rooms / Casework / Furniture / Furniture Systems / Plumbing Fixtures / Railings / Entourage sin subtype | 16 Rooms + 5 Casework + 4 Furniture + 1 Furniture System + 6 Plumbing + 2 Railings + 1 Entourage = 35 | Parámetro vacío + export apagado en ambos niveles | pendiente desde 11-sep si se quiere excluir del IFC de cumplimiento (mobiliario/Rooms) | Confirmar si excluir mobiliario/Rooms del IFC de cumplimiento fue una decisión deliberada o quedó así por accidente junto con el Grupo A |

Total referenciado arriba (excluyendo Grupo A, que es transversal): B+C+D+E = 14 con causa de subtype accionable ya identificada; F+G = 138 adicionales sin subtype pero mayormente resueltos por default del exportador (F) o pendientes de decisión de alcance (G). La suma exacta que bimeco.io reportó como "53" probablemente mezcla un subconjunto de F y G evaluados en un momento en que tenían `Export=Sí`; no se puede reconstruir con certeza el snapshot exacto sin volver a exportar y validar — decisión "verificar con export de prueba" (punto 2 de decisiones acumuladas) sigue pendiente.

### Decisiones pendientes acumuladas (actualizado 17-sep, reordenadas)

1. 🟡 **Grupo A (reclasificado 17-sep):** ya no se considera confirmado como bloqueo real — ver corrida de hoy arriba. Antes de actuar, verificar con export de prueba real + ifcopenshell.
2. Grupo G: confirmar si excluir mobiliario/Rooms/Plumbing/Casework/Furniture/Railing/Entourage del IFC de cumplimiento es intencional.
3. Muro de cerramiento de lote (30 instancias, Grupo A): confirmar si excluirlo del IFC fue intencional.
4. Grupo C — Puerta corrediza (714518): asignar `DOOR`.
5. Grupo D — Floors Acera y Pavimento flotante: asignar `SIDEWALK` y `FLOOR`.
6. Grupo E — Contrapisos: unificar a `BASESLAB`, prioridad en corregir 760059 (`ROOF`→`BASESLAB`).
7. Grupo B — Puertas sin subtype (8): decidir default del exportador vs. `DOOR` explícito.
8. Grupo F: opcional, blindar Vigas/Columnas/Ventanas/Escalera con subtype explícito.
9. Aparatos sanitarios (dentro de Grupo G): si se reactivan, cambiar `Export Type to IFC As` a `IfcSanitaryTerminalType`.
10. Verificar con un export de prueba real a IFC + `ifcopenshell` para confirmar de una vez cuál flag manda (tipo vs. instancia) y recalibrar el conteo exacto de "53" — **ahora la decisión más importante de la lista**, dado que resolvería tanto el Grupo A como la duda sobre el conteo. No ejecutado en ninguna corrida de solo-lectura.

**No se aplicó ningún cambio en esta corrida — todo lo anterior es lectura y verificación, pendiente de confirmación explícita.**

---

## 🔴🔴 CORRIDA 2026-09-16 (hoy, tarde) — hallazgo crítico: el modelo completo no exportaría casi nada a IFC ahora mismo

**⚠️ Nota (17-sep): este hallazgo probablemente fue un error de lectura del parámetro tri-estado `Export to IFC`/`Export Type to IFC` (valor `0` = "By Type"/"Default" = heredar, no "No" = excluir). Ver la corrida del 17-sep arriba antes de actuar sobre cualquier decisión basada en este hallazgo.**

**Método:** Solo lectura vía `rvt-mcp` (código C# sin transacción, cero cambios aplicados), Revit 2027, Casa San Carlos, verificado por Title/PathName (`CSC01-OIA-ZZ-XX-ARQ-MOD-CASA_SAN_CARLOS-v0.1.rvt`).

### El hallazgo

Revit IFC maneja el flag de exportación en **dos niveles independientes**: instancia (`Export to IFC`) y tipo (`Export Type to IFC`). El comportamiento estándar documentado de Revit es que **ambos deben estar en Sí** para que un elemento salga en el IFC — si el tipo dice No, ningún instancia de ese tipo exporta, sin importar lo que diga la instancia. (No se pudo confirmar esto al 100% con una fuente que probara exactamente este caso — ver "Cómo verificar" abajo — pero es el comportamiento ampliamente reportado para este par de parámetros).

Se revisaron 209 elementos "físicos" candidatos (Muros, Pisos, Puertas, Ventanas, Estructura, Escalera, Barandas, Rooms, Casework, Furniture, Furniture Systems, Plumbing Fixtures, Entourage — coincide exacto con el conteo total de esas categorías en el modelo). Resultado:

- **`Export Type to IFC` (nivel de TIPO) = No en absolutamente todos los tipos del modelo, excepto un único tipo de Escalera.** Es decir, 208 de 209 elementos pertenecen a un tipo marcado como "no exportar" a nivel de tipo.
- **`Export to IFC` (nivel de INSTANCIA) = No en 194 de 209 elementos.** Solo Puertas (9) y Pisos (6) = 15 elementos tienen la instancia en Sí — pero como su TIPO también está en No, si el tipo manda, tampoco saldrían.
- La única Escalera del modelo tiene el tipo en Sí pero la instancia en No — tampoco exportaría.

**Conclusión (si el tipo manda sobre la instancia, como es el comportamiento típico): ningún elemento de estas 209 saldría en el próximo IFC.** No es un problema de 53 elementos con subtype incorrecto — es que el archivo IFC probablemente saldría prácticamente vacío (sin muros, sin estructura, sin puertas, sin ventanas, sin pisos, sin rooms), lo cual explicaría un fallo de validación en bimeco.io mucho más severo que "53 elementos sin subtype".

### Comparación con la corrida del 11-sep (5 días atrás)

El 11-sep solo se había revisado el flag de INSTANCIA, y solo aparecía apagado en 65 elementos (Muro cerramiento 30, Rooms 16, Plumbing 6, Casework 5, Furniture 5, Furniture Systems 1, Railings 1, Entourage 1). El flag de TIPO (`Export Type to IFC`) no se había revisado nunca antes en este diagnóstico.

Hoy, a nivel de instancia, el apagado se **expandió** a categorías que el 11-sep SÍ exportaban: el segundo tipo de Muro "Interior Divisorio Mampostería" (25, antes exportaba), **todas las Columnas estructurales (30)**, **toda la Estructura metálica (66 vigas)**, **todas las Ventanas (6)**, la Escalera (1), y apareció un nuevo tipo de Baranda "Barrotes redondos" (1, también apagado). No se pudo determinar si el flag de TIPO ya estaba en No desde antes (nunca se revisó) o si cambió junto con todo esto.

**Nota (17-sep):** esta "expansión" repentina de 65 a ~200 elementos entre el 11-sep y el 16-sep es justo el patrón que uno esperaría de un error de lectura de código que se propagó a más categorías, no de un cambio real y deliberado en el modelo — refuerza la sospecha de que el hallazgo original fue un falso positivo.

### Por qué importa más que el tema de subtype

Corregir el `Type IFC Predefined Type` de puertas/pisos/contrapisos (lo que pedía la tarea original) **no sirve de nada mientras el flag de exportación —de tipo o de instancia— siga en No** para esos elementos: seguirían sin aparecer en el IFC pase lo que pase con su subtype.

### Decisión que hace falta (prioridad máxima, antes que cualquier otra cosa de este documento)

1. **Confirmar si el apagado masivo de `Export Type to IFC` (nivel tipo) y `Export to IFC` (nivel instancia) fue intencional.** No parece serlo: incluye Muros, Estructura, Ventanas — elementos de envolvente/estructura que SÍ deben estar en el IFC de cumplimiento POT/NSR-10. Si no fue intencional, hay que decidir cómo revertirlo (candidato a automatizar una vez confirmado, pero **no se tocó nada en esta corrida**).
2. **Verificar con un export de prueba real a IFC** qué gana en la práctica cuando tipo=No e instancia=Sí (Puertas y Pisos) — no se hizo en esta corrida por mantenerse estrictamente de solo lectura sin generar archivos nuevos sin confirmación.
3. Si se decide reactivar, definir el alcance: ¿todo excepto mobiliario/Rooms (que parecía ser la intención original del 11-sep, ver decisión #2 más abajo), o absolutamente todo?

### Cómo verificar (sugerido, no ejecutado)

Un export de prueba a IFC de una sola vista/nivel pequeño y abrir el resultado con `ifcopenshell` (como se hizo el 11-sep con el .ifc real) confirmaría en minutos cuál flag manda. No se hizo porque implica generar un archivo nuevo y la instrucción de esta corrida fue solo lectura/diagnóstico.

---

## 🔧 Corrida 2026-09-16 — hallazgos de subtype entre los elementos que SÍ tienen `Export to IFC` (instancia) = Sí

Aunque el hallazgo de arriba probablemente hace irrelevante lo siguiente hasta que se resuelva, se revisó el patrón pedido (mismo patrón que Muros: `USERDEFINED` sin `ObjectType` → valor IFC válido según función real) para Puertas y Pisos, que son las únicas categorías con instancia = Sí ahora mismo.

### Puertas (9 instancias, instExport=Sí, typeExport=No)

| Tipo | Instancias | `IFC Predefined Type` | Causa probable | Sugerido |
|---|---|---|---|---|
| "100 x 210 cm" | 4 | vacío | sin subtype — el exportador de Revit suele resolver Puertas vacías a `DOOR` por defecto (confirmado en el .ifc real del 11-sep) | probablemente sin acción, pero no re-confirmado contra un .ifc nuevo |
| "80 x 210 cm" | 2 | vacío | igual que arriba | igual |
| "90 x 210 cm" | 1 | vacío | igual que arriba | igual |
| "1810 x 2400mm" | 1 | vacío | igual que arriba | igual |
| "V_CORREDIZA_1.20x2.40" (id 714518) | 1 | `NOTDEFINED` (explícito) | subtype inválido explícito — sigue igual que el 11-sep | asignar `DOOR` |

**Decisión:** confirmar si se confía en el default del exportador para las 8 puertas vacías, o se prefiere asignar `DOOR` explícitamente a todas por seguridad (más robusto ante cambios de exportador). Para la corrediza (714518): confirmar asignar `DOOR`.

### Pisos (6 instancias, instExport=Sí, typeExport=No)

| Tipo / instancia | `IFC Predefined Type` (a nivel de INSTANCIA — no de tipo) | ObjectType | Causa probable | Sugerido |
|---|---|---|---|---|
| "Acera 19 cm" (715316) | `USERDEFINED` | vacío | mismo patrón que Muros: USERDEFINED sin ObjectType | `SIDEWALK` |
| "Con pavimento flotante - 30 cm" (753454) | `USERDEFINED` | vacío | mismo patrón | `FLOOR` |
| "LOSA_3  200mm" (753419) | `BASESLAB` | vacío | correcto | sin acción |
| Contrapiso (706281, Piso 01) | `BASESLAB` | vacío | correcto | sin acción |
| Contrapiso (755382, Piso 02) | `FLOOR` | vacío | ⚠️ ver hallazgo nuevo abajo | ver abajo |
| Contrapiso (760059, Nivel 03_CUBIERTA) | `ROOF` | vacío | ⚠️ ver hallazgo nuevo abajo | ver abajo |

### 🆕 Hallazgo nuevo: los 3 Contrapisos (mismo tipo exacto) ahora tienen 3 valores de PredefinedType distintos, asignados a nivel de INSTANCIA

El 11-sep los 3 contrapisos (706281, 755382, 760059 — mismo tipo `S1_LOSA_CONTRAPISO_ e=0.10m_RESO-RESIDENCIAL`) tenían el parámetro vacío en Revit y el exportador los resolvía a `BASESLAB` por defecto (confirmado en el .ifc real). Hoy, alguien o algún script asignó un override de INSTANCIA distinto a cada uno:

- 706281 (01_PISO 01) → `BASESLAB` ✅ correcto
- 755382 (02_PISO 02) → `FLOOR` — genérico, no es incorrecto pero es inconsistente con el mismo tipo de elemento en otro piso
- 760059 (03_CUBIERTA) → `ROOF` ⚠️ probablemente **incorrecto** — es un contrapiso/losa de piso (mismo tipo constructivo que los otros dos), no una cubierta. Parece que el valor se asignó según el **nombre del nivel** ("CUBIERTA") en vez de la función real del elemento — es decir, el error inverso al patrón que ya se corrigió en Muros.

**Decisión pendiente:** ¿unificar los 3 a `BASESLAB` (correcto para una losa de contrapiso sin importar el piso)? Prioridad especial en corregir el 760059 (ROOF es la asignación más claramente equivocada).

---

## Historial de corridas anteriores (11-sep-2026)

### Corrida de la tarde (11-sep) — hallazgo de `Export to IFC` (instancia) apagado en 65 elementos

Se conectó a Revit (rvt-mcp, target 2027, Casa San Carlos) para re-leer en vivo los 53 elementos/tipos ya identificados. Solo lectura, cero cambios aplicados.

Se confirmó `Export to IFC = 0` en el 100% de las instancias de: Muro cerramiento de lote (30/30), Rooms (16/16), Plumbing Fixtures (6/6), Casework (5/5), Furniture (5/5), Furniture Systems (1/1), Railings (1/1), Entourage (1/1). Total: 65 elementos.

Elementos que sí seguían con `Export to IFC=1` y necesitaban decisión de subtype: Puerta corrediza (714518, NOTDEFINED), Floor "Acera 19cm" (715316, vacío→SIDEWALK), Floor "Con pavimento flotante" (753454, vacío→FLOOR), 3 Contrapisos (vacío en ese momento, resueltos a BASESLAB por el exportador).

**Nota (17-sep):** aquí "Export to IFC = 0" sí puede haberse interpretado igual que hoy (0 = "By Type", no "No") — sin poder confirmarlo retroactivamente, pero es consistente con la corrección de arriba.

### Corrida de la mañana (11-sep) — análisis del .ifc real exportado

Se abrió `CSC01-OIA-ZZ-XX-ARQ-MOD-CASA_SAN_CARLOS-v0.ifc` (11-sep 11:39am, IFC4 Reference View) con `ifcopenshell`. Export parcial: solo 102 IfcElement + 15 IfcSpace, 87% en Piso 01.

Categorías que exportan bien con default del exportador aunque el parámetro esté vacío en Revit: Vigas→BEAM, Columnas→COLUMN, Ventanas→WINDOW, Puertas "1 hoja"→DOOR, Escalera→STRAIGHT_RUN_STAIR. Plumbing Fixtures exportaban como `IfcDistributionElementType` genérico (sin PredefinedType en IFC4) — ahora secundario porque están con Export a No. Rooms: 15 `IfcSpace`, todas `NOTDEFINED` — ahora con Export a No, no deberían aparecer.

### Corridas iniciales (11-sep, antes del .ifc real)

Barrido inicial (excluyendo Muros): 27 tipos con `Type IFC Predefined Type` vacío — coincidía con el número de bimeco.io pero resultó engañoso, varias categorías (Vigas, Columnas, Ventanas, algunas Puertas, Escalera) exportan con default válido pese al parámetro vacío. Grupo B (26 "subtype inválido"): solo se confirmaron ~4-5 casos en esa corrida.
