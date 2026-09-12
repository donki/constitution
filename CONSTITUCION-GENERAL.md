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
2. **Sistema de diseño unificado**: paleta índigo, tipografía del sistema, esquinas redondeadas,
   diálogos propios (`ModernDialog`) en vez de los del sistema.
3. **Modo claro y oscuro** en todo lo que tenga interfaz.
4. **Accesibilidad**: contraste suficiente, áreas táctiles de 48 dp mínimo, textos escalables.

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
- [ ] Si cambia algo de cara al usuario, la ficha o la web se actualizan también.

## 9. Cómo se registran las tareas

| Fichero | Qué va ahí |
|---|---|
| `TAREAS-PENDIENTES.md` | Trabajo pendiente que puede hacer Claude. Solo lo pendiente. |
| `Tareas-pendientes-josep.md` | Lo que exige intervención humana (consolas web, móvil, cuentas). |
| `Tareas-completadas.md` | Archivo histórico de lo cerrado. |
| `WISHLIST.md` | Ideas sin compromiso ni fecha. |

Todos llevan **fecha y hora de última actualización** en la cabecera.
