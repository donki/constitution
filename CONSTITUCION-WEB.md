# Constitución — Web

> Amplía la [constitución general](CONSTITUCION-GENERAL.md). El detalle técnico está en
> [constitucion.md](constitucion.md), **Anexo C** (web y servicios de servidor).
>
> Alcance: `Web/socraticweb` y cualquier sitio o servicio web del proyecto.
>
> **Última actualización: 2026-09-01**

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
- **Hoy la política dice que no hay cuentas de usuario y que no se guarda nada en ningún servidor.**
  Es cierto para las 8 publicadas. **Deja de serlo el día que se publique Task Manager**, que entra
  con cuenta de Google o Microsoft y guarda las tareas en Supabase. Ese día, y **antes** de subir
  nada a la consola, la política tiene que decir: qué cuenta se usa y para qué, qué se guarda en el
  servidor, que el texto se guarda **cifrado**, dónde está alojado y cómo se borra todo. Publicar la
  app con la política actual sería declarar algo falso.
- Tiene que reflejar la realidad de cada app: qué permisos usa y para qué. Si una app cambia de
  permisos, la política se actualiza en el mismo ciclo.
- Lleva fecha de última actualización visible.

## 3. Google Sites

El sitio público vive en Google Sites, que **no admite subir HTML**. Por eso:

- En `Web/socraticweb/` se mantiene la versión HTML autónoma (`index.html`, `privacidad.html`),
  que es la fuente de verdad del contenido y sirve si algún día se aloja en otro sitio.
- `CONTENIDO-PARA-GOOGLE-SITES.md` contiene el mismo texto en formato pegable en el editor.
- Cuando se cambia el contenido, se cambian **los dos** y se vuelve a pegar en Sites.

## 4. Contenido

- La página de inicio lista las aplicaciones con su estado real (producción / prueba cerrada) y su
  enlace a Google Play. **El estado se revisa cada vez que cambia en Play**, no se deja obsoleto.
- Español e inglés cuando haya versión internacional; hoy, español.
- **Botones con iconos** cuando haya interfaz, igual que en el resto del catálogo.

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
