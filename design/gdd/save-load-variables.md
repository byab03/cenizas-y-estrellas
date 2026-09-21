# Save/Load + Variables

> **Status**: In Design
> **Author**: Sora (Hermes) — rol system-designer / engine-integration
> **Last Updated**: 2026-09-12
> **Last Verified**: 2026-09-12
> **Implements Pillar**: Pilar 9 — "Persistencia de progreso como fundamento" (sin guardar, todo lo diseñado se pierde; Save/load es la columna vertebral) + Pilar 1 — "Tecnología al servicio de la narrativa" (el sistema no debe interferir en la fantasía del legado)

## Summary

El sistema **Save/Load + Variables** es la mecánica de persistencia estándar de RPG Maker XP + Essentials, adaptada a las necesidades de *Cenizas y Estrellas*. Garantiza que todo el progreso narrativo y mecánico (Marcas, Purificados, Flashbacks, Dibujos, estado de Acreedores) sobreviva entre sesiones de juego. El diseño es deliberadamente "invisible": el jugador usa el menú Save/Load del motor y el sistema rellena las variables correctas automáticamente.

> **Quick reference** — Layer: `Engine` · Priority: `MVP` · Key deps: `Cuaderno de Viaje`, `Marcas de Acreedores`, `Purificación`, `Flashbacks jugables`, `Silas/Compañero`, `map-systems`

## Overview

RPG Maker XP usa un sistema de guardado automático basado en archivos `.rvdata2` (o el sistema nativo de Essentials), con variables switch y variables de juego globales. En *Cenizas y Estrellas*, diseñamos una capa de variables críticas que este sistema persiste y que los demás subsistemas (Cuaderno, Marcas, Purificados) leen/escriben.

El sistema no es una "nueva tecnología" — es el **framework de RPG Maker**, diseñado para ser usado. Nuestro trabajo es declarar qué variables son críticas y asegurar que se guardan/cargan correctamente.

## Detailed Design

### Core Rules

**Regla 1 — Motor estándar Essentials.** Save/Load usa el sistema nativo de RPG Maker XP/Essentials. No reinventamos el archivo .rvdata2 ni la interfaz F5/F9. Nuestro diseño declara **qué variables** deben persistir y aseguramos que los scripts de los demás sistemas escriben/leen esas variables.

**Regla 2 — Variables críticas.** Estas son las variables globales que el sistema Save/Load persiste entre sesiones:

| Variable | Tipo | Rango | Valor por defecto | Descripción |
|----------|------|-------|------------------|-------------|
| `msg_marcas_obtenidas` | integer | 0–8 | 0 | Contador de Marcas de Acreedores (se incrementa al obtener cada una). |
| `msg_acreedores_derrotados` | bitset (8 bits) | 0–255 | 0 | Bit i = 1 si el Acreedor i fue vencido (combate o prueba). |
| `msg_purificados` | texto serializado | IDs ilimitados | "" | Lista separada por comas de IDs de Pokémon purificados + movimiento especial aprendido (formato: `id1:m1,id2:m2,…`). |
| `msg_flashbacks` | texto serializado | IDs ilimitados | "" | Lista separada por comas de IDs de flashbacks desbloqueados (`id1,id2,…`). |
| `msg_dibujos` | texto serializado | IDs ilimitados | "" | Lista separada por comas de IDs de dibujos del protagonista desbloqueados (`id1,id2,…`). |
| `msg_fuentes_bautizadas` | texto serializado | IDs 1–7 | "" | IDs de Fuentes de la Verdad ya bebidas (`id1,id2,…`) — una purificación/+15 por fuente por partida (ver `fuentes-de-la-verdad.md` Regla 9). |
| `msg_fuentes_completadas` | texto serializado | IDs 1–7 | "" | Fuentes cuyo puzzle/catacumba fue completado (`id1,id2,…`) — desbloquea el acceso directo a la Sala de la Fuente (independiente de bautizadas). |
| `msg_capitulo_actual` | integer | 1–10 | 1 | Número de capítulo/acto actual. |
| `msg_silas_ubicacion` | integer | 0–3 | 0 | Ubicación Silas en el mapa (0=Pueblo Ámbar, 1=Ciudad Leteo, etc.). |

**Regla 3 — Guardado automático.** El motor guarda automáticamente al cumplirse uno de los *checkpoints críticos*:

| Checkpoint | Cuándo se dispara | Variables actualizadas |
|------------|-------------------|------------------------|
| Nueva Marca | Al obtener Marca de Acreedor | `msg_marcas_obtenidas` + `msg_acreedores_derrotados` |
| Nueva purificación | Al purificar Semi-Oscuro | `msg_purificados` |
| Nuevo flashback | Al desbloquear flashback | `msg_flashbacks` |
| Nuevo dibujo | Al obtener dibujo hito | `msg_dibujos` |
| Hito narrativo | Al iniciar capítulo nuevo | `msg_capitulo_actual` |
| Cambio Silas | Al mover Silas entre zonas | `msg_silas_ubicacion` |

**Regla 4 — Carga inicial.** Al cargar el juego, el sistema lee las variables y actualiza la interfaz:
- `msg_marcas_obtenidas` → contador en menú "Marcas".
- `msg_purificados` → lista en pestaña "Purificados" del Cuaderno.
- `msg_flashbacks` → archivo "Recuerdos" en el Cuaderno (flashbacks disponibles).
- `msg_dibujos` → colección en pestaña "Diario" del Cuaderno.
- `msg_capitulo_actual` → capítulo inicial de la historia.
- `msg_silas_ubicacion` → Silas en su posición de partida.

**Regla 5 — Interface Save/Load.** El jugador usa el menú nativo. No hay botones personalizados ni pantallas nuevas. El sistema opera *tras bambalinas* leyendo/escribiendo variables. Opcionalmente, una pequeña notificación ("Guardado automático") confirma que el checkpoint se cumplió.

### States and Transitions

El sistema Save/Load tiene pocos estados propios, pero su contenido define el estado inicial del juego:

| State | Condition | Transición |
|-------|-----------|------------|
| `Nueva partida` | No hay archivo de guardada | `msg_*` inicializados a 0/"" |
| `Partida existente` | Archivo .rvdata2 encontrado | `msg_*` cargados del archivo |
| `Guardado manual` | Usuario pulsa F5/F9 | Igual que checkpoint automático + marque en lista reciente |
| `Carga` | Usuario pulsa F9 | Todas las variables se cargan y UI se actualiza |

### Interactions with Other Systems

| Sistema | Dirección | Flujo de variables |
|---------|-----------|-------------------|
| **Cuaderno de Viaje** | Lee/escribe | Cada cambio en Cuaderno actualiza `msg_purificados`, `msg_flashbacks`, `msg_dibujos` |
| **Marcas de Acreedores** | Lee/escribe | `msg_marcas_obtenidas`, `msg_acreedores_derrotados` |
| **Purificación** | Lee/escribe | `msg_purificados` |
| **Flashbacks jugables** | Lee/escribe | `msg_flashbacks` |
| **Silas/Compañero** | Lee | `msg_silas_ubicacion` |
| **Map-systems** | Lee | `msg_capitulo_actual`, flags de zona |

### Formulas

Sin fórmulas numéricas nuevas. El único "progreso" cuantificable es el conteo de Marcas (0–8) y la presencia/ausencia de IDs en las listas serializadas.

### Edge Cases

| Escenario | Comportamiento esperado | Rationale |
|-----------|------------------------|-----------|
| **Partida corrupta** (archivo dañado) | Motor esencial usa respaldo previo o reinicio. Nuestro sistema no gestiona esto; es responsabilidad del motor. | Fallo de bajo nivel, fuera de nuestro control. |
| **Interrupción durante guardado** | El motor Essentials tiene transacciones atómicas de archivo; riesgo mínimo. | Confía en el motor. |
| **Variables fuera de rango** | Si algún script escribe valor > rango, se recorta al máximo (8 para Marcas, etc.). | Protección defensiva. |
| **Nueva partida desde guardada** | Reinicia `msg_*` a valores por defecto y borra contenido coleccionable (Marcas=0, listas vacías). | Flujo estándar. |

### Dependencies

| System | Direction | Nature |
|--------|-----------|--------|
| Cuaderno de Viaje | Depende de este | Cada entrada/modificación escribe en `msg_*` |
| Marcas de Acreedores | Depende de este | `msg_marcas_obtenidas` + `msg_acreedores_derrotados` |
| Purificación | Depende de este | `msg_purificados` |
| Flashbacks jugables | Depende de este | `msg_flashbacks` |
| Silas/Compañero | Lee | `msg_silas_ubicacion` |
| Mapa/Árbol de progreso | Lee | `msg_capitulo_actual` |

### Tuning Knobs

| Parámetro | Valor actual | Rango seguro | Efecto al aumentar | Efecto al disminuir |
|-----------|-------------|-------------|-------------------|---------------------|
| Frecuencia de checkpoint automático | cada hito narrativo | 0–entre-hitos | Más guardados, menos riesgo de pérdida | Menos guardados, más riesgo |
| Formato serialización | texto CSV simple | JSON–CSV | Más legible/extensible vs. más compacto | — |

### Visual/Audio Requirements

| Evento | Visual | Audio | Prioridad |
|--------|--------|-------|-----------|
| Guardado automático | Pequeña notificación "Guardado" (opcional) | — | Baja |
| Carga exitosa | Pantalla de "Cargando..." (motor) | Música de área | Media |
| Error de carga | Pantalla roja + mensaje | Efecto error | Alta |

### Game Feel

**Feel reference:** Save/Load debe ser **invisible y confiable** — el jugador no debe notar el sistema, solo que su progreso persiste. Anti-referencia: pantallas de "Guardando..." interminables o mensajes de error cripticos.

**Weight and Responsiveness Profile:**
- **Weight**: absolutamente ligero; el sistema no añade fricción.
- **Snap quality**: el motor gestiona el archivo al instante; nuestro script añade <50ms.
- **Failure texture**: si falla, el motor muestra error nativo; nosotros solo actualizamos variables.

### UI Requirements

| Información | Fuente | Actualización | Condición |
|-------------|--------|---------------|-----------|
| Contador de Marcas | variable `msg_marcas_obtenidas` | Al cargar / al obtener nueva | Siempre |
| Lista de purificados | variable `msg_purificados` | Al cargar / al purificar | Siempre |
| Flashbacks disponibles | variable `msg_flashbacks` | Al cargar / al desbloquear | Siempre |
| Colección de dibujos | variable `msg_dibujos` | Al cargar / al obtener | Siempre |
| Capítulo actual | variable `msg_capitulo_actual` | Al cargar / al iniciar capítulo | Siempre |
| Silas ubicación | variable `msg_silas_ubicacion` | Al cargar / al mover Silas | Siempre |

### Cross-References

| This Document References | Target GDD | Specific Element Referenced | Nature |
|--------------------------|-----------|----------------------------|--------|
| Registro de progreso | `design/gdd/cuaderno-de-viaje.md` | `msg_purificados`, `msg_flashbacks`, `msg_dibujos` | Data dependency |
| Sistema de Marcas | `design/gdd/marcas-acreedores.md` (pendiente) | `msg_marcas_obtenidas` | Data dependency |
| Flashbacks jugables | `design/gdd/flashbacks-jugables.md` (pendiente) | `msg_flashbacks` | Data dependency |
| Cuaderno | `design/gdd/cuaderno-de-viaje.md` | `msg_flashbacks`, `msg_dibujos` | Data dependency |

### Acceptance Criteria

- [ ] **GIVEN** partida nueva, **WHEN** se guarda y recarga, **THEN** todas las variables `msg_*` inicializan a valores por defecto.
- [ ] **GIVEN** Marca obtenida, **WHEN** se guarda y recarga, **THEN** `msg_marcas_obtenidas` sube y la UI del menú "Marcas" refleja el nuevo valor.
- [ ] **GIVEN** Pokémon purificado, **WHEN** se guarda y recarga, **THEN** `msg_purificados` incluye el nuevo ID + movimiento.
- [ ] **GIVEN** flashback desbloqueado, **WHEN** se guarda y recarga, **THEN** `msg_flashbacks` incluye el nuevo ID y el Cuaderno "Recuerdos" lo muestra.
- [ ] **GIVEN** dibujo obtenido, **WHEN** se guarda y recarga, **THEN** `msg_dibujos` incluye el nuevo ID y el Cuaderno "Diario" lo muestra.
- [ ] **GIVEN** partida existente, **WHEN** se carga, **THEN** todas las variables se restauran y el estado del juego coincide con cuando se guardó.
- [ ] **Performance**: operación Save/Load <200ms (tiempo de motor + sobrecarga de scripts).
- [ ] **No datos duros**: todas las variables son configurables vía datos, no hardcoded en scripts.

### Open Questions

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| **Formato de serialización de listas**: ¿texto CSV simple (`id1,id2`) o JSON embebido? (mi asunción: CSV simple por compatibilidad Essentials) | system-designer | Fase 2 | Pendiente validación |
| **Manejo de partida nueva en existente**: ¿si el usuario empieza "Nueva Partida" con un archivo guardado existente, se borra todo o se fusionan? (mi asunción: borrado total + reinicio) | system-designer | Fase 2 | Pendiente validación |
| **Variables adicionales**: ¿alguna variable crítica que se me escapó de la lista original? | system-designer | Fase 2 | — |

---

**Notas del orquestador (marcadas como corregibles):**
1. El formulario de aclaraciones expiró sin respuestas. Decisiones tomadas yo: variables estándar Essentials, CSV serialización, checkpoint por hito narrativo, guardado invisible.
2. Si prefieres formato JSON, checkpoint diferente o variables adicionales, dímelo y ajusto.

## Acceptance Criteria (reiteradas)

- [ ] GIVEN partida nueva WHEN guardar/recargar THEN variables `msg_*` inicializan por defecto
- [ ] GIVEN Marca obtenida WHEN guardar/recargar THEN `msg_marcas_obtenidas` sube + UI refleja
- [ ] GIVEN Pokémon purificado WHEN guardar/recargar THEN `msg_purificados` incluye ID + movimiento
- [ ] GIVEN flashback desbloqueado WHEN guardar/recargar THEN `msg_flashbacks` incluye ID + Cuaderno lo muestra
- [ ] GIVEN dibujo obtenido WHEN guardar/recargar THEN `msg_dibujos` incluye ID + Cuaderno lo muestra
- [ ] GIVEN partida existente WHEN cargar THEN variables restauran y estado coincide
- [ ] Performance: Save/Load <200ms
- [ ] No hardcoded values: variables configurables vía datos