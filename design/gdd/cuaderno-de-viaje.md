# Cuaderno de Viaje

> **Status**: In Design
> **Author**: Sora (Hermes) — rol game-designer / narrative-director
> **Last Updated**: 2026-09-12
> **Last Verified**: 2026-09-12
> **Implements Pillar**: Pilar 2 — "El legado como tema central" (el Cuaderno es el objeto físico que hereda el protagonista y el puente entre narrativa y mecánica) + Pilar 4 — "Mecánicas integradas en la narrativa"

## Summary

El **Cuaderno de Viaje** es el hub central de progresión y narrativa del juego: un objeto heredado de Saga que unifica el quest log, el registro de las **Marcas de Acreedores**, el diario con los dibujos del protagonista, y el archivo de **flashbacks jugables**. Es la interfaz física del "legado" — el jugador consulta el Cuaderno para saber qué hacer, qué ha sanado y qué recuerda.

> **Quick reference** — Layer: `Presentation` · Priority: `MVP` · Key deps: `Save/load`, `Purificación`, `Marcas de Acreedores`, `Silas/Compañero`

## Overview

El protagonista hereda el Cuaderno en el ático de Pueblo Ámbar (Capítulo 1), junto a Silas y una carta de Saga. A lo largo del juego, el Cuaderno se va llenando: registra los Pokémon que purificó, las Marcas que obtuvo de los ocho Acreedores, las pistas para encontrar al siguiente Acreedor, los dibujos que el protagonista (artista) va creando, y los flashbacks del pasado de Saga que puede revivir.

Funcionalmente es el **quest log** del juego: sin él, el jugador no tiene un norte. Pero su diseño narrativo lo eleva por encima de un menú — es un objeto con peso emocional, alineado con el tema central.

## Player Fantasy

**Directo e íntimo.** La fantasía es de **custodia de un legado**: el jugador siente que está *completando* el cuaderno de su madre página a página, como un artista que hereda un lienzo inacabado. Consultar el Cuaderno debe sentirse como abrir un diario querido, no como abrir un menú de misiones. Referencia: el "diario del protagonista" de juegos narrativos (p.ej. *Life is Strange*), combinado con el quest log de un RPG clásico. El detalle de que el protagonista sea **artista** y dibuje en él refuerza la fantasía: el Cuaderno es también su obra.

## Detailed Design

### Core Rules

**Regla 1 — Hub integrado.** El Cuaderno unifica en una sola interfaz (con pestañas/secciones) los siguientes subsistemas:
1. **Viaje (quest log)** — objetivo actual, pista del siguiente Acreedor, tareas secundarias.
2. **Marcas** — las 8 Marcas de Acreedores obtenidas (ver sistema "Marcas de Acreedores").
3. **Diario** — entradas narrativas + dibujos/ilustraciones del protagonista que se desbloquean con el progreso.
4. **Purificados** — lista de Pokémon purificados con su **resultado de convergencia** (forma regional oscura si la especie la tiene, o tipo original puro si no) y el **movimiento especial exclusivo** aprendido por cada uno (la cicatriz; nunca lo aprende una forma regional natural).
5. **Recuerdos** — archivo de flashbacks jugables disponibles.

**Regla 2 — Acceso.** El Cuaderno se abre desde el menú principal (slot dedicado) y, opcionalmente, con una tecla de acceso rápido. Está disponible desde que se obtiene (Capítulo 1).

**Regla 3 — Actualización automática.** Los subsistemas se actualizan sin intervención del jugador:
- Nueva Marca → nueva entrada en "Marcas" + notificación.
- Nueva purificación → entrada en "Purificados".
- Nueva pista de Acreedor → actualización de "Viaje".
- Nuevo flashback desbloqueado → entrada en "Recuerdos".

**Regla 4 — Dibujos del protagonista (mecánica de expresión).** El protagonista es artista; en hitos narrativos, el Cuaderno recibe un **dibujo** (ilustración) que registra el momento. Los dibujos son coleccionables no funcionales, pero su presencia refuerza el arco ("de rechazar el legado a transformarlo"). El dibujo final (protagonista + Saga, "Gracias. Por todo.") cierra la colección.

**Regla 5 — Silas reacciona al Cuaderno (pista ligera).** Silas, como compañero que "traduce y presagia", reacciona cuando el Cuaderno tiene contenido nuevo o cuando el jugador está sin rumbo:
- Aviso visual sutil ("Silas quiere mostrarte algo") cuando hay una pista pendiente de leer.
- Al interactuar con Silas o el Cuaderno, ofrece una **pista** (sin resolver el objetivo, solo orientar).
- **No intrusivo**: el jugador puede ignorarlo. Es un sistema de apoyo, no un tutorial forzado.

**Regla 6 — Flashbacks revivibles.** Los flashbacks jugables (Saga joven) se archivan en "Recuerdos" una vez desbloqueados. El jugador puede **revivirlos** desde el Cuaderno. Nota de diseño: las decisiones tomadas en un flashback afectan el presente (ver sistema "Flashbacks jugables"); revivir permite re-experimentar, pero la última decisión es la que vale para el estado actual (a confirmar con ese sistema).

### States and Transitions

El Cuaderno no tiene estados dinámicos propios, pero su contenido tiene un ciclo de vida:

| State | Condition | Transición |
|-------|-----------|------------|
| `Vacío` (inicio) | Solo la carta de Saga y el prólogo | Obtener el Cuaderno (Cáp. 1) |
| `En progreso` | Pistas y Marcas parciales | Progresión de la historia |
| `Completo` | 8 Marcas + dibujo final + todos los recuerdos | Epílogo |

### Interactions with Other Systems

| Sistema | Dirección | Interface (datos que fluyen) |
|---|---|---|
| **Purificación** | Depende de este | Cada purificación añade entrada a "Purificados" |
| **Marcas de Acreedores** | Depende de este | Cada Marca obtenida se registra |
| **Flashbacks jugables** | Depende de este | Desbloqueo y archivo en "Recuerdos" |
| **Save/load** | Depende de este | Persistencia de todas las entradas del Cuaderno |
| **Silas/Compañero** | Interactúa | Reacciones/pistas ante contenido nuevo |
| **Eventos narrativos** | Depende de este | Pistas de Acreedor, cierre narrativo |

## Formulas

Sin fórmulas numéricas. El único "progreso" cuantificable es el conteo de Marcas (0–8) y de purificados (0–N), mostrados como contadores visuales.

## Edge Cases

| Scenario | Expected Behavior | Rationale |
|----------|-------------------|-----------|
| **El jugador abre el Cuaderno sin contenido nuevo** | Se muestra el estado actual sin avisos | Nada que notificar |
| **Intenta revivir un flashback no desbloqueado** | Entrada bloqueada/oculta | No debe revelar contenido futuro |
| **Marca obtenida fuera de orden (secuencia rota)** | Se registra igualmente; las Marcas son un set, no una secuencia estricta | Flexibilidad de progresión |
| **El jugador ignora la pista de Silas** | Nada se fuerza; la pista queda disponible en el Cuaderno | No intrusivo |
| **Dibujo que "sobreescribe" uno anterior** | No ocurre; cada dibujo es una entrada nueva | Colección acumulativa |

## Dependencies

| System | Direction | Nature of Dependency |
|--------|-----------|---------------------|
| Save/load + variables | Este depende | Persistencia de entradas y estado |
| Purificación | Depende de este | Lista "Purificados" |
| Marcas de Acreedores | Depende de este | Registro de Marcas |
| Flashbacks jugables | Depende de este | Archivo "Recuerdos" |
| Silas/Compañero | Interactúa | Pistas y reacciones |

## Tuning Knobs

| Parameter | Current Value | Safe Range | Effect of Increase | Effect of Decrease |
|-----------|--------------|------------|-------------------|-------------------|
| Frecuencia de aviso de Silas | solo cuando hay pista nueva | 0–siempre | Más mano invisible, menos autonomía | Menos apoyo, más exploración |
| Dibujos por hito | 1 por hito clave | 0–2 | Más coleccionables de arte | Menos |

## Visual/Audio Requirements

| Event | Visual Feedback | Audio Feedback | Priority |
|-------|----------------|---------------|----------|
| Abrir el Cuaderno | Transición de "pasar página" | Sonido de papel | Media |
| Nueva entrada/Marca | Badge de notificación | Nota suave | Media |
| Dibujo desbloqueado | Revelado de ilustración | — | Media |
| Aviso de Silas | Ícono sobre Silas | Ladrido/ruido sutil | Alta |

## Game Feel

### Feel Reference

Abrir el Cuaderno debe sentirse **táctil y personal** — como hojear un diario heredado, no como un menú genérico. La transición de "pasar página" y el sonido de papel son clave para esa sensación. Anti-referencia: una lista de texto plano tipo menú de misiones estándar.

### Weight and Responsiveness Profile

- **Weight**: ligero y calmado; el Cuaderno es un espacio de consulta, no de acción.
- **Snap quality**: transiciones suaves (fade de página), no instantáneas abruptas.
- **Failure texture**: si no hay pistas nuevas, el Cuaderno debe sentirse "completo por ahora", no vacío o roto.

## UI Requirements

| Information | Display Location | Update Frequency | Condition |
|-------------|-----------------|-----------------|-----------|
| Objetivo actual (Viaje) | Pestaña "Viaje" | Al cambiar | Siempre |
| Marcas obtenidas (0–8) | Pestaña "Marcas" | Al obtener | Siempre |
| Dibujos/ilustraciones | Pestaña "Diario" | Al desbloquear | Hitos narrativos |
| Pokémon purificados | Pestaña "Purificados" | Al purificar | Siempre |
| Flashbacks disponibles | Pestaña "Recuerdos" | Al desbloquear | Siempre |

## Cross-References

| This Document References | Target GDD | Specific Element Referenced | Nature |
|--------------------------|-----------|----------------------------|--------|
| Registro de purificaciones | `design/gdd/purificacion.md` | Regla 7 (registro en Cuaderno) | Data dependency |
| Marcas de Acreedores | `design/gdd/marcas-acreedores.md` (pendiente) | Set de 8 Marcas | Data dependency |
| Flashbacks jugables | `design/gdd/flashbacks-jugables.md` (pendiente) | Archivo y revivir | Data dependency |
| Silas como pista | `design/gdd/silas.md` (pendiente) | Reacciones al Cuaderno | Rule dependency |

## Acceptance Criteria

- [ ] **GIVEN** el juego tras obtener el Cuaderno, **WHEN** se abre desde el menú, **THEN** se muestran las 5 pestañas (Viaje, Marcas, Diario, Purificados, Recuerdos).
- [ ] **GIVEN** una purificación completada, **WHEN** se abre "Purificados", **THEN** aparece el Pokémon y su movimiento especial.
- [ ] **GIVEN** una Marca obtenida, **WHEN** se abre "Marcas", **THEN** aparece la Marca y el conteo sube.
- [ ] **GIVEN** una pista nueva de Acreedor, **WHEN** se abre "Viaje", **THEN** el objetivo actual se actualiza.
- [ ] **GIVEN** un flashback desbloqueado, **WHEN** se abre "Recuerdos", **THEN** está listado y es revivible.
- [ ] **GIVEN** contenido nuevo pendiente, **WHEN** Silas reacciona, **THEN** aparece un aviso sutil no intrusivo.
- [ ] **Performance**: abrir el Cuaderno y cambiar de pestaña <100ms.
- [ ] **No hardcoded values**: pistas, Marcas, flashbacks y dibujos viven en datos/eventos, no en código.

## Open Questions

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| **Revivir flashbacks y decisiones**: si revivo un flashback y tomo otra decisión, ¿sobreescribe el estado actual? (mi asunción: la última decisión vale; a confirmar con Flashbacks jugables) | game-designer | Fase 4 | — |
| **Frecuencia exacta de aviso de Silas**: ¿solo pistas de acreedor, o también ante objetivos secundarios? | game-designer | Fase 4 | — |
| **Dibujos**: ¿ilustraciones reales por hito, o solo placeholders hasta Fase de arte? (mi asunción: placeholders hasta Fase 2+) | art-director | Fase 4 | — |
| **Decisiones de diseño del orquestador (corregibles)**: hub integrado (sí), Silas como pista ligera (sí), flashbacks revivibles (sí). El usuario no respondió el formulario; marcar si quiere cambios. | user | Fase 2 | Pendiente de validación del usuario |