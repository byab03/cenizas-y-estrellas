# Fundamentos Essentials (Motor + Maker Studio + Combate base)

> **Status**: In Design
> **Author**: Sora (Hermes) — rol technical-director / lead-programmer
> **Last Updated**: 2026-09-14
> **Implements Pillar**: Pilar técnico — "Essentials como base, no como lienzo en blanco" (configurar, no reescribir el motor)

## Summary

Documento unificador de los **3 sistemas de infraestructura MVP** (#1 Motor base RMXP + Essentials 21.1, #3 Integración Maker Studio + plugin PE21.1, #11 Combate base). No diseña mecánicas nuevas: **declara la configuración del stack y los contratos** que todos los demás GDDs asumen. Es el "suelo" bajo los 9 sistemas ya diseñados.

> **Quick reference** — Layer: `Foundation` · Priority: `MVP` · Systems cubiertos: #1, #3, #11

## 1. Motor base (sistema #1)

**Stack fijado:** RPG Maker XP (RGSS1, Ruby 1.8) + Pokémon Essentials 21.1 (única versión compatible con RMXP) + Maker Studio (editor moderno) + plugin PE21.1 de integración.

**Reglas de configuración:**
- **Regla 1 — No fork del núcleo.** Todo cambio vía PBS + scripts de extensión modulares + plugin system; nunca parchar `PokeBattle_*.rb` base in-place (facilita updates).
- **Regla 2 — Versionado:** repo git desde el día 1 (Data/*.pbs, Scripts/, Graphics/ versionados; Audio pesado vía LFS o excluido).
- **Regla 3 — Convención de nombres:** prefijo `msg_` para variables/flags del proyecto (ya usado en `save-load-variables.md`), `cye_` para scripts/plugin propios.
- **Regla 4 — Target de rendimiento:** 60 FPS en combate en hardware bajo; ningún overlay (#22) añade >2 ms/frame.

## 2. Integración Maker Studio (sistema #3)

**Qué es:** flujo de trabajo donde el mapa/eventos se crean en Maker Studio y se sincronizan al proyecto Essentials (plugin PE21.1).

- **Regla 1 — Source of truth de mapas:** `.rvdata2` (exportado) + scripts de eventos en Maker Studio. Los PBS de encuentros (#28) referencian mapas por ID numérico estable.
- **Regla 2 — Pipeline de sincronización:** Maker Studio → export → git commit → build RMXP. Nunca editar `.rvdata2` a mano.
- **Regla 3 — Placeholders de arte:** tiles/BGM provisionales de la comunidad hasta arte propio (decisión de hoja de ruta del maestro).

## 3. Combate base (sistema #11)

**Configuración sobre Essentials estándar** (turnos, capturas, estados, clima):

| Aspecto | Valor de diseño | Fuente |
|---|---|---|
| Estructura de turno | Estándar Essentials (velocidad) | Motor |
| Tipos jugables | 18 oficiales + 5 nuevos (Luz, Espectral, Cristal, Estelar, Oscuro) = **23** | `pbs-tipos-nuevos.md` (matriz 23×23 simétrica verificada) |
| Captura | Estándar (Poké Ball); Semi-Oscuros capturables normalmente | `zonas-de-rencor.md` Q resuelta |
| Reglas Semi-Oscuro en combate | Sin moveset de tipo original, pool de 4 Oscuros, no evoluciona | `semi-oscuro.md` |
| Efectividad | Tablas del PBS de tipos; los 5 nuevos siguen reglas de `pbs-tipos-nuevos.md` | PBS |
| Climas | 17 estándar + 4 nuevos (Fase Alpha #7) | Pendiente de diseño Alpha |

**No se rediseña el combate**: los GDDs de features (#5, #6, #22, #27, #28) ya definieron sus contratos con el motor. Este documento solo fija que **Essentials 21.1 en configuración base cubre el 90%** y el resto son extensiones declaradas en esos GDDs.

## Acceptance Criteria (integradas)

- [ ] **GIVEN** RMXP + Essentials 21.1 + Maker Studio instalados, **WHEN** se abre el proyecto, **THEN** arranca un build vacío funcional (capítulo 0 placeholder).
- [ ] **GIVEN** una modificación de tipo en PBS, **WHEN** se reinicia el juego, **THEN** sin tocar Ruby (convención datos primero).
- [ ] **GIVEN** un mapa editado en Maker Studio, **WHEN** se exporta, **THEN** sus IDs de encuentro casan con `encuentros-pbs.md`.
- [ ] **GIVEN** un combate con tipo nuevo (ej. Cristal vs Luz), **WHEN** se resuelve, **THEN** la efectividad respeta la matriz ×2/×½ verificada de `pbs-tipos-nuevos.md`.
- [ ] **GIVEN** 60 FPS objetivo, **WHEN** overlay del aura (#22) activo, **THEN** no baja de 58 FPS en hardware mínimo.

## Open Questions

| Pregunta | Owner | Deadline |
|---|---|---|
| ¿Maker Studio reemplaza totalmente el editor de RMXP o se mantiene dual? | technical-director | Fase 0 |
| ¿Plugin PE21.1 requiere fork para las extensiones de #5/#6 o alcanza con scripts `cye_*`? | lead-programmer | Fase 0 (prototipo) |

---

**Nota del orquestador:** fusión de 3 sistemas infraestructura en un solo documento a propósito: el framework los lista aparte, pero su "diseño" es configuración del motor — un GDD por cada uno sería relleno. Si el `gate-check` lo exige separados, los dividimos sin cambiar contenido.
