# Menú de Combate Semi-Oscuro

> **Status**: In Design
> **Author**: Sora (Hermes) — rol ui-programmer / ux-designer
> **Last Updated**: 2026-09-14
> **Last Verified**: 2026-09-14
> **Implements Pillar**: Pilar 4 — "Mecánicas integradas a la narrativa" (el jugador debe *ver* la corrupción y *sentir* el progreso hacia la sanación en combate, no solo leerlo en un menú aparte)

## Summary

El **Menú de Combate Semi-Oscuro** es la capa de presentación en pantalla que hace visible el estado Semi-Oscuro durante el combate y en las fichas del equipo: la **barra del medidor de purificación**, el **aura de corrupción** sobre el sprite, el aviso de **"Listo"** al alcanzar el umbral, y la lectura de la **cicatriz** (movimiento Oscuro residual) en Pokémon purificados. Es el puente entre el sistema de datos (Semi-Oscuro + Purificación, ya diseñados) y lo que el jugador percibe.

> **Quick reference** — Layer: `UI` · Priority: `MVP` · Key deps: `Semi-Oscuro`, `Purificación`, `Save/load` · NO introduce mecánicas nuevas: solo expone las existentes.

## Overview

Essentials ya provee la pantalla de combate y las fichas del equipo. Este sistema añade **overlays condicionales** que solo aparecen para Pokémon Semi-Oscuros y purificados: no altera el combate para los normales. Su trabajo es responder visualmente tres preguntas del jugador: *"¿este Pokémon está corrupto?", "¿cuánto falta para sanarlo?", "¿ya puedo llevarlo al altar?"*.

## Player Fantasy

**Indirecta pero emocional.** El jugador no "usa" este menú: lo *lee*. La fantasía es la del cuidador que ve cómo su esfuerzo (caminar, luchar junto al Pokémon) se refleja en una barra que sube, y siente el momento exacto en que la sombra "afloja" y debe buscar un lugar sagrado. Anti-referencia: que la corrupción sea invisible o puramente numérica (rompería Pilar 1 — "sana heridas, no solo ganes batallas").

## Detailed Design

### Core Rules

**Regla 1 — Solo se muestra para estados relevantes.** El medidor, el aura y el aviso "Listo" aparecen ÚNICAMENTE si el Pokémon está en estado `Semi-Oscuro` o `Listo` (según la máquina de estados de `semi-oscuro.md`). Un Pokémon `Normal` o `Purificado` no muestra medidor.

**Regla 2 — Barra del medidor en la ficha (status screen).** En la pantalla de resumen del Pokémon, bajo la barra de HP, una barra estrecha **"Purificación: N/100"** (estilo `msg` del medidor de `semi-oscuro.md`). Se llena de izquierda a derecha; a N=100 cambia a color dorado y el texto a **"Listo para sanar"**.

**Regla 3 — Aura de corrupción en combate.** En la pantalla de batalla, un Pokémon Semi-Oscuro lleva un **resplandor oscuro tenue** bajo el sprite (no un recolor completo: preserva la legibilidad del tipo). Al pasar a `Listo`, el aura **parpadea suavemente** (pulso lento), señal visual sin texto.

**Regla 4 — Aviso de "Listo" (una sola vez, no intrusivo).** Cuando el medidor cruza a 100 (en el evento de combate/paso que lo complete, ver fórmula del medidor en `semi-oscuro.md`), se muestra **una** notificación transitoria: *"Sientes que la sombra afloja… busca un lugar sagrado."* No bloquea el combate; aparece al finalizar la acción que lo disparó. Silas reacciona de forma consistente con su rol de pista ligera (`cuaderno-de-viaje.md` Regla 5).

**Regla 5 — NO hay comando "Purificar" en el menú de combate.** La purificación es un **ritual en lugar sagrado** (Altar/Menhir, ver `purificacion.md`), no una acción de batalla. El menú de combate solo *informa*; el *acto* ocurre en el mapa. Esta separación preserva la solemnidad del ritual y evita spam de purificación a mitad de fight.

**Regla 6 — Lectura de la cicatriz en purificados.** En la ficha de un Pokémon purificado, su **movimiento especial exclusivo** (el de purificación) lleva un **marcador "✦ Sanado"** y tooltip: *"Recuerda la sombra que superó."* Esto hace visible la decisión de diseño de convergencia (forma regional vs. tipo puro + cicatriz) sin menús adicionales. El icono diferencia al purificado de una forma regional natural (que no tiene el movimiento).

**Regla 7 — Accesibilidad: no solo color.** El estado se distingue también por **forma/icono** (aura = borde ondulante; Listo = pulso + texto; cicatriz = glifo ✦). Nunca depende únicamente de un cambio de color (daltonismo).

### States and Transitions

Este menú refleja, no define, la máquina de estados de `semi-oscuro.md`:

| Estado del sistema | Qué muestra la UI |
|---|---|
| `Normal` | Nada extra (HUD estándar) |
| `Semi-Oscuro` (0–99) | Aura oscura + barra "Purificación: N/100" |
| `Listo` (100) | Aura pulsante + barra dorada "Listo para sanar" + aviso único |
| `Purificado` | Sin aura ni medidor; glifo ✦ en el movimiento de cicatriz |

### Interactions with Other Systems

| Sistema | Dirección | Qué lee la UI |
|---|---|---|
| **Semi-Oscuro** | Lee | Estado (`Normal/Semi-Oscuro/Listo`) y valor del medidor `N` (0–100) |
| **Purificación** | Lee | Marca `Purificado` + ID del movimiento de cicatriz (Regla 6) |
| **Save/load** | Lee | Persistencia de estado/medidor (`msg_purificados`, variable de medidor) |
| **Combate base (Essentials)** | Extiende | Añade overlays al sprite/HP sin tocar la resolución de turnos |
| **Cuaderno / Silas** | Interactúa | El aviso "Listo" dispara la reacción de pista de Silas |

### Formulas

Ninguna fórmula nueva. El valor mostrado es el `medidor` definido en `semi-oscuro.md` (acumulación +1/+1/+3, meta 100). La UI solo formatea `N/100` y el ancho de barra = `N/100 * ancho_max`.

### Edge Cases

| Escenario | Comportamiento esperado | Rationale |
|---|---|---|
| **Pokémon purificado en combate** | Sin aura, sin medidor; se comporta visualmente como un Pokémon normal | Ya sanado; no confundir con corrupto |
| **Dos Pokémon Semi-Oscuros en el equipo** | Cada uno con su propia barra en ficha; en combate solo se ve el aura del que está activo | El activo es el foco |
| **Medidor llega a 100 a mitad de una animación de ataque** | El aviso "Listo" se encola y se muestra al terminar la animación (no solapa texto) | Evita perder el mensaje |
| **Jugador carga partida con un Semi-Oscuro ya en `Listo`** | La barra aparece dorada y el aviso se muestra UNA vez al entrar al área del Pokémon (no en cada recarga) | Sincronía con save sin spam |
| **Piedra de la Verdad usada (purifica saltando medidor)** | El aura desaparece al instante y el estado pasa a Purificado; si no estaba en `Listo`, no se muestra aviso (el acto fue instantáneo) | Coherente con `purificacion.md` |
| **Forma regional natural (no purificada) del mismo tipo** | NO muestra glifo ✦ ni marcador "Sanado" | Diferencia purificado de adaptado (Regla 6) |
| **Resolución de pantalla baja (RMXP)** | La barra usa una franja compacta bajo HP; si no cabe, se relega a la ficha (no al HUD de combate) | Essentials es 512×384, espacio limitado |

### Dependencies

| System | Direction | Nature |
|---|---|---|
| Semi-Oscuro | Depende de este | Lee estado + medidor (fuente de verdad) |
| Purificación | Depende de este | Lee marca de purificado + cicatriz |
| Save/load | Depende de este | Persistencia del valor a mostrar |
| Combate base | Extiende | Overlay sobre HUD de Essentials |
| Cuaderno/Silas | Interactúa | Reacción al aviso "Listo" |

### Tuning Knobs

| Parámetro | Valor actual | Rango seguro | Efecto al aumentar | Efecto al disminuir |
|---|---|---|---|---|
| Opacidad del aura | 35% | 15–60% | Más legible "corrupto", tapa más el sprite | Más sutil, puede no notarse |
| Frecuencia del pulso "Listo" | 1.2 s | 0.6–2.0 s | Más calmado | Más ansioso/urgente |
| Mostrar medidor en HUD de combate | No (solo en ficha) | sí/no | Más info en pantalla, más ruido | Más limpio, menos feedback |

*(Nota de diseño: el medidor numérico vive en la **ficha**; en combate basta el aura + el aviso. Mostrar la barra también en combate fue descartado por ruido visual en 512×384. Corregible.)*

### Visual/Audio Requirements

| Evento | Visual | Audio | Prioridad |
|---|---|---|---|
| Pokémon Semi-Oscuro en campo | Aura/resplandor oscuro tenue bajo sprite | — | Alta |
| Paso a `Listo` | Aura pulsa lento; barra dorada | Campanilla suave / tono esperanzador | Alta |
| Aviso "Listo para sanar" | TextBox transitorio + ícono Silas | Ladrido/ruido sutil de Silas | Media |
| Purificación completada (ritual) | Aura se disipa en destello limpio | Resolución luminosa | Alta |
| Glifo ✦ (cicatriz) | Pequeño marcador junto al movimiento | — | Baja |

### Game Feel

**Feel reference:** el aura debe sentirse como una **enfermedad visible que retrocede**, no como un "debuff genérico". El pulso de `Listo` es el latido de alivio. Anti-referencia: parpadeos tipo veneno/quemadura de Essentials (confundir corrupción con estado alterado de combate).

**Weight and Responsiveness:** el feedback es ambiental (nunca bloquea input); el aviso "Listo" es el único momento que pide lectura, y es transitorio.

### UI Requirements

| Información | Dónde | Cuándo se actualiza | Condición |
|---|---|---|---|
| Barra "Purificación: N/100" | Ficha del Pokémon | Al cambiar el medidor | Estado Semi-Oscuro/Listo |
| Estado "Listo para sanar" | Ficha (barra dorada) | Al cruzar a 100 | Listo |
| Aura de corrupción | HUD de combate (sprite activo) | Al entrar/salir de combate | Semi-Oscuro/Listo |
| Aviso de búsqueda de altar | TextBox transitorio | Una vez al cruzar a 100 | Listo (nuevo) |
| Glifo ✦ "Sanado" | Ficha, junto al movimiento | Al purificar | Purificado |

### Cross-References

| This Document References | Target GDD | Element | Nature |
|---|---|---|---|
| Máquina de estados | `semi-oscuro.md` | `Normal→Semi-Oscuro→Listo→Purificado`, medidor 0–100 | Data dependency |
| Ritual de purificación | `purificacion.md` | Purificación solo en lugares sagrados (Regla 5 de aquí la respeta) | Rule dependency |
| Convergencia/cicatriz | `semi-oscuro.md` Regla 7b | Glifo ✦ distingue purificado de forma regional | Rule dependency |
| Pista Silas | `cuaderno-de-viaje.md` Regla 5 | Aviso "Listo" dispara reacción de Silas | Rule dependency |
| Persistencia | `save-load-variables.md` | `msg_purificados` + variable de medidor | Data dependency |

### Acceptance Criteria

- [ ] **GIVEN** un Pokémon Semi-Oscuro con medidor 40, **WHEN** se abre su ficha, **THEN** la barra muestra "Purificación: 40/100" y el aura es visible en combate.
- [ ] **GIVEN** un Pokémon `Normal`, **WHEN** está en combate/ficha, **THEN** NO se muestra aura ni medidor.
- [ ] **GIVEN** un Semi-Oscuro que sube de 99→100 por una acción, **WHEN** termina esa acción, **THEN** la barra se pone dorada, el aura pulsa y aparece el aviso único "busca un lugar sagrado".
- [ ] **GIVEN** un Pokémon purificado, **WHEN** se abre su ficha, **THEN** no hay aura ni medidor y su movimiento de cicatriz muestra el glifo ✦ "Sanado".
- [ ] **GIVEN** una forma regional natural (no purificada) del mismo tipo, **WHEN** se abre su ficha, **THEN** NO muestra glifo ✦.
- [ ] **GIVEN** el jugador en un Altar, **WHEN** busca la acción, **THEN** el menú de combate NO ofrece "Purificar" (la purificación solo ocurre en el lugar sagrado, no en batalla).
- [ ] **GIVEN** una Piedra de la Verdad usada sobre un Semi-Oscuro con medidor 20, **WHEN** se consume, **THEN** el aura se disipa y pasa a Purificado sin esperar a 100.
- [ ] **Accesibilidad**: el estado es distinguible por forma/icono sin depender solo del color.
- [ ] **Performance**: el overlay del aura no añade >2 ms por frame al combate.

### Open Questions

| Question | Owner | Deadline | Resolution |
|---|---|---|---|
| **Ícono exacto del glifo ✦** y su paleta según tema (¿dorado neutro o color de Luz?) | art-director | Fase 2 (arte) | Pendiente del arte bible; placeholder ✦ |
| **¿Aura también para tipos nuevos corruptibles?** (los 5 nuevos NO son corrompibles según Semi-Oscuro) — confirmar que nunca hay Semi-Oscuro de tipo Luz/Cristal/etc. | game-designer | Fase 2 | Coherente: solo las 18 especies originales se corrompen |
| **Sonido del pulso "Listo"**: ¿melodía propia o reuse de un SFX de cura? | sound-designer | Fase 5 | Pendiente de fonógrafo/audio |

---

**Notas del orquestador (Sora) — decisiones corregibles:**
1. **Sin medidor numérico en el HUD de combate** (solo aura + aviso); la barra N/100 vive en la ficha. Evita ruido en 512×384. Si prefieres la barra visible también en batalla, lo muevo.
2. **Sin comando "Purificar" en batalla** — refuerza tu decisión de que el ritual es solemne y por lugar. 
3. **El glifo ✦ "Sanado" es la pieza de UI que materializa tu decisión Q1** (cicatriz como marca): es la única forma de que el jugador distinga un purificado de una forma regional natural a simple vista.

## Notes de consistencia

Este GDD **no introduce mecánicas**: expone las de `semi-oscuro.md` y `purificacion.md`. Cualquier contradicción de estado/medidor se resuelve a favor de esos dos documentos (son fuente de verdad).
