# Constitución de Proyectos de Software

## 1. Propósito
Documento de referencia para la **arquitectura, gobernanza, seguridad, publicación y mantenimiento**
de cualquier proyecto de software, con independencia de su plataforma (móvil, escritorio,
web/servicios de servidor o código nativo) y de su stack tecnológico. Define estándares
operacionales y de calidad que todo proyecto debe cumplir.

Su origen es una constitución específica de aplicaciones **Android en .NET MAUI**; se ha
generalizado para que **el núcleo común** (secciones 1–22) aplique a *cualquier* proyecto y los
detalles específicos de cada plataforma vivan en sus **anexos** (A–D). Un proyecto cumple esta
constitución aplicando el núcleo común **más** el/los anexo(s) de las plataformas a las que se
dirige.

## 2. Alcance y cómo se aplica
- **Tipos de proyecto cubiertos:** aplicaciones móviles, aplicaciones de escritorio, servicios y
  APIs de servidor, paneles/aplicaciones web y módulos de código nativo.
- **Ciclo cubierto:** desde la arquitectura hasta la publicación, el despliegue y el mantenimiento.
- **Áreas cubiertas:** estructura, licencia y procedencia, seguridad y secretos, arquitectura de
  presentación, internacionalización, persistencia, errores y logging, versionado, build,
  distribución, despliegue de servidor, autoactualización, calidad, colaboración y estilo.
- **Estructura del documento:**
  - **Núcleo común (secciones 3–22):** obligatorio para todo proyecto.
  - **Anexo A — Móvil y tiendas de aplicaciones** (.NET MAUI / Android / Google Play…).
  - **Anexo B — Escritorio** (Windows/Linux/macOS).
  - **Anexo C — Web y servicios de servidor** (ASP.NET Core / APIs / paneles / autoalojado).
  - **Anexo D — Código nativo** (C++ / interop).
- **Fuera del alcance:** decisiones de producto específicas, requisitos funcionales particulares y
  la elección de integraciones de terceros concretas.

## 3. Principios no negociables
- **Privacidad primero:** los datos del usuario se mantienen bajo su control; se minimiza la
  recolección y la salida de datos fuera del dispositivo o del servidor autoalojado.
- **Mínimo privilegio:** solo los permisos, puertos y accesos estrictamente necesarios.
- **Seguridad por defecto:** cifrado en tránsito siempre que haya red; secretos fuera del
  repositorio; credenciales sin valores por defecto (ver secciones 4 y 6).
- **Confiabilidad operativa:** prevenir fallos silenciosos y errores no documentados.
- **Trazabilidad:** toda publicación y todo despliegue debe ser reproducible y verificable.
- **Reproducibilidad:** cualquier versión debe poder regenerarse desde el código fuente.
- **Procedencia limpia de la licencia:** no incorporar código que contamine la licencia elegida
  (ver sección 4).
- **Cambios pequeños y seguros:** evitar refactorizaciones masivas sin necesidad.
- **Estabilidad sobre mejora:** ninguna mejora justifica dejar el producto en estado no funcional
  (ver sección 21).

## 4. Licencia, Procedencia y Dependencias
- **Licencia MIT obligatoria:** todo proyecto se publica bajo la licencia **MIT**, declarada en un
  fichero `LICENSE` en la raíz del repositorio; todo el código de primera mano se publica bajo ella.
- **Código propio:** salvo decisión explícita y documentada, el código es de desarrollo propio; no
  se forkea, vendoriza ni reutiliza código de terceros con licencia **copyleft** (GPL/AGPL/LGPL)
  que contamine la licencia del proyecto.
- **Dependencias permitidas:** solo dependencias de terceros con licencia **permisiva**
  (MIT/BSD/Apache-2.0) o **APIs del sistema operativo**. El uso de APIs del SO no contamina la
  licencia del proyecto.
- **Inventario de terceros:** mantener un fichero de avisos de terceros
  (`THIRD-PARTY-NOTICES.md`) con cada dependencia, su uso, su licencia y su titular.
- **Revisión antes de añadir:** antes de incorporar una dependencia nueva, verificar su licencia y
  registrarla en el inventario.

## 5. Estructura del Proyecto
Reglas de organización aplicables a cualquier stack (los nombres concretos de carpetas se
detallan en los anexos):
- **Separación por responsabilidades:**
  - **Lógica de negocio / servicios:** aislada y reutilizable (carpeta de servicios), nunca en la
    interfaz.
  - **Modelos:** clases de datos y entidades, sin lógica de presentación.
  - **Presentación:** estado y comandos separados de la interfaz (ver sección 7).
  - **Interfaz / vistas:** solo presentación.
  - **Utilidades transversales / helpers / convertidores:** agrupadas y reutilizables.
  - **Código específico de plataforma:** encapsulado en su módulo o carpeta de plataforma; la capa
    común no contiene referencias directas a APIs de una plataforma concreta.
- **Código compartido:** los contratos comunes (protocolo, modelos del API, constantes de versión)
  residen en un módulo compartido único, reutilizado por todos los clientes y servidores.
- **Inyección de dependencias** para servicios y componentes de presentación; no instanciarlos
  manualmente saltándose el contenedor, salvo casos justificados.
- **Una sola fuente por componente:** no debe quedar código muerto ni vistas/componentes
  duplicados; mantener un único origen por cada responsabilidad.

## 6. Seguridad, Secretos y Cumplimiento
- **Política de secretos:**
  - Nunca subir al repositorio archivos con credenciales (JSON de servicio, contraseñas, almacenes
    de claves, tokens).
  - Usar variables de entorno, ficheros locales *gitignored* o secretos del pipeline de CI para las
    credenciales.
  - **Sin credenciales por defecto:** los ficheros de configuración versionados se publican con los
    campos sensibles vacíos; con credenciales vacías el sistema no permite el acceso.
  - Tratar las credenciales como material sensible incluso en máquinas locales.
- **Cifrado en tránsito:**
  - Todo canal de red usa cifrado (TLS/HTTPS) por defecto.
  - Cuando el contenido es sensible y atraviesa intermediarios, aplicar cifrado **extremo a
    extremo** de modo que el intermediario no vea el contenido.
  - Documentar qué viaja en claro (y por qué es inocuo) si algo no puede cifrarse.
- **Almacenamiento de credenciales:** las contraseñas se guardan como **hash**, nunca en claro;
  los secretos en reposo se protegen según la sensibilidad del dato (sección 10 del anexo aplicable).
- **Mínimo privilegio:** revisar y justificar cada permiso (móvil), cada puerto abierto en el
  firewall (servidor) y cada acceso concedido, antes de cada publicación o despliegue.
- **Consentimiento y uso responsable:** cuando el software pueda afectar a terceros (acceso remoto,
  recolección de datos), incluir aviso de uso responsable y, cuando proceda, confirmación explícita
  en el equipo afectado.
- **Metadata veraz:** descripciones, títulos y notas de lanzamiento precisos; sin promesas no
  cumplidas ni contenido engañoso, en todos los idiomas soportados.
- **Aviso legal y exención de responsabilidad (obligatorio):** toda aplicación incluye, de forma
  visible y accesible (p.ej. en la pantalla "Acerca de"), un aviso legal localizado en todos los
  idiomas soportados (mínimo es/en) que establezca:
  - El software se entrega "tal como está", sin garantías de ningún tipo, expresas o implícitas.
  - En ningún caso los autores serán responsables de reclamaciones, daños u otras responsabilidades
    derivadas del uso del software.
  - El uso es bajo el propio riesgo del usuario; al usar la aplicación, el usuario exime al
    desarrollador de toda responsabilidad.
  - El aviso no debe contradecir la licencia (sección 4) ni la política de privacidad.

## 7. Arquitectura de Presentación
- **Separar presentación de lógica siempre**, sea cual sea el framework de interfaz.
- **La lógica de negocio reside en Services**, nunca en la interfaz. El code-behind / la actividad /
  el controlador se limita a orquestar la interfaz y **delega en Services**; no contiene reglas de
  negocio ni acceso directo a datos.
- **Capa de presentación ligera:** preferir la solución más simple que separe interfaz y lógica.
  **Evitar patrones de presentación pesados** (por ejemplo, MVVM con ViewModels y librerías de
  binding) cuando no aporten un valor claro; el code-behind delgado que delega en Services es el
  enfoque por defecto.
- Los servicios se registran e **inyectan por dependencias**; no se instancian manualmente,
  salvo casos justificados.
- No mezclar dos estilos de presentación para el mismo tipo de pantalla sin justificación.

## 8. Internacionalización y Localización (i18n)
- Todo texto visible al usuario debe ser **localizable**; no incrustar cadenas fijas en la interfaz
  cuando exista soporte multiidioma.
- Centralizar las traducciones (servicio de localización o ficheros de recursos, p.ej. `.resx`) y
  acceder a ellas de forma uniforme.
- Definir un **idioma por defecto** válido y fijarlo antes de eliminar cualquier traducción.
- Formatos sensibles a la cultura (fechas, números, divisas) deben usar la cultura del usuario, no
  valores fijos.
- Mantener las traducciones de la interfaz sincronizadas con la metadata de la tienda/distribución
  en todos los idiomas soportados.
- **Bilingüe siempre (es/en):** el español y el inglés son idiomas base obligatorios. La interfaz y
  todos los materiales (ficha de tienda, política de privacidad, notas de versión y aviso legal)
  deben estar disponibles, como mínimo, en español e inglés.

## 9. Persistencia de Datos
- Coherente con "Privacidad primero" (sección 3): los datos del usuario se almacenan en el
  dispositivo o en el servidor autoalojado bajo su control.
- Usar el mecanismo adecuado según el dato: preferencias/clave-valor para configuración ligera;
  almacenamiento estructurado (archivo o base de datos) para históricos y conjuntos de datos.
- No almacenar secretos ni credenciales en claro; aplicar la política de la sección 6.
- Validar y manejar datos ausentes o corruptos sin provocar fallos silenciosos (sección 3).
- Considerar la **migración de esquema** cuando cambie el formato de los datos persistidos entre
  versiones.

## 10. Manejo de Errores y Registro (Logging)
- **Prohibido el fallo silencioso** (sección 3): toda excepción esperada se gestiona y se informa
  de forma adecuada.
- Operaciones que puedan fallar (E/S, red, plataforma) se envuelven con manejo de errores y
  mensajes claros al usuario cuando proceda.
- Usar el sistema de logging del framework; habilitar trazas de depuración solo en configuración
  Debug.
- **No registrar** datos personales ni secretos en los logs.
- **Logging operativo en servidores/servicios:** logging persistente con **rotación** (límite de
  tamaño y número de ficheros) para no agotar el disco; cuando aplique, segmentar por sesión/par.
- Antes de publicar, revisar que no queden excepciones no controladas en los flujos principales.

## 11. Versionado
- **Esquema único y coherente** para todo el proyecto, definido en una **constante única**
  reutilizada por todos los componentes. Esquemas admitidos:
  - **Fecha:** `AAAA.MM.DD.N` (p.ej. `2026.06.18.1`), donde `N` es el build incremental del día.
  - **Semántico / tienda:** versión legible (`1.10.0`) más, cuando la tienda lo exija, un entero
    incremental de build (ver Anexo A).
- **Versión legible y código de versión deben actualizarse en sincronía** antes de cada publicación.
- **Incremento automatizado:** un script de build incrementa la versión de forma reproducible (la
  constante de versión y, en móvil, el `versionCode`).
- Registrar todos los cambios de versión en un **CHANGELOG**.

## 12. Build, Empaquetado y Reproducibilidad
- **Build reproducible:** scripts versionados (p.ej. PowerShell) que automatizan la compilación, el
  empaquetado y el firmado, de modo que cualquier versión se regenere desde el código fuente.
- **Salida autocontenida** cuando reduzca dependencias del entorno de destino (p.ej. *publish
  self-contained* para servicios de servidor).
- **Multi-toolchain:** si el proyecto combina varios lenguajes/toolchains (gestionado + nativo),
  documentar todas las herramientas requeridas y orquestar el build completo con un único script.
- **Artefactos firmados** cuando la plataforma lo requiera, con el material de firma fuera del
  repositorio (sección 6).
- La compilación debe finalizar **sin errores**; las advertencias se documentan y se reducen
  progresivamente.

## 13. Distribución y Publicación
- **Liberación escalonada:** validar primero en un canal restringido (pruebas cerradas/internas,
  rollout parcial) antes de la distribución general; no pasar a producción sin haber validado y sin
  cumplir la lista de verificación de la sección 19.
- **Verificación de integridad:** los artefactos distribuidos (instaladores, paquetes de
  actualización) se verifican por **hash (SHA-256)** antes de instalarse.
- **Metadata consistente:** título, descripciones, notas de lanzamiento y capturas coherentes en
  todos los idiomas soportados; fijar el idioma por defecto antes de eliminar traducciones.
- **No mezclar** cambios funcionales con cambios solo de metadata en la misma publicación.
- Los detalles por canal (tiendas, instaladores de escritorio, autoactualización autoalojada) se
  describen en los anexos y en la sección 15.

## 14. Despliegue de Servidor y Servicios
*(Aplica a proyectos con componente de servidor; ver Anexo C para el detalle.)*
- **Instalación como servicio** del sistema (servicios de Windows / unidades systemd) con arranque
  automático; el instalador abre los puertos de firewall necesarios y aplica la configuración.
- **Configuración externalizada** en ficheros junto al binario, sin secretos versionados (sección 6).
- **Alcanzabilidad:** las direcciones públicas que el servidor anuncia a los clientes deben ser
  realmente alcanzables por ellos (IP pública / DNS), no direcciones de LAN privada.
- **CI/CD:** flujo `checkout → instalar toolchain → build → desplegar → verificar`. Las credenciales
  de despliegue se guardan como **secretos del pipeline**, nunca en el repositorio ni en el YAML.
- **Verificación post-despliegue:** comprobar que el servicio responde y sirve la versión esperada.

## 15. Autoactualización
*(Aplica a clientes/servicios con actualización automática.)*
- **Comprobación de versión** contra una fuente de confianza (el propio servidor del proyecto) al
  arrancar o conectar.
- **Descarga desde fuente de confianza** y **verificación SHA-256** del artefacto antes de
  instalarlo (sección 13).
- **Reinstalación preservando la configuración** del usuario (p.ej. swap + reinicio en escritorio;
  instalador del sistema con confirmación del usuario en móvil).
- **Rollout escalonado:** limitar las descargas concurrentes en el servidor y aplicar *jitter* por
  dispositivo para no saturar; no actualizar a todos los clientes a la vez.

## 16. Estándares de Calidad
- **Compilación:** sin errores; advertencias documentadas y en reducción.
- **Pruebas:** incluir pruebas unitarias y de integración cuando sea viable; para sistemas
  distribuidos, **prueba de extremo a extremo automatizada** ejecutable sin intervención manual.
- **Permisos y superficie:** revisar permisos (móvil), puertos (servidor) y accesos, justificando
  cada uno.
- **Validación manual:** cada cambio funcional se verifica en un entorno real (dispositivo real,
  emulador, servidor de pruebas) antes de publicar.
- **Documentación:** mantener README y CHANGELOG actualizados con los cambios relevantes.

## 17. Flujo de Publicación Estándar
1. Actualizar la versión (legible y código) en la constante única / proyecto.
2. Actualizar el **CHANGELOG** con los cambios de la versión.
3. Ejecutar el script de build y firmado (reproducible); generar los artefactos.
4. Validar localmente los artefactos firmados (y verificar su hash).
5. Publicar/desplegar en el canal restringido correspondiente (pruebas cerradas / rollout parcial /
   servidor de pruebas).
6. Confirmar la lista de verificación del canal (ver anexo): versión incremental correcta, canal
   adecuado, iconografía/idioma por defecto válidos, metadata en todos los idiomas, permisos/puertos
   justificados.
7. Promover a producción y registrar la publicación en el repositorio (tag de versión, commit de
   merge).

## 18. Plan de Contingencia
Errores comunes y procedimientos de recuperación (adaptar al stack):
- **Compilación bloqueada por permisos/procesos:** cerrar procesos de compilación, limpiar
  `bin`/`obj` (o equivalente), reintentar.
- **Código/versión de build rechazado por la tienda:** regenerar con un entero incremental válido
  no usado antes.
- **Bloqueo por idioma por defecto:** cambiar el idioma por defecto a uno soportado y reintentar.
- **Problemas de firma:** validar accesibilidad del keystore/certificado, contraseña y alias.
- **Fallo de autenticación de publicación/despliegue:** verificar cuenta de servicio/credenciales,
  permisos y caducidad de tokens.
- **Fallo de despliegue de servidor:** verificar conectividad (WinRM/SSH), reglas de firewall y
  estado de los servicios.

## 19. Criterio de Entrega (Definition of Done)
Una versión se considera lista para producción cuando:
- Compila sin errores y genera artefactos firmados correctamente (y con hash verificable).
- Se publica/despliega en el canal correspondiente sin fallos.
- La iconografía es visible y legible donde aplique.
- La metadata (título, descripción, notas) está en todos los idiomas soportados.
- Los permisos/puertos solicitados están justificados y documentados.
- Los cambios principales están registrados en el CHANGELOG.
- La versión (legible y código) está incrementada y registrada.
- Se ha realizado validación manual en un entorno real (dispositivo, emulador o servidor de pruebas).
- La aplicación incluye el aviso legal de exención de responsabilidad (sección 6), localizado en
  todos los idiomas soportados.

## 20. Convenciones de Colaboración en Repositorio
- **Confirmaciones (commits) atómicas:** un cambio lógico por commit.
- **Mensajes de commit descriptivos:** explicar el "qué" y el "por qué", no solo el "qué".
- **No mezclar** cambios funcionales con cambios cosméticos o refactorización.
- **Ramas de trabajo:** crear una rama descriptiva para cada feature o fix.
- **Pull requests:** incluir descripción del cambio, impacto y verificación realizada; al menos un
  revisor antes de merge a la rama principal.
- **Documentación:** actualizar README y CHANGELOG cuando aplique.
- **Publicación/despliegue:** si el cambio afecta a build, scripts o despliegue, registrar el
  procedimiento.

## 21. Revisión Continua y Mejora Incremental
- Revisar periódicamente el código, la arquitectura y las dependencias para identificar mejoras.
- Aplicar mejoras **pequeñas e incrementales**: refactorización, actualización de dependencias,
  optimización de rendimiento, claridad de código.
- **Principio fundamental: nunca sacrificar estabilidad por mejora.** Todo cambio debe dejar el
  producto en estado funcional y verificado.
- Los cambios de mejora deben: aislarse en rama y commit independientes; no alterar la funcionalidad
  existente ni la experiencia del usuario; incluir validación antes de merge; documentarse en el
  CHANGELOG como "mejora técnica" o "refactorización".
- Si una mejora requiere cambios mayores, planificarla como feature independiente en una versión
  futura.

## 22. Convenciones de Nombres y Estilo de Código
- **Un idioma para el código y los comentarios por proyecto**, de forma consistente.
- **C#:** PascalCase para tipos, métodos y propiedades; camelCase para variables locales y
  parámetros; prefijo `_` para campos privados; interfaces con prefijo `I`. Habilitar `Nullable` e
  `ImplicitUsings` y tratar las advertencias del compilador como deuda a reducir. Sufijos por rol:
  vistas/páginas en `Page`/`Form`, servicios en `Service`.
- **C/C++:** estilo consistente en todo el módulo nativo (convención de nombres de funciones,
  tipos y ficheros uniforme); cabeceras públicas mínimas y bien delimitadas; gestión explícita de
  recursos y errores. Ver Anexo D.
- **Interfaz declarativa (XAML/markup):** nombrar recursos y estilos de forma consistente; preferir
  estilos y recursos compartidos sobre valores repetidos en línea.
- Preferir nombres descriptivos sobre abreviaturas; evitar duplicación de constantes (sección 5).

---

# Anexo A — Móvil y Tiendas de Aplicaciones (.NET MAUI / Android / Google Play)

## A.1 Estructura específica (.NET MAUI)
- `Pages/` o `Views/`: interfaces en XAML/C# (elegir una convención y mantenerla; no duplicar la
  misma página en ambas). La lógica vive en el code-behind delgado, que delega en `Services/`.
- `Services/`, `Models/`, `Converters/`, `Helpers/`: como en la sección 5.
- `Platforms/Android/`: código Android nativo, manifiestos y recursos específicos. La interfaz MAUI
  no debe contener lógica de plataforma ni referencias directas a APIs Android.
- `Resources/`: iconos, imágenes, estilos, fuentes. `Properties/`: configuración del proyecto.

## A.2 Identificador de paquete
- Formato obligatorio: `com.socratic.[nombre-aplicacion]` (en minúsculas, sin espacios).
- Configurar en CSPROJ: `ApplicationId = "com.socratic.[nombre-aplicacion]"`.
- Es **permanente y públicamente visible**; no puede cambiar una vez publicado.

## A.3 Permisos Android
- Revisar `AndroidManifest.xml` antes de cada publicación.
- Justificar cada permiso solicitado en la documentación y aplicar mínimo privilegio (sección 6).

## A.4 Versionado de tienda
- `ApplicationDisplayVersion`: cadena legible con esquema de fecha (p.ej. `2026.05.20.0`).
- `ApplicationVersion`/`versionCode`: entero incremental requerido por la tienda (p.ej. `202605200`).
- Ambos se actualizan en sincronía antes de cada publicación (coherente con sección 11).
- **En cada build (Debug o Release)** se incrementa siempre la última cifra de
  `ApplicationDisplayVersion` (p.ej. `2026.05.20.0` → `2026.05.20.1`) y se actualiza `versionCode`.
- **Al publicar en Google Play** se fija toda la versión a la fecha actual:
  `ApplicationDisplayVersion = AAAA.MM.DD.0` y `versionCode = AAAAMMDD0`
  (ejemplo para 2026-06-26: `2026.06.26.0` y `202606260`).

## A.5 Política de Google Play Store
- **Canales:** pruebas cerradas (primer canal obligatorio), pruebas internas (validación rápida sin
  revisor) y producción (distribución pública).
- Publicar **siempre primero en pruebas cerradas** con testers antes de cualquier otro canal.
- No pasar a producción sin validar en pruebas cerradas y sin cumplir la lista completa de metadata
  y permisos.
- El icono debe ser visible, legible y cumplir las guías de Google Play.
- Mantener metadatos consistentes en todos los idiomas; fijar el idioma por defecto antes de
  eliminar traducciones.

## A.6 Flujo de publicación móvil
1. Actualizar `ApplicationDisplayVersion` y `ApplicationVersion` en el CSPROJ.
2. Actualizar el CHANGELOG.
3. Ejecutar el script de compilación y firmado (APK y AAB).
4. Validar localmente el AAB firmado.
5. Ejecutar el script de publicación con parámetros de Play Console (cuenta de servicio, pista,
   metadata).
6. Confirmar en Play Console: `versionCode` incremental, canal correcto, icono correcto, idioma por
   defecto válido, descripción y notas en los idiomas soportados.
7. Registrar la publicación en el repositorio (tag, commit de merge).

## A.7 Despliegue con ensamblados embebidos
- **En cualquier despliegue** a emulador (MuMu) o dispositivo, tanto en **Debug** como en
  **Release**, el paquete debe incluir todos los ensamblados dentro del APK/AAB
  (`EmbedAssembliesIntoApk=true`).
- Prohibido instalar manualmente paquetes con **Fast Deployment**: los ensamblados no viajan en el
  APK y la app aborta al arrancar con `No assemblies found ... Fast Deployment`.
- Recuperación ante ese fallo: recompilar con `EmbedAssembliesIntoApk=true`, volver a firmar e
  instalar el paquete autocontenido.

## A.8 Validación móvil
- Cada cambio funcional se verifica en **dispositivo Android real o emulador** antes de publicar.

---

# Anexo B — Escritorio

## B.1 Interfaz
- Frameworks admitidos: WinForms, WPF, WinUI (Windows); equivalentes en otras plataformas.
- Aplicar la sección 7: code-behind delgado que delega en Services, evitando patrones de
  presentación pesados salvo que aporten un valor claro.
- Tema claro/oscuro coherente con el del sistema cuando sea viable.

## B.2 Empaquetado e instalación
- Empaquetado a instalador o ZIP mediante script reproducible (sección 12).
- Núcleo nativo (si lo hay) compilado y empaquetado junto a la UI (ver Anexo D).
- Opciones de integración con el SO (arranque automático, bandeja del sistema) configurables por el
  usuario.

## B.3 Actualización
- Autoactualización por swap + reinicio **preservando la configuración** del usuario (sección 15),
  con verificación SHA-256 del paquete.

---

# Anexo C — Web y Servicios de Servidor (ASP.NET Core / APIs / Autoalojado)

## C.1 Estructura y responsabilidades
- API/servicio (p.ej. ASP.NET Core) con registro, autenticación, señalización y/o panel web según
  el proyecto.
- Persistencia propia (p.ej. SQLite/SQL Server) según el despliegue.
- Los modelos del API se comparten con los clientes desde el módulo común (sección 5).

## C.2 Seguridad de servidor
- **API siempre por HTTPS (TLS).** Documentar el uso de certificados (CA de confianza o
  autofirmados aceptados explícitamente por el cliente).
- Autenticación por token (p.ej. bearer); contraseñas almacenadas como hash (sección 6).
- Cuando el servidor reenvía tráfico entre clientes, preferir **cifrado extremo a extremo** entre
  los clientes para que el servidor no vea el contenido.
- Mínimo privilegio en puertos: abrir solo los necesarios y documentarlos.

## C.3 Despliegue (coherente con sección 14)
- Publicar de forma **autocontenida** para no depender del runtime en el servidor.
- Instalar como servicios del sistema con arranque automático; el instalador abre el firewall local
  y aplica la configuración.
- **CI/CD:** flujo `checkout → toolchain → build → deploy → verificar` (p.ej. WinRM/SSH); secretos
  de despliegue como secretos del pipeline.
- Las direcciones públicas anunciadas a los clientes deben ser alcanzables (sección 14).
- Abrir los puertos necesarios tanto en el firewall local como en el del proveedor (Security
  List/NSG, grupo de seguridad, etc.).

## C.4 Operación
- Logging persistente con **rotación** (tamaño × número de ficheros); segmentar por sesión/conexión
  cuando ayude al diagnóstico (sección 10).
- Verificación post-despliegue automatizada (sección 14).

---

# Anexo D — Código Nativo (C++ / Interop)

## D.1 Alcance y encapsulación
- El código nativo se encapsula en un módulo propio con una **cabecera pública mínima**; la capa
  gestionada (C#) interactúa a través de esa frontera (P/Invoke / interop NDK), sin filtrar detalles
  de implementación nativa al resto del proyecto.
- El código específico de plataforma del módulo nativo se mantiene aislado.

## D.2 Toolchain y build
- Compilación con MSVC/CMake (Windows) o NDK (Android), orquestada por el script de build común
  (sección 12).
- Documentar las herramientas requeridas (p.ej. Visual Studio Build Tools, `vcvars`, NDK).
- Enlazar contra bibliotecas de importación del SO y **APIs del sistema** (captura, códec, red) en
  lugar de redistribuir binarios o librerías copyleft de terceros (sección 4).

## D.3 Calidad
- Gestión explícita de recursos (RAII / liberación determinista) y de errores; no propagar fallos
  silenciosos a la capa gestionada (sección 10).
- Estilo y convenciones de nombres consistentes en todo el módulo (sección 22).
