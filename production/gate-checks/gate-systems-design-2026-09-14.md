# Gate Check: Systems Design → Technical Setup

**Fecha**: 2026-09-14
**Chequeado por**: gate-check (Sora/Hermes) + Panel de Directores (4 subagentes independientes, modo lean)
**Modo de review**: lean (default — no existe production/review-mode.txt)

## Veredicto: **FAIL**

Un director (TD) dictaminó NOT READY → el veredicto mínimo es FAIL según las reglas del gate. No es un revés de calidad: los GDDs existentes son sólidos; el gate exige **verificación independiente** que aún no se ha hecho.

## Panel de Directores

| Director | Veredicto | Esencia |
|---|---|---|
| Technical Director | **NOT READY** | Falta GDD #17 (MVP); 0 design-reviews; sin cross-GDD review; `fundamentos-essentials.md` incompleto (6 secciones, estado "In Design") |
| Creative Director | CONCERNS | Purificación por Fuentes puede trivializar el rito (sugiere: fuente acelera, ritual cierra el arco); contraparte narrativa (Acreedores) sin GDD; contrato de flashback genérico vs. por especie pendiente; deriva de citas del Pilar 1 |
| Producer | CONCERNS | 12 GDDs "Designed" sin revisar en proyecto en solitario; feature estrella sin prototipo; respetar la mitigación MVP Cap.1-3; no omitir reviews "en sesiones frescas" |
| Art Director | READY | Lenguaje visual sembrado coherentemente; riesgos no bloqueantes (30 formas regionales, paleta global → art bible) |

## Artefactos requeridos

- [x] `design/gdd/systems-index.md` — 31 sistemas, MVP con tiers justificados por pilares ✓
- [ ] **GDD #17 Diálogo + escenas (MVP)** — FALTA (error de conteo del tracker: decía 12/12; real 10/11 ✓ + parcial #31) → **corregido en el índice**
- [ ] GDDs MVP con `/design-review` individual aprobado — **0/10 hechos**
- [ ] Reporte cross-GDD `/review-all-gdds` en `design/gdd/` — NO EXISTE

## Chequeos de calidad

- [x] MVP tier definido y mapeado a fases de la hoja de ruta
- [x] Dependencias bidireccionales (los GDDs recientes retro-alimentaron a los antiguos: Purificación↔Fuentes, Save/load↔#31)
- [ ] "Designed" es auto-declarado, no veredicto de revisión — todos los docs pendientes de review
- [ ] `fundamentos-essentials.md` diverge del estándar (6 secciones vs 8; "In Design" vs "Designed" en índice) — decisión consciente de fusión, pero el gate la marca

## Bloqueadores y camino a PASS (orden mínimo)

1. **Diseñar #17 Diálogo + escenas** (MVP restante) — próximo paso de diseño.
2. **`/design-review` en sesión fresca** sobre los 10 GDDs (empezando por la cadena crítica: tipos → Semi-Oscuro → Purificación, como indicó Producer).
3. **`/review-all-gdds`** (cross-consistency) y resolver/aceptar hallazgos.
4. *(Recomendado, no bloqueante)*: **`/prototype` del loop Semi-Oscuro** en Essentials antes de arquitectura (Producer+TD coinciden).
5. Decidir objeción CD sobre Fuentes: propongo **revertir a "la fuente empuja (+15), el ritual purifica"** excepto `Listo` que sí bebe directamente — lo presento a Angel.

## Concerns no bloqueantes (para Technical Setup)

- Art bible debe definir marco visual de las 30 formas regionales y paleta global (AD).
- Flashback de fuente: ¿genérico o por especie? (CD) → cerrar en GDD de Flashbacks #16.
- Fijar redacción canónica del Pilar 1 (CD).
- Revisar ruta de roles del hub: `references/agents/X.md` → real es `references/X.md` (TD/AD lo reportaron al leer sus roles).

---

**Chain-of-Verification**: 5 preguntas checadas contra los archivos — [TOOL ACTION] grep de #17 (confirmado MVP sin doc), grep Pilar 1 (1 variante), recount de MVP; veredicto **unchanged (FAIL)**. Corregido el tracker a 10/11 tras confirmar el error de conteo.

*Nota del orquestador: el FAIL es honesto por diseño del framework — "crear el archivo que falta y pasar el gate" fabricaría un PASS vacío. Se sigue el camino real.*
