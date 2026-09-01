# Constitución — Mobile

> Amplía la [constitución general](CONSTITUCION-GENERAL.md). El detalle técnico está en
> [constitucion.md](constitucion.md), **Anexo A**.
>
> Alcance: las 9 apps en .NET MAUI de `Mobile/`.
>
> - **En Google Play (8):** FileManager, Hiker, MusicPlayer, PDFReader, QuitSmoke, SMSForwarder,
>   TXTReader, Uninstaller. Todo lo de firma, ficha, permisos y publicación es para estas.
> - **Fuera de Play (1): Task Manager.** Se reparte por APK (`C:\ID\OneDrive\TaskManager`) y es la
>   única que ademas tiene **cliente de escritorio** (WPF, `TaskManager.Desktop`) y **servidor**
>   (Supabase). Le aplican la firma y el versionado de aquí; lo de la consola de Play, solo el día
>   que se publique — y ese día hay que repasar antes la política de privacidad (ver
>   [Web §2](CONSTITUCION-WEB.md)) y las secciones 4 y 5 de la
>   [constitución general](CONSTITUCION-GENERAL.md).
>
> **Última actualización: 2026-09-01**

---

## 1. Estructura

- Una carpeta por app dentro de `Mobile/`, con su propio repositorio git.
- `Mobile/Shared/` contiene la firma y el código común. Se referencia con `..\Shared\...`.
  **No mover ninguna app fuera de `Mobile/`** o se rompen esas rutas.
- `Mobile/GooglePlayConsole/<app>/` guarda la ficha (`ficha.md`), el icono 512, la imagen destacada
  y las capturas de cada app.
- `Mobile/PlayConsole-Videos/` guarda los vídeos de demostración que pide Google.

## 2. Firma

- **Todas las apps se firman con la misma clave que File Manager**, sin excepciones:
  `Mobile/Shared/socratic.keystore`, alias `smsforwarder`
  (SHA1 `C1:CF:43:32:98:3B:A0:DA:B5:70:7C:13:DF:98:7A:DC:CD:60:E2:A4`). Las 8 apps la usan, Music
  Player incluido. **Ninguna app tiene *upload key* propia**: si alguna aparece firmada con otra
  clave, se corrige para que use la de File Manager — nunca al revés — y se comprueba que su csproj
  importe `..\Shared\signing.props` sin sobrescribir `AndroidSigningKeyStore` ni
  `AndroidSigningKeyAlias`.
- **Keystores que NO se usan.** Quedan dos copias sueltas que no valen para firmar:
  `Mobile/Shared/socratic-musicplayer-upload.keystore` (intento descartado de clave propia para
  Music Player) y `Mobile/Hiker/Hiker/socratic.keystore` (copia antigua, distinta de la compartida;
  su `keystore.password.txt` es de Hiker y no abre la clave común). La única válida es la que
  referencia `signing.props`.
- **Ojo con un falso bloqueo.** Al subir el **primer** bundle de una app nueva, Play puede
  rechazarla con *"APK signed with a key that is also used to sign an APK that is delivered to
  users. Because this app is enrolled in App Signing, you should create a different key"*. **No hay
  que crear ninguna clave nueva**: comprobado el 2026-08-28 con Music Player, la clave compartida
  se acepta sin problema en los intentos siguientes. Si aparece ese error, reintentar (con un
  `versionCode` nuevo si el anterior ya se gastó) antes de tocar nada de la firma.
- **La clave no se puede perder.** Si se pierde hay que pedirle a Google un *upload key reset*,
  con la espera que eso supone. El certificado público para esa solicitud está exportado en
  `Mobile/Shared/upload_certificate.pem` y en
  `Mobile/GooglePlayConsole/upload_certificate_socratic.pem` (son el mismo):
  `keytool -export -rfc -keystore socratic.keystore -alias smsforwarder -file upload_certificate.pem`
- Configuración centralizada en `Mobile/Shared/signing.props`, importada por cada csproj:
  `<Import Project="..\Shared\signing.props" />`.
- La contraseña **no se guarda en el repositorio**: se pasa al compilar.
  ```
  dotnet publish <proj> -c Release -f <tfm> -p:AndroidPackageFormat=aab \
    -p:AndroidSigningStorePass=<pass> -p:AndroidSigningKeyPass=<pass>
  ```

## 3. Versionado

- `ApplicationVersion` (versionCode) con formato `AAAAMMDDN` — p. ej. `202607310`. Leído de otra
  forma: **fecha × 10 + número de compilación del día**.
- `ApplicationDisplayVersion` con formato `AAAA.MM.DD.N`.
- **Si un día se pasa de 10 compilaciones**, el versionCode sigue subiendo de uno en uno y se mete
  en el hueco del día siguiente (la undécima del 1-sep es `202609020`, no `2026090110`, que se
  saldría del máximo que admite Android). El nombre visible sí sigue contando (`2026.09.01.11`). Lo
  único que Android exige es que el número **suba**; que se lea como una fecha es comodidad nuestra.
  *(Pasó el 2026-09-01 con Task Manager, que llegó a la compilación 12 del día.)*
- **El versionCode solo sube, nunca baja.** Antes de compilar una release hay que comprobar qué
  versionCode está publicado en Play; si el csproj tiene uno menor, se corrige primero.
- El versionCode del csproj y el del AAB publicado deben coincidir. Si se restaura el repositorio
  desde git, hay que revisarlo.

## 4. Permisos

- Se declara **el mínimo imprescindible**. Cada permiso del manifiesto tiene que corresponder a una
  función real y visible de la app.
- Los permisos con función asociada **deben estar promocionados en la ficha de Play Store**: es un
  requisito de política, no una recomendación.
- Los permisos restringidos (SMS, registro de llamadas, ubicación en segundo plano,
  `REQUEST_INSTALL_PACKAGES`, `MANAGE_EXTERNAL_STORAGE`) requieren declaración en la consola y, a
  menudo, un vídeo de demostración.

## 5. Política de Google Play

1. **Casos de uso de SMS/registro de llamadas.** Usar `SEND_SMS` para reenviar mensajes es un caso
   **prohibido** (*"Ineligible use case"*). La única vía válida es ser
   **app de SMS predeterminada** (*default SMS handler*), lo que obliga a implementar los 4
   componentes: `SMS_DELIVER`, `WAP_PUSH_DELIVER`, `SENDTO` y `RESPOND_VIA_MESSAGE`.
2. **API objetivo (`targetSdk`): es obligatoria, no una recomendación.** Ninguna app se compila
   ni se sube con una API objetivo por debajo de la que exige Google Play en ese momento.
   - **Hoy la exigencia es API 36 (Android 16)**: desde el **31-ago-2026** se aplica a las apps
     nuevas y a **cualquier actualización** de una app ya publicada. El 35 es otro requisito
     distinto: es el mínimo para que una app *ya publicada* siga siendo visible a usuarios nuevos
     en dispositivos con Android reciente.
   - **Se fija en dos sitios a la vez**, y los dos tienen que decir lo mismo: el TFM con versión
     (`net9.0-android36.0`, nunca `net9.0-android` a secas, que resuelve a la que le apetezca al
     SDK instalado) y `<TargetSdkVersion>36</TargetSdkVersion>` explícito en el csproj.
   - **Se comprueba en el manifiesto generado**, no en el csproj:
     `obj/Release/**/android/AndroidManifest.xml` → `android:targetSdkVersion`. Es el único sitio
     donde consta lo que de verdad se sube.
   - **Antes de cada publicación** se revisa si Google ha movido la exigencia; si la ha movido, se
     sube la API objetivo **antes** de compilar el AAB, no después del rechazo.
   - Hay **prórroga solicitable hasta el 1-nov-2026** desde *Estado de las políticas* en la consola.
   - Estado a 2026-08-28: las 8 apps en API 36. `Hiker` y `QuitSmoke` estaban en `net9.0-android`
     (resolvía a 35) y se corrigieron ese día; `TXTReader` está en `net10.0-android`, que ya
     resuelve a 36, pero conviene fijarlo explícito por lo dicho arriba.
   - Referencia: https://support.google.com/googleplay/android-developer/answer/11926878

3. **Pista de prueba cerrada**: `alpha`. Todas las apps llevan los mismos grupos de testers.
4. **Vídeo de demostración**: sin cortes, mostrando el flujo completo que justifica los permisos.
4b. **Declaraciones de la consola que bloquean la subida.** Hay permisos que la API deja subir pero
   **no dejan cerrar el `commit`** hasta que se rellena un formulario en la consola web. No es un
   error del script: es política, y el mensaje lo dice tal cual.
   - **Servicios en primer plano** (`FOREGROUND_SERVICE_*`): *App content › Foreground service
     permissions*. Sin ella, el commit devuelve `403 You must let us know whether your app uses any
     Foreground Service permissions`. Le pasa a **Hiker** desde que graba rutas con servicio en
     primer plano, y le pasará a Music Player por la reproducción en segundo plano.
   - **`READ_SMS` y demás permisos restringidos**: declaración de permisos, con vídeo. Es lo que
     tiene bloqueado a SMS Forwarder.
   - **App en estado borrador** (alta nueva, sin ninguna publicación): las releases solo se pueden
     crear con `status: draft`; con `completed` la API responde
     `400 Only releases with status draft may be created on draft app`.
5. **Developer verification** (requisito nuevo, independiente del targetSdk). Para que una app se
   instale en un dispositivo Android certificado, su nombre de paquete debe estar registrado y
   vinculado a un desarrollador con identidad verificada.
   - **Las 8 publicadas quedan cubiertas de oficio**: al crear la app en Play Console el paquete se
     registra automáticamente, y la verificación de identidad que ya exigía Play sirve como
     verificación de desarrollador.
   - **Comprobar una vez** en la portada de Play Console que no hay avisos de paquetes sin registrar,
     y en *Configuración › Cuenta de desarrollador* que la identidad sigue verificada. Un paquete sin
     registrar el **30-sep-2026** puede suponer retirada **global** de la tienda, aunque el bloqueo de
     instalación aún no aplique en España.
   - **Fechas**: 30-sep-2026 en Brasil, Indonesia, Singapur y Tailandia; global en 2027.
   - **APK firmados con `socratic.keystore`**: esa firma **no** está registrada — lo que se distribuye
     por Play va firmado con la clave de Play. Instalar por `adb` seguirá funcionando siempre, pero si
     algún día se reparten APK fuera de Play habrá que registrar los paquetes con la clave propia en
     la Android Developer Console, aportando un APK firmado con ella
     (certificado en `GooglePlayConsole/upload_certificate_socratic.pem`).
   - Referencia: https://developer.android.com/developer-verification/guides/google-play-console

## 6. Publicación

Orden estándar:

1. Bump de versión en el csproj.
2. `dotnet publish` firmado → AAB.
3. Verificar el `versionCode` real en `obj/Release/**/android/AndroidManifest.xml`.
4. Subir con el script de Python (service account `hiker-433118`) a `alpha` + `internal`.
5. Probar en dispositivo real.
6. Promover a producción solo tras el re-test.

Notas de la API:
- El flag `changesNotSentForReview=True` lo exigen unas apps y lo prohíben otras según tengan o no
  cambios pendientes de revisión. **Probar sin él primero.**
- `edits().testers().update()` **reemplaza** la lista de grupos: hay que leer la actual y hacer
  merge, nunca sobrescribir.

## 7. Interfaz

- Se aplica el sistema de diseño índigo común y `ModernDialog` en vez de los diálogos del sistema.
- **Botones con iconos**, no con palabras.
- Pantalla **About** homogénea en todas (logo, versión, contacto, idioma, licencia).
- Las que sincronizan enseñan **quién ha entrado** y con qué cuenta, en Ajustes, y permiten salir.

## 8. Pruebas en dispositivo

- Dispositivos de referencia: Xiaomi `24090RA29G` (Android 16) y tablet Samsung `SM-X130`.
- El material de pruebas va en `testing/<app>/`, con `testing/PRUEBAS.md` como índice.
- **Instalar por adb en Xiaomi/HyperOS**: además de activar *"Install via USB"* en las opciones de
  desarrollador, aparece un diálogo en pantalla con cuenta atrás de **6 segundos** que se
  auto-deniega. Hay que pulsar **Install** dentro de ese margen; si no, el error es
  `INSTALL_FAILED_USER_RESTRICTED`, que despista porque parece un problema de permisos.
- Instalar un APK firmado localmente sobre una app instalada desde Play da
  `INSTALL_FAILED_UPDATE_INCOMPATIBLE` (firma de Play ≠ upload key). Hay que desinstalar primero.
