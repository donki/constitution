# Constitución — Games

> Amplía la [constitución general](CONSTITUCION-GENERAL.md).
> Alcance: `Games/sOCTheGame` (Godot) y los videojuegos que vengan después.
>
> **Última actualización: 2026-09-01**

---

## 1. Motor y estructura

- **Godot** como motor. Escenas `.tscn` y scripts `.gd`, con `.uid` versionados junto a ellos.
- Estructura mínima:
  ```
  <juego>/
  ├── scenes/      escenas de juego y de interfaz
  ├── scripts/     lógica
  ├── assets/      arte, audio, fuentes (con su procedencia documentada)
  ├── tools/       generadores y utilidades de desarrollo (Python/GDScript)
  └── i18n/        cadenas traducibles
  ```
- `tools/` es para desarrollo: **no se empaqueta** en la build final.

## 2. Assets y licencias

1. Todo asset (arte, música, efectos, fuentes, voces) debe ser **MIT-compatible o de uso comercial
   libre**, igual que el código. Sin excepciones.
2. Cada asset lleva su **procedencia y licencia documentadas**. Si no se puede acreditar, no entra.
3. **Voz y audio generados**: se usan modelos con licencia apta para uso comercial —
   **Kokoro** y **Piper**. *(XTTS quedó descartado por licencia.)*
4. Los assets generados por herramientas de `tools/` se regeneran de forma reproducible: el script
   que los produce se versiona junto al resultado.

## 3. Contenido

- Clasificación por edades adecuada y declarada en la tienda.
- Sin compras integradas, sin anuncios, sin telemetría — igual que el resto del catálogo.
- El progreso del jugador se guarda **en local**. Un juego no es de los casos del principio 4 de la
  general: no necesita cuenta. Si alguna vez hubiera partida compartida o marcador entre
  dispositivos, aplicaría entera la [sección 5 de la general](CONSTITUCION-GENERAL.md) — el nombre que
  pone el jugador es texto suyo y saldría cifrado.

## 4. Interfaz

- Se aplica el sistema de diseño común (paleta índigo, esquinas redondeadas, modo claro/oscuro).
- **Botones con iconos**, no con palabras.
- Los menús deben ser navegables con mando y con pantalla táctil, no solo con ratón.

## 5. Idiomas

- Español e inglés obligatorios, con las cadenas extraídas a `i18n/` mediante el script
  `tools/extract_i18n.gd`. Ningún texto incrustado en escenas ni en scripts.

## 6. Rendimiento

- Objetivo: **60 fps estables** en el dispositivo de referencia.
- Límite de tamaño de texturas fijado por proyecto (`tools/set_texture_size_limit.py`) y aplicado
  antes de exportar.
- Se mide antes de publicar: tiempo de carga inicial, memoria y fps en la escena más pesada.

## 7. Publicación

- Si el juego va a Google Play, se le aplica también la
  [constitución de Mobile](CONSTITUCION-MOBILE.md): firma con `socratic.keystore`,
  versionado `AAAAMMDDNN` (con el contador del día a dos cifras, ver su sección 3), targetSdk
  vigente y ficha con capturas reales de juego.
- Build de Android desde `tools/build_android.ps1`.
