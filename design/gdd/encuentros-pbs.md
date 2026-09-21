# Encuentros / PBS de Mapas

> **Status**: In Design
> **Author**: Sora (Hermes) — rol systems-designer / content-designer
> **Last Updated**: 2026-09-14
> **Last Verified**: 2026-09-14
> **Implements Pillar**: Pilar técnico — "Datos primero (PBS)" (el contenido de spawn vive en tablas declarativas, no en código), y Pilar 4 (los encuentros son el vehículo del loop Semi-Oscuro)

## Summary

**Encuentros/PBS de mapas** define el **roster de Pokémon que aparece en cada mapa** (rutas, hierba, agua, cuevas), sus **tasas por slot**, niveles, método de aparición y **condiciones** (clima, hora, surf, buceo), usando el sistema de **grupos de encuentros de Pokémon Essentials** vía archivos PBS. Es la fuente de verdad de *"qué puede aparecer aquí"*. Las **Zonas de Rencor (#27)** se apoyan en este roster para decidir qué se corrompe (su Regla 6/7).

> **Quick reference** — Layer: `Content` · Priority: `MVP` · Key deps: `Mapas y rutas`, `5 tipos nuevos`, `Zonas de Rencor` (consume este roster) · Formato: **PBS** (Convención "datos primero" de `pbs-tipos-nuevos.md`)

## Overview

Essentials ya trae un motor de encuentros robusto (`Encounters.pb`, `PokemonEncounters`) con grupos por mapa, slots y condiciones. Este sistema **no lo reescribe**: declara **cómo se poblan** esos grupos en Veridia, qué **reglas de diseño** gobiernan la distribución por zona/tipo/nivel, y cómo se conecta con el flag de corrupción de `zonas-de-rencor.md`. El trabajo real es de **datos (PBS)**, no de script.

## Player Fantasy

**Indirecta.** El jugador no "usa" este sistema: lo *experimenta* como la vida salvaje de Veridia. La fantasía que sirve es de **coherencia ecológica**: "en el bosque hay Planta/Bicho, en la mina Roca, en el mar Agua" — y, con las Zonas de Rencor, "donde hay dolor, esos animales sufren". La mecánica invisible sostiene el mundo creíble.

## Detailed Design

### Core Rules

**Regla 1 — Formato PBS nativo de Essentials.** Los encuentros se declaran por **mapa** con grupos y slots, siguiendo el esquema de Essentials (`PokemonGroup`, `Encounters`). Campos por entrada: `{ especie, método, nivel_min–nivel_max, tasa }`. No se inventa formato nuevo (respeta `pbs-tipos-nuevos.md`).

**Regla 2 — Roster temático por bioma.** Cada zona del mapa tiene una **paleta de especies coherente** con su geografía (GDD maestro 2.2):
| Bioma | Sesgo de tipos | Ejemplos del roster |
|---|---|---|
| Bosque Espina | Planta/Bicho + Fuego (espinas/brasas) | Caterpie–Butterfree, Oddish–Vileplume, Houndour |
| Riberas del Leteo | Agua/Bicho | Psyduck, Poliwag, Goldeen |
| Aldea Ocrida (niebla) | Fantasma/Planta | Gastly–Gengar, Duskull |
| Costa / Camino del Faro | Agua/Volador | Wingull, Starly, Taillow |
| Puerto / Mar | Agua (surf/buceo) | Wailmer, Carvanha, Feebas |
| Estribaciones / Mina Vetro | Roca/Acero/Sinal | Aron, Rhyhorn, Magnemite, Baltoy |
| Praderas Sombrías | Normal/Planta | Sentret, Bidoof, Shroomish |
| Ciudad Nepente (norte) | Volador/Hada | Hoothoot–Noctowl, Togepi |

**Regla 3 — Métodos de encuentro Essentials.** Se usan los métodos estándar: `Grass` (hierba alta/doble), `Water` (surf), `Fish` (caña), `Cave`/`Landmark`, `Headbutt` (árbol), `Shadow/Swarm` (enjambre por hora/evento). Cada mapa declara cuáles aplican.

**Regla 4 — Curva de nivel por zona (progresión).** Los niveles del roster escalan con la ruta/acto, alineados a la **curva de jefes 5→12→…→60** ya fijada en el proyecto: rutas tempranas Lv2–8, tardías y zonas de Rencor post-Acreedor hasta Lv40+. El nivel es dato por grupo, ajustable sin código.

**Regla 5 — Condiciones de spawn.** Un slot puede requerir: **hora** (día/noche — clave para tipos Espectral/estacionales), **clima** (los climas nuevos #7 activan/enmascaran apariciones, p.ej. Cielo Estrellado → Estelar), **flag de historia** (no aparecen ciertas especies hasta desbloquear un arco), y **método** (necesita Surf/Buceo/Caña). 

**Regla 6 — Interfaz con Zonas de Rencor.** Cada **mapa** expone a `zonas-de-rencor.md`:
- Su **roster corruptible** = las especies declaradas aquí que sean de las **18 originales** (los 5 tipos nuevos nunca se corrompen).
- El flag `semi_oscuro_pct` del mapa se aplica **sobre** el resultado de este roster: si un encuentro sale "Semi-Oscuro", la especie viene de esta tabla.
- **Este sistema NO marca estados**; entrega la especie base. La conversión a estado es de #27/#5.

**Regla 7 — Legibilidad del roster.** El roster se documenta por zona en una **tabla PBS legible** (para balance y QA), no solo en binario de datos. Herramienta: hoja de cálculo/CSV que se exporta a los archivos PBS de Essentials.

### States and Transitions

No tiene estados dinámicos propios; su contenido cambia por **flags de progresión**:

| Estado del roster | Condición | Transición |
|---|---|---|
| `Inicial` | Inicio de partida, ruta temprana | Rosters de zonas accesibles |
| `Expandido` | Desbloqueado Surf/Buceo/horas | Se activan slots de agua/surf/buceo |
| `Condicionado por arco` | Flag de historia | Especies/eventos aparecen o desaparecen |
| `Corruptible` (overlay #27) | Mapa en Zona de Rencor | Roster local + flag `semi_oscuro_pct` |

### Interactions with Other Systems

| Sistema | Dirección | Qué fluye |
|---|---|---|
| **Mapas y rutas (#26)** | Depende de este | Cada mapa referencia su grupo de encuentros por ID |
| **5 tipos nuevos** | Lee | Especies de tipos nuevos solo aparecen vía eventos/guardianes, NO en spawn silvestre común |
| **Zonas de Rencor (#27)** | Alimenta a este | Roster corruptible base para el flag de corrupción |
| **Climas nuevos (#7)** | Modifica este | Clima activa/enmascara slots (Regla 5) |
| **Combate base (#11)** | Aporta a este | El encuentro generado se resuelve en combate |
| **Pokédex Veridia (#20)** | Alimenta este | Qué especies pueden verse/registrarse |
| **Objetos clave (#23)** | Aporta a este | Ítems raros como "slot" landmark (piedras evolutivas) |

### Formulas

**Fórmula de slot (`encounter_roll`):** al generarse un encuentro, Essentials hace roll ponderado sobre los slots del grupo por `rate`. Este sistema define los **pesos** para que la rareza de diseño se cumpla:

`P(especie_i) = rate_i / Σ rate_j` (sobre los slots activos por método/condición)

**Variables:**
| Variable | Símbolo | Tipo | Rango | Descripción |
|---|---|---|---|---|
| tasa del slot i | `rate_i` | int | 1–100 | Peso relativo de la especie i en el grupo |
| suma de tasas activas | `Σr` | int | — | Normalizador (Essentials exige sume 100 por método) |
| método activo | — | enum | grass/water/… | Filtra qué slots aplican |

**Ejemplo slot Bosque Espina (grass):** Butterfree 25, Spinarak 25, Houndour 10, Teddiursa 10, Oddish 20, **Semi-Oscuro-check** sobre el 14% de #27. Un slot especial "Houndoom raro" 5% (landmark/noche) introduce al Acreedor-temático del arco.

*(Las tasas se ajustan con `balance-check`; este GDD fija el método, no los valores definitivos.)*

### Edge Cases

| Escenario | Comportamiento esperado | Rationale |
|---|---|---|
| **Roster con especies de tipo nuevo en spawn común** | Prohibido por diseño: los 5 nuevos solo por evento/guardián | Evita banalizar tipos únicos |
| **Método sin slots activos (p.ej. surf sin roster agua)** | Essentials fallback: no hay encuentro o usa grupo genérico | Debe evitarse declarando roster por método |
| **Flag de historia que oculta una especie ya vista en Pokédex** | Sigue registrada en Pokédex; solo no spawnea | No borrar progreso de captura |
| **Slot por hora y el jugador cambia de hora en combate** | El roster se fija al iniciar el encuentro (no muta a mitad) | Determinismo |
| **Zona de Rencor con roster solo de tipos nuevos** | `semi_oscuro_pct` efectivo 0 (nada corruptible) | Coherente con #27 Edge Case |
| **Dos métodos simultáneos (hierba + enjambre)** | Essentials prioriza; este GDD declara cuál gana para evitar ambigüedad | Control explícito |
| **Encounter rate Σ ≠ 100** | Validador PBS rechaza la tabla (checklist de datos) | Integridad de datos primero |

### Dependencies

| System | Direction | Nature |
|---|---|---|
| Mapas y rutas | Depende de este | Referencia de grupo por mapa |
| 5 tipos nuevos | Lee restricción | Qué NO spawnea común |
| Zonas de Rencor | Aporta a este | Roster corruptible |
| Climas nuevos | Condición de spawn | Activa slots |
| Combate base | Entrega a este | El encuentro generado |

### Tuning Knobs

| Parámetro | Valor actual | Rango seguro | Efecto al aumentar | Efecto al disminuir |
|---|---|---|---|---|
| Nº de slots por método | 8–12 | 4–20 | Roster diverso, difícil de encontrar especies clave | Pobre, pero focalizado |
| Frecuencia de especies temáticas de arco | 5–15% | 1–25% | Más presencia del conflicto del arco | Más sutil |
| Techo de nivel en ruta | según acto | 2–60 | Rutas duras | Progresión plana |
| Rareza de shinies/variantes | config Essentials | 1/4096–habilitado | Más sorteos | — |

### Visual/Audio Requirements

| Evento | Visual | Audio | Prioridad |
|---|---|---|---|
| Transición a combate salvaje | Sprite del método (hierba se agita, agua salpica) | Stinger de encuentro | Media (lo provee Essentials) |
| Encuentro Semi-Oscuro (via #27) | Aura (ver `menu-semi-oscuro.md`) | Stinger de corrupción | Alta |
| Encuentro legendario/guardián | Pantalla dedicada + BGM único | Motivo del guardián | Alta (post-juego) |

### Game Feel

**Feel reference:** la vida salvaje debe sentirse **ecológica y coherente** (no lista aleatoria). Entrar al bosque y ver Bicho/Planta "vende" el mundo; que aparezca un Semi-Oscuro ahí debe sentirse como **descubrir un animal enfermo en su hábitat**, no como un enemigo random. Anti-referencia: rosters incongruentes (fuego en el fondo del mar) que rompen inmersión.

### UI Requirements

| Información | Dónde | Cuándo | Condición |
|---|---|---|---|
| Nombre de especie al descubrirse | Pokédex (post-captura) | Al ver/capturar | Primera vez |
| Indicador de método (surf/hierba) | Feedback del motor | Al iniciar encuentro | Según método |

### Cross-References

| This Document References | Target GDD | Element | Nature |
|---|---|---|---|
| Roster corruptible | `zonas-de-rencor.md` Regla 6/7 | Flag `semi_oscuro_pct` sobre este roster | Data dependency |
| Tipos no-comunes al spawn | `pbs-tipos-nuevos.md` | Los 5 nuevos no spawnean salvajes | Rule dependency |
| Formato de datos | `pbs-tipos-nuevos.md` | Convención PBS "datos primero" | Rule dependency |
| Mapa físico | `mapas-rutas` (#26) | ID de grupo por mapa | Data dependency |
| Niveles | Curva de jefes del proyecto (GDD maestro) | Escala por acto | Data dependency |

### Acceptance Criteria

- [ ] **GIVEN** un mapa con grupo de encuentros declarado, **WHEN** el jugador camina por hierba, **THEN** sale una especie del roster local con la probabilidad de sus `rate` (Σ=100).
- [ ] **GIVEN** un slot de agua, **WHEN** el jugador NO tiene Surf, **THEN** ese método no genera encuentros.
- [ ] **GIVEN** un slot con condición nocturna, **WHEN** es de día, **THEN** esa especie no aparece (y viceversa).
- [ ] **GIVEN** un mapa marcado Zona de Rencor (`semi_oscuro_pct>0`), **WHEN** spawnea una especie original, **THEN** puede salir Semi-Oscuro según el cálculo de #27; una especie de tipo nuevo NUNCA.
- [ ] **GIVEN** una tabla PBS con `Σ rate ≠ 100`, **WHEN** se valida, **THEN** el importador la rechaza con error por mapa.
- [ ] **GIVEN** una ruta de acto tardío, **WHEN** se generan encuentros, **THEN** sus niveles caen dentro del rango del acto (curva del proyecto).
- [ ] **Datos primero**: todo el roster se edita en PBS/CSV sin tocar Ruby (verificado por checklist de import).

### Open Questions

| Question | Owner | Deadline | Resolution |
|---|---|---|---|
| **¿Spawnean variantes "oscuras" directamente o solo vía flag #27?** | systems-designer | Fase 3 | Propuesta: solo vía flag #27 (una sola fuente de corrupción) |
| **Roster definitivo de las 11 mazmorras + post-juego** | content-designer | Fase 4 | Pendiente de `mapas-rutas` (#26) |
| **Método de enjambre (swarm) para especies de arco** | game-designer | Fase 3 | Propuesta: usar `Swarm` para reforzar temáticamente cada zona |
| **Regla de shiny/variante**: ¿se toca el rate de Essentials o queda estándar? | game-designer | Fase 5 | Propuesta: estándar (no complicar) |

---

**Notas del orquestador (Sora) — decisiones corregibles:**
1. **Los 5 tipos nuevos NO spawnean en salvaje común** — solo por eventos/guardianes. Protege su rareza narrativa y evita que el spawn silvestre los diluya.
2. **La corrupción vive SOLO en #27**; este sistema entrega la especie base limpia. Así hay **una sola fuente de verdad** de "está corrupto" (no dos mecanismos).
3. **Niveles ligados a la curva de jefes del proyecto** (5→…→60) para que rutas y bosses no desentonen.

## Notas de consistencia

Este GDD **no define** estados Semi-Oscuro (#5) ni el % de corrupción (#27); define **qué especies hay** y **a qué nivel/tasa**, sobre las cuales #27 aplica la conversión. Formato PBS = convención de `pbs-tipos-nuevos.md`.
