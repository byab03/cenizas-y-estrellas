# Fuentes de la Verdad

> **Status**: In Design
> **Author**: Sora (Hermes) — rol game-designer / narrative-director
> **Last Updated**: 2026-09-14
> **Last Verified**: 2026-09-14
> **Implements Pillar**: Pilar 1 — "Sana heridas, no solo ganes batallas" (la sanación tiene un lugar sagrado *además* del altar) + Pilar 2 — "El legado como tema central" (cada fuente es un recuerdo oculto de Saga: explorar Veridia es conocerla a ella)

## Summary

Las **Fuentes de la Verdad** son un conjunto de **7 manantiales sagrados ocultos en catacumba-mazmorra** (mini-mazmorra con puzzle temático; tras completarla, acceso directo a la Sala de la Fuente), una por gran zona de Veridia. Cada fuente guarda una **verdad oculta sobre el pasado de Saga** (un flashback que el Cuaderno archiva) y tiene un **efecto mecánico**: al "bautizar" a tu equipo, **avanza el medidor de purificación de todos los Semi-Oscuros activos** (+15) y **purifica automáticamente** a cualquier Pokémon ya en estado `Listo`. Son el coleccionable espiritual del juego: quien explora a fondo encuentra tanto lore como atajo.

> **Quick reference** — Layer: `Content` + `Narrative` · Priority: **MVP** (al menos la Fuente 1–2 para validar el loop) · Key deps: `Purificación`, `Semi-Oscuro`, `Cuaderno`, `Flashbacks`, `Save/load` · Hereda el naming canónico: **Menhir de la Verdad** (Ruta 5, maestro) = una de las 7 entradas ya existía.

## Overview

El GDD maestro ya plantaba la semilla: el **Menhir de la Verdad** en Praderas Sombrías y el **Altar del Perdón** en la Cueva del Origen. Este sistema los unifica en una **serie** — las Fuentes — y las esconde donde debe estar lo sagrado: **debajo**. Catacumbas, criptas, pozos secos, grutas tras cascadas. El jugador las halla por señales débiles (Silas se niega a avanzar / se detiene; grietas que exhalan vapor; agua que suena bajo el suelo).

**Distinción crítica con el Altar del Perdón:** el Altar es donde el jugador *elige* purificar con solemnidad (ritual, uno por uno). La Fuente es donde el mundo *te regala* purificación: no hay ritual, no hay uno por uno — es un bautismo colectivo, accidental-grato, ligado a descubrir la verdad. **El altar sana lo que decides sanar; la fuente sana lo que descubres.**

## Player Fantasy

**El peregrino que encuentra lo que nadie buscó.** La fantasía es de **descubrimiento sagrado**: detrás de una pared falsa, una escalera bajando, oscuridad, y de pronto luz y agua — un lugar que Saga conocía y calló. Beber de ahí es dos cosas a la vez: *entenderla mejor* (verdad) y *sanar junto a ella* (mecánica). Referencia: los santuarios ocultos de *Hyper Light Drifter* y las criptas de *Hollow Knight* — el mapa guarda secretos que premian curiosidad con poder Y con historia.

## Detailed Design

### Core Rules

**Regla 1 — Siete fuentes, una por gran zona.** Cada zona geográfica (maestro §2.2 + post-juego) esconde una:

| # | Zona | Catacumba | Verdad de Saga que guarda | Efecto extra |
|---|---|---|---|---|
| 1 | Bosque Espina | Cripta de raíces bajo el bosque | Saga de niña, primera vez que vio un Pokémon sufrir — eligió escucharlo en vez de huir | Desbloquea Silas-pista-forever en la zona |
| 2 | Valle del Leteo | Pozo seco bajo los sauces | Por qué Saga y Kael se separaron | Fuente donde el Menhir de la Verdad ya existía (unificación) |
| 3 | Costa | Cripta inundada bajo el faro viejo | La noche que Saga grabó la canción que Orfeo nunca terminó | Da la partitura (pista prueba de Orfeo) |
| 4 | Montañas | Galería minera sellada bajo Mina de Vetro | Por qué Saga escondió las Piedras de la Verdad en Veridia | Receta/crafteo desbloqueado de Piedra |
| 5 | Norte | Osario bajo el orfanato de Nepente | Que Saga adoptó en secreto a un niño del orfanato (revelación tardía, cap. 7+) | Cambia diálogos de Marina |
| 6 | Centro/vereda a la Cueva | Gruta tras cascada en Bosque profundo | La enfermedad de Saga y su pacto con Aeon | Desbloquea flashbacks de la parte 3 |
| 7 | Cueva del Origen (post-juego) | **Fuente Madre** junto al Altar del Perdón | La última voluntad de Saga: "si lees esto, ya eres más de lo que yo fui" | Purifica TODO el equipo sin importar medidor (una sola vez) |

*(Las verdades concretas son editable por narrativa; el sistema fija que **cada fuente = 1 verdad + 1 efecto**.)*

**Regla 2 — Descubrimiento por señales, no por mapa.** Las fuentes NO aparecen en el minimapa ni dan marcador. Señales de proximidad: **Silas se detiene y mira al suelo** (primera vez por zona), vapor tenue, sonido de agua bajo los pies, inscripción partial en pared. Entrada oculta requiere a menudo una acción simple del mundo (Surf bajo cascada, Headbutt en árbol fósil, mover estela).

**Regla 3 — El bautismo (efecto mecánico).** Al interactuar con la fuente por primera vez:
1. Aparece el flashback de la verdad (secuencia corta, se archiva en Recuerdos).
2. **Todos los Semi-Oscuros en el equipo activo ganan +15** de medidor (cap 100).
3. Cualquier Pokémon en `Listo` (100) al momento del bautismo **se purifica al instante** — sin ritual, sin Altar: la fuente lo hace por ti.
4. El agua brilla (feedback permanente de que *esa* fuente ya fue bebida).

**Regla 4 — Una vez por partida, sin farmeo.** El efecto de medidor es **una sola vez por fuente** (flag por fuente). El jugador no puede "darse la vuelta y volver a bañar". Esto protege la economía del medidor (`semi-oscuro.md`) y convierte cada fuente en un **hito**, no un dispensador.

**Regla 5 — Sinergia de diseño (el +15 no es casual).** +15 equivale a ~5 combates o ~15 combates con participación moderada — lo suficiente para que encontrar la fuente **casi siempre empuje a un Semi-Oscuro a Listo**, pero no sustituye el trabajo. La Fuente Madre (7) es la excepción: purifica total, post-juego, una vez.

**Regla 6 — Purificación de fuente vs. purificación de altar.** Ambas producen el **mismo resultado de convergencia** (Regla 7b de `semi-oscuro.md`: forma regional si existe, tipo puro + cicatriz ✦ si no). La diferencia es de **rito**: el altar es elegido y solemne; la fuente es descubierta y compartida. El registro del Cuaderno anota el origen: *"Sanado en la Fuente de las Raíces"* vs *"Sanado en el Altar del Perdón"*. (Textura narrativa, no mecánica.)

**Regla 7 — Contrato de datos.** Cada fuente = fila en `fuentes.pb` (nuevo PBS): `{ id, zona, mapa_entrada, mapa_mazmorra, mapa_salafuente, condicion_descubrimiento, puzzle_id, flashback_id, efecto_medidor=15 }`. Los flags persisten vía `msg_fuentes_completadas` / `msg_fuentes_bautizadas` (listas CSV, estilo `save-load-variables.md`).

**Regla 8 — Cada fuente es una mini-mazmorra con puzzle; luego, acceso directo.** Estructura de visita:
1. **Primera vez:** la entrada oculta lleva a una **mini-mazmorra** (5–10 min) con **un puzzle temático de la memoria** (diseño propio por fuente, ver tabla de puzzles abajo) que culmina en la **Sala de la Fuente**. Resolverlo = beber = flashback + bautismo (Regla 3).
2. **Desde la segunda visita:** la catacumba **recuerda al jugador**: aparece un **paso directo** (escalera sellada que se abre / agua que se aparta / sendero que antes no estaba) desde el punto del mundo hacia la Sala de la Fuente — sin repetir el puzzle ni las salas. El acceso directo es **persistente** (flag por fuente).
3. Usos del reingreso: **revivir el flashback** desde la propia fuente, y — si el jugador prefiere — como **atajo de mundo** (la salida directa conecta de vuelta, útil post-juego).

**Puzzles propuestos por fuente (temáticos de la memoria, placeholder corregible):**
| Fuente | Puzzle | Mecánica que usa |
|---|---|---|
| 1 Raíces (Bosque Espina) | Guiar luciérnagas a sus nidos en orden de un canto | Observación/secuencia |
| 2 Leteo (Valle) | Reflejos: alinear espejos de agua para que el sendero aparezca en el reflejo, no en el suelo | Perspectiva |
| 3 Faro sumergido (Costa) | Secuencia de notas de la canción inacabada de Orfeo | Sonido/memoria |
| 4 Galería sellada (Mina) | Vagonetas: despejar el camino a la cripta | Empuje/logística |
| 5 Osario (Nepente) | Ordenar lápidas por fechas para que el muro se abra | Deducción/lore |
| 6 Cascada (Bosque profundo) | Puzzle de luz: prismas de Cristal guian un rayo al sello | Tipos nuevos (Cristal) |
| 7 Fuente Madre (Cueva del Origen) | **Sin puzzle**: la última voluntad de Saga se lee, no se resuelve | Gracia |

**Regla 9 — El flag de mazmorra es distinto del flag de bautismo.** Una fuente puede estar **completada** (puzzle resuelto, entrada abierta) y aún no **bautizada** si el jugador salió sin beber (raro, pero posible). Dos flags en `save-load`: `msg_fuentes_completadas` (abre acceso directo) y `msg_fuentes_bautizadas` (efecto +15/purificación único). El flashback se concede al **beber**, no al llegar.

### States and Transitions

| Estado de la fuente | Condición | Transición |
|---|---|---|
| `Oculto` | Nunca descubierta | Señal de Silas/entorno al entrar al tile cercano |
| `Mazmorra` | Descubierta, puzzle no resuelto | Entrar → mini-mazmorra con el puzzle |
| `Hollada` | Puzzle resuelto, aún no bebida (Sala de la Fuente accesible) | Al beber → Bautismo |
| `Completada` | Puzzle resuelto (flag) | Desbloquea **paso directo** mundo→Sala en todas las visitas futuras |
| `Bautizada` | Efecto aplicado, flashback archivado (flag) | Permanente (agua brillante, sin reuso del efecto) |

### Interactions with Other Systems

| Sistema | Dirección | Qué fluye |
|---|---|---|
| **Semi-Oscuro** | Escribe | +15 al medidor de cada SO en equipo activo |
| **Purificación** | Escribe | Purifica instantáneo de los `Listo` (mismo resultado, origen "fuente") |
| **Flashbacks jugables** | Aporta | Cada fuente desbloquea 1 flashback canónico |
| **Cuaderno** | Escribe | Nuevas entradas en "Recuerdos" + registro de origen de sanación |
| **Save/load** | Persiste | `msg_fuentes_bautizadas` (CSV de flags) |
| **Silas/Compañero** | Lee | Se detiene como pista de proximidad (extiende su rol R5 Cuaderno) |
| **Zonas de Rencor** | Alivia | La fuente 6–7 cerca de zonas de alta corrupción = alivio temático (dolor y gracia lado a lado) |
| **Encuentros/PBS** | Ninguna | Las fuentes no alteran rosters |

### Formulas

**Fórmula de bautismo (`fuente_gain`):**

`medidor_final = min(medidor_actual + G, 100)` por cada Semi-Oscuro en equipo activo.

**Variables:**
| Variable | Símbolo | Tipo | Rango | Descripción |
|---|---|---|---|---|
| Ganancia de fuente | `G` | int | 5–20 (def. **15**) | Empuje al medidor; único knob de balance |
| Medidor actual | `N` | int | 0–99 | Del Semi-Oscuro, estado `Semi-Oscuro` |
| Umbral | — | int | 100 | `Listo`; al cruzarlo aquí → purificación instantánea |

**Ejemplo:** Arcanine Semi-Oscuro con `N=88` + fuente (`G=15`) → `min(103,100)=100` → se purifica en el acto. Mismo con `N=40` → `55` (aún no; "casi", el jugador siente el empujón y vuelve al altar).

**Output Range:** con 7 fuentes, el jugador que explore TODO obtiene +90 de medidor global + 1 purificación total post-juego — un bonus de explorador, no un requisito de completion (el loop funciona sin ninguna fuente).

### Edge Cases

| Escenario | Comportamiento esperado | Rationale |
|---|---|---|
| **Equipo con >6 (cajas) — ¿reciben SO almacenados?** | Solo **equipo activo** recibe +15; cajas no | Evita "bautismo total" y premia viajar con ellos (vínculo, tema del juego) |
| **Pokémon purificado en fuentes: ¿pierde acceso a su ritual de altar?** | El resultado es idéntico (misma convergencia + cicatriz ✦); solo cambia el *registro* | Fuente = altar en eficacia; no castigar descubrir |
| **Todos los SO del equipo ya `Listo`** | Purificación múltiple en escena (hasta 6 destellos cortos, rápidos) | Momento espectacular, sin ritual uno por uno |
| **Ningún SO en el equipo** | Solo flashback + agua brillante (valor puro de lore) | La fuente siempre paga: la verdad ES la recompensa |
| **Fuente Madre (7) antes de completar las otras 6** | Funciona igual (post-juego, el jugador decidió saltar) | No gatear el final |
| **Reingresar a fuente bautizada** | Flashback revivible desde Cuaderno; agua sigue brillante; 0 efectos | Una vez por partida (Regla 4) |
| **Silas se detiene pero el jugador ignora** | La señal persiste al volver; sin penalización | No intrusivo, coherente con R5 Cuaderno |
| **Jefe corrupto garantizado cerca de fuente** | La fuente no lo purifica (no está en tu equipo) | Solo el ritual/Piedra lo sana |
| **Reingreso tras `Completada`** | Paso directo visible desde el punto del mundo (escalera abierta / agua apartada); el puzzle NO reaparece ni puede re-resolverse | Regla 8 — la catacumba recuerda |
| **Muerte/retirada dentro de la mazmorra** | Reaparece en la entrada de la mazmorra (no en el mundo); progreso del puzzle conservado en sesión | RMXP estándar; sin checkpoint interno |
| **Salió de la mazmorra con el puzzle a medias** | El puzzle resetea a su estado inicial al volver a entrar (sin guardar estado de puzzle) | Los puzzles son de una sesión; los hitos (flag completada) sí persisten |
| **Puzzle resuelto pero el jugador abandona sin beber** | Flag `Completada` puesto; en la próxima visita aparece directo en la Sala (no en la entrada) y puede beber | Regla 9: completada ≠ bautizada |

### Dependencies

| System | Direction | Nature |
|---|---|---|
| Purificación | Depende de este (resultado) | Mismo estado final que altar |
| Semi-Oscuro | Modifica | Escribe medidor |
| Flashbacks | Aporta a este | 7 verdades = 7 flashbacks canónicos |
| Cuaderno | Escribe | Recuerdos + registro de origen |
| Save/load | Depende | Flags de fuentes bautizadas |
| Mapas y rutas | Hospedan | Las catacumbas son mazmorras menores |

### Tuning Knobs

| Parámetro | Valor actual | Rango seguro | Efecto al aumentar | Efecto al disminuir |
|---|---|---|---|---|
| `G` (ganancia) | 15 | 5–20 | Fuentes casi-suficientes por sí solas (peligro) | Fuentes triviales |
| Nº de fuentes | 7 | 3–12 | Más lore/exploración, más carga de arte | Más escaso, más precioso |
| Fuente Madre: purificar todo | Sí | sí/no | Clímax generoso | Si no, ¿qué la hace única? |
| Señal de Silas | 1ª vez por zona | siempre/nunca | Más guiado | Más misterio puro |

### Visual/Audio Requirements

| Evento | Visual | Audio | Prioridad |
|---|---|---|---|
| Silas se detiene | Se planta + mira al suelo | Ladrido grave / silencio | Alta |
| Entrada a catacumba | Luz de vela, vapor, agua subterránea | Reverb de cueva + goteo | Alta |
| Revelación de la fuente | Claraboya + agua luminosa | Motivo de "verdad" (7 notas, motivo Luminark invertido) | **Crítica** |
| Bautismo | Destello dorado sobre cada SO del equipo (secuencia corta) | Coro suave / cuerdas | Alta |
| Flashback reproducido | Estilo visual de Saga joven (ya definido en maestro) | Tema del Cuaderno | Alta |
| Fuente ya bebida | Agua con brillo perenne + pétalos | — | Media |

### Game Feel

**Feel reference:** el momento debe ser **descender → contener el aliento → revelación**. La mazmorra es oscura y silenciosa, con el puzzle como "prueba de dignidad" antes de lo sagrado; al beber, luz cálida y el equipo "respira" contigo (cada SO brilla + medidor sube con sonido de campanas). El reingreso con paso directo debe sentirse como **volver a casa de una amiga**: la catacumba ya no te prueba, te recibe. Anti-referencia: que se sienta como "checkpoint de guardado con cutscene". Es un **sacramento descubierto**, no un fast-travel.

### UI Requirements

| Información | Dónde | Cuándo | Condición |
|---|---|---|---|
| Medidor sube +15 (vuela al chip) | Ficha + aura del SO | Al beber | Equipo con SO |
| "Purificado por la fuente" | TextBox de resultado | Al cruzar a 100 | Cualquier Listo |
| Verdad archivada | Cuaderno > Recuerdos | Post-flashback | Siempre |
| Origen de sanación | Cuaderno > Purificados ("Sanado en la Fuente X") | Al purificar | Registro |
| Nº de fuentes bebidas (0–7) | Cuaderno > Viaje (contador discreto, **sin mapa**) | Consultable | Para el explorador |

### Cross-References

| This Document References | Target GDD | Element | Nature |
|---|---|---|---|
| Medidor y estados | `semi-oscuro.md` | +15 al medidor, umbral 100 | Data dependency |
| Resultado de purificación | `purificacion.md` Regla 5 | Convergencia idéntica, origen "fuente" | Rule dependency |
| Ritual del altar (contraste) | `purificacion.md` Regla 3 | Fuente sin ritual; Altar solemne | Rule dependency |
| Flashbacks | maestro §historia Saga | 7 verdades canónicas | Content |
| Pista de Silas | `cuaderno-de-viaje.md` Regla 5 | Extiende rol a proximidad sagrada | Rule dependency |
| Flags de persistencia | `save-load-variables.md` | `msg_fuentes_bautizadas` | Data dependency |
| Piedras de la Verdad | `semi-oscuro.md` | Fuente 4 revela su origen | Lore |
| Altar del Perdón | `purificacion.md` | Fuente Madre junto a él (Cueva del Origen) | Content |

### Acceptance Criteria

- [ ] **GIVEN** un equipo con un SO en `N=40`, **WHEN** el jugador bebe de una fuente (`G=15`), **THEN** el medidor queda en 55, el flashback se archiva en Recuerdos y la fuente queda marcada `Bautizada`.
- [ ] **GIVEN** un SO en `Listo` al beber, **WHEN** termina el bautismo, **THEN** se purifica instantáneamente con resultado idéntico al altar (convergencia según Regla 7b) y el Cuaderno registra "Sanado en la Fuente…".
- [ ] **GIVEN** fuente ya bautizada, **WHEN** el jugador vuelve e interactúa, **THEN** puede revivir el flashback PERO no gana +15 ni purificaciones.
- [ ] **GIVEN** equipo con SO en cajas (no activo), **WHEN** se bebe, **THEN** solo los del equipo activo reciben el efecto.
- [ ] **GIVEN** el jugador cerca de una fuente oculta sin descubrir, **WHEN** entra al tile, **THEN** Silas muestra la señal la primera vez y hay pista ambiental (vapor/agua/inscripción).
- [ ] **GIVEN** ningún Semi-Oscuro en el equipo, **WHEN** se bebe, **THEN** igualmente se obtiene el flashback (la fuente siempre paga).
- [ ] **GIVEN** una fuente `Completada` (puzzle resuelto antes), **WHEN** el jugador vuelve desde el mundo, **THEN** aparece el paso directo a la Sala de la Fuente y NO se repite el puzzle.
- [ ] **GIVEN** un puzzle resuelto por primera vez, **WHEN** se culmina la mazmorra, **THEN** se marca `Completada` (persiste) aunque el jugador no beba ese día.
- [ ] **GIVEN** muerte dentro de la mazmorra, **WHEN** el jugador revive, **THEN** reaparece en la entrada de la mazmorra sin perder el flag de progreso global.
- [ ] **GIVEN** la Fuente Madre (7), **WHEN** se bebe una vez post-juego, **THEN** TODOS los SO del equipo (sin importar medidor) se purifican; no repetible.
- [ ] **Integridad PBS**: `fuentes.pb` valida que cada id de flashback, mapa_entrada y flag exista antes del build (checklist datos primero).
- [ ] **Performance**: secuencia de bautismo (hasta 6 destellos) no añade lag al combate siguiente.

### Open Questions

| Question | Owner | Deadline | Resolution |
|---|---|---|---|
| ¿Las 7 **verdades** son las definitivas de la tabla o quiere el usuario escribirlas? | narrative-director | Fase 4 | **RESUELTA (usuario 2026-09-14): las 7 verdades de la tabla quedan APROBADAS como canónicas** — son la fuente para escribir los flashbacks |
| ¿**Cada catacumba** es una mazmorra mini (5–10 min) o una pantalla (sala con puzzle simple)? | level-designer | Fase 2 | **RESUELTA (usuario 2026-09-14): mazmorra con puzzle; tras completarla una vez, acceso directo a la Sala de la Fuente en visitas futuras (Regla 8)** |
| ¿La señal de Silas también marca **otras** reliquias ocultas o SOLO fuentes? | game-designer | Fase 3 | Propuesta: solo fuentes sagradas (lo demás que se busque a mano) |
| ¿+15 aplica también a SO **de evento** capturados tardíamente? | systems-designer | Fase 3 | Sí — la regla no distingue origen del SO |

---

**Notas del orquestador (Sora) — decisiones corregibles:**
1. **"El altar sana lo que decides sanar; la fuente sana lo que descubres."** Es la frase-semilla: mantiene el ritual solemne intacto (Purificación #6) y da a las fuentes identidad propia = exploración+lore, sin canibalizarse.
2. **+15 y una-sola-vez** protegen la economía del medidor. El explorador voraz no "completa" el sistema; lo *acelera* y se lleva la historia.
3. **La Fuente Madre post-juego junto al Altar del Perdón** une los dos clímax de sagrado: quien llega al origen de Luminark con equipo aún corrupto recibe la gracia total.

## Notas de consistencia

No contradice a `purificacion.md`: la fuente es un **canal más de purificación** (como la Piedra de la Verdad), no un reemplazo del ritual. El estado/medidor sigue siendo propiedad de `semi-oscuro.md`. Prioridad: al menos las fuentes 1–2 entren en MVP para validar el loop de exploración→sanación; el resto es Alpha/Vertical Slice por capítulo.
