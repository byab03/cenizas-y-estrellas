# Session State — Cenizas y Estrellas

> **Task**: map-systems + design-system — **MVP COMPLETO (11/11) + #31 Fuentes de la Verdad**
> **Status**: ✓ 11 GDDs de diseño (12/12 MVP con parcial de Fuentes). Nuevo sistema: `fuentes-de-la-verdad.md` (7 catacumbas, verdad+medidor+15, bautismo sin ritual, Fuente Madre post-juego). Siguiente: gate-check + design-review, luego Alpha (climas, habilidades, formas regionales)
> **File**: design/gdd/fuentes-de-la-verdad.md (último)
> **Date**: 2026-09-14
> **Next**: `gate-check systems-design` → `design-review` en sesiones frescas

## Avance acumulado

1. Framework Game Studio (CCGS) portado e integrado a Hermes: hub `game-studio` + 73 skills + 49 agentes + 40 templates + pipeline.
2. Puente añadido a skill `cenizas-y-estrellas` (mapeo pipeline→proyecto).
3. Auditoría de estado: proyecto en Fase 0 real (GDD completo narrativo, 0 artefactos estructurales).
4. `map-systems` ejecutado: 30 sistemas identificados → `design/gdd/systems-index.md`.
5. `design-system` ejecutado (sistemas #2+#4): `design/gdd/pbs-tipos-nuevos.md` redactado (GDD completo, 13 secciones).
6. Índice actualizado: sistemas #2 y #4 en estado "Designed".

## Decisiones de diseño Semi-Oscuro (ya cerradas)

- **Pool de 8 movimientos Oscuros**: cada Semi-Oscuro recibe 4 al azar, sin repetición. Jefes usan sets manuales.
- **Medidor de purificación**: visible (barra en pantalla del Pokémon). +1 combate en equipo, +1 por 256 pasos, +3 participación activa, meta 100.
- **Piedra de la Verdad**: purificación instantánea, salta medidor y sellado, se consume.
- **Destino tras purificar (convergencia condicional)**: si la especie tiene una de las 30 formas regionales oscuras → converge a ella (tipo original + Oscuro secundario); si NO tiene → vuelve a tipo original puro. El **movimiento especial de purificación es siempre exclusivo del purificado** (la cicatriz como regalo único); la forma regional natural nunca lo aprende.
- **Doble tipo original**: el tipo primario define el movimiento especial de purificación.
- **Los 5 tipos nuevos no son "tipos originales" corrompibles** (son de fuerzas primordiales/guardianes); la tabla de movimientos especiales cubre los 18 oficiales.
- Semi-Oscuro (estado+medidor) y Purificación (sellado+ritual) son GDDs separados.

## Configuración de tipos — triángulo Oscuro→Cristal→Luz→Oscuro

El usuario definió un ciclo triangular de efectividad entre los tres tipos (análogo a Fuego/Agua/Planta):
- **Oscuro golpea x2 a Cristal** (y Cristal es débil a Oscuro)
- **Cristal golpea x2 a Luz** (y Luz es débil a Cristal)
- **Luz golpea x2 a Oscuro** (y Oscuro es débil a Luz)

Esto **reemplazó** una decisión anterior ("Luz débil a Oscuro") que quedó revertida. Ambas direcciones (ofensiva y defensiva) están declaradas de forma simétrica en `GDD.md` (Parte 3) y en `design/gdd/pbs-tipos-nuevos.md` (Reglas 4, 5 y 7/matriz cruzada).

### Self-interacciones (auditoría de simetría completada)

Se resolvieron 4 asimetrías de self-interacción, dejando la matriz **100% simétrica**:
- **Espectral** es débil a sí mismo ×2 (la memoria se erosiona a sí misma, como Dragón oficial).
- **Estelar** se resiste a sí mismo ×½ (el destino resiste el destino).
- **Oscuro** se resiste a sí mismo ×½ (el vacío contiene el vacío).
- **Estelar ↔ Oscuro** se refrenan mutuamente ×½ (relación de resistencia bidireccional).

Auditoría programática confirmó **cero asimetrías** en las 25 interacciones de la matriz cruzada (5×5).

## Configuración acordada

- Ubicación del proyecto: `C:/Users/Admin/hermes-projects/cenizas-y-estrellas/`
- MVP = Fase 0-3 de la hoja de ruta
- Review mode: default `lean`
- Método de diseño: delegar a subagente (rol technical-director) + revisión del orquestador