# Purificación

> **Status**: In Design
> **Author**: Sora (Hermes) — rol game-designer / systems-designer
> **Last Updated**: 2026-09-12
> **Last Verified**: 2026-09-12
> **Implements Pillar**: Pilar 1 — "Narrativa emocional" (la purificación es el acto de sanación que define el arco de cada Acreedor) + Pilar 4 — "Mecánicas integradas en la narrativa"

## Summary

La **purificación** es el proceso que libera a un Pokémon Semi-Oscuro de su corrupción: sin revertirla, la transforma en una cicatriz integrada (el Pokémon conserva el tipo Oscuro como secundario, convirtiéndose en una forma regional oscura de Veridia). Consta de dos fases —rellenar el medidor (definido en el GDD de Semi-Oscuro) y **sellarlo en un lugar sagrado**—, y culmina con la recuperación del moveset y el aprendizaje de un movimiento especial. Es la recompensa emocional y mecánica central del loop.

> **Quick reference** — Layer: `Feature` · Priority: `MVP` · Key deps: `Semi-Oscuro`, `5 tipos nuevos`, `Cuaderno de Viaje`, `Zonas de Rencor`

## Overview

Cuando un Pokémon Semi-Oscuro alcanza 100 puntos de medidor, queda "listo". El jugador lo lleva a un **lugar sagrado** —el Altar del Perdón (Cueva del Origen) o un Menhir de la Verdad (altares en rutas)— y activa el **ritual de purificación**. El ritual es una secuencia cinematográfica corta (destello de luz + flashback de la memoria del Pokémon) que concluye con la liberación. Alternativamente, la **Piedra de la Verdad** permite purificar en cualquier lugar, al instante.

El resultado no es "volver a ser como antes": el Pokémon conserva el tipo Oscuro como secundario y su diseño corrompido deja una marca visual. La purificación *honra* la herida en lugar de borrarla.

## Player Fantasy

**Directo.** La fantasía es de **redención tangible**: el jugador sella un acto de perdón que tiene consecuencias permanentes y visibles. Cada purificación debe sentirse como el momento en que el Pokémon —y simbólicamente, el Acreedor correspondiente— queda en paz. La referencia es *Pokémon Colosseum/XD* (la purificación como desbloqueo emocional), pero elevada aquí por el tema central: no se borra la cicatriz, se integra como parte de la identidad.

## Detailed Design

### Core Rules

**Regla 1 — Entrada al ritual.** Una purificación solo puede iniciarse si el Pokémon está en estado `Listo` (medidor = 100). La Piedra de la Verdad es la única excepción (salta el medidor).

**Regla 2 — Lugares de sellado.** Dos tipos de lugar sagrado habilitan la purificación:
- **Altar del Perdón** (Cueva del Origen, nivel 3): el lugar canónico; la primera purificación allí desencadena la visión de Luminark (evento narrativo). Es la opción "de peregrinaje".
- **Menhir de la Verdad** (altares menores repartidos por rutas): opción práctica; misma mecánica, sin evento narrativo especial.

**Regla 3 — El ritual (secuencia).** Al confirmar la purificación en un lugar sagrado:
1. **Cinemática corta** (~8–12 segundos): se muestra al Pokémon envuelto en su aura oscura, un destello de luz (color según el destino del Pokémon), y un **flashback breve** de la memoria del Pokémon (imagen/sonido).
2. **Transición de estado**: `Listo → Purificado` (ver Semi-Oscuro).
3. **Feedback de resultado**: mensaje "¡[Pokémon] ha sido purificado!" + lista de cambios (tipo, moveset, movimiento especial).
4. La secuencia es **no interactiva** (no hay minigame); el jugador solo confirma al inicio.

**Regla 4 — Piedra de la Verdad (atajo).** Usar una Piedra de la Verdad en el menú de objeto, sobre un Semi-Oscuro (en cualquier estado de medidor, incluso 0), purifica al instante, saltando el medidor y el lugar sagrado. Se consume. Objeto raro y valioso (ver GDD de objetos clave).

**Regla 5 — Resultado de la purificación (consistente con Semi-Oscuro Regla 7/7b):**
1. Tipo: si la especie **tiene** forma regional oscura → tipo original + Oscuro secundario (converge a la forma regional). Si **no tiene** → tipo original puro (sin Oscuro secundario).
2. Pierde los 4 movimientos Oscuros de la pool.
3. Recupera su moveset de nivelación completo.
4. Aprende el movimiento especial de purificación (según tipo primario original), **exclusivo del purificado**.
5. Desbloquea EXP/evolución/megaevolución.

**Regla 6 — Purificación individual.** Cada purificación es un **evento singular** (un ritual por Pokémon). No hay purificación "en masa"; el jugador repite el ritual por cada Pokémon. Esto preserva la solemnidad del acto.

**Regla 7 — Registro en el Cuaderno.** Cada purificación se registra en el Cuaderno de Viaje (lista "Purificados" + nota del movimiento especial aprendido). La purificación de un Pokémon ligado a un Acreedor (jefes) marca el progreso de esa Marca.

### States and Transitions

(El estado del Pokémon vive en el GDD de Semi-Oscuro. Este GDD describe el **flujo del ritual**, no nuevos estados del Pokémon.)

| Paso | Trigger | Resultado |
|------|---------|-----------|
| `Confirmar ritual` | Interactuar con Altar/Menhir con Pokémon `Listo` en equipo | Inicia cinemática |
| `Cinemática` | Confirmation | Destello + flashback (~8–12s) |
| `Sellar` | Fin de cinemática | Transición `Listo → Purificado` + feedback |
| `Ataque alternativo` | Usar Piedra de la Verdad (cualquier medidor) | Purificación instantánea, item consumido |

### Interactions with Other Systems

| Sistema | Dirección | Interface (datos que fluyen) |
|---|---|---|
| **Semi-Oscuro** | De este depende | Consume el estado `Listo` (medidor=100); produce el estado `Purificado` |
| **5 tipos nuevos** | Depende | El movimiento especial usa Luz/Cristal/Estelar/Oscuro |
| **Cuaderno de Viaje** | Depende de este | Registra purificaciones y movimiento aprendido |
| **Fuentes de la Verdad (#31)** | Canal alterno | Beber de una fuente bautiza al equipo: +15 medidor a SO activos y purifica instantáneamente a los `Listo` (sin ritual; mismo resultado de convergencia). Ver `fuentes-de-la-verdad.md` |
| **Formas regionales (30)** | Convergencia | El purificado se convierte en la forma regional oscura de su especie (si existe); el movimiento de purificación es exclusivo del purificado |
| **Objetos clave** | Depende | Piedra de la Verdad como atajo consumible |
| **Eventos narrativos** | Depende | Primera purificación en Altar dispara visión de Luminark / entrega de Marca |

## Formulas

La purificación en sí no tiene fórmulas numéricas (el medidor ya está cubierto en Semi-Oscuro). Los únicos números relevantes son:

| Parámetro | Valor | Nota |
|---|---|---|
| Medidor requerido para sellar | 100 | Salvo Piedra de la Verdad |
| Duración de la cinemática | ~8–12 s | Configurable |
| Piedra de la Verdad a consumir | 1 | Por purificación |

## Edge Cases

| Scenario | Expected Behavior | Rationale |
|----------|-------------------|-----------|
| **Intentar purificar un Pokémon con medidor < 100 en Altar/Menhir** | El ritual no se oferta o muestra "aún no está listo" | Medidor es requisito |
| **Usar Piedra de la Verdad sobre un Pokémon no Semi-Oscuro** | No tiene efecto / item no se consume (o se bloquea su uso) | La Piedra solo purifica corrompidos |
| **Equipo con varios Semi-Oscuros listos** | Se purifica uno por uno; el jugador elige cuál | Ritual individual (Regla 6) |
| **Primera purificación en Altar** | Dispara la visión de Luminark (evento único) | Hito narrativo |
| **Purificar sin haber visto la visión (usando Menhir primero)** | La visión de Luminark queda pendiente para la siguiente purificación en el Altar | No se bloquea el progreso |
| **Un Pokémon purificado ya es forma regional oscura** | No puede volver a purificarse; no es Semi-Oscuro | Purificación es irreversible |
| **Piedra de la Verdad sin Semi-Oscuros en el equipo** | Uso bloqueado con aviso | Evita desperdicio |

## Dependencies

| System | Direction | Nature of Dependency |
|--------|-----------|---------------------|
| Semi-Oscuro | Este depende | Estado `Listo`/`Purificado`, medidor |
| 5 tipos nuevos | Este depende | Movimientos especiales por tipo |
| Cuaderno de Viaje | Este depende | Registro de purificaciones |
| Formas regionales | Este depende (solapamiento) | Definir identidad del purificado |
| Objetos clave | Este depende | Piedra de la Verdad |

## Tuning Knobs

| Parameter | Current Value | Safe Range | Effect of Increase | Effect of Decrease |
|-----------|--------------|------------|-------------------|-------------------|
| Duración de cinemática | 8–12 s | 5–20 s | Más solemnidad, más fricción | Más ágil, menos épico |
| Nº de Menhires en rutas | (por definir en mapas) | 3–12 | Purificación más cómoda | Más peregrinaje al Altar |
| Rareza Piedra de la Verdad | rara | 1–5 por partida | Ataijo más frecuente | Más valiosa |

## Visual/Audio Requirements

| Event | Visual Feedback | Audio Feedback | Priority |
|-------|----------------|---------------|----------|
| Inicio del ritual | Aura oscura intensificada | Tono de tensión sostenida | Alta |
| Destello de purificación | Luz del color del destino + disipación del aura | Clímax de sanación | Alta |
| Flashback del Pokémon | Imagen/recuerdo breve | Música del recuerdo | Alta |
| Confirmación | Mensaje + marca visual en el Pokémon | Nota de cierre | Media |

## Game Feel

### Feel Reference

El ritual debe sentirse como un **momento de peso y alivio simultáneos**: tensión sostenida (el aura) que se rompe en un destello (la luz), análogo al "pulse de purificación" de *Colosseum* pero más cinematográfico. Anti-referencia: que se sienta como pulsar un botón de menú.

### Weight and Responsiveness Profile

- **Weight**: el ritual es **deliberado** (no inmediato): la cinemática corta da peso al acto sin volverlo tedioso.
- **Snap quality**: el destello es un corte crisp; el paso corrompido→purificado debe leerse en un frame.
- **Failure texture**: si no se puede purificar (medidor<100), el feedback debe explicar claramente por qué.

## UI Requirements

| Information | Display Location | Update Frequency | Condition |
|-------------|-----------------|-----------------|-----------|
| Indicador "Listo para purificar" | Pantalla del Pokémon / Altar-Menhir | Al consultar | Medidor=100 |
| Opción "Purificar" | Interacción con Altar/Menhir | Al interactuar | Pokémon Listo en equipo |
| Mensaje de cambios post-purificación | Diálogo | Una vez | Tras sellar |
| Registro de purificados | Cuaderno de Viaje | Al añadirse | Tras cada purificación |
| Uso de Piedra de la Verdad | Menú de mochila | Al seleccionar | Semi-Oscuro en equipo |

## Cross-References

| This Document References | Target GDD | Specific Element Referenced | Nature |
|--------------------------|-----------|----------------------------|--------|
| Estado `Listo`/`Purificado` y medidor | `design/gdd/semi-oscuro.md` | Reglas 4–7, States | Data dependency |
| Movimiento especial por tipo | `design/gdd/semi-oscuro.md` | Tabla de 18 tipos | Data dependency |
| Tipo Oscuro secundario | `design/gdd/pbs-tipos-nuevos.md` | Tipo `Oscuro` | Data dependency |
| Formas regionales oscuras | `design/gdd/formas-regionales.md` (pendiente) | Solapamiento de identidad | Rule dependency |

## Acceptance Criteria

- [ ] **GIVEN** un Semi-Oscuro con medidor 100, **WHEN** se interactúa con Altar/Menhir, **THEN** se ofrece la opción de purificar y la cinemática se reproduce.
- [ ] **GIVEN** un Semi-Oscuro con medidor <100 en Altar/Menhir, **WHEN** intenta purificar, **THEN** se bloquea con aviso "aún no listo".
- [ ] **GIVEN** una Piedra de la Verdad usada sobre un Semi-Oscuro (cualquier medidor), **WHEN** se confirma, **THEN** purifica al instante y la Piedra se consume.
- [ ] **GIVEN** la purificación completada, **WHEN** se revisa el Pokémon, **THEN** tipo primario original + Oscuro secundario, moveset completo, movimiento especial aprendido, 4 Oscuros eliminados.
- [ ] **GIVEN** la primera purificación en el Altar del Perdón, **WHEN** se completa, **THEN** se dispara la visión de Luminark (evento único).
- [ ] **GIVEN** una purificación completada, **WHEN** se abre el Cuaderno, **THEN** el Pokémon aparece en la lista de purificados.
- [ ] **GIVEN** un Pokémon ya purificado, **WHEN** se intenta purificar de nuevo, **THEN** no es posible (irreversible).
- [ ] **Performance**: la cinemática de purificación no excede 12s y no bloquea el input más allá del ritual.
- [ ] **No hardcoded values**: lugares sagrados, duración y rareza de la Piedra viven en datos/config.

## Open Questions

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| **Solapamiento con formas regionales**: ¿el Semi-Oscuro purificado es inconcebiblemente igual a una forma regional (duplicado), o es una categoría distinta (diseño propio, historia de "purificado")? | game-designer | Fase 2 | RESUELTO: purificar produce exactamente la forma regional oscura de la especie (si existe). El movimiento de purificación es exclusivo del purificado. |
| **Nº y distribución de Menhires**: ¿cuántos Menhires de la Verdad hay y dónde? | level-designer | Fase 4 | Depende del mapa de rutas. |
| **Flashback del Pokémon**: ¿qué memoria se muestra? ¿Es genérica por especie o única por Pokémon? (mi asunción: genérica por especie/tipo, tema narrativo) | narrative-director | Fase 4 | — |
| **Cinética del ritual**: ¿8-12s es aceptable, o se prefiere un ritual más corto/largo? | user | Fase 2 | Decisión del usuario pendiente. |