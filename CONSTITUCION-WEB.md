# Constitución — Web

> Amplía la [constitución general](CONSTITUCION-GENERAL.md). El detalle técnico está en
> [constitucion.md](constitucion.md), **Anexo C** (web y servicios de servidor).
>
> Alcance: `Web/socraticweb` y cualquier sitio o servicio web del proyecto.
>
> **Última actualización: 2026-09-25**

---

## 1. Principios

1. **Sin rastreo.** Nada de analítica, cookies de terceros, píxeles ni fuentes remotas. Cero
   peticiones a dominios externos.
2. **Estático por defecto.** Si se puede resolver con HTML y CSS, no se añade JavaScript.
3. **Autocontenido.** CSS en línea o en un fichero propio; nada de CDN. Así el sitio funciona
   aunque se aloje en cualquier sitio y no filtra visitas a terceros.
4. **Accesible.** HTML semántico, contraste suficiente, funciona sin JavaScript y con el teclado.
5. **Responsive.** Unidades relativas, sin scroll horizontal, legible en móvil.
6. **Modo claro y oscuro** con `prefers-color-scheme`.

## 2. La política de privacidad es un documento de producto

- Es **una sola** para todas las aplicaciones, y su URL se referencia desde la ficha de Play Console
  de las 8 apps publicadas y desde la pantalla About de cada una.
- **Si cambia la URL, hay que actualizarla en los dos sitios de las 8.** No es opcional: Google
  rechaza fichas con política de privacidad rota.
- **Está escrita en genérico, por casos, no aplicación por aplicación** (1-sep-2026). No enumera qué
  toca cada app ni para qué: dice que cada una accede solo a lo que necesita para la función que
  anuncia en su ficha, y separa los dos casos que existen — las que funcionan enteras en el
  dispositivo y las que sincronizan, que necesitan cuenta y servidor. Así una app nueva no obliga a
  reescribirla, y la que sí toca al detalle es su ficha de Play.
- **El caso «sincroniza» lleva sus condiciones escritas**: cuenta que el usuario ya tiene, sin ver ni
  guardar contraseñas, contenido **cifrado en el dispositivo antes de salir** y guardado cifrado,
  solo lo que esa función necesita, y sin publicidad, perfiles ni analítica. Hasta el 1-sep-2026 la
  política decía que no había cuentas ni servidor, y había dejado de ser cierto.
- **Lo que dicen la política, la ficha y la aplicación tiene que ser lo mismo.** Si cambia lo que se
  guarda o dónde, se cambian los tres en el mismo ciclo, y la política **antes** de subir nada a la
  consola: publicar con una política que no cuadra es declarar algo falso.
- La fuente son `Web/socraticweb/privacidad.html` y `CONTENIDO-PARA-GOOGLE-SITES.md`; **Google Sites
  hay que actualizarlo a mano** pegando el segundo, y WordPress con `build.py --publicar` (sección 3).
- El detalle de permisos de cada app va **en su ficha de Play**, no aquí. Si una app cambia de
  permisos se actualiza la ficha; la política solo se toca si cambia el **tipo** de tratamiento
  (p.ej. una app que hasta ahora era solo local empieza a sincronizar).
- Lleva fecha de última actualización visible.

## 3. Dónde vive el sitio

Desde el 2026-09-25 el sitio público es **https://socraticweb0.wordpress.com** (WordPress.com, plan
gratuito). La política de privacidad sigue también en **Google Sites**, porque es la URL que tienen
las fichas de Play y las pantallas About; esa URL no se cambia sin hacer lo de la sección 2.

- **La fuente de verdad es el repositorio `Web/socraticweb`**, nunca el editor de WordPress. Lo que
  se cambie a mano en el panel se pierde en la siguiente publicación.
- Cada aplicación tiene su ficha en `contenido/apps/<Carpeta>.md`, con una cabecera fija (`slug`,
  `plataformas`, `lema`, `github`, `tiendas` con su estado, `descarga_alternativa`) y las secciones
  «Descripción», «Funciones principales», «Guía de uso (soporte)», «Preguntas frecuentes» y
  «Privacidad».
- `python build.py` genera la **copia local**: `sitio/` (HTML autónomo que se abre sin servidor, con
  `estilo.css`) y `wordpress/` (el cuerpo de cada página en bloques, tal cual se publica). Las dos se
  commitean: son lo que hay publicado.
- `python build.py --publicar` crea o actualiza las páginas por su `slug` en WordPress.com, deja la
  portada como página de inicio y fija título, lema e idioma del sitio. El token OAuth está en
  `D:\dev\secrets\wordpress-socraticweb0.token`, **fuera del repositorio** (General §4).
- En WordPress.com no se añaden plugins, widgets de terceros ni código de seguimiento. Sus
  estadísticas propias vienen de serie en el plan gratuito: es la única excepción al principio 1, y
  solo afecta al sitio alojado, no a la copia local.
- `CONTENIDO-PARA-GOOGLE-SITES.md` queda para la política de privacidad de Google Sites; el
  generador publica la misma política en WordPress a partir de ese texto, más la sección de la
  propia web (`contenido/legal/privacidad-web.md`).
- **El diseño va en los atributos de los bloques** (colores, bordes, espaciado, rejillas `grid` con
  ancho mínimo para que se reordenen solas en el móvil), porque el plan gratuito no admite plugins,
  CSS propio ni estilos globales. La cabecera y el pie del tema también los escribe `build.py`
  (`wordpress/_cabecera.html`, `_pie.html`). Nada de texto de ejemplo del tema a la vista.
- **Se revisa en escritorio y en móvil antes de dar nada por terminado**, con capturas de la web
  publicada: escritorio a 1440 px y móvil **con emulación de dispositivo** (Playwright con
  `devices['Pixel 7']`), no con una ventana estrecha, porque el navegador sin ventana no baja de
  unos 500 px y la captura sale cortada. Nada puede tener scroll horizontal.
- Los comentarios están cerrados en todo el sitio, y sin «Me gusta» ni botones de compartir.

## 3 bis. Normativa (España y UE)

El sitio cumple la LSSI-CE, el RGPD y la LOPDGDD con tres páginas enlazadas desde el pie de todas
las páginas, cuyo texto está en `contenido/legal/`:

- **Aviso legal** (LSSI art. 10): titular, contacto, objeto, propiedad intelectual (MIT y marcas de
  terceros), enlaces, responsabilidad y ley aplicable. Josep decidió el 2026-09-25 poner solo
  nombre y correo; si la web pasa a tener actividad económica, hay que añadir NIF y domicilio.
- **Privacidad**: la de las aplicaciones más la de la web (responsable, qué se recoge, base legal,
  derechos y reclamación ante la AEPD).
- **Cookies** (LSSI art. 22.2 y guía de la AEPD): lista de cada cookie con quién la pone, para qué
  y cuánto dura, y cómo rechazarlas. Lleva un **aviso de cookies** en todas las páginas (bloque
  `jetpack/cookie-consent` en el pie).
- **Límite conocido:** WordPress.com pone sus cookies de estadísticas (`tk_ai`, `tk_qs`) al entrar,
  antes de que se acepte nada, y en el plan gratuito no se puede impedir. Si hace falta cumplir al
  pie de la letra (bloqueo previo y botón de rechazar), hay que pasar a un plan con plugins o a un
  alojamiento propio con la copia de `sitio/`.
- **Cuando cambian las cookies** (otra plataforma, un servicio nuevo) se vuelven a medir con el
  navegador —qué cookies y qué dominios de terceros aparecen— y se actualiza la política.

## 4. Contenido

El sitio tiene tres partes, y **cada aplicación del catálogo aparece en las tres**:

1. **Portada**: qué es sOCratic, las tarjetas de todas las aplicaciones y cómo trabajamos.
2. **Una página por aplicación** (`aplicaciones/<slug>`): lema, plataformas, descripción, funciones
   y privacidad, con estos enlaces:
   - **Descarga:** la tienda si está publicada allí de verdad (Play en **producción**, Microsoft
     Store o tienda de extensiones **publicada**). Una app en prueba cerrada no tiene ficha pública
     (Play responde «no encontrado»), así que entonces se enlaza a las releases de GitHub.
   - **GitHub, siempre:** el repositorio de código y, si hay tienda, también las releases. Si el
     repositorio es privado no se enlaza (daría 404).
3. **Soporte** (`soporte/<slug>`): cómo se pone en marcha, **cada pantalla con todas sus opciones**
   con el nombre que tienen en la interfaz y qué hace cada una, y las preguntas frecuentes.

Reglas:

- **Toda aplicación del catálogo está en la web.** Solo quedan fuera dos casos: el **repositorio es
  privado**, o la aplicación **está a medias** (no se puede descargar ni usar). En cuanto deja de
  estarlo, entra en el mismo ciclo: ficha en `contenido/apps/`, `build.py --publicar` y commit.
  Hoy fuera: sOC the Game (a medias) y RemoteSoc (privado).
- **En cada cambio de cualquier aplicación se mira si hay que actualizar la web** (General §8): su
  página, su guía de soporte o el estado de sus tiendas. Es un paso de la lista de «terminada», no
  algo que se deja para después.
- **Si cambia algo de cara al usuario, cambia su guía de soporte**: una opción nueva, un botón que
  cambia de nombre o de sitio, un permiso más. La guía describe la versión que se descarga hoy; si la
  de la tienda va por detrás, se dice en las preguntas frecuentes.
- **El estado de cada tienda se revisa cada vez que cambia** (paso a producción, publicación en
  Microsoft Store, retirada), y con él cambia el botón de descarga.
- Los nombres de pantallas y opciones se copian de los textos reales de la aplicación en castellano,
  no se inventan.
- Español e inglés cuando haya versión internacional; hoy, español.
- **Botones con iconos** cuando haya interfaz propia, igual que en el resto del catálogo, y
  **planos**: SVG de línea, nunca emoji (ver [General §6.2](CONSTITUCION-GENERAL.md)).

## 5. Servicios de servidor

Ya hay uno: **Supabase**, para la sincronización de Task Manager. No es un servidor nuestro, pero
las reglas son las mismas y alguna aprieta más por ser de un tercero.

- Se aplica el **Anexo C** del submódulo: HTTPS obligatorio, secretos fuera del repositorio,
  autenticación robusta en cualquier endpoint que ejecute algo, rate-limiting y registro.
- **En el cliente solo va la clave publicable.** La clave secreta y el token de gestión no aparecen
  en el código, ni en el repositorio, ni en un fichero que se distribuya.
- **La autorización se comprueba en el servidor**, no en la aplicación: cada tabla con RLS y
  políticas que digan quién ve cada fila. Que el cliente no pida algo no es una protección.
- **El texto del usuario, cifrado** ([sección 5 de la general](CONSTITUCION-GENERAL.md)). Y si el propio
  servidor rellena algún campo de texto —un disparador, una función—, hay que quitarle esa parte: lo
  escribiría en claro.
- **El esquema se versiona** como código, en ficheros numerados dentro del repositorio de la app
  (`supabase/NN_loquesea.sql`), aplicados en orden y relanzables. Nada de cambios hechos a mano en
  la consola web que luego nadie sepa reproducir.
- **Si el servicio deja de estar disponible, la aplicación sigue funcionando**: los datos están
  también en el dispositivo, y sincronizar es lo que se pierde, no las tareas.
