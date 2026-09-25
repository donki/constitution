# Constitución general — sOCratic

> Norma común a **todo** lo que hay en este repositorio: `Mobile/`, `Games/`, `Tools/` y `Web/`.
> Cada categoría añade la suya, que **amplía** esta pero nunca la contradice:
> [Mobile](CONSTITUCION-MOBILE.md) · [Games](CONSTITUCION-GAMES.md) ·
> [Tools](CONSTITUCION-TOOLS.md) · [Web](CONSTITUCION-WEB.md)
>
> El detalle técnico exhaustivo está al lado, en [constitucion.md](constitucion.md) (secciones 1-24
> + anexos A-D). Este documento es la capa de arriba: lo que aplica a todo el catálogo y lo que se ha
> aprendido trabajando.
>
> Los cuatro documentos viven en este repositorio, que cada aplicación monta como submódulo
> `constitution/`. **Se editan aquí**: una copia suelta en la carpeta de trabajo se queda vieja sin
> que nadie se entere.
>
> **Última actualización: 2026-09-12**

---

## 1. Principios no negociables

1. **Licencia MIT y uso comercial.** Todo el código propio y **todas** las dependencias deben ser
   MIT-compatibles y aptas para uso comercial. Si una biblioteca no lo es, se descarta: no se
   discute, se busca alternativa. *(Por eso XTTS quedó fuera y se usan Kokoro y Piper para TTS.)*
2. **Sin anuncios, sin rastreadores, sin analítica.** Esto no tiene excepción ni la tendrá. No se
   mide al usuario, no se le perfila y no se vende nada de lo suyo.
3. **Privacidad primero: local por defecto.** El procesamiento es local y las aplicaciones se usan
   sin cuenta. Nada sale del dispositivo salvo lo que el usuario configure explícitamente y
   entienda. Si un requisito choca con esto, gana la privacidad.
4. **Cuenta y servidor, solo cuando la función *es* esa.** Una aplicación puede exigir cuenta y
   guardar datos en un servidor cuando su razón de ser es compartir lo mismo entre varios
   dispositivos o entre varias personas. No es una excepción que se conceda por comodidad: si la app
   funcionaría igual sin cuenta, va sin cuenta. Cuando se aplica:
   - Se usa una cuenta que el usuario **ya tiene** (Google, Microsoft). Nunca una cuenta nuestra:
     así no custodiamos contraseñas.
   - El texto del usuario sale **cifrado** (sección 5). Legible en el servidor, nunca.
   - Se dice claro **qué sale y para qué**: en la pantalla de entrada, en la política de privacidad
     y en la ficha de la tienda, y los tres tienen que decir lo mismo.
   - Sigue sin haber anuncios, rastreadores ni analítica (principio 2).

   *Hoy la única es **Task Manager**: sin cuenta no hay manera de saber que el móvil y el portátil
   son la misma persona, y sin eso no hay tareas compartidas. Las demás aplicaciones no tienen
   cuenta ni servidor, y el catálogo no se mueve hacia ahí por defecto.*
5. **Gratis y completo.** No hay funciones de pago ni recortes artificiales.

## 2. Estructura del repositorio

```
sOCProjects/
├── Mobile/    aplicaciones Android (.NET MAUI) + Shared/ + GooglePlayConsole/ + PlayConsole-Videos/
│           (Task Manager añade además un cliente de escritorio WPF en la misma carpeta)
├── Games/     videojuegos (Godot)
├── Tools/     utilidades internas y de escritorio
├── Web/       sitios y contenido web
└── testing/   material de pruebas y capturas de todas las apps
```

- **`Mobile/Shared/`** es la fuente única de la firma (`signing.props`, `socratic.keystore`) y del
  código compartido (`AuthorNotes.cs`, `ModernDialog.cs`). Se referencia con rutas relativas
  `..\Shared\...`, así que **mover una app fuera de `Mobile/` rompe la compilación**.
- Los ficheros de gobernanza viven en la raíz de la carpeta de trabajo (`sOCProjects/`), que **no**
  es un repositorio: `TAREAS-PENDIENTES.md`, `Tareas-pendientes-josep.md`,
  `Tareas-completadas.md`, `WISHLIST.md`.

## 3. Control de versiones

1. **Commitear a menudo.** Cada sesión de trabajo termina con commit. *(El 2026-08-01 se perdieron
   10 h de trabajo por tener los cambios sin commitear cuando falló una operación de ficheros.)*
2. Un repositorio por proyecto. La constitución entra como **submódulo**, no como copia.
3. Nunca se commitean secretos: keystores, contraseñas, ficheros de service account. Van en
   `.gitignore` y se pasan por CLI o por fichero local.
4. Mensajes de commit en español, en imperativo, describiendo el **qué** y el **por qué**.
5. **Claude (ni ningún otro asistente) no aparece como autor ni como colaborador.** Los commits
   van firmados solo por Josep: ningún `Co-Authored-By: Claude …`, ningún `Generated with Claude
   Code` en descripciones de PR, ningún autor de commit que no sea una persona. El código es del
   proyecto, y quien responde de él es quien lo publica. *(El 2026-09-12 se reescribió el historial
   de todos los repositorios para quitar esos avales, y GitHub dejó de listar a Claude como
   contribuidor.)* Vale también para la documentación: nada de «hecho con Claude» en READMEs ni en
   fichas de tienda.

## 4. Secretos

- Contraseñas y claves **nunca** en el repositorio ni en documentación.
- El keystore común es `Mobile/Shared/socratic.keystore` (alias `smsforwarder`). La contraseña se
  pasa por CLI al compilar.
- El service account de la Play Developer API es
  `Mobile/Hiker/Hiker/hiker-433118-98861f2881fa.json`.

## 5. Datos y bases de datos

Aplica a **toda** aplicación que guarde datos del usuario en un servidor o en una base de datos
remota, es decir a las que caen en el principio 4: si algo tiene que salir del dispositivo, sale
ilegible.

1. **El texto del usuario viaja cifrado.** Todo campo de **texto libre** que salga del dispositivo
   hacia un servidor —títulos, notas, etiquetas, nombres, apodos, direcciones, perfil— se cifra en
   el cliente antes de subir y se descifra al bajar. **La base local se queda en claro**: es del
   usuario, está en su dispositivo, y es donde buscan, filtran y ordenan las pantallas.
2. **No se cifra lo que la base necesita entender**: fechas, booleanos, números, identificadores,
   los códigos por los que se busca y los hashes que hay que comparar. Son los que deciden qué se
   baja, quién gana en un conflicto y quién puede ver una fila. Cifrarlos no deja la aplicación más
   discreta: la deja rota.
3. **Con qué clave.** Lo de una persona, con su identificador de usuario. Lo de un grupo —sus datos,
   su nombre y los apodos de sus miembros—, con el identificador del grupo: es lo que permite que lo
   lean los demás miembros, y solo ellos.
4. **Marca de versión delante** (`enc1:`). Es lo que permite convivir con lo que ya se subió en
   claro y cambiar de algoritmo más adelante sin perder lo anterior.
5. **Ningún tope de longitud en el servidor** sobre una columna cifrada. El cifrado alarga el texto
   (cabecera más un tercio por el base64) y un solo valor largo hace que se rechace el lote entero.
6. **El servidor no escribe texto del usuario.** Si un disparador o una función lo rellena, hay que
   quitarle esa parte: reescribiría en claro lo que el cliente acababa de cifrar.
7. **Al cifrar por primera vez, migrar lo que ya estaba subido**: una reescritura única de todo lo
   del usuario, apuntada para no repetirla, y sin tocar la marca de modificación, para que los demás
   dispositivos no vean un cambio que no existe.
8. **Decir hasta dónde llega**, escrito donde se implementa: una clave derivada de un dato que el
   servidor también conoce protege de quien vea la tabla, no de quien tenga la tabla y ese dato.
9. **Sincronizar no puede fallar en silencio.** El rechazo del servidor se guarda donde se pueda
   leer en una versión publicada; una traza de depuración no la lee nadie. *(El 2026-09-01 la cola
   de subida estuvo horas encallada por un CHECK de longitud sin que nada lo dijera.)*

El detalle y el cómo, en la sección 9 del submódulo de gobernanza.

## 6. Interfaz de usuario

1. **Botones con iconos, no con palabras.** Un icono reconocible por acción; el texto acompaña solo
   cuando el icono es ambiguo. Vale para móvil, escritorio, juegos y web.
2. **Los iconos son siempre planos.** Dibujo de línea en SVG, lienzo 24×24, `fill="none"`, trazo
   1.8 con puntas y uniones redondeadas, un solo color: el índigo de la paleta (`#3525CD`), blanco
   (`_w`) sobre fondos rellenos y el rojo de peligro (`_danger`) para borrar. **Nunca emoji**, ni
   iconos de color, ni degradados, ni sombras: los emoji cambian de dibujo según el teléfono, se
   cortan con la letra grande y no siguen el tema. Se guardan en `Resources/Images/ic_<nombre>.svg`
   (referencia: `Mobile/TaskManager`) y **cada acción usa el mismo icono en toda la aplicación**.
   Las banderas del selector de idioma son el único dibujo con color, también plano: **los botones
   de idioma llevan el icono de su bandera** (`ic_flag_es.svg`, `ic_flag_us.svg`, `ic_flag_gb.svg`…,
   rectángulo 24×16 con los colores de la bandera) al lado del nombre del idioma. Nunca las letras
   del país («ES», «US») ni el emoji de bandera, que Windows no dibuja y enseña como esas dos letras.
   *(Decisión de Josep del 2026-09-24.)*
   *(Decisión de Josep del 2026-09-23.)*
3. **Sistema de diseño unificado**: paleta índigo, tipografía del sistema, esquinas redondeadas,
   diálogos propios (`ModernDialog`) en vez de los del sistema.
4. **Modo claro y oscuro** en todo lo que tenga interfaz.
5. **Accesibilidad**: contraste suficiente, áreas táctiles de 48 dp mínimo, textos escalables. Con
   la letra del sistema en grande no se puede cortar ningún texto ni ningún icono: se prueba en un
   dispositivo real con la escala de letra que tenga puesta el usuario.
6. **Toda casilla de contraseña lleva el botón del ojo para verla.** Contraseñas de conexión, de
   cuenta, frases de cifrado, códigos: siempre con el ojo dentro de la casilla, que alterna entre
   puntos y texto. En WPF el `PasswordBox` no sabe destapar, así que se usa un control propio con un
   `PasswordBox` y un `TextBox` superpuestos (`RevealPasswordBox` en RCManager, referencia); en
   MAUI, `IsPassword` alternado con un botón. *(Decisión de Josep del 2026-09-16.)*
7. **Toda aplicación enseña sus novedades.** Tiene una pantalla (o diálogo) de **Novedades** con lo
   que cambió en las **cinco últimas versiones**, de la más nueva a la más antigua, sacado del
   CHANGELOG y escrito para el usuario (qué nota él, no cómo está hecho), en los dos idiomas. **Sale
   sola la primera vez que se abre la aplicación tras instalar una versión nueva**: se guarda la
   última versión vista y, si la instalada es distinta, se muestra y se actualiza; al cerrarla no
   vuelve a salir hasta la siguiente versión. Además se puede abrir cuando se quiera desde el menú o
   desde «Acerca de». *(Decisión de Josep del 2026-09-24.)*
8. **Nunca se pierde lo escrito sin avisar.** Lo que el usuario escribe o pega en una casilla **se
   aplica al salir de ella y al guardar**, no solo al pulsar Intro. Si no es válido, se avisa y no se
   guarda; nunca se descarta en silencio. *(En sOC Credentials, el secreto de doble factor pegado se
   perdía al pulsar «Guardar», y el usuario creyó que la app no sabía leer su código.)*
9. **Los errores se dicen en el idioma del usuario, con la razón y qué hacer.** El usuario nunca ve
   un mensaje técnico ni en otro idioma (excepciones, nombres de parámetros, textos de librerías, un
   JSON del servidor): cada error previsible tiene su texto localizado. Un fallo que impide lo que
   el usuario pidió (conectar, sincronizar, entrar) sale en un **aviso**, no en una línea de estado
   que se pasa por alto, y dice en una frase **la razón** y **qué hacer**. Lo técnico va al registro.
   *(sOC Credentials enseñaba «Argon2 needs a password set»; un JSON de error de veinte líneas dejó
   inservible la ventana de RC Manager.)*
10. **Guía de configuración y opciones en todas las aplicaciones.** Toda aplicación tiene una
    **guía paso a paso** con lo que hay que configurar para sacarle partido: sus opciones principales
    y, si las necesita, los ajustes del sistema o de otras aplicaciones (permisos, servicio de
    autocompletar, rol por defecto, extensiones de navegador, arranque con el sistema…).
    - Cada paso explica qué hace, tiene un **botón que lo hace** o abre la pantalla donde se hace, y
      su **estado** (hecho, pendiente u opcional) se vuelve a comprobar solo, también al volver de
      otra pantalla.
    - **Sale sola una vez**, tras el primer uso, y después se abre cuando se quiera **desde el menú**.
    - Los botones de avanzar van **fijos abajo**: con la letra grande el texto se desplaza, pero
      «Siguiente» siempre se ve.
    Referencia: `Mobile/Credentials/Pages/TutorialPage.cs`.
11. **Desbloqueo de las aplicaciones con contraseña propia** (bóveda, cuenta local…):
    - En Windows, **sin Windows Hello**: se abre con su contraseña o con **«Confiar en este usuario y
      dispositivo»**.
    - Esa confianza es **por usuario y dispositivo**, y así se dice en la interfaz: la clave queda
      protegida por la cuenta del sistema, y otro usuario del mismo equipo, u otro dispositivo,
      sigue necesitando la contraseña. Al activarla se pide confirmación.
    - En Windows, la ventana que pide la contraseña sale **pequeña y abajo a la derecha** del
      escritorio; al abrirse vuelve a su tamaño y a su sitio.
    Referencia: sOC Credentials 2026.09.24.01-02.

## 7. Idiomas

- Mínimo obligatorio: **español (es-ES)** e **inglés (en-US)**.
- Ningún texto va incrustado en el código: todo pasa por el servicio de localización.
- Los idiomas nuevos se proponen en [WISHLIST.md](WISHLIST.md) antes de comprometerse.

## 8. Calidad y entrega

Una tarea está **terminada** cuando:

- [ ] Compila sin warnings nuevos.
- [ ] Se ha probado en dispositivo real, no solo en emulador.
- [ ] Si la aplicación sincroniza, se ha probado **en dos dispositivos con la misma cuenta**: en uno
      solo no se ve nada de lo que puede fallar.
- [ ] Los textos están en los dos idiomas.
- [ ] Está commiteada.
- [ ] La documentación afectada está actualizada.
- [ ] Si cambia algo de cara al usuario, la ficha de la tienda y la web se actualizan también: su
      página y su **guía de soporte** en socraticweb0.wordpress.com, con `Web/socraticweb/build.py
      --publicar` ([Web §3-4](CONSTITUCION-WEB.md)). Una aplicación nueva entra en la web al publicarse.
- [ ] **La versión está publicada como release en GitHub**, con sus paquetes adjuntos (APK en
      Android; EXE autocontenido y MSIX en Windows) y las notas de la primera sección del
      CHANGELOG. Etiqueta `v` + versión del csproj (`v2026.09.13.2`). Se hace con
      `Mobile/Shared/release-github.py <etiqueta> <ficheros…>`, que usa la credencial de GitHub que
      ya tiene git. La copia en OneDrive es para Josep; la release es lo que queda de cada versión y
      lo que se puede enlazar. *(Regla del 2026-09-13.)*
- [ ] **Toda aplicación Windows tiene su carpeta en OneDrive**, `C:\ID\OneDrive\<App>`, y en cada
      versión se dejan ahí el **EXE** autocontenido, el **MSIX** (con la versión en el nombre, y solo
      el último) y los **assets** que el EXE necesite al lado (por ejemplo `Assets\` con adb y
      scrcpy-server en Phone Mirror), más un `LEEME.txt` que diga qué es cada cosa. Lo que hay en
      esa carpeta tiene que arrancar tal cual, sin copiar nada más. *(Regla del 2026-09-18.)*

### 8.1 Dónde se consigue cada aplicación

El README de cada repositorio lleva, al principio, una sección **«Dónde conseguirla»** con los
enlaces a sus tiendas y a las releases de GitHub:

- Android: `https://play.google.com/store/apps/details?id=<paquete>` (aunque siga en prueba
  cerrada: el enlace es el mismo cuando pase a producción).
- Windows: el enlace de producto de Microsoft Store (`https://apps.microsoft.com/detail/<id>`)
  en cuanto Partner Center lo dé; hasta entonces, la búsqueda por el nombre reservado.
- Siempre: `https://github.com/donki/<repo>/releases`.

Cuando una aplicación se publica en una tienda nueva, el README se actualiza en el mismo commit.

### 8.2 Fichas de tienda en castellano e inglés (Microsoft Store y extensiones)

Toda aplicación que va a la **Microsoft Store**, y toda **extensión de navegador** que va a una tienda
(**Edge Add-ons**, **Chrome Web Store**, **Firefox Add-ons**), tiene sus fichas en `.md` dentro del
repo, **una en castellano y otra en inglés**, listas para pegar campo a campo:

- Microsoft Store: `store/microsoft/ficha-es-ES.md` y `ficha-en-US.md` (nombre, descripción,
  características, notas de la versión, palabras clave, imágenes).
- Edge Add-ons: `store/edge/ficha-es-ES.md` y `ficha-en-US.md` (descripción, términos de búsqueda,
  propiedades, imágenes), más `store/edge/privacidad.md` con el propósito único, la justificación de
  cada permiso, el código remoto y el uso de datos.
- Chrome Web Store: `store/chrome/ficha-es-ES.md` y `ficha-en-US.md`. Como pide lo mismo que Edge,
  **remite a los textos y las imágenes de Edge** en vez de copiarlos: un solo sitio que mantener.
- Firefox Add-ons: `store/firefox/ficha-es-ES.md` (con lo común: categorías, licencia, notas para
  revisores) y `ficha-en-US.md` (solo lo que se traduce).

Las **imágenes** de la extensión (capturas 1280×800 y mosaicos 440×280 y 1400×560) van por idioma en
`store/edge/imagenes/es-ES/` y `en-US/`, hechas con el código real de la extensión y datos
inventados sobre un sitio de ejemplo (nunca datos reales ni marcas ajenas).

Las dos versiones dicen lo mismo, y se actualizan en el mismo commit que el cambio que las deja
viejas: una función que desaparece (Windows Hello en sOC Credentials, 2026-09-24) sale también de
las fichas. Los límites de cada tienda se respetan en el propio paquete (la descripción corta de la
extensión, 132 caracteres como máximo; el zip de las tiendas, sin el campo `key`). Referencia:
`Mobile/Credentials/store/`. *(Decisión de Josep del 2026-09-24.)*

### 8.3 Aplicaciones de escritorio: instancia única y entrega

- **Instancia única: manda la versión nueva.** Una aplicación de escritorio con instancia única que
  al arrancar encuentra otra **de una versión anterior** la cierra y sigue ella; no le pasa el
  turno. Con otra de la misma versión, le pide que se enseñe y **espera su acuse**; si no llega en
  unos segundos (un proceso colgado o sin ventana), arranca igual. Y si dos arrancan a la vez (la
  abre el usuario y la levanta el navegador en el mismo segundo), una espera a la otra: nunca
  quedan dos. *(En sOC Credentials, tras cada entrega el navegador relanzaba la versión vieja en
  segundo plano y abrir la nueva le pasaba el turno a la vieja: tres veces en un día.)*
- **La entrega vuelve a dejar la aplicación abierta.** Si el script de entrega cierra la aplicación
  para sustituirla, al acabar la vuelve a abrir con la versión nueva y comprueba que la que corre
  es la nueva.

### 8.4 Pruebas en el equipo del desarrollador

Cuando se prueba en el mismo equipo donde el desarrollador **usa la aplicación de verdad**:

- La aplicación tiene un **modo de pruebas aislado**, solo en Debug y activado con una variable de
  entorno (p. ej. `SOC_SANDBOX`), con sus propios datos y ajustes. Es una herramienta de
  desarrollo, **no una función para el uso normal**: nunca llega a Release.
- En ese modo no se registra en nada compartido con la instalación real: ni instancia única, ni
  puentes con navegadores, ni ganchos del sistema (autocompletar, bandeja, arranque con Windows).
- Una prueba que pulsa o teclea en la aplicación **no puede llegar a servidores, cuentas ni datos
  reales**: se usan servidores de prueba locales, bóvedas de prueba y paquetes aparte (`.test` en
  Android). Antes de cada clic se comprueba qué ventana está en primer plano, las capturas se hacen
  sin robar el foco (`PrintWindow`), y no se teclea en una ventana de la aplicación mientras la real
  está abierta.

*(La instancia de pruebas de sOC Credentials chocaba con la real y habría registrado su puente en
los navegadores del desarrollador; al teclear en ella, el autocompletar de la real la rellenó con la
contraseña maestra. Un clic perdido en RC Manager abrió una sesión RDP real contra servidores de
trabajo.)*

### 8.5 Política de privacidad en el repositorio

Cada repositorio de una aplicación publicada tiene **`PRIVACY.md` en castellano e inglés**. Es la URL
que se pone en las tiendas mientras no exista la página del catálogo, y dice lo mismo que las
fichas: qué datos se recogen (normalmente ninguno), dónde se guardan y con quién se comparten.
Referencia: `Mobile/Credentials/PRIVACY.md`.

## 9. Cómo se registran las tareas

| Fichero | Qué va ahí |
|---|---|
| `TAREAS-PENDIENTES.md` | Trabajo pendiente que puede hacer Claude. Solo lo pendiente. |
| `Tareas-pendientes-josep.md` | Lo que exige intervención humana (consolas web, móvil, cuentas). |
| `Tareas-completadas.md` | Archivo histórico de lo cerrado. |
| `WISHLIST.md` | Ideas sin compromiso ni fecha. |

Todos llevan **fecha y hora de última actualización** en la cabecera.
