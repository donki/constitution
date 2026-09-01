# Constitución — sOCratic

La norma de todo el catálogo, en un solo sitio. Cada aplicación monta este repositorio como
submódulo `constitution/`, así que **se edita aquí** y llega a todas: una copia suelta en la carpeta
de trabajo se queda vieja sin que nadie se entere.

## Qué hay y qué manda

| Documento | Qué cubre |
|---|---|
| [CONSTITUCION-GENERAL.md](CONSTITUCION-GENERAL.md) | La capa de arriba: principios no negociables, datos y bases de datos, interfaz, idiomas, entrega. **Aplica a todo.** |
| [CONSTITUCION-MOBILE.md](CONSTITUCION-MOBILE.md) | Android / .NET MAUI: firma, versionado, permisos, política de Play, publicación. |
| [CONSTITUCION-GAMES.md](CONSTITUCION-GAMES.md) | Godot: motor, assets y licencias, rendimiento. |
| [CONSTITUCION-TOOLS.md](CONSTITUCION-TOOLS.md) | Utilidades internas y de escritorio. |
| [CONSTITUCION-WEB.md](CONSTITUCION-WEB.md) | Sitio público, política de privacidad y servicios de servidor. |
| [constitucion.md](constitucion.md) | El detalle técnico exhaustivo: secciones 1-24 y anexos A-E. |

Las de categoría **amplían** la general y nunca la contradicen. Si alguna vez se contradicen, manda
la general y se corrige la otra en el mismo commit.

## Cómo se actualiza

1. Se edita aquí y se commitea con el **porqué**, no solo el qué.
2. En cada aplicación afectada: `git -C constitution pull`, y commit del puntero del submódulo.
3. Una regla nueva se escribe cuando ya se ha aprendido, con el caso real entre paréntesis. La
   constitución no es una lista de buenas intenciones: es lo que costó averiguar.

## Aviso sobre los nombres de fichero

`CONSTITUCION-GENERAL.md` se llama así, y no `CONSTITUCION.md`, porque en Windows convivir con
`constitucion.md` es imposible: el sistema de ficheros no distingue mayúsculas y una copia se lleva
la otra por delante.
