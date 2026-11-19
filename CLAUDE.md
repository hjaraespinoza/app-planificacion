# Contexto del proyecto: Planificador de OA

App web (un solo HTML, sin build) para planificar OA del currículum chileno. El usuario es docente; responder en español.

## Archivos
- `planificador-hgcs-2m.html`: fuente principal (formato Artifact de Claude: sin `<!doctype>/<html>/<body>`, empieza con `<title>` y `<style>`). El nombre del archivo es histórico; ahora contiene todas las asignaturas.
- `index.html`: versión para GitHub Pages = cabecera `<!doctype html>…<body>` + contenido de la fuente + `</body></html>`. Regenerarla tras cada cambio (comando PowerShell con `WriteAllText` UTF-8 sin BOM).
- `README.md`.

## Publicación
- Artifact Claude (privado): https://claude.ai/artifact/KaW3QGVViZeF97yrzHEYPp. Actualizar con Artifact publish y `url` de ese enlace (capability `downloads: true` ya declarada). Versión 11 (19-11-2026) incluye 5° a 8° básico. Ojo: desde una sesión nueva, la herramienta exige leer completa la versión publicada antes de reemplazarla (con ~400 KB fueron 8 lecturas por tramos de ~52 KB); con la fuente actual de ~770 KB serán el doble. Conviene publicar desde la misma sesión que hace los cambios.
- GitHub (público): repo `hjaraespinoza/app-planificacion`, rama `main`, Pages en https://hjaraespinoza.github.io/app-planificacion/ (se actualiza solo al hacer push). Commit/push solo cuando el usuario lo pida.
- Herramientas: Git en `C:\Program Files\Git\cmd\git.exe`, GitHub CLI en `C:\Program Files\GitHub CLI\gh.exe` (sesión iniciada como hjaraespinoza). Autor git configurado solo en este repo con correo noreply.

## Asignaturas (objeto `SUBJ` en el `<script>`, 36 niveles en 9 asignaturas)
- Historia: `hist5b` (OA 1–22), `hist6b` (OA 1–26), `hist7b` (OA 1–23), `hist8b` (OA 1–22), `hist1m` (OA 1–25, 6 unidades sugeridas), `hist2m` (OA 1–25, 4 unidades del Programa). 5° y 6° usan `HB56_BASE` (actitudes `HB_OAA` A–I; habilidades propias de cada nivel: 5° a–m, 6° a–n). 7° y 8° reutilizan `HIST.oah/oaa/res`.
- Educación Ciudadana: `eciu3m`, `eciu4m` (OA 1–8 cada uno, base `ECIU_BASE`).
- Lengua y Literatura: `len8b` (OA 1–26 por eje), `len2m` (OA 1–24 por eje), `len3m` (OA 1–9), `len4m` (OA 1–8).
- Matemática: `mat5b` (OA 1–27), `mat6b` (OA 1–24) con `MAT56_BASE` (habilidades a–n, actitudes A–F); `mat7b` (OA 1–19), `mat8b` (OA 1–17) con `MAT78_BASE` (habilidades a–m, actitudes `MAT_OAA`); `mat1m` (OA 1–15), `mat2m` (OA 1–12), `mat3m` y `mat4m` (OA 1–4).
- Ciencias Naturales (tarjeta `cn`): `cn5b` (OA 1–14, habilidades a–h), `cn6b` (OA 1–18, habilidades a–g) con `CN56_BASE` (actitudes A–F); `cn7b` y `cn8b` (OA 1–15, ejes Biología/Física/Química) reutilizan `CN_OAH/CN_OAA/CN_RES`.
- Biología: `bio1m` (OA 1–8 de CN 1°M), `bio2m` (OA 1–8 de CN 2°M). Química: `quim1m` (OA 17–20), `quim2m` (OA 15–18).
- Ciencias para la Ciudadanía: `cpc3m` y `cpc4m` comparten `CPC_BASE` (12 OA en 4 módulos, claves 1–12 con `lab` "BS 1", "SPA 2", etc.; `unitWord:"Módulo"`).
- Educación Física y Salud: `ef5b`, `ef6b` (OA 1–11 en 3 ejes, base `EF56_BASE`, actitudes A–H); `ef7b`, `ef8b`, `ef1m`, `ef2m` (OA 1–5 por eje, `EF_BASE`). Todas usan `pre`/`post` (calentamiento y vuelta a la calma en cada clase).
- Pie del documento (`dec`): 5°–6° `DEC_B16` (Decreto 439/2012) y EF 5°–6° `DEC_EF16` (Decreto 433/2012); `EF_BASE` usa Decreto 369/2015; 7° básico a 2° medio sin `dec` usan el texto por defecto (Decreto 614/2013).
- 3° y 4° medio usan `ACT34` como actitudes (habilidades siglo XXI; letras A–H son internas, no oficiales) y `dec:DEC34` para el pie del documento.
- Numeración de OA de 5° a 8° básico verificada con curriculumnacional.cl el 19-11-2026 (textos adaptados, no literales).
- `CATALOG` define las tarjetas de asignatura y sus niveles. Para agregar un nivel: crear objeto con `area, level, file, unitWord ("Unidad"|"Eje"|"Módulo"), hab, eje(n), units, OA, oah, oahs, oaa, oaas, res` (opcionales: `oaaLabel`, `dec`, `pre`, `post`, `lab` por OA) y registrarlo en `SUBJ` y `CATALOG`. Usar `oaName(n)` para mostrar el código del OA.
- Formato de cada OA: `t` texto, `k` tema corto, `c` conceptos, `f` 3 focos [verbo, tema, pregunta], `i` 4 indicadores (índice 0 = Bloom básico, 1–2 = intermedio, 3 = superior; ver `IND_LEVEL`), `a` 3 actividades [nombre, "mediante…", descripción, letra de habilidad], `aa` actitudes sugeridas.

## Lógica
- Estado `S` en localStorage `plan-oa-v2` (selección por nivel en `S.selBy`).
- Duración de clases: `S.cps` clases por semana y `S.durs` [4 duraciones, una por posición en la semana; opciones `DUR_OPTS` 45/60/80/90/120]. `durOf(idx)` da la duración de la clase según `idx % S.cps`; `times(d)` reparte inicio/desarrollo/cierre (15 %/70 %/15 %); `durSummary()` y `weekMinutes()` para los textos. Cada clase guarda `c.dur`.
- `buildPlan()` reparte clases entre OA (síntesis si ≥4 clases por OA; clase sumativa final si N≥3). Formativa: `FORM` (10 estrategias); `formStrat:"random"` usa `formSeq()` con semilla `S.seed` (botón "Volver a sortear").
- Sumativa: `INSTR` (rúbricas, prueba, `paes`, escala, cotejo, portafolio). `S.pts` puntaje total y `S.bloom` [% básicas, intermedias, superiores]; `bloomPts()` reparte; `instrBlocks()` arma tabla Bloom + tabla de especificaciones (prueba/PAES) o criterios con puntaje.
- `buildBlocks()` → `blocksHTML()` vista previa y `blocksXML()`/`makeDocx()` Word con JSZip 3.10.1 (cdnjs). Descarga: `claude.use("downloads")` dentro de Claude, Blob fuera.

## Orientación de la página
- `S.orient` ("v" vertical por defecto | "h" horizontal). Selector `data-orient` en la última sección de opciones ("Orientación de la página").
- `pageW()` da el ancho útil para `tbl()` (carta con márgenes de 900: 10440 vertical, 14040 horizontal); `makeDocx` usa `<w:pgSz … w:orient="landscape"/>` en horizontal. La vista previa agrega la clase `.doc.land` (más ancha).

## Orientación de la tabla de planificación
- `S.tblOr` ("v" predeterminada | "h" horizontal). Selector `data-tblor` en la sección siguiente a "Orientación de la página".
- "v" = diseño habitual de cada modalidad. "h" = `hTable(cs, showWeek)`: una fila por clase con columnas Clase · OA y objetivo (con habilidad) · Indicadores · Inicio · Desarrollo · Cierre · Evaluación y recursos, más `adRow(c)` debajo. Se usa en semanal, mensual y por unidad. Se recomienda combinarla con página horizontal (el hint lo sugiere).
- Clase a clase: `S.claseFmt` ("ficha" predeterminado | "tabla"), selector `data-clasefmt` en Temporalidad (solo visible en ese modo). Combinaciones: ficha+v = kv por clase (`classRows`); ficha+h = `fichaH(c)` (9 columnas `ELEM`, una fila + adecuaciones); tabla+h = `fullTable(C)` (columna Clase + `ELEM`, una fila por clase + `adRow`); tabla+v = `transTable(cs)` (una fila por elemento de `classRows` + Semana, una columna por clase; 3 clases por tabla, 4 con página horizontal).

## Documento generado
- Semanal, mensual y por unidad: tablas con una fila por clase y, debajo, una fila de ancho completo con sus adecuaciones (`adRow(c)` devuelve `{full:…}`; `blocksHTML` usa `colspan` y `tbl()` usa `gridSpan` en el Word).

## Respaldo
- Tag y release de GitHub `respaldo-2026-11-18` (commit 5a5036a): 8 asignaturas, 20 niveles, adecuaciones por clase.
- Tag y release de GitHub `respaldo-2026-11-19` (commit 707374c): agrega duración distinta para cada clase de la semana.
- Tag y release de GitHub `respaldo-2026-11-19b` (commit fe7213a): evaluaciones desmarcadas por defecto con botón "Seleccionar todas" (estado `S.momV:2` ignora la selección guardada antigua).
- Tag y release de GitHub `respaldo-2026-11-19d` (commit ef81c10): orientación de página, orientación de la tabla y ficha o tabla en clase a clase.
- Tag y release de GitHub `respaldo-2026-11-19e` (commit f32b09e): agrega 5° a 8° básico en Historia, Matemática, Ciencias Naturales y Educación Física (36 niveles en 9 asignaturas). Es el respaldo más reciente. (El tag local `respaldo-2026-11-19c` apunta al mismo estado que 19b y no se subió.)

## Pendientes / notas
- Textos de OA adaptados (no literales); verificar con curriculumnacional.cl si se requiere redacción oficial.
- Las unidades de varios niveles son agrupación sugerida (verificar con el Programa de Estudio). En Ciencias para la Ciudadanía, 3° y 4° muestran los mismos 4 módulos (las Bases los definen para ambos niveles).
- Las habilidades de Lengua y Literatura y de Educación Física son categorías de planificación (no tienen letras oficiales).
- La descarga .docx en GitHub Pages aún no se probó manualmente.
