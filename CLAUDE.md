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

## Asignaturas (objeto `SUBJ` en el `<script>`)
- `hist2m` Historia, Geografía y Cs. Sociales 2° Medio: OA 1–25, 4 unidades del Programa.
- `bio1m` Biología 1° Medio: OA 1–8 de CN 1° Medio, 4 unidades sugeridas.
- `quim1m` Química 1° Medio: OA 17–20 de CN 1° Medio, 2 unidades sugeridas.
- `len8b` Lengua y Literatura 8° Básico: OA 1–26, agrupados por eje (`unitWord:"Eje"`).
- `CATALOG` define las tarjetas de asignatura y sus niveles. Para agregar un nivel: crear objeto con `area, level, file, unitWord, hab, eje(n), units, OA, oah, oahs, oaa, oaas, res` y registrarlo en `SUBJ` y `CATALOG`.
- Formato de cada OA: `t` texto, `k` tema corto, `c` conceptos, `f` 3 focos [verbo, tema, pregunta], `i` 4 indicadores (índice 0 = Bloom básico, 1–2 = intermedio, 3 = superior; ver `IND_LEVEL`), `a` 3 actividades [nombre, "mediante…", descripción, letra de habilidad], `aa` actitudes sugeridas.

## Lógica
- Estado `S` en localStorage `plan-oa-v2` (selección por nivel en `S.selBy`).
- `buildPlan()` reparte clases entre OA (síntesis si ≥4 clases por OA; clase sumativa final si N≥3). Formativa: `FORM` (10 estrategias); `formStrat:"random"` usa `formSeq()` con semilla `S.seed` (botón "Volver a sortear").
- Sumativa: `INSTR` (rúbricas, prueba, `paes`, escala, cotejo, portafolio). `S.pts` puntaje total y `S.bloom` [% básicas, intermedias, superiores]; `bloomPts()` reparte; `instrBlocks()` arma tabla Bloom + tabla de especificaciones (prueba/PAES) o criterios con puntaje.
- `buildBlocks()` → `blocksHTML()` vista previa y `blocksXML()`/`makeDocx()` Word con JSZip 3.10.1 (cdnjs). Descarga: `claude.use("downloads")` dentro de Claude, Blob fuera.

## Pendientes / notas
- Textos de OA adaptados (no literales); verificar con curriculumnacional.cl si se requiere redacción oficial.
- Unidades de Biología/Química son agrupación sugerida (verificar con el Programa de Estudio).
- La descarga .docx en GitHub Pages aún no se probó manualmente.
