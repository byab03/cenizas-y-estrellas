# Sistema Semi-Oscuro

> **Status**: In Design
> **Author**: Sora (Hermes) — rol game-designer / systems-designer
> **Last Updated**: 2026-09-12
> **Last Verified**: 2026-09-12
> **Implements Pillar**: Pilar 4 — "Mecánicas integradas en la narrativa" (el Semi-Oscuro no es una mecánica aislada; es la manifestación del rencor de Los Nueve Ecos y el corazón del loop emocional)

## Summary

El sistema **Semi-Oscuro** convierte a Pokémon salvajes en criaturas corrompidas por la energía de Oscureos: tipo Oscuro puro, con 4 movimientos Oscuros elegidos al azar de una pool de 8, incapaces de ganar experiencia o evolucionar y con tasa de captura reducida. Son el "corazón cerrado" que el jugador debe abrir mediante la **purificación**, restituyendo su tipo original y un movimiento especial ligado a ese tipo. Es la feature estrella que define la identidad del juego (herencia de Colosseum/XD).

> **Quick reference** — Layer: `Feature` · Priority: `MVP` · Key deps: `5 tipos nuevos`, `Purificación`, `Zonas de Rencor`, `Cuaderno de Viaje`

## Overview

En combate salvaje dentro de una Zona de Rencor, un Pokémon tiene entre 5% y 12% de probabilidad de aparecer en estado **Semi-Oscuro**. En ese estado funciona como un "jefe menor": su tipo es exclusivamente Oscuro (nuevo, no Siniestro oficial), sus 4 movimientos son de una pool fija de 8 movimientos Oscuros, no gana EXP ni sube de nivel, no evoluciona ni megaevoluciona, y es un 20% más difícil de capturar (multiplicador 0.8). La única forma de "liberarlo" es purificarlo.

La **purificación** es un proceso de dos etapas: (1) **rellenar un medidor interno** mediante combates y pasos, y (2) **sellarlo en un lugar sagrado** (Altar del Perdón o Menhir de la Verdad) o con la Piedra de la Verdad. Al purificar, el Pokémon recupera su tipo y moveset originales y aprende un movimiento especial de purificación fijo según su tipo original.

## Player Fantasy

**Directo.** La fantasía emocional es de **salvación**: el jugador no captura un Pokémon corrompido para usarlo, sino que rescata a un ser atrapado en dolor perpetuo. La purificación debe sentirse como un acto de perdón/redención tangible, no como una transacción mecánica. Referencia: *Pokémon Colosseum* (la sensación de "snag + purify" como rescate, no como captura normal), y *Pokémon XD* (medidor de purificación como progreso de sanación). El medidor visible refuerza esta fantasía: el jugador *ve* al Pokémon sanar.

Sirve al Pilar 4: la mecánica es literalmente el arco emocional de los 8 Acreedores — cada Acreedor entrega su Marca tras purificar a un Pokémon que encarna su herida.

## Detailed Design

### Core Rules

**Regla 1 — Aparición.** En una Zona de Rencor, todo Pokémon salvaje tiene una probabilidad `P` ∈ [5%, 12%] de aparecer en estado Semi-Oscuro. El valor concreto de `P` lo define cada Zona de Rencor (ver sistema "Zonas de Rencor", GDD pendiente). Es independiente de la especie: cualquier Pokémon salvaje de la zona puede ser Semi-Oscuro.

**Regla 2 — Estado corrompido.** Mientras está Semi-Oscuro, el Pokémon:
- Su tipo es **Oscuro puro** (el tipo nuevo; se ignora su tipo original, incluido si tuviera tipos dobles).
- Posee **4 movimientos** elegidos al azar de la pool de 8 (Regla 3), sin sus movimientos de nivelación.
- **No gana EXP** ni sube de nivel (los puntos de EXP de combate se descartan).
- **No puede evolucionar** ni **megaevolucionar**.
- Tiene tasa de captura **reducida ×0.8**.

**Regla 3 — Pool de movimientos Oscuros.** Los 8 movimientos forman una pool; al aparecer, el Pokémon recibe 4 elegidos al azar (sin repetición). Pool:

| Movimiento | Categoría | Poder | Precisión | PP | Efecto |
|---|---|---|---|---|---|
| Tinieblas | Especial | 80 | 100 | 15 | Movimiento básico |
| Pulso Umbrío | Especial | 90 | 100 | 10 | Ignora Pantalla de Luz |
| Garra Sombra | Físico | 80 | 100 | 15 | Alta probabilidad de crítico |
| Onda de Rencor | Especial | 75 | 100 | 10 | 30% de confundir |
| Puño del Vacío | Físico | 85 | 95 | 10 | 20% de bajar Defensa |
| Niebla Abisal | Estado | — | 100 | 10 | Reduce precisión dos niveles |
| Auto-Rencor | Estado | — | — | 5 | Sacrifica 25% PS para maximizar Ataque/AtqEsp |
| Venganza Umbría | Físico | 100 | 100 | 5 | Poder ×2 si PS < 50% |

*(Regla de diseño: si el Pokémon va a ser un jefe de evento, el diseñador puede definir los 4 manualmente en vez de azar. Los movimientos no pueden repetirse.)*

**Regla 4 — Medidor de purificación (visible).** Cada Semi-Oscuro tiene un medidor de 0 a 100 puntos que se muestra en la pantalla del Pokémon (barra/progreso visible). Se llena:
- **+1 punto** por cada combate en el que el Pokémon esté en el equipo (aunque no participe).
- **+1 punto** por cada 256 pasos caminados (mientras esté en el equipo).
- **+3 puntos** si participa activamente en el combate (sale al campo).
- Al llegar a **100 puntos**, el medidor queda en 100 (no se pasa) y el Pokémon queda "listo para purificar".

**Regla 5 — Sellado de purificación.** Con el medidor en 100, la purificación se completa llevando al Pokémon a:
- El **Altar del Perdón** (Cueva del Origen, nivel 3), o
- cualquier **Menhir de la Verdad** (altares menores en rutas).

**Regla 6 — Piedra de la Verdad (atajo).** La Piedra de la Verdad purifica **instantáneamente y sin necesidad de medidor** (salta todo el proceso). Es un objeto raro y valioso; se contabiliza su uso (se consume).

**Regla 7 — Efecto de la purificación.** Al purificar:
1. El Pokémon **conserva el tipo Oscuro como tipo secundario permanente** (su tipo primario pasa a ser su tipo original). La purificación no revierte la corrupción: la transforma en una cicatriz integrada. (Decisión del usuario, alineada con el tema central: "las cicatrices se honran en lugar de borrarse".)
2. **Pierde los 4 movimientos Oscuros** de la pool.
3. **Recupera su moveset de nivelación completo** (los movimientos que tendría por su nivel).
4. **Aprende el movimiento especial de purificación** fijo según su tipo original (tabla de 18 tipos, ver sección "Movimiento especial").
5. A partir de ahí, gana EXP, evoluciona y megaevoluciona con normalidad.

**Regla 7b — Destino tras purificar (convergencia condicional).** Tras purificar:
- Si la especie **tiene** una de las 30 formas regionales oscuras → el Pokémon converge a esa forma (tipo original + Oscuro secundario).
- Si la especie **no tiene** forma regional → el Pokémon **vuelve a su tipo original puro** (sin Oscuro secundario).
- En ambos casos, aprende el **movimiento especial de purificación**, que es **exclusivo del purificado**: la forma regional obtenida por adaptación natural nunca lo aprende. Esta es la marca distintiva del sanado.

**Regla 8 — Movimiento especial por tipo original.** Tras purificar, el Pokémon aprende un movimiento único según su tipo original. Ver la tabla completa en `Design Data` (18 entradas, una por tipo). El movimiento se conoce además del moveset; si el Pokémon ya tiene 4 movimientos, uno debe olvidarse (UI estándar de "olvidar movimiento").

### States and Transitions

| State | Entry Condition | Exit Condition | Behavior |
|-------|-----------------|----------------|----------|
| `Normal` | (estado habitual de un Pokémon salvaje/criado) | Aparecer en Zona de Rencor con `P`% | Comportamiento estándar |
| `Semi-Oscuro` | Spawn con flag corrompido en Zona de Rencor | Purificado (medidor 100 + sellado) | Tipo Oscuro puro, 4 movs pool, sin EXP/evo/mega, captura ×0.8 |
| `Listo` (medidor 100) | Medidor alcanza 100 | Sellado en Altar/Menhir o Piedra | Sigue en estado Semi-Oscuro pero elegible para purificar |
| `Purificado` | Sellado completado | — | Tipo original + Oscuro secundario (forma regional oscura), moveset completo + movimiento especial, EXP/evo normal |

Transiciones inválidas (a especificar como edge cases): Semi-Oscuro no puede evolucionar, ni megaevolucionar, ni ganar EXP en ningún punto antes de `Purificado`.

### Interactions with Other Systems

| Sistema | Dirección | Interface (datos que fluyen) |
|---|---|---|
| **5 tipos nuevos** | Depende de este | El Semi-Oscuro usa el tipo `Oscuro` (nuevo); los movimientos de purificación usan Luz/Cristal/Estelar/Oscuro |
| **Purificación** | Extiende | El medidor y el sellado son el subsistema de purificación (se diseña en detalle en su propio GDD; aquí se define el estado y las reglas de transición) |
| **Zonas de Rencor** | Depende de este | Define `P`% de aparición y dónde ocurre |
| **Cuaderno de Viaje** | Consume | Registra qué Pokémon purificaste y por qué Acreedor (Marcas) |
| **Captura (Essentials)** | Modifica | Aplica multiplicador ×0.8 a la tasa de captura |
| **Combate (Essentials)** | Modifica | Bloquea ganancia EXP/EVs para Semi-Oscuros |

## Formulas

### Probabilidad de aparición Semi-Oscuro

```
P_semi = zone_rate          (un valor fijo por zona, configurable)
```

| Variable | Type | Range | Source | Description |
|----------|------|-------|--------|-------------|
| zone_rate | float | 0.05–0.12 | Zona de Rencor | Probabilidad de spawn corrompido (ej. Bosque Espina 0.08, Mansión 0.10) |

**Expected output range**: 5% a 12% por encuentro salvaje.
**Edge case**: clamp a [0.05, 0.12]; valores fuera de rango se normalizan al límite.

### Tasa de captura efectiva

```
capture_rate_eff = base_capture_rate * 0.8
```

| Variable | Type | Range | Source | Description |
|----------|------|-------|--------|-------------|
| base_capture_rate | float | (de la especie) | pokemon data | Tasa base del Pokémon |

**Expected output range**: 80% de la tasa normal.
**Edge case**: el ×0.8 se aplica siempre que el estado sea Semi-Oscuro; al purificar, la tasa vuelve al 100% base.

### Medidor de purificación

```
medidor += 1   (por combate en equipo)
medidor += 1   (por cada 256 pasos en equipo)
medidor += 3   (por participar activamente en combate)
medidor = clamp(medidor, 0, 100)
```

| Variable | Type | Range | Source | Description |
|----------|------|-------|--------|-------------|
| medidor | int | 0–100 | estado del Pokémon | Progreso de sanación |

**Expected output range**: 0 a 100 (saturado).
**Example**: un Growlithe Oscuro que participa en 20 combates (×3=60) + 30 combates en equipo sin salir (×1=30) + caminando 2,560 pasos (×1=10) alcanzaría 100 en ~20 combates activos.

## Design Data — Movimientos de purificación por tipo original

| Tipo original | Movimiento | Tipo | Categoría | Poder | Prec. | PP | Efecto |
|---|---|---|---|---|---|---|---|
| Fuego | Llama del Perdón | Luz | Especial | 95 | 100 | 10 | Cura al usuario 30% del daño |
| Agua | Marea Celestial | Estelar | Especial | 85 | 100 | 10 | 20% de dormir |
| Planta | Raíz de la Verdad | Cristal | Físico | 80 | 100 | 15 | 30% bajar Velocidad |
| Eléctrico | Relámpago del Alba | Luz | Especial | 90 | 100 | 10 | 20% paralizar, prioridad vs Siniestro |
| Hielo | Velo del Silencio | Oscuro | Estado | — | — | 10 | +2 DefEsp, inmune a sonido |
| Lucha | Puño del Perdón | Luz | Físico | 85 | 100 | 10 | 20% quemar, limpia cambios negativos |
| Veneno | Niebla del Olvido | Espectral | Estado | — | 90 | 10 | -2 precisión, confunde a Psíquico/Hada |
| Tierra | Cristalización | Cristal | Físico | 90 | 100 | 10 | 30% subir Defensa |
| Volador | Canto del Alba | Estelar | Especial | 100 | 100 | 5 | Crítico bajo Cielo Estrellado |
| Psíquico | Recuerdo Estelar | Estelar | Especial | 80 | 100 | 10 | Ignora cambios stats y reflejos |
| Bicho | Danza del Eclipse | Oscuro | Estado | — | — | 5 | +2 Ataque y Velocidad, no ataca turno sig. |
| Roca | Fragmentación | Cristal | Físico | 80 | 100 | 15 | 30% bajar Defensa |
| Fantasma | Eco del Pasado | Espectral | Especial | 80 | 100 | 10 | Usa último mov. del rival +50% poder |
| Dragón | Gravitón | Estelar | Especial | 80 | 100 | 5 | Anula habilidad 3 turnos, impide cambio |
| Siniestro | Venganza Umbría | Oscuro | Físico | 100 | 100 | 5 | Poder ×2 si PS < 50% |
| Hada | Gracia Matinal | Luz | Estado | — | — | 5 | Cura estados y 25% PS a todo el equipo |
| Normal | Vínculo Estelar | Estelar | Estado | — | — | 5 | Todos los movimientos críticos 3 turnos |
| Acero | Muro Prisma | Cristal | Estado | — | — | 10 | +2 DefEsp, absorbe Dragón/Psíquico |

*(Nota: los 5 tipos nuevos del juego — Luz, Espectral, Cristal, Estelar, Oscuro — NO tienen movimiento de purificación propio en esta tabla porque son tipos de las fuerzas primordiales, no tipos "originales" de un Pokémon corrompible. Ver Open Questions.)*

## Edge Cases

| Scenario | Expected Behavior | Rationale |
|----------|-------------------|-----------|
| **Un Semi-Oscuro intenta ganar EXP** | No gana nada; el punto de EXP se descarta | Coherencia con "corazón cerrado" |
| **Un Semi-Oscuro sube "de nivel" (media de 100)** | El medidor se satura en 100; no sube nivel | El medidor no es un nivel |
| **Un Semi-Oscuro con medidor 100 intenta purificar antes del sellado** | No purifica; requiere Altar/Menhir o Piedra | Sellado es requisito inescapable |
| **Piedra de la Verdad sobre un Semi-Oscuro con medidor 0** | Purifica de inmediato (salta medidor y sellado) | La Piedra es el atajo total |
| **Un Pokémon cuyo tipo original es Oscuro (o uno de los 5 nuevos) es purificado** | No hay movimiento de purificación definido; se registra sin movimiento especial (o se define ad hoc) | Ver Open Questions |
| **Captura de un Semi-Oscuro falla** | Se aplica el ×0.8; no hay otro efecto | La captura es más difícil pero posible |
| **Semi-Oscuro intenta evolucionar con objeto** | La evolución se bloquea hasta purificar | Sin evolución mientras corrompido |
| **Doble tipo original (ej. Fuego/Volador)** | El movimiento de purificación depende del tipo PRIMARIO | Se simplifica a un tipo canónico para evitar ambigüedad |

## Dependencies

| System | Direction | Nature of Dependency |
|--------|-----------|---------------------|
| 5 tipos nuevos | Este depende | Usa el tipo `Oscuro` nuevo como tipo puro |
| Purificación | Este extiende / Purificación depende de este | El medidor y sellado se amplían en el GDD de Purificación |
| Zonas de Rencor | Este depende | Define la probabilidad `P` y los lugares |
| Cuaderno de Viaje | Depende de este | Registra purificaciones y Marcas |
| Captura (Essentials) | Este modifica | Aplica ×0.8 |
| Movimientos (pool) | Este depende | La pool de 8 movimientos oscuros |

## Tuning Knobs

| Parameter | Current Value | Safe Range | Effect of Increase | Effect of Decrease |
|-----------|--------------|------------|-------------------|-------------------|
| Probabilidad de spawn `P` | 5–12% (por zona) | 1–25% | Más Semi-Oscuros, purificación trivial | Menos, feature rara |
| Tasa de captura ×0.8 | 0.8 | 0.5–1.0 | Captura más difícil | Captura más fácil |
| Medidor: puntos por combate en equipo | +1 | +1–3 | Purificación más rápida | Más lenta |
| Medidor: puntos por participación activa | +3 | +2–5 | Incentiva usarlo en combate | Lo desincentiva |
| Medidor: pasos por punto | 256 | 128–512 | Purificación por exploración más rápida | Más lenta |
| Meta del medidor | 100 | 50–200 | Requiere más esfuerzo | Más accesible |

## Visual/Audio Requirements

| Event | Visual Feedback | Audio Feedback | Priority |
|-------|----------------|---------------|----------|
| Aparición de Semi-Oscuro | Aura oscura/violeta envolvente, aura de "corazón cerrado" | Tono grave de corrupción | Alta |
| Medidor de purificación | Barra de progreso visible en pantalla del Pokémon | — | Alta |
| Purificación completa | Destello de luz que limpia el aura, el Pokémon recupera color | Clímax musical de sanación | Alta |
| Movimiento Oscuro en combate | VFX oscuro/violeta | Golpe umbrío | Media |

## Game Feel

### Feel Reference

La aparición de un Semi-Oscuro debe sentirse como un **encuentro de jefe** súbito (aunque no lo sea estadísticamente): el aura y el tono grave deben crear un "oh no, este está corrompido" inmediato. La purificación debe sentirse como el alivio de una tensión acumulada — análogo a *Colosseum* cuando el "pulse" de purificación rompe la oscuridad. Anti-referencia: que un Semi-Oscuro se sienta como un Pokémon salvaje normal con otro color.

### Weight and Responsiveness Profile

- **Weight**: el estado corrompido se siente "pesado" (el Pokémon está atrapado); la purificación es un alivio "ligero".
- **Snap quality**: la transición corrompido→purificado debe ser un corte limpio y claro (destello), no un fade ambiguo.
- **Failure texture**: si la captura falla o el medidor avanza lento, el jugador debe entender que es por el estado corrompido, no por falla suya.

## UI Requirements

| Information | Display Location | Update Frequency | Condition |
|-------------|-----------------|-----------------|-----------|
| Indicador "Semi-Oscuro" | Pantalla del Pokémon / equipo | Al consultar | Estado Semi-Oscuro |
| Medidor de purificación (0–100) | Pantalla del Pokémon | En tiempo real tras combate/pasos | Estado Semi-Oscuro |
| Mensaje de purificación completada | Diálogo al sellar en Altar/Menhir | Una vez | Medidor 100 + sellado |
| Movimiento especial aprendido | Mensaje post-purificación | Una vez | Tipo original con movimiento definido |

## Cross-References

| This Document References | Target GDD | Specific Element Referenced | Nature |
|--------------------------|-----------|----------------------------|--------|
| Usa el tipo Oscuro como tipo puro | `design/gdd/pbs-tipos-nuevos.md` | Tipo `Oscuro` (y su matriz de efectividad) | Data dependency |
| Movimientos de purificación usan tipos nuevos | `design/gdd/pbs-tipos-nuevos.md` | Luz/Cristal/Estelar/Oscuro | Data dependency |
| Medidor y sellado se detallan | `design/gdd/purificacion.md` (pendiente) | Proceso completo de purificación | Ownership handoff |
| Probabilidad `P` por zona | `design/gdd/zonas-de-rencor.md` (pendiente) | Tasa de spawn corrompido | Data dependency |

## Acceptance Criteria

- [ ] **GIVEN** una Zona de Rencor, **WHEN** aparece un Pokémon salvaje, **THEN** su spawn Semi-Oscuro ocurre con probabilidad `P` de la zona (5–12%).
- [ ] **GIVEN** un Pokémon Semi-Oscuro en combate, **WHEN** se intenta ganar EXP o evolucionar, **THEN** no gana EXP ni evoluciona.
- [ ] **GIVEN** un intento de captura de un Semi-Oscuro, **WHEN** se calcula la captura, **THEN** la tasa es ×0.8 de la base.
- [ ] **GIVEN** un Semi-Oscuro recién aparecido, **WHEN** se inspecciona su moveset, **THEN** tiene exactamente 4 movimientos de la pool de 8, sin repetición.
- [ ] **GIVEN** un Semi-Oscuro con medidor 100, **WHEN** se lleva al Altar/Menhir, **THEN** purifica: recupera tipo, moveset completo y movimiento especial, y pierde los 4 Oscuros.
- [ ] **GIVEN** una Piedra de la Verdad usada sobre un Semi-Oscuro (cualquier medidor), **WHEN** se confirma, **THEN** purifica de inmediato y la Piedra se consume.
- [ ] **GIVEN** un Semi-Oscuro purificado, **WHEN** se revisa, **THEN** el medidor desaparece y gana EXP/evoluciona con normalidad.
- [ ] **GIVEN** el medidor visible, **WHEN** el Pokémon participa en combate o acumula pasos, **THEN** el medidor aumenta según +1/+3/+1 por 256 pasos y se satura en 100.
- [ ] **Performance**: el cálculo de spawn y medidor añade <1ms por encuentro/combate.
- [ ] **No hardcoded values**: la pool, los multiplicadores y la tabla de movimientos viven en PBS/datos, no en código Ruby.

## Open Questions

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| **Purificación de Pokémon cuyo tipo original es Oscuro (o uno de los 5 nuevos)** | game-designer | Fase 2 | RESUELTO: el Semi-Oscuro purificado conserva Oscuro como secundario (Opción A). Los 5 tipos nuevos NO son "tipos originales corrompibles" — son tipos de fuerzas primordiales/guardianes. La tabla de movimientos especiales cubre los 18 oficiales. |
| **Doble tipo original** | game-designer | Fase 2 | RESUELTO: tipo primario define el movimiento especial. |
| **Jefes Semi-Oscuros** | game-designer | Fase 2 | RESUELTO: sets manuales (curados) para jefes; aleatorios de pool para comunes. |
| **Interacción con el sistema de Purificación** | systems-designer | Fase 2 | RESUELTO: Semi-Oscuro define estado+medidor; Purificación detalla sellado+ritual (GDDs separados). |
| **Solapamiento con formas regionales (NUEVO)** | game-designer | Fase 2 | RESUELTO: si la especie tiene forma regional, purificar converge a ella; si no, vuelve al tipo original puro. El movimiento de purificación siempre es exclusivo del purificado. |