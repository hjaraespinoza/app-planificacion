# Contexto del proyecto: Planificador de OA

App web (un solo HTML, sin build) para planificar OA del currículum chileno. El usuario es docente; responder en español.

## Archivos
- `planificador-hgcs-2m.html`: fuente principal (formato Artifact de Claude: sin `<!doctype>/<html>/<body>`, empieza con `<title>` y `<style>`). El nombre del archivo es histórico; ahora contiene todas las asignaturas.
- `index.html`: versión para GitHub Pages = cabecera `<!doctype html>…<body>` + contenido de la fuente + `</body></html>`. Regenerarla tras cada cambio (comando PowerShell con `WriteAllText` UTF-8 sin BOM).
- `README.md`.

## Publicación
- Artifact Claude (privado): https://claude.ai/artifact/KaW3QGVViZeF97yrzHEYPp. Actualizar con Artifact publish y `url` de ese enlace (capability `downloads: true` ya declarada).
- GitHub (público): repo `hjaraespinoza/app-planificacion`, rama `main`, Pages en https://hjaraespinoza.github.io/app-planificacion/ (se actualiza solo al hacer push). Commit/push solo cuando el usuario lo pida.
- Herramientas: Git en `C:\Program Files\Git\cmd\git.exe`, GitHub CLI en `C:\Program Files\GitHub CLI\gh.exe` (sesión iniciada como hjaraespinoza). Autor git configurado solo en este repo con correo noreply.

## Asignaturas (objeto `SUBJ` en el `<script>`, 20 niveles en 8 asignaturas)
- Historia: `hist1m` (OA 1–25, 6 unidades sugeridas), `hist2m` (OA 1–25, 4 unidades del Programa).
- Educación Ciudadana: `eciu3m`, `eciu4m` (OA 1–8 cada uno, base `ECIU_BASE`).
- Lengua y Literatura: `len8b` (OA 1–26 por eje), `len2m` (OA 1–24 por eje), `len3m` (OA 1–9), `len4m` (OA 1–8).
- Matemática: `mat1m` (OA 1–15), `mat2m` (OA 1–12), `mat3m` y `mat4m` (OA 1–4).
- Biología: `bio1m` (OA 1–8 de CN 1°M), `bio2m` (OA 1–8 de CN 2°M). Química: `quim1m` (OA 17–20), `quim2m` (OA 15–18).
- Ciencias para la Ciudadanía: `cpc3m` y `cpc4m` comparten `CPC_BASE` (12 OA en 4 módulos, claves 1–12 con `lab` "BS 1", "SPA 2", etc.; `unitWord:"Módulo"`).
- Educación Física y Salud: `ef1m`, `ef2m` (OA 1–5 por eje; `pre`/`post` agregan calentamiento y vuelta a la calma a cada clase).
- 3° y 4° medio usan `ACT34` como actitudes (habilidades siglo XXI; letras A–H son internas, no oficiales) y `dec:DEC34` para el pie del documento.
- `CATALOG` define las tarjetas de asignatura y sus niveles. Para agregar un nivel: crear objeto con `area, level, file, unitWord ("Unidad"|"Eje"|"Módulo"), hab, eje(n), units, OA, oah, oahs, oaa, oaas, res` (opcionales: `oaaLabel`, `dec`, `pre`, `post`, `lab` por OA) y registrarlo en `SUBJ` y `CATALOG`. Usar `oaName(n)` para mostrar el código del OA.
- Formato de cada OA: `t` texto, `k` tema corto, `c` conceptos, `f` 3 focos [verbo, tema, pregunta], `i` 4 indicadores (índice 0 = Bloom básico, 1–2 = intermedio, 3 = superior; ver `IND_LEVEL`), `a` 3 actividades [nombre, "mediante…", descripción, letra de habilidad], `aa` actitudes sugeridas.

## Lógica
- Estado `S` en localStorage `plan-oa-v2` (selección por nivel en `S.selBy`).
- Duración de clases: `S.cps` clases por semana y `S.durs` [4 duraciones, una por posición en la semana; opciones `DUR_OPTS` 45/60/80/90/120]. `durOf(idx)` da la duración de la clase según `idx % S.cps`; `times(d)` reparte inicio/desarrollo/cierre (15 %/70 %/15 %); `durSummary()` y `weekMinutes()` para los textos. Cada clase guarda `c.dur`.
- `buildPlan()` reparte clases entre OA (síntesis si ≥4 clases por OA; clase sumativa final si N≥3). Formativa: `FORM` (10 estrategias); `formStrat:"random"` usa `formSeq()` con semilla `S.seed` (botón "Volver a sortear").
- Sumativa: `INSTR` (rúbricas, prueba, `paes`, escala, cotejo, portafolio). `S.pts` puntaje total y `S.bloom` [% básicas, intermedias, superiores]; `bloomPts()` reparte; `instrBlocks()` arma tabla Bloom + tabla de especificaciones (prueba/PAES) o criterios con puntaje.
- `buildBlocks()` → `blocksHTML()` vista previa y `blocksXML()`/`makeDocx()` Word con JSZip 3.10.1 (cdnjs). Descarga: `claude.use("downloads")` dentro de Claude, Blob fuera.

## Documento generado
- Semanal, mensual y por unidad: tablas con una fila por clase y, debajo, una fila de ancho completo con sus adecuaciones (`adRow(c)` devuelve `{full:…}`; `blocksHTML` usa `colspan` y `tbl()` usa `gridSpan` en el Word).

## Respaldo
- Tag y release de GitHub `respaldo-2026-11-18` (commit 5a5036a): 8 asignaturas, 20 niveles, adecuaciones por clase.
- Tag y release de GitHub `respaldo-2026-11-19` (commit 707374c): agrega duración distinta para cada clase de la semana.
- Tag y release de GitHub `respaldo-2026-11-19b` (commit fe7213a): evaluaciones desmarcadas por defecto con botón "Seleccionar todas" (estado `S.momV:2` ignora la selección guardada antigua). Es el respaldo más reciente.

## Pendientes / notas
- Textos de OA adaptados (no literales); verificar con curriculumnacional.cl si se requiere redacción oficial.
- Las unidades de varios niveles son agrupación sugerida (verificar con el Programa de Estudio). En Ciencias para la Ciudadanía, 3° y 4° muestran los mismos 4 módulos (las Bases los definen para ambos niveles).
- Las habilidades de Lengua y Literatura y de Educación Física son categorías de planificación (no tienen letras oficiales).
- La descarga .docx en GitHub Pages aún no se probó manualmente.
