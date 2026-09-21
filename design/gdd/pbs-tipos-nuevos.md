# Pipeline PBS + 5 Tipos Nuevos (Luz, Espectral, Cristal, Estelar, Oscuro)

> **Status**: In Design
> **Author**: Sora (Hermes) — rol technical-director / systems-designer
> **Last Updated**: 2026-09-12
> **Last Verified**: 2026-09-12
> **Implements Pillar**: Pilar 3 — "Nuevos tipos con significado" (los cinco tipos nuevos están ligados a la mitología y a la historia)

## Summary

Este sistema introduce **cinco tipos elementales nuevos** (Luz, Espectral, Cristal, Estelar, Oscuro) a Pokémon Essentials 21.1 mediante **datos PBS** (archivos de texto de definición de tipos), no mediante código Ruby. Cada tipo está ligado a una fuerza primordial de la mitología del juego y tiene una tabla de efectividad propia bidireccional frente a todos los tipos existentes. Es la base de datos sobre la que se construyen el sistema Semi-Oscuro, la purificación, los climas y las formas regionales.

> **Quick reference** — Layer: `Feature` · Priority: `MVP` · Key deps: `Motor base RMXP + Essentials 21.1`, `Pipeline PBS`

## Overview

El jugador experimenta los cinco tipos nuevos a través del combate: cada tipo tiene fortalezas, debilidades e inmunidades propias que modifican el cálculo de daño, tal como los 18 tipos oficiales. A nivel de diseño, estos tipos rompen y amplían la matriz clásica: **Luz** (verdad/redención) vence a lo siniestro y lo fantasmagórico; **Espectral** (memoria/ecos) domina lo psíquico y lo feérico; **Cristal** (memoria sólida) rompe dragones y domina aguas/voladores; **Estelar** (cosmos/destino) golpea lo dragón, lo psíquico y lo fantasma; **Oscuro** (sombra/vacío) es sombra pura que vence a lo psíquico, lo feérico y a la propia Luz, y es inmune a Normal.

Técnicamente, el sistema se implementa **casi en su totalidad como datos PBS** (`types.txt`/`PBS/types.txt` según la convención de Essentials 21.1), con un mínimo de script auxiliar solo si la representación visual (colores/símbolos de tipo) lo requiere. El principio rector es "PBS primero, scripts después".

## Player Fantasy

**Directo.** El jugador siente que domina un léxico de combate más rico y simbólico: cada tipo nuevo no es un simple valor de daño, sino la manifestación de una fuerza cósmica con identidad propia. Ganar una batalla usando el tipo correcto debe sentirse como *entender* el ecosistema de Veridia, no solo como memorizar una tabla. La referencia emocional es la mitología interna (Necrozma fragmentado en cinco hermanos): usar "Luz" contra una sombra debe sentirse como un acto de redención literal.

Sirve al Pilar 3 ("nuevos tipos con significado"): los tipos no son decoración mecánica; están atados a la historia de las cinco fuerzas primordiales.

## Detailed Design

### Core Rules

**Regla 0 — Ámbito de implementación:** Los 5 tipos son DATOS PBS, no código. No se añade lógica de Ruby salvo para (a) pintar el color/símbolo del tipo en la UI si Essentials no lo soporta por datos, y (b) exponer el tipo a sistemas que lo consultan en tiempo de ejecución si fuera necesario.

**Regla 1 — Archivos PBS a crear/modificar:**

| Archivo PBS | Acción | Contenido |
|---|---|---|
| `types.txt` (o `PBS/types.txt`) | **Crear** 5 entradas nuevas | Definición de cada tipo con su tabla de efectividad completa |
| `moves.txt` (o `PBS/moves.txt`) | **Crear** movimientos que usen los tipos nuevos | Solo si un movimiento nuevo asigna un tipo nuevo (delegar a su propio GDD) |
| `pokemon.txt`/`pokemon_forms.txt` | **Modificar** (futuro) | Asignar los tipos nuevos a Pokémon (formas regionales, guardianes, fuerzas primordiales) |

**Regla 2 — Nombres internos de tipo (convención PBS):** asignar identificadores en inglés sin tildes para compatibilidad con el parser de Essentials:

| Tipo | ID interno PBS |
|---|---|
| Luz | `LIGHT` |
| Espectral | `SPECTRAL` |
| Cristal | `CRYSTAL` |
| Estelar | `STELLAR` |
| Oscuro | `DARK` → **CONFLICTO** (ver Open Questions) |

**Regla 3 — Tabla de efectividad bidireccional:** cada tipo nuevo debe declarar, en ambas direcciones, su relación con TODOS los tipos (los 18 oficiales + los otros 4 nuevos). No basta con declarar la interacción entre tipos nuevos; un tipo nuevo que no declare cómo se relaciona con, p.ej., Acero o Dragón, queda indefinido (daño neutro) y rompe la consistencia.

**Regla 4 — Efectividad ofensiva (este tipo ATACA a...):**

| Tipo atacante | x2 (súper eficaz) | x½ (poco eficaz) | x0 (inmune) |
|---|---|---|---|
| Luz | Siniestro, Fantasma, Veneno, Oscuro | Planta, Psíquico, Acero | — |
| Espectral | Psíquico, Hada, Espectral | Siniestro, Acero, Normal | — |
| Cristal | Dragón, Volador, Agua, Luz | Fuego, Lucha, Acero | — |
| Estelar | Dragón, Psíquico, Fantasma | Acero, Roca, Hielo, Estelar, Oscuro | — |
| Oscuro | Psíquico, Hada, Cristal | Lucha, Acero, Estelar, Oscuro | — |

**Regla 5 — Efectividad defensiva (este tipo es GOLPEADO por...):**

| Tipo defensor | Resiste x½ | Débil x2 | Inmune a |
|---|---|---|---|
| Luz | Siniestro, Fantasma, Lucha | Planta, Acero, Dragón, Cristal | — |
| Espectral | Psíquico, Hada, Fantasma | Siniestro, Normal, Roca, Espectral | — |
| Cristal | Dragón, Volador, Psíquico | Fuego, Lucha, Tierra, Oscuro | — |
| Estelar | Psíquico, Fantasma, Estelar, Oscuro | Siniestro, Hielo, Normal | — |
| Oscuro | Psíquico, Veneno, Oscuro, Estelar | Lucha, Hada, Luz | Normal |

**Regla 6 — No confundir "Oscuro" con "Siniestro" (Dark oficial):** son tipos distintos. El tipo `Siniestro` oficial sigue existiendo; el tipo `Oscuro` de este juego es una entidad nueva. Esto es crítico para el naming interno (ver Open Questions) y para las tablas: "Luz es x2 contra Siniestro" (oficial) y "Luz es x2 contra Oscuro" (nuevo) usan la misma palabra "Luz" pero apuntan a tipos distintos, uno oficial y otro nuevo. Deben representarse sin colisión de nombres.

**Regla 7 — Interacción entre tipos nuevos (matriz cruzada):**

| Atacante \ Defensor | Luz | Espectral | Cristal | Estelar | Oscuro |
|---|---|---|---|---|---|
| Luz | ×1 | ×1 | ×1 | ×1 | ×2 |
| Espectral | ×1 | ×2 | ×1 | ×1 | ×1 |
| Cristal | ×2 | ×1 | ×1 | ×1 | ×1 |
| Estelar | ×1 | ×1 | ×1 | ×½ | ×½ |
| Oscuro | ×1 | ×1 | ×2 | ×½ | ×½ |

*(Matriz cruzada completa y simétrica: triángulo Oscuro→Cristal→Luz→Oscuro + self-interacciones: Espectral ×2 contra sí mismo, Estelar ×½ contra sí mismo, Oscuro ×½ contra sí mismo, Estelar ×½ contra Oscuro.)*

**Regla 8 — Representación visual/UI:** color y símbolo de cada tipo (ver Visual/Audio Requirements) se registran para la pantalla de tipo, la Pokédex y el indicador de efectividad en combate.

### States and Transitions

Este sistema es **datos estáticos**, no tiene estados dinámicos ni máquina de estados propia. Su único "estado" es el estado de definición del propio dato:

| State | Entry Condition | Exit Condition | Behavior |
|-------|-----------------|----------------|----------|
| `Defined` | Tipo declarado en `types.txt` con tabla completa | — | Participa en el cálculo de daño |
| `Undefined` (bug) | Tipo referenciado por un Pokémon/movimiento pero ausente en `types.txt` | Se añade la entrada PBS | Essentials lanza error o trata como neutro (comportamiento no deseado) |

### Interactions with Other Systems

| Sistema | Dirección | Interface (datos que fluyen) |
|---|---|---|
| **Cálculo de daño (Essentials)** | Consume | Lee la matriz de efectividad del tipo atacante → tipo defensor y aplica multiplicador ×2/×½/×0 |
| **Semi-Oscuro** | Depende de este | Usa el tipo `Oscuro` como "tipo puro" del estado corrompido |
| **Purificación** | Depende de este | Los movimientos de purificación usan tipos Luz/Cristal/Estelar/Oscuro |
| **Climas nuevos** | Depende de este | Los climas potencian tipos (Oscuro/Agua, Estelar/Hada, Fuego/Luz) |
| **Habilidades nuevas** | Depende de este | Algunas habilidades invocan climas que potencian estos tipos |
| **Formas regionales (30)** | Depende de este | Añaden `Oscuro` como tipo secundario |
| **UI de combate / Pokédex** | Consume | Muestra color/símbolo/nombre del tipo |

## Formulas

### Multiplicador de efectividad de daño

```
multiplicador = product( entradas_de_tabla_tipo[tipo_atacante][tipo_defensor] )
```

| Variable | Type | Range | Source | Description |
|----------|------|-------|--------|-------------|
| tipo_atacante | enum | 23 tipos (18 + 5) | types.txt | Tipo del movimiento usado |
| tipo_defensor | enum (x2) | 23 tipos | pokemon data | Tipo(s) del Pokémon objetivo (doble tipo → producto) |

**Expected output range**: ×0.25 a ×4 (doble tipo con doble x2 o doble x½; ×0 si algún tipo es inmune).

**Edge case**: inmunidad ×0 domina sobre cualquier otro factor (×0 anula ×2/×4). Doble debilidad = ×4; doble resistencia = ×0.25. Sin entrada explícita → ×1 (neutro), pero esto solo debe ocurrir por error, nunca por diseño.

### Ejemplo trabajado

Movimiento tipo Luz (100 de poder) contra un Pokémon Fantasma/Veneno (p.ej. Gengar):
- Luz × Fantasma = ×2 (súper eficaz)
- Luz × Veneno = ×2 (súper eficaz)
- Efectividad doble = ×4.

Agrupado con el resto de la fórmula de daño de Essentials (STAB, stats, etc.) fuera del alcance de este GDD.

## Edge Cases

| Scenario | Expected Behavior | Rationale |
|----------|-------------------|-----------|
| **Un Pokémon referencia un tipo nuevo no definido en types.txt** | Essentials lanza error de carga; el tipo NO se trata como neutro silenciosamente | Un bug debe ser ruidoso, no un daño neutro accidental |
| **El tipo "Oscuro" y el tipo "Siniestro" (Dark oficial) coexisten** | Se representan como IDs PBS distintos; ninguna tabla los confunde | Son entidades de diseño distintas; colisionarlos invalida las tablas |
| **Movimiento de tipo nuevo usado contra un Pokémon de doble tipo con una inmunidad** | Inmunidad ×0 domina; el multiplicador total es ×0 aunque el otro tipo sea débil | Regla oficial: inmunidad anula todo |
| **Clima que potencia un tipo nuevo se activa en combate** | El bonus de clima (×1.3) se aplica tras la efectividad, nunca antes | Orden de operaciones: primero efectividad, luego clima/STAB |
| **Doble tipo incluye dos tipos nuevos (p.ej. formes con Oscuro secundario)** | Producto de ambas entradas; ×4 si ambas son débiles, ×0.25 si ambas resisten | Mismo comportamiento que los tipos oficiales |

## Dependencies

| System | Direction | Nature of Dependency |
|--------|-----------|---------------------|
| Motor base RMXP + Essentials 21.1 | Este depende | Proporciona el parser PBS y el motor de cálculo de daño |
| Pipeline PBS (convención datos-primero) | Este depende | Define cómo se estructuran los archivos de datos |
| Sistema Semi-Oscuro | Depende de este | Consume el tipo `Oscuro` |
| Purificación | Depende de este | Movimientos de purificación usan los tipos nuevos |
| Climas nuevos | Dependen de este | Potencian tipos nuevos |
| Formas regionales | Dependen de este | Añaden `Oscuro` como tipo |
| UI/Pokédex | Depende de este | Muestra los tipos nuevos |

## Tuning Knobs

| Parameter | Current Value | Safe Range | Effect of Increase | Effect of Decrease |
|-----------|--------------|------------|-------------------|-------------------|
| Multiplicadores de efectividad | ×2/×½/×0 fijos | Fijo (no se tunea por partida) | — | — |
| Número de tipos que Luz golpea x2 | 3 (Siniestro, Fantasma, Veneno) | 1–5 | Luz se vuelve dominante | Luz se vuelve marginal |
| Inmunidad de Oscuro | Solo Normal | 0–3 tipos | Oscuro contrarresta demasiado | Oscuro pierde identidad defensiva |

*(Nota: las tablas ya están cerradas en el GDD maestro, por lo que estos knobs son "de referencia de diseño" para la revisión de balance con `game-studio/balance-check`, no valores a tocar libremente en producción.)*

## Visual/Audio Requirements

| Event | Visual Feedback | Audio Feedback | Priority |
|-------|----------------|---------------|----------|
| Ataque súper eficaz con tipo nuevo | Número de daño con color del tipo + brillo característico | Golpe con acento del elemento | Alta |
| Ataque poco eficaz | Número en gris opaco | Golpe apagado | Media |
| Tipo mostrado en Pokédex/UI | Chip con símbolo y color del tipo | — | Alta |

**Símbolos y colores por tipo:**

| Tipo | Color | Símbolo |
|---|---|---|
| Luz | Dorado claro / blanco amarillento | Sol naciente estilizado o halo dorado |
| Espectral | Azul pálido / gris plata con brillo tenue | Espiral o reloj de arena roto |
| Cristal | Cian claro con reflejos blancos | Prisma o cristal facetado |
| Estelar | Violeta profundo con partículas doradas/plateadas | Estrella de cuatro puntas con estela |
| Oscuro | Negro profundo con bordes violetas | Círculo negro con anillo de sombra difusa |

## Game Feel

### Feel Reference

La sensación al acertar una debilidad con un tipo nuevo debe ser análoga a acertar una debilidad *legendaria* en la franquicia oficial: el multiplicador ×2/×4 debe leerse de inmediato por el color del número de daño y el acento sonoro del elemento — **no** requerir memorizar la tabla. Anti-referencia: que un tipo nuevo se sienta indistinguible de un tipo oficial genérico.

### Weight and Responsiveness Profile

- **Weight**: ligero — es feedback de datos, no una acción física con peso.
- **Snap quality**: crisp y binario (×2/×½/×0 se lee de un vistazo por color).
- **Failure texture**: si el jugador falla la efectividad, el feedback "poco eficaz" debe ser claro para que entienda POR QUÉ hizo poco daño (read on the failure).

## UI Requirements

| Information | Display Location | Update Frequency | Condition |
|-------------|-----------------|-----------------|-----------|
| Tipo y color del movimiento | Menú de combate / selección de movimiento | En cada turno | Siempre visible |
| Multiplicador de efectividad | Mensaje de combate + número de daño | Al resolver el golpe | Color según ×2/×½/×0 |
| Tipo del Pokémon | Pantalla de equipo / Pokédex | Al consultar | Siempre |

## Cross-References

| This Document References | Target GDD | Specific Element Referenced | Nature |
|--------------------------|-----------|----------------------------|--------|
| Semi-Oscuro usa tipo "Oscuro" como tipo puro | `design/gdd/semi-oscuro.md` (pendiente) | Estado corrompido = tipo Oscuro | Data dependency |
| Movimientos de purificación usan tipos nuevos | `design/gdd/purificacion.md` (pendiente) | Tabla de movimientos por tipo original | Data dependency |
| Climas potencian tipos nuevos ×1.3 | `design/gdd/climas.md` (pendiente) | Bonus climático sobre tipos | Rule dependency |

## Acceptance Criteria

- [ ] **GIVEN** un Pokémon de un tipo nuevo (p.ej. Luz) ataca, **WHEN** el movimiento impacta a un Siniestro, **THEN** el multiplicador es ×2 y se muestra el feedback de "súper eficaz".
- [ ] **GIVEN** un movimiento tipo Oscuro ataca a un Pokémon Normal, **WHEN** se resuelve el daño, **THEN** el resultado es ×0 y se muestra "no afecta".
- [ ] **GIVEN** los 5 tipos declarados en types.txt, **WHEN** se carga el juego, **THEN** Essentials inicializa sin errores de parser y los 5 tipos aparecen en la UI.
- [ ] **GIVEN** un Pokémon de doble tipo con inmunidad (p.ej. Oscuro/Normal golpeado por Normal), **WHEN** se calcula el daño, **THEN** el multiplicador es ×0 (inmunidad domina).
- [ ] **GIVEN** las tablas de efectividad, **WHEN** se ejecuta una auditoría (balance-check), **THEN** no hay tipo nuevo sin interacción declarada frente a cada uno de los 22 tipos restantes.
- [ ] **GIVEN** un combate, **WHEN** un tipo nuevo acierta una debilidad, **THEN** el color/símbolo del tipo es visible en el feedback de daño.
- [ ] **Performance**: la resolución de efectividad (lookup de tabla) añade <1ms por golpe.
- [ ] **No hardcoded values**: los multiplicadores y nombres de tipo viven en PBS, no en código Ruby.

## Open Questions

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| **Colisión de naming "Oscuro" vs "Siniestro"**: ¿qué ID PBS usar para Oscuro si `DARK` ya lo usa el tipo oficial Siniestro? (p.ej. `DARKVOID`, `UMBRA`, `VOID`) | technical-director | Antes de Fase 1 | — |
| **Sintaxis exacta de Essentials 21.1** para declarar un tipo y su matriz completa (¿`TypeEffectiveness` inline en types.txt, o archivo separado?) | technical-director | Antes de Fase 1 | — |
| **Representación visual por datos o por script**: ¿Essentials 21.1 permite color/símbolo de tipo vía datos, o requiere un script auxiliar para los 5 tipos nuevos? | technical-director | Fase 1 | — |
| **Nombres de los movimientos nuevos** que usan estos tipos (si alguno): se definen en GDD aparte o aquí? | game-designer | Fase 1 | — |
| **Asimetría Luz↔Oscuro (del GDD maestro)**: Oscuro declara golpear Luz ×2, pero Luz no lista a Oscuro como debilidad (solo Planta/Acero/Dragón). Resolver fuente de verdad: simetría (Luz y Oscuro se anulan mutuamente) vs. eliminar la interacción. | game-designer / technical-director | Antes de balance final | — |