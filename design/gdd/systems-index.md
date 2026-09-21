# Systems Index: Pokémon — Cenizas y Estrellas

> **Status**: Draft
> **Created**: 2026-09-12
> **Last Updated**: 2026-09-12
> **Source Concept**: GDD.md (raíz del proyecto, export narrativo de DeepSeek)
> **Stack**: RPG Maker XP (RGSS) + Pokémon Essentials 21.1 + Maker Studio + plugin PE21.1

---

## Overview

Cenizas y Estrellas es un RPG de Pokémon narrativo-emocional que subvierte la fórmula clásica (el protagonista es un artista, no un aspirante a Campeón). Su profundidad mecánica está en **tres frentes integrados a la historia**: (1) cinco tipos nuevos ligados a cinco fuerzas primordiales, (2) el sistema **Semi-Oscuro** + purificación (la feature estrella, herencia de Colosseum/XD), y (3) formas regionales oscuras + climas/habilidades nuevas. Sobre la base de Essentials, el trabajo real es de **datos (PBS) primero, scripts después**, con una hoja de ruta ya acordada en 8 fases (Fase 0 = cimientos → Fase 7 = post-juego).

Este índice descompone el GDD narrativo en sistemas diseñables y construibles, ordenados por dependencia y prioridad para alimentar el pipeline del framework Game Studio.

---

## Systems Enumeration

| # | System Name | Category | Priority | Status | Design Doc | Depends On |
|---|-------------|----------|----------|--------|------------|------------|
| 1 | Motor base RMXP + Essentials 21.1 | Core | MVP | Designed | [fundamentos-essentials.md](fundamentos-essentials.md) | (ninguno) |
| 2 | Pipeline PBS (datos primero) | Core | MVP | Designed | [pbs-tipos-nuevos.md](pbs-tipos-nuevos.md) | Motor base |
| 3 | Integración Maker Studio + plugin PE21.1 | Core | MVP | Designed | [fundamentos-essentials.md](fundamentos-essentials.md) | Motor base |
| 4 | 5 tipos nuevos (Luz, Espectral, Cristal, Estelar, Oscuro) | Gameplay | MVP | Designed | [pbs-tipos-nuevos.md](pbs-tipos-nuevos.md) | Pipeline PBS |
| 5 | Sistema Semi-Oscuro | Gameplay | MVP | Designed | [semi-oscuro.md](semi-oscuro.md) | 5 tipos nuevos |
| 6 | Purificación (medidor + Altar/Menhir/Piedra) | Gameplay | MVP | Designed | [purificacion.md](purificacion.md) | Semi-Oscuro |
| 7 | Climas nuevos (4) | Gameplay | Alpha | Not Started | — | 5 tipos nuevos |
| 8 | Habilidades nuevas (10) | Gameplay | Alpha | Not Started | — | 5 tipos nuevos |
| 9 | Megaevolución (Silas, Sombra) | Gameplay | Vertical Slice | Not Started | — | Combate base |
| 10 | 30 formas regionales + evolución especial | Gameplay | Alpha | Not Started | — | 5 tipos nuevos |
| 11 | Combate base (turnos, captura) | Gameplay | MVP | Designed | [fundamentos-essentials.md](fundamentos-essentials.md) | Motor base |
| 12 | Cuaderno de Viaje (quest log + Marcas + flashbacks) | Narrative | MVP | Designed | [cuaderno-de-viaje.md](cuaderno-de-viaje.md) | Motor base |
| 13 | Marcas de Acreedores (progresión narrativa) | Progression | Vertical Slice | Not Started | — | Cuaderno |
| 14 | Actos/Capítulos (8 Acreedores + 5 arcos post-juego) | Narrative | Vertical Slice | Not Started | — | Marcas |
| 15 | Rival Kael (encuentros con niveles) | Narrative | Alpha | Not Started | — | Actos/Capítulos |
| 16 | Flashbacks jugables (Saga joven) | Narrative | Alpha | Not Started | — | Actos/Capítulos |
| 17 | Diálogo + escenas (por evento) | Narrative | MVP | Not Started | — | Actos/Capítulos |
| 18 | Registro de historia/personajes | UI | Full Vision | Not Started | — | Diálogo |
| 19 | Menú principal / HUD | UI | Vertical Slice | Not Started | — | Motor base |
| 20 | Pokédex Veridia (entradas narrativas) | UI | Full Vision | Not Started | — | 5 tipos nuevos |
| 21 | Mapa de región | UI | Vertical Slice | Not Started | — | Motor base |
| 22 | Menús combate Semi-Oscuro (medidor, purificación) | UI | MVP | Designed | [menu-semi-oscuro.md](menu-semi-oscuro.md) | Semi-Oscuro, Purificación |
| 23 | Objetos clave (evolución + reliquias) | Economy | Alpha | Not Started | — | 5 tipos nuevos |
| 24 | Economía base (dinero, tiendas, MT/MO) | Economy | Alpha | Not Started | — | Motor base |
| 25 | Save/load + variables de estado | Persistence | MVP | Designed | [save-load-variables.md](save-load-variables.md) | Motor base |
| 26 | Mapas y rutas (6 zonas + 11 mazmorras + post-juego) | Content | Vertical Slice | Not Started | — | Maker Studio |
| 27 | Zonas de Rencor (spawn semi-oscuros) | Content | MVP | Designed | [zonas-de-rencor.md](zonas-de-rencor.md) | Semi-Oscuro, Mapas |
| 28 | Encuentros/PBS de mapas | Content | MVP | Designed | [encuentros-pbs.md](encuentros-pbs.md) | Mapas |
| 29 | Música (exploración/batalla/clímax) | Audio | Full Vision | Not Started | — | (ninguno) |
| 30 | Fonógrafo + grabación (prueba de Orfeo) | Audio | Alpha | Not Started | — | Actos/Capítulos |
| 31 | Fuentes de la Verdad (7 mini-mazmorras con puzzle + acceso directo; verdad + bautismo) | Content/Narrative | MVP* | Designed | [fuentes-de-la-verdad.md](fuentes-de-la-verdad.md) | Purificación, Flashbacks, Cuaderno |

*\*#31: MVP parcial (Fuentes 1–2 para validar el loop exploración→sanación); Fuentes 3–7 en Alpha/Vertical Slice por capítulo.*

**Nota:** los sistemas marcados como "Inferido" (11, 17, 18, 22, 24, 25, 28) no se listan explícitamente como sistemas separados en el GDD, sino que vienen implícitos por la base de Essentials o por ser infraestructura que todo RPG de Pokémon necesita. Se incluyen porque el framework exige que sean diseñados/verificados explícitamente.

---

## Categories

| Category | Description | Systems de CyE |
|----------|-------------|----------------|
| **Core** | Fundamento del motor y pipeline de datos | Motor base, Pipeline PBS, Maker Studio |
| **Gameplay** | Lo que hace divertido al juego | 5 tipos, Semi-Oscuro, Purificación, Climas, Habilidades, Mega, Formas regionales, Combate base |
| **Progression** | Crecimiento del jugador/journey | Marcas de Acreedores |
| **Narrative** | Historia y entrega de diálogo | Cuaderno, Actos/Capítulos, Kael, Flashbacks, Diálogo |
| **UI** | Información al jugador | Menú/HUD, Pokédex, Mapa, Menús Semi-Oscuro, Registro |
| **Economy** | Recursos y objetos | Objetos clave, Economía base |
| **Persistence** | Guardado y continuidad | Save/load + variables |
| **Content** | Mapas, encuentros, mazmorras | Mapas/rutas, Zonas de Rencor, Encuentros PBS |
| **Audio** | Música y efectos | Música, Fonógrafo |

---

## Priority Tiers

Los tiers se mapean a la hoja de ruta ya acordada en el GDD (Fases 0-7).

| Tier | Definición | Mapeo a hoja de ruta CyE |
|------|------------|--------------------------|
| **MVP** | Necesario para validar el loop core (capturar → purificar → vencer primer Acreedor). | **Fase 0-3**: cimientos + 5 tipos + Semi-Oscuro/purificación + Silas/Cuaderno |
| **Vertical Slice** | Experiencia completa y pulida de una zona (el demo Cap.1-3). | **Fase 4** (Acreedores) + conexión con la feature estrella |
| **Alpha** | Mecánicas completas en forma cruda, placeholders OK. | **Fase 5-6** (clímax, 30 formas, climas/habilidades) |
| **Full Vision** | Pulido, edge cases, contenido completo. | **Fase 7** (post-juego + UI final + audio) |

**Razones de colocación clave (experiencia de jugador, no solo técnica):**
- **Semi-Oscuro en MVP** — es la feature estrella: sin ella el juego pierde identidad (Pilar 4: "mecánicas integradas a la narrativa"). El primer loop completo del jugador es capturar un Growlithe Oscuro → purificarlo → aprender su movimiento exclusivo. Si esto no funciona, no hay juego.
- **Purificación en MVP** — es la recompensa emocional del loop; sin purificar, la captura semi-oscura no tiene sentido (Pilar 1: "sana heridas, no solo gana batallas").
- **Cuaderno en MVP** — es el objeto que hace de puente narrativo y mecánico (quest log + Marcas + flashbacks). Sin él, el jugador no sabe por qué está haciendo lo que hace (Pilar 2: "el legado como tema central").
- **5 tipos nuevos en MVP** — son el cimiento de datos: Semi-Oscuro (Oscuro), purificación (movimientos Luz/Cristal/Estelar), y las Mekánicas dependen todos de ellos.
- **Megaevolución en Vertical Slice** — es el clímax emocional (Mega Absol vs Mega Houndoom), no el loop core; puede esperar a que el resto funcione.

---

## Dependency Map

Orden de construcción de arriba a abajo.

### Foundation Layer (sin dependencias)

1. **Motor base RMXP + Essentials 21.1** — andamiaje de todo.
2. **Música** — independiente del resto; puede tratarse en paralelo durante toda la producción.

### Core Layer (depende de foundation)

3. **Pipeline PBS** — depende de: motor base. Convención "datos primero".
4. **Integración Maker Studio** — depende de: motor base.
5. **Combate base** — depende de: motor base (lo provee Essentials, hay que configurarlo).
6. **Save/load + variables** — depende de: motor base.

### Feature Layer (depende de core)

7. **5 tipos nuevos** — depende de: pipeline PBS.
8. **Climas nuevos** — depende de: 5 tipos.
9. **Habilidades nuevas** — depende de: 5 tipos.
10. **Semi-Oscuro** — depende de: 5 tipos (usa tipo Oscuro puro).
11. **Purificación** — depende de: Semi-Oscuro.
12. **30 formas regionales** — depende de: 5 tipos + evoluciones.
13. **Cuaderno de Viaje** — depende de: save/load + variables.
14. **Mapas y rutas** — depende de: Maker Studio.
15. **Economía base** — depende de: motor base.

### Presentation Layer (depende de features)

16. **Menú principal / HUD** — depende de: motor base + combate.
17. **Menús Semi-Oscuro** — depende de: Semi-Oscuro + purificación.
18. **Marcas de Acreedores** — depende de: Cuaderno.
19. **Actos/Capítulos** — depende de: Marcas.
20. **Diálogo + escenas** — depende de: Actos/Capítulos.
21. **Zonas de Rencor** — depende de: Semi-Oscuro + Mapas.
22. **Mapa de región** — depende de: Mapas.
23. **Objetos clave** — depende de: 5 tipos + formas regionales.

### Polish Layer (depende de todo)

24. **Megaevolución** — depende de: combate base + clímax narrativo.
25. **Kael** — depende de: Actos/Capítulos.
26. **Flashbacks jugables** — depende de: Actos/Capítulos + save/load.
27. **Pokédex Veridia** — depende de: 5 tipos + encuentros.
28. **Registro historia/personajes** — depende de: Diálogo.
29. **Fonógrafo** — depende de: Actos/Capítulos (prueba de Orfeo).

---

## Recommended Design Order

Combinando orden de dependencia + tier de prioridad. Diseñar en este orden; sistemas independientes del mismo layer pueden diseñarse en paralelo.

| Order | System | Priority | Layer | Agent(s) | Est. Effort |
|-------|--------|----------|-------|----------|-------------|
| 1 | Motor base RMXP + Essentials 21.1 | MVP | Foundation | technical-director | M |
| 2 | Pipeline PBS | MVP | Core | systems-designer | S |
| 3 | Integración Maker Studio | MVP | Core | technical-director | S |
| 4 | 5 tipos nuevos | MVP | Feature | game-designer | M |
| 5 | Combate base (configuración) | MVP | Core | gameplay-programmer | M |
| 6 | Semi-Oscuro | MVP | Feature | game-designer | L |
| 7 | Purificación | MVP | Feature | game-designer | L |
| 8 | Menús Semi-Oscuro | MVP | Presentation | ui-programmer | M |
| 9 | Cuaderno de Viaje | MVP | Feature | narrative-director | M |
| 10 | Save/load + variables | MVP | Core | lead-programmer | S |
| 11 | Mapas y rutas | VS | Feature | level-designer | L |
| 12 | Marcas de Acreedores | VS | Presentation | game-designer | S |
| 13 | Actos/Capítulos | VS | Presentation | narrative-director | L |
| 14 | Diálogo + escenas | MVP | Presentation | writer | M |
| 15 | Zonas de Rencor | MVP | Content | systems-designer | M |
| 16 | Megaevolución | VS | Polish | gameplay-programmer | M |
| 17 | Climas nuevos | Alpha | Feature | systems-designer | M |
| 18 | Habilidades nuevas | Alpha | Feature | systems-designer | M |
| 19 | 30 formas regionales | Alpha | Feature | game-designer | L |
| 20 | Objetos clave | Alpha | Economy | game-designer | M |
| 21 | Kael | Alpha | Narrative | writer | S |
| 22 | Flashbacks jugables | Alpha | Narrative | narrative-director | L |
| 23 | Economía base | Alpha | Economy | game-designer | S |
| 24 | Mapa de región | VS | UI | ui-programmer | S |
| 25 | Menú principal / HUD | VS | UI | ui-programmer | M |
| 26 | Pokédex Veridia | Full Vision | UI | ui-programmer | M |
| 27 | Registro historia/personajes | Full Vision | UI | writer | S |
| 28 | Música | Full Vision | Audio | sound-designer | L |
| 29 | Fonógrafo | Alpha | Audio | audio-director | S |

---

## Circular Dependencies

- [None found]

No se detectaron ciclos en el grafo de dependencias. El único acoplamiento a vigilar es **Semi-Oscuro ↔ Purificación ↔ Zonas de Rencor**: forman una tríada íntimamente ligada (no circular, pero de frontera difusa). Se diseñan como un bloque cohesionado (Orders 6-8 + 15) para no dejar contratos sin definir entre ellos.

---

## High-Risk Systems

| System | Risk Type | Risk Description | Mitigation |
|--------|-----------|-----------------|------------|
| **Semi-Oscuro + Purificación** | Design + Technical | Feature estrella heredada de XD; el estado "Oscuro puro" rompe la lógica normal de tipos/EXP/evo en Essentials. Si no está bien diseñada, el loop core se cae. | **Prototipar primero** (Fase 2 de la hoja de ruta); validar con un Pokémon de cada tipo la captura→purificación→movimiento exclusivo antes de escalar a 50. |
| **5 tipos nuevos** | Design | Tablas de efectividad propias fáciles de desbalancear; afectan a todo el resto. | Testeo exhaustivo contra la matriz de tipos oficial; iterar tablas con `game-studio/balance-check`. |
| **Flashbacks jugables** | Technical + Scope | RPG Maker XP no soporta "decisiones que afectan el presente" de forma nativa; requiere variables de estado persistentes y diseño de ramas. | Acotar a 3 flashbacks clave; usar variables de save (ya cubiertas por save/load). |
| **Story muy larga (52h)** | Scope | Riesgo de creep y burnout en solitario. | Dividir en capítulos con objetivos cerrados (ya estructurado en el GDD); MVP Cap.1-3 como checkpoint de demo. |
| **Sprites personalizados** | Scope | 30 formas regionales + 5 tipos + variantes legendarias = carga de arte enorme. | Placeholders hasta Fase 2 (principio ya acordado); recursos de comunidad. |

---

## Progress Tracker

| Metric | Count |
|--------|-------|
| Total systems identified | 31 |
| Design docs started | 11 |
| Design docs reviewed | 0 |
| Design docs approved | 0 |
| MVP systems designed | 10/11 ✓ (falta #17 Diálogo+escenas — error de conteo corregido en gate-check 2026-09-14) |
| Vertical Slice systems designed | 0/6 |

*(11 sistemas MVP = Orders 1-10 + 14-15. 6 sistemas VS = Orders 11-13, 16, 24-25.)*

---

## Next Steps

- [x] Enumeración de sistemas aprobada por el usuario
- [ ] Diseñar los sistemas MVP primero (usar `game-studio/design-system [sistema]`)
- [ ] Ejecutar `game-studio/design-review` en cada GDD completado
- [ ] Ejecutar `game-studio/gate-check systems-design` cuando los MVP estén diseñados
- [ ] Validar los sistemas de alto riesgo (Semi-Oscuro) con `game-studio/prototype` antes de comprometer producción
- [ ] Siguiente fase del framework: `game-studio/create-architecture` + ADRs sobre este índice