# Zonas de Rencor

> **Status**: In Design
> **Author**: Sora (Hermes) — rol systems-designer / level-designer
> **Last Updated**: 2026-09-14
> **Last Verified**: 2026-09-14
> **Implements Pillar**: Pilar 4 — "Mecánicas integradas a la narrativa" (la corrupción del mundo ES el contenido: las zonas no son decorado, son heridas visibles de Veridia que el jugador sale a sanar)

## Summary

Las **Zonas de Rencor** son regiones del mapa donde el dolor acumulado de Veridia (los rencores de los Acreedores y sus víctimas) se filtra al mundo y **corrompe a los Pokémon silvestres**, haciendo que aparezcan en estado **Semi-Oscuro**. Son el *suministro* del loop core: aquí el jugador encuentra, captura y luego purifica. Sin ellas no hay Semi-Oscuros que sanar, y la feature estrella queda vacía.

> **Quick reference** — Layer: `Content` · Priority: `MVP` · Key deps: `Semi-Oscuro`, `Mapas y rutas`, `Encuentros/PBS de mapas` · Define: dónde spawnan los Semi-Oscuros, con qué probabilidad, cómo escalan y cómo se conectan a cada Acreedor.

## Overview

El GDD maestro (Sección 2.2 y 9) ya ubica las Zonas de Rencor en el mapa y les asigna un **porcentaje de aparición Semi-Oscuro** (5%–12%) y **jefes/eventos corruptos** concretos (Misdreavus, Electabuzz, Feebas, Larvitar, Togekiss, Gyarados, Tyranitar, Lugia…). Este sistema **formaliza esas cifras como reglas de spawn reproducibles**, define cómo escala la corrupción con la trama, y fija el contrato con `semi-oscuro.md` (estado) y `encuentros-pbs.md` (datos de spawn por mapa).

## Player Fantasy

**El explorador que ve dónde sangra el mundo.** Al entrar a una Zona de Rencor, el jugador debe *sentir* que algo está mal (niebla, aura, BGM tenso) y entender que **aquí** encontrará los Pokémon que necesita sanar. La fantasía es de **diagnóstico**: "esta zona está corrupta → aquí capturo Semi-Oscuros → los llevo al altar". Referencia emocional: las "zonas de sombra" de *Wind Waker* o la "corrupción" de *Albion* — lugares que el mundo te muestra enfermos.

## Detailed Design

### Core Rules

**Regla 1 — Cada Zona de Rencor define un % base de spawn Semi-Oscuro.** El porcentaje del GDD maestro es el **% base** de Pokémon silvestres que aparecen en estado Semi-Oscuro al entrar a esa zona:

| Zona (GDD maestro) | % base | Jefe/evento corrupto | Notas |
|---|---|---|---|
| Bosque Espina (Zona 1) | 8% | — (primera zona de aprendizaje) | Tutorial del loop |
| Mansión Abandonada | 10% | Mismagius Oscuro (jefe) | Espectros de la Memoria |
| Faro de Villa Alba | 8% | Electabuzz Oscuro (jefe) | |
| Muelle Viejo | 8% | Feebas Oscuro (evento) | |
| Mina de Vetro | 10% | Larvitar / Gyarados / Tyranitar Oscuros | |
| Praderas Sombrías (Ruta 5) | 5% | — (Menhir de la Verdad cercano) | Zona de tránsito, menor presión |
| Orfanato Nepente | 8% | Togekiss Oscuro | |
| Cavernas de Cristal Líquido | (a definir) | — | Ver Open Questions |
| Santuario Estelar (post-juego) | (alta) | Altar del Perdón | Clima Cielo Estrellado |
| Fosa Abisal (post-juego) | 12% | Lugia Oscuro (trono) | Máxima corrupción |

*(El % por zona es dato editable vía PBS/mapa; la tabla fija el valor de diseño.)*

**Regla 2 — El % escala con el progreso de la trama (arco de Acreedor).** La corrupción refleja el peso emocional del rencores aún no resueltos. Modificador simple, por defecto lineal:

```
% efectivo = % base + (arcos_sin_resolver * paso_escalado)   [topado a un máximo por zona]
```

Al **vencer/sanar al Acreedor dueño del rencor de esa zona**, su corrupción **baja** (los Pokémon sanan con el mundo). Esto hace visible la tesis del juego: *sanar el rencor cura el mapa*.

**Regla 3 — Un Semi-Oscuro aparece con su pool de 4 movimientos.** Al generar un silvestre Semi-Oscuro, se aplica la Regla de pool de `semi-oscuro.md`: 4 movimientos Oscuros al azar (o set manual si es jefe/evento nombrado). El medidor inicia en 0.

**Regla 4 — Los jefes/eventos corruptos son SIEMPRE Semi-Oscuros garantizados.** Mismagius, Electabuzz, Larvitar, Togekiss, Gyarados, Tyranitar, Lugia y el Feebas del evento aparecen forzosos en estado Semi-Oscuro, con **set manual de movimientos** (decisión Q3) y **medidor al 100** para poder purificarlos como clímax del arco (el "boss" es la curación, no solo vencerlo). Ver Edge Cases sobre "jefe al 100".

**Regla 5 — Feedback ambiental de la zona (contrato con level-design/mapa).** Cada Zona de Rencor se señala con: niebla/aura tenue, **BGM propio tenso**, y (opcional) partículas oscuras. No es mecánica, es *diegesis*: el jugador debe saber que está en zona corrupta sin leer un cartel. Se alinea con el aura de `menu-semi-oscuro.md`.

**Regla 6 — La zona define QUÉ especies pueden corromperse (subconjunto local).** Cada Zona de Rencor tiene su roster de spawn propio (de las **18 especies originales** — los 5 tipos nuevos no son corrompibles, ver `menu-semi-oscuro.md` Open Q). El % de Regla 1 se aplica **sobre ese roster local**. Así el Bosque Espina corrompe Fuego/Oscuro y Planta/Oscuro; la Mina, roca/agua; etc.

**Regla 7 — Contrato de datos (dónde vive qué).** Esta es la frontera con `encuentros-pbs.md` (sistema #28) y Essentials:
- `encuentros-pbs.md` define el **roster y rates de spawn normal** de cada mapa (fuente de verdad de "qué aparece").
- **Zonas de Rencor** añade un **flag por mapa** (`semi_oscuro_pct`, `corruption_arc`) y la **lógica de conversión**: al spawnear una especie del roster local, un chequeo `% efectivo` decide si sale `Normal` o `Semi-Oscuro`.
- La **marca de estado** (`Semi-Oscuro` + medidor) es propiedad de `semi-oscuro.md`, no de aquí.

### States and Transitions

Refleja (no define) los estados de `semi-oscuro.md`, añadidos a nivel de *sala*:

| Estado de la Zona | Condición | Transición |
|---|---|---|
| `Sana` | Sin rencor activo / Acreedor de la zona resuelto | — |
| `Corrupta` | Acreedor dueño del rencor aún en pie | Al iniciar el arco → `%` sube |
| `En sanación` | Arco parcialmente resuelto (pistas, marcas parciales) | `%` intermedio |
| `Restaurada` | Acreedor vencido/sanado | `%` baja; BGM/aura se despejan |

### Interactions with Other Systems

| Sistema | Dirección | Qué fluye |
|---|---|---|
| **Semi-Oscuro** | Aporta contexto | Esta zona decide *si* un silvestre nace Semi-Oscuro; `semi-oscuro.md` define *cómo es* ese estado |
| **Encuentros/PBS de mapas (#28)** | Modifica | Aquí el flag `semi_oscuro_pct` por mapa sobre el roster de #28 |
| **Mapas y rutas (#26)** | Depende de este | Necesita la geometría/roster del mapa para aplicar el % |
| **Actos/Capítulos (#14)** | Lee | `arcos_sin_resolver` para el escalado de Regla 2 |
| **Marcas de Acreedores (#13)** | Lee | Al resolver un Acreedor, baja la corrupción de su zona |
| **Purificación** | Alimenta | Aquí se *consigue* el Semi-Oscuro que luego se purifica |
| **Menús Semi-Oscuro (#22)** | Expone | El aura de zona se coherencia con el aura de combate |

### Formulas

**Fórmula de efectividad de spawn (`corruption_spawn_rate`):**

`%_efectivo = min(%_base + arcos_sin_resolver × paso_escala, %_max_zona)`

**Variables:**
| Variable | Símbolo | Tipo | Rango | Descripción |
|---|---|---|---|---|
| % base de zona | `b` | int | 5–12 | Valor de diseño de la Regla 1 |
| Arcos sin resolver | `a` | int | 0–8 | Nº de rencores aún vivos en la trama |
| Paso de escala | `s` | int | 0–2 | % que añade cada arco pendiente (default 1) |
| % máx de zona | `m` | int | 10–40 | Techo para que nunca sea insoportable |
| % efectivo final | `E` | int | `b`–`m` | Probabilidad de que un silvestre del roster salga Semi-Oscuro |

**Output Range:** al inicio (todo corrupto, `a` alto) una zona base 8% puede llegar a ~16–20%; al final (Acreedor resuelto, `a=0`) vuelve al % base o por debajo si `Restaurada`.
**Ejemplo:** Bosque Espina, `b=8`, `a=6`, `s=1`, `m=20` → `E = min(8+6, 20) = 14%`. Tras vencer al Acreedor de su arco, `a=5` → `E=13%` (el mundo se siente un poco más sano).

**Chequeo de conversión:** al generarse un encuentro de una especie del roster local, `roll(0..100) < E` → nace `Semi-Oscuro` (medidor 0, pool de 4), si no → `Normal`.

### Edge Cases

| Escenario | Comportamiento esperado | Rationale |
|---|---|---|
| **Jefe/evento corrupto con medidor 0 y jugador aún no listo** | Aparece garantizado Semi-Oscuro pero el combate permite *debilitarlo sin capturarlo*; si el diseño quiere purificación inmediata, arranca en `Listo` (ver nota orquestador 1) | Evita bosses intragables |
| **Roster local sin ninguna especie corruptible** | El % se ignora; salen todos Normal | Zonas temáticas sin corruptos posibles |
| **Especie con forma regional al corromperse** | Se corrompe la especie base; al purificar converge según Regla 7b de `semi-oscuro.md` | Coherente con decisión de convergencia |
| **Dos zonas del mismo arco** | Al resolver el Acreedor, **ambas** bajan `arcos_sin_resolver` (contador global de arco, no por sala) | Evita desincronía |
| **Jugador entra a zona antes de la mecánica Semi-Oscuro desbloqueada** | Si la story aún no introduce Semi-Oscuros, el flag se fuerza a 0 hasta el capítulo de desbloqueo | No revelar mecánica antes de tiempo |
| **Pokémon capturado fuera de la zona pierde aura** | El estado Semi-Oscuro viaja con el Pokémon, no con la zona | La corrupción es del individuo |
| **% efectivo calculado > probabilidad real por redondeo** | Se aplica `min(E, m)`; el techo siempre manda | Nunca zona injugable |

### Dependencies

| System | Direction | Nature |
|---|---|---|
| Mapas y rutas | Depende de este | Geometría + roster del mapa |
| Encuentros/PBS de mapas | Depende de este | Roster/rates normales a los que se añade el flag |
| Semi-Oscuro | Aporta a este | La definición de estado/medidor que aquí se instancia |
| Actos/Capítulos | Lee | Estado de arcos para el escalado |
| Marcas de Acreedores | Lee | Señal de "zona en sanación/restaurada" |

### Tuning Knobs

| Parámetro | Valor actual | Rango seguro | Efecto al aumentar | Efecto al disminuir |
|---|---|---|---|---|
| `% base` por zona | 5–12 | 3–15 | Más Semi-Oscuros, más oferta de sanación | Más escasez, loop lento |
| `paso_escala` (s) | 1 | 0–2 | La corrupción pesa más en el mundo | Escalado casi plano |
| `% máx zona` (m) | 20 (post-juego hasta 30) | 12–40 | Zonas tardías muy corruptas | Techo bajo, siempre manejable |
| Corrupción residual post-Acreedor | baja a % base | %base–(por debajo) | Zona queda "cicatrizada" | Zona sana del todo |

### Visual/Audio Requirements

| Evento | Visual | Audio | Prioridad |
|---|---|---|---|
| Entrada a Zona de Rencor | Niebla/aura ambiental tenue, tinte desaturado | Cambio a BGM tenso de zona | Alta |
| Aparición de Semi-Oscuro silvestre | Aura de combate (ver `menu-semi-oscuro.md`) | Stinger de corrupción | Alta |
| Acreedor resuelto (zona en sanación) | La niebla se aclara progresivamente | BGM se suaviza / motivo de luz | Alta |
| Zona Restaurada | Ambiente normal, luz cálida | BGM esperanzado | Media |

### Game Feel

**Feel reference:** entrar a una zona corrupta debe dar **nudo en el estómago**, no "nivel difícil". La señal es emocional (música + niebla + colores apagados), no un cartel de peligro. Al sanar el arco, el mundo **respira**: el jugador ve el mapa recuperar color y eso valida su viaje. Anti-referencia: zonas que se sienten como "dungeon de alto nivel" en lugar de "herida que hay que curar".

### UI Requirements

| Información | Dónde | Cuándo | Condición |
|---|---|---|---|
| Señal de "zona corrupta" | Ambiente (sin texto) | Al entrar | `% efectivo > 0` |
| Nombre de zona (con/sin indicador) | Banner de entrada (motor) | Al entrar | Siempre |
| Reacción de Silas en zona corrupta | Ícono/aviso | Al entrar | Corrupción activa (pista ligera, ver Cuaderno R5) |

### Cross-References

| This Document References | Target GDD | Element | Nature |
|---|---|---|---|
| Estado/medidor del Semi-Oscuro | `semi-oscuro.md` | Pool de 4, medidor 0–100, estados | Rule dependency |
| Convergencia tras purificar | `semi-oscuro.md` Regla 7b | Forma regional si existe / tipo puro | Rule dependency |
| Roster de spawn por mapa | `encuentros-pbs` (#28, pendiente) | Flag `semi_oscuro_pct` | Data dependency |
| Geometría de la zona | `mapas-rutas` (#26, pendiente) | Mapa físico | Data dependency |
| Arcos narrativos | Actos/Capítulos (#14) | `arcos_sin_resolver` | Data dependency |
| Aura de combate coherente | `menu-semi-oscuro.md` | Aura/pulso | Rule dependency |

### Acceptance Criteria

- [ ] **GIVEN** Bosque Espina (`b=8`) con 6 arcos pendientes (`s=1`), **WHEN** se genera un encuentro de una especie del roster local, **THEN** sale Semi-Oscuro con probabilidad 14% (medidor 0, pool de 4 movimientos).
- [ ] **GIVEN** un Mismagius Oscuro como jefe de la Mansión, **WHEN** el jugador lo encuentra, **THEN** aparece garantizado en estado Semi-Oscuro con **set manual** (no aleatorio).
- [ ] **GIVEN** un Semi-Oscuro capturado fuera de su zona, **WHEN** cambia de mapa, **THEN** conserva estado y medidor (la corrupción viaja con el individuo).
- [ ] **GIVEN** una zona con Acreedor resuelto, **WHEN** el jugador regresa, **THEN** `arcos_sin_resolver` bajó y el % efectivo disminuyó + ambiente más claro.
- [ ] **GIVEN** una especie que NO está en el roster corruptible de la zona, **WHEN** spawnea allí, **THEN** sale Normal (no puede corromperse fuera de su roster local).
- [ ] **GIVEN** una zona donde la mecánica Semi-Oscuro aún no está desbloqueada en la trama, **WHEN** se entra, **THEN** `semi_oscuro_pct` efectivo es 0 (sin corruptos prematuros).
- [ ] **GIVEN** el techo `m` de una zona, **WHEN** el cálculo daría `E > m`, **THEN** `E = m` (nunca zona injugable).
- [ ] **Performance**: el chequeo de conversión añade <1 ms por encuentro generado.

### Open Questions

| Question | Owner | Deadline | Resolution |
|---|---|---|---|
| **Jefes corruptos: ¿arrancan en `Listo` (purificables tras el fight) o en medidor 0?** (nota 1 del orquestador) | game-designer | Fase 3 | Propuesta: arrancan en `Listo` para que el clímax sea *sanar*, no farmear pasos |
| **Cavernas de Cristal Líquido y Santuario Estelar**: falta su % base exacto en el maestro | narrative-director | Fase 4 | Placeholder hasta mapear Zona 6/post |
| **¿Baja la corrupción global al resolver un Acreedor aunque no sea "su" zona?** (acoplamiento zona↔arco) | systems-designer | Fase 3 | Propuesta: cada zona tiene un "Acreedor ancla"; solo ese la restaura |
| **Formas de captura**: ¿el Semi-Oscuro silvestre se captura igual que un normal o necesita algún tipo especial de Pokéball? | game-designer | Fase 2 | Propuesta: captura estándar (no añadir fricción); la diferencia está en purificar |

---

**Notas del orquestador (Sora) — decisiones corregibles:**
1. **Jefes/eventos corruptos arrancan en `Listo` (100)** para que el momento cumbre de un Acreedor sea la **sanación** (ritual en altar cercano), no pasar horas farmeándole pasos al boss. Si prefieres que haya que sanarlos "a lo XD" (combate prolongado con medidor desde 0), lo cambio a medidor 0 y ajusto.
2. **Cada zona tiene un "Acreedor ancla"**; resolverlo restaura esa zona. El escalado global (`arcos_sin_resolver`) da presión general, pero la *sanación visible* es local — así el mundo reacciona de forma legible.
3. **El % es un flag de mapa**, no código: se declara en datos (PBS/mapa) junto al roster de `encuentros-pbs`. Respeta "datos primero" (Pilar técnico del proyecto).

## Notas de consistencia

Este GDD **no define** el estado Semi-Oscuro ni la purificación (viven en `semi-oscuro.md` y `purificacion.md`); solo decide **dónde y con qué frecuencia nacen** los Semi-Oscuros, y **cómo el mapa reacciona** al sanar los rencores. Cualquier conflicto de estado/medidor se resuelve a favor de esos documentos.
