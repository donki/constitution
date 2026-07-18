# Constitución de Proyectos de Software

## 1. Propósito
Documento de referencia para la **arquitectura, gobernanza, seguridad, publicación y mantenimiento**
de cualquier proyecto de software, con independencia de su plataforma (móvil, escritorio,
web/servicios de servidor o código nativo) y de su stack tecnológico. Define estándares
operacionales y de calidad que todo proyecto debe cumplir.

Su origen es una constitución específica de aplicaciones **Android en .NET MAUI**; se ha
generalizado para que **el núcleo común** (secciones 1–23) aplique a *cualquier* proyecto y los
detalles específicos de cada plataforma vivan en sus **anexos** (A–D). Un proyecto cumple esta
constitución aplicando el núcleo común **más** el/los anexo(s) de las plataformas a las que se
dirige.

## 2. Alcance y cómo se aplica
- **Tipos de proyecto cubiertos:** aplicaciones móviles, aplicaciones de escritorio, servicios y
  APIs de servidor, paneles/aplicaciones web y módulos de código nativo.
- **Ciclo cubierto:** desde la arquitectura hasta la publicación, el despliegue y el mantenimiento.
- **Áreas cubiertas:** estructura, licencia y procedencia, seguridad y secretos, arquitectura de
  presentación, internacionalización, persistencia, errores y logging, versionado, build,
  distribución, despliegue de servidor, autoactualización, calidad, colaboración, estilo y
  gobernanza.
- **Estructura del documento:**
  - **Núcleo común (secciones 3–23):** obligatorio para todo proyecto.
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
  - **Utilidades transversales / helpers / convertidores:** agrupadas y reutilizables. Los
    *helpers* son utilidades **puras y sin estado** (formato, parseo, ficheros, algoritmos): no
    contienen lógica de plataforma ni dependencias de interfaz. Lo que necesite estado o
    plataforma es un servicio (sección 7) o código de plataforma, no un helper.
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
- Todo texto visible al usuario debe **externalizarse** (servicio de localización, ficheros de
  recursos, CSV o catálogo de traducciones). **No se permite texto de interfaz escrito directamente
  en código o en el markup.**
- Centralizar las traducciones (servicio de localización o ficheros de recursos, p.ej. `.resx`) y
  acceder a ellas de forma uniforme.
- Definir un **idioma por defecto** válido y fijarlo antes de eliminar cualquier traducción.
- **Fallback sin fugas:** cuando falte una traducción se recurre al idioma por defecto; **nunca se
  muestra la clave interna al usuario**.
- **Lista de idiomas sincronizada con la realidad:** los idiomas declarados en código deben
  corresponderse con las traducciones realmente disponibles. Si un idioma se anuncia pero no está
  traducido, debe completarse o **retirarse de la lista de soportados**.
- **Selección de idioma:** se persiste en los ajustes del usuario y **respeta el idioma del sistema
  en el primer arranque**.
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
  Debug. Preferir logging condicionado a compilación (`#if DEBUG`) o niveles de severidad, para no
  filtrar detalle interno en release.
- **No registrar** datos personales ni secretos en los logs.
- **Los ficheros de log (`*.log`) no se versionan:** deben estar en `.gitignore` y mantenerse fuera
  del control de versiones.
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
2. Actualizar el **CHANGELOG** con los cambios de la versión y comprobar que el submódulo de
   gobernanza está al día (sección 23).
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

## 23. Documentos de Gobernanza y Submódulos
- Esta constitución es el **documento de gobernanza de referencia** y se comparte entre proyectos
  como **repositorio independiente** (`donki/constitution`).
- **Una sola copia canónica:** cuando se consume como **submódulo Git**, el submódulo es la fuente
  canónica de **solo lectura**; no se edita desde el proyecto consumidor ni se copia a mano dentro
  de él. Un fichero de constitución fuera del submódulo es una divergencia, no una versión.
- **Las mejoras se proponen aguas arriba:** cualquier extensión que sea de utilidad general se lleva
  al repositorio canónico. Lo que sea específico de un proyecto se marca como tal y vive en el
  README o el CHANGELOG del proyecto, nunca en una copia editada del documento.
- **Anclaje deliberado:** los submódulos se fijan a un **commit concreto** y se actualizan de forma
  deliberada y documentada, nunca de forma automática e implícita. Anclar a un commit es una
  decisión legítima; **quedarse anclado sin revisar no lo es**: al publicar una versión se comprueba
  si el submódulo de gobernanza está al día (sección 17).
- Registrar en el CHANGELOG la incorporación o actualización de submódulos de gobernanza.

## 24. Sistema de Diseño Visual
Esta sección fija el aspecto de las aplicaciones. Está extraída de `FileManager` y `PDFReader`, que
son la referencia; las aplicaciones anteriores se adaptarán cuando se decida, no de forma
automática.

**Principio: un valor visible, un origen.**
- **Ningún valor visual se escribe en línea en el markup**: ni colores, ni tamaños de fuente, ni
  radios, ni sombras, ni alturas. Todo procede de un *token* de color o de un estilo con nombre.
  Un color en línea es una decisión que nadie puede volver a encontrar.
- Un color literal en una página es una **divergencia**, no un atajo. La única excepción es el
  blanco puro del texto sobre un botón de marca, y aun así se prefiere el token.

**Tokens de color.** Se declaran en un único diccionario de recursos por aplicación, con nombres
**semánticos** (por rol, no por apariencia: `Primary`, no `Azul`) y **sin prefijo de aplicación**:
el diccionario ya delimita el ámbito. Conjunto mínimo:

| Rol | Tokens |
|---|---|
| Marca | `Primary`, `PrimaryDark`, `Accent` |
| Estado | `Danger`, `Success` |
| Superficies | `PageBackground{Light,Dark}`, `CardBackground{Light,Dark}`, `Separator{Light,Dark}` |
| Texto | `TextPrimary{Light,Dark}`, `TextSecondary{Light,Dark}` |
| Neutros | `White`, `Black` |

- **Todo color de superficie o de texto se declara por pares claro/oscuro** y se consume con
  `AppThemeBinding`. Una aplicación con tema oscuro a medias es peor que sin él.
- Los colores de marca pueden ser únicos si mantienen contraste suficiente sobre ambos temas.

**Estilos de componente.** Se declaran con `x:Key` y nombre estable. Conjunto mínimo:

| Estilo | Rol |
|---|---|
| `Card` | Superficie contenedora. Es la base visual de la aplicación. |
| `CardTitle` | Título dentro de una tarjeta. |
| `BodyText` | Texto corriente. |
| `SecondaryText` | Texto de apoyo, atenuado. |
| `PrimaryButton` | Acción principal, fondo de marca. |
| `OutlineButton` | Acción secundaria, contorno. |

- Los estilos específicos de una pantalla (barras de herramientas, botones de icono) se permiten,
  pero se declaran junto a los anteriores y siguen las mismas reglas.
- **Un estilo con `x:Key` no hereda el estilo implícito de su tipo.** Si el control tiene estados
  (deshabilitado, pulsado), el estilo debe declararlos: si no, un control inactivo se ve idéntico a
  uno activo y la interfaz miente.
- **Objetivos táctiles: mínimo 48 dp.** Es el mínimo de accesibilidad de Android, no una
  preferencia estética.

**Escala.** Los valores concretos se fijan una vez por aplicación y no se reinventan por pantalla.
Línea base: radio de tarjeta 12; altura de botón 48; radio de botón 10-12; texto 18 (título de
tarjeta) / 14 (cuerpo) / 12-13 (secundario). Una aplicación puede desviarse si lo documenta, pero
**no puede desviarse de sí misma**: dos radios distintos para la misma clase de tarjeta son un
defecto.

**Errores que esta sección existe para impedir.** Todos observados en aplicaciones de este
propietario:
- **Diccionarios de estilo que no se fusionan.** Un `Colors.xaml` o `Styles.xaml` que no está en
  `MergedDictionaries` es código muerto que aparenta ser un sistema de diseño. Si existe, se usa;
  si no se usa, se borra.
- **Publicar la plantilla por defecto del framework sin tocarla**, y decorar encima con valores en
  línea. La plantilla no es un sistema de diseño.
- **`AppThemeBinding` neutralizado**: definirlo en los estilos y luego fijar un color claro en cada
  página. El tema oscuro deja de existir sin que nadie lo note.
- **Varias paletas conviviendo** en la misma aplicación por copiar pantallas de proyectos distintos.
- **Texto de interfaz en el markup** (prohibido por la sección 8) o traducciones que recorren el
  árbol visual por índices.

**Verificación antes de publicar** (complementa el anexo A.8.2): comprobar en tema claro y oscuro
que ninguna pantalla queda ilegible, y que no hay literales de color en el markup.

---

# Anexo A — Móvil y Tiendas de Aplicaciones (.NET MAUI / Android / Google Play)

## A.1 Estructura específica (.NET MAUI)
- `Pages/` o `Views/`: interfaces en XAML/C# (elegir una convención y mantenerla; no duplicar la
  misma página en ambas). La lógica vive en el code-behind delgado, que delega en `Services/`.
- `Services/`, `Models/`, `Converters/`, `Helpers/`: como en la sección 5.
- `Platforms/Android/`: código Android nativo, manifiestos y recursos específicos. La interfaz MAUI
  no debe contener lógica de plataforma ni referencias directas a APIs Android.
- `Resources/`: iconos, imágenes, estilos, fuentes, traducciones y assets crudos.
  `Properties/`: configuración del proyecto (`launchSettings.json`).

**Plataformas secundarias de desarrollo**
- Se permite habilitar plataformas adicionales (por ejemplo Windows) como objetivo **secundario de
  desarrollo y depuración**.
- El objetivo de publicación y soporte sigue siendo Android; **las plataformas secundarias no
  relajan ninguna regla** de esta constitución.
- El código específico de una plataforma secundaria se encapsula en su carpeta `Platforms/`
  correspondiente.

## A.2 Identificador de paquete
- Formato obligatorio: `com.socratic.[nombre-aplicacion]` (en minúsculas, sin espacios).
- Configurar en CSPROJ: `ApplicationId = "com.socratic.[nombre-aplicacion]"`.
- Es **permanente y públicamente visible**; no puede cambiar una vez publicado.

## A.3 Permisos Android
- Revisar `AndroidManifest.xml` antes de cada publicación.
- Justificar cada permiso solicitado en la documentación y aplicar mínimo privilegio (sección 6).
- **No solicitar en Android moderno permisos que las APIs actuales ya no requieren.** Preferir las
  APIs que no exigen permiso (por ejemplo, el *Storage Access Framework* concede acceso solo al
  archivo que el usuario elige, sin permiso de almacenamiento).
- **Acotar con `android:maxSdkVersion`** los permisos que solo son necesarios en versiones antiguas
  de Android (por ejemplo, almacenamiento externo cuando el almacenamiento con alcance ya cubre el
  caso).
- **Revisar los permisos que introduce la plantilla del proyecto** y retirar los que la aplicación
  no use (`INTERNET` y `ACCESS_NETWORK_STATE` vienen por defecto en .NET MAUI): un permiso heredado
  y no usado incumple el mínimo privilegio igual que uno añadido a mano.

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

### A.8.1 Emulador de referencia: MuMu Player
MuMu Player es el emulador estándar para la validación manual del día a día (sección 16) por su
rapidez de arranque frente a un AVD. Cada proyecto debe incluir un script `install_mumu.ps1` que
resuelva adb, instale el APK y lance la aplicación, con los conmutadores `-BuildFirst`, `-Launch`
y `-GrantPermissions`.

**Convenciones de conexión**
- adb de MuMu: `C:\Program Files\Netease\MuMuPlayer\nx_main\adb.exe`.
- Puertos habituales, en este orden: `127.0.0.1:16384`, `127.0.0.1:7555`, `127.0.0.1:16416`,
  `127.0.0.1:5555`. El script debe intentar `adb connect` sobre todos ellos antes de fallar.
  El puerto real de una instancia se confirma con `MuMuManager.exe info -v all` (campo `adb_port`).
- Arrancar la instancia sin abrir la interfaz: `MuMuManager.exe control -v 0 launch`.
- Instalar con `adb install -r -d`: MuMu rechaza la instalación incremental y `-d` permite
  reinstalar sobre una versión con `versionCode` mayor durante las pruebas.
- El paquete instalado debe llevar los ensamblados embebidos (A.7).

**Reglas de uso**
- Lanzar la aplicación con `adb shell am start -n <paquete>/<actividad>`, nunca con `monkey`:
  `monkey` inyecta un evento aleatorio además de abrir la app y falsea la pantalla que se valida.
  La actividad real se resuelve con `adb shell cmd package resolve-activity --brief <paquete>`.
- MuMu no incluye el binario `appops`, así que los permisos especiales (por ejemplo
  MANAGE_EXTERNAL_STORAGE) deben concederse desde la interfaz de la aplicación. Esto es una ventaja:
  obliga a validar el flujo real de permisos que verá el usuario.
- Puede haber otras aplicaciones abiertas en el emulador: confirmar con
  `adb shell dumpsys window | findstr mCurrentFocus` que la ventana en primer plano es la nuestra
  antes de dar por buena una captura.
- Comprobar que el arranque no produce ninguna excepción: `adb logcat -d | findstr "FATAL
  EXCEPTION"` debe salir vacío. Un arranque que "se ve bien" en una captura puede haber registrado
  una excepción no controlada (sección 10).

**Límites (no negociables, sección 3 "confiabilidad operativa")**
- MuMu ejecuta una versión de Android antigua (Android 12 / API 32) y modificada. **No sustituye**
  a la validación en dispositivo real ni cubre los comportamientos obligatorios de las APIs
  recientes (por ejemplo el modo edge-to-edge forzado desde Android 15).
- Antes de publicar (sección 19) sigue siendo obligatoria la validación en **dispositivo real o en
  un AVD con el nivel de API objetivo**.
- Un dispositivo real es siempre preferible cuando el cambio toca hardware (cámara, sensores,
  telefonía, SMS).

### A.8.2 Qué se verifica antes de publicar
- Cada cambio funcional se ejercita **de extremo a extremo**: no basta con que compile ni con que
  pasen las pruebas.
- La verificación recorre el **flujo real del usuario** (elegir un fichero con el selector del
  sistema, recibir un intent de otra app, etc.), no solo la pantalla afectada.
- Se comprueban explícitamente, por ser fuentes habituales de fallo que la compilación no detecta:
  - **arranque en frío** de la aplicación instalada desde el paquete de Release;
  - **tema claro y oscuro** (el emulador y el dispositivo pueden diferir);
  - **cada idioma soportado**, incluida la resolución del idioma del sistema en el primer arranque
    (sección 8);
  - **estados vacíos y de error**, no solo el camino feliz.
- Los datos de prueba son **sintéticos**: no se cargan documentos, contactos ni ficheros personales
  reales en un emulador (sección 3, privacidad primero).

## A.9 Sistema de diseño en .NET MAUI
Concreción de la sección 24 para MAUI. Referencia: `FileManager` y `PDFReader`.

**Ubicación y carga**
- Los tokens de color en `Resources/Styles/Colors.xaml`; los estilos en `Resources/Styles/Styles.xaml`.
- **Ambos se fusionan en `App.xaml`** dentro de `<ResourceDictionary.MergedDictionaries>`. Sin esa
  fusión el diccionario no existe en tiempo de ejecución, por mucho que esté en el repositorio.
- La plantilla `Styles.xaml` que genera `dotnet new maui` **se sustituye, no se acompaña**: se parte
  de ella y se recorta a lo que la aplicación usa de verdad. No se deja intacta al lado de un
  segundo diccionario propio.
- Comprobación barata de que el diccionario está vivo: si al renombrar un `x:Key` la aplicación no
  falla al arrancar, es que no se estaba cargando.

**Controles**
- **`Border`, nunca `Frame`.** `Frame` está obsoleto en MAUI y arrastra
  `Microsoft.Maui.Controls.Compatibility`. Una tarjeta es un `Border` con `StrokeShape`
  `RoundRectangle`.
- El estilo `Card` deja `Padding="0"`: el relleno lo pone el contenedor interior, que es quien sabe
  cuánto aire necesita su contenido.

**Temas**
```xml
<Setter Property="BackgroundColor"
        Value="{AppThemeBinding Light={StaticResource CardBackgroundLight},
                                Dark={StaticResource CardBackgroundDark}}" />
```
- Ninguna página fija `BackgroundColor` con un literal: lo hereda del estilo implícito de `Page`.

**Estados**
- Un estilo con `x:Key` pierde el estilo implícito del tipo. Para `Button` eso incluye el estado
  `Disabled`, que hay que declarar con `VisualStateManager` o los botones inactivos se verán
  activos.

**Iconografía**
- **Estilo: flat, de contorno (line icons).** Los iconos de acción de la interfaz son trazos sin
  relleno, de grosor uniforme, sin sombras ni degradados. Es el estilo de la aplicación; no se
  mezcla con iconos rellenos o multicolor para la misma clase de elemento.
- **Los iconos de acción son vectoriales, no emoji.** Un emoji lo dibuja la fuente del sistema:
  cambia de aspecto entre dispositivos, suele ser multicolor y relleno, y no respeta el color de la
  interfaz. Se usa un SVG en `Resources/Images/` (que MAUI rasteriza por densidad) o una fuente de
  iconos. Un emoji solo es admisible como glifo grande y decorativo de un estado vacío, nunca como
  icono de un control.
- **Color por contexto.** Un icono sobre una superficie de color de marca lleva trazo blanco fijo
  (la superficie es del mismo color en ambos temas). Un icono sobre una superficie neutra toma su
  color del token de texto correspondiente, con `AppThemeBinding` o tintado, para seguir el tema.
- Si se adopta una fuente de iconos, se registra en `ConfigureFonts` y **se comprueba que el `.ttf`
  es una fuente real**, no un fichero de relleno.
- El icono de ficha de Google Play (`Resources/AppIcon/play_store_icon.png`, 512×512) **no es** el
  icono de launcher que MAUI genera en el AAB: es un asset aparte de la ficha. Si `appicon.svg` es
  solo un rectángulo de color y el dibujo vive en `appiconfg.svg`, hay que **componer los dos**;
  renderizar `appicon.svg` a solas produce un cuadrado liso.

**Textos**
- Los textos se resuelven con el servicio de localización y se asignan desde el code-behind
  (sección 8). Un `Text="..."` literal en el XAML solo es aceptable para un glifo decorativo sin
  significado lingüístico.

**Pantalla «Acerca de»**
Todas las aplicaciones comparten la misma estructura de tarjetas, en este orden:
1. **Cabecera:** la **imagen real del icono de la aplicación** (no un emoji), el nombre, la versión
   —que coincide con `ApplicationDisplayVersion` del csproj— una descripción breve y «Socratic».
2. **Contacto:** correo **`jsoladelarosa@gmail.com`** en un botón que abre el cliente de correo.
3. **Apoyo:** enlace de Ko-fi **`https://ko-fi.com/josepsola`** (el mismo en todas las apps), que
   abre el navegador y, si falla, copia la URL al portapapeles.
4. **Idioma:** dos botones **Español / English** (los idiomas oficiales, ver sección 8), que aplican
   el idioma de inmediato y resaltan el activo con el color de acento de la app.
5. **Privacidad:** una declaración breve y honesta de qué datos usa la app y que no se comparten con
   terceros (adaptada a cada aplicación).
6. **Licencia:** declaración de software libre bajo licencia **MIT** + línea `MIT License · Copyright
   © <año> Socratic`.
7. **Aviso legal:** el descargo de responsabilidad («tal cual», sin garantías) rematado con un
   recuadro de advertencia «⚠️ Uso bajo su propio riesgo».

- Las tres declaraciones —**Privacidad, Licencia y Aviso legal**— son obligatorias y aparecen en la
  «Acerca de» de **todas** las aplicaciones (incluido el juego sOC, adaptadas a su formato).
- **No** se incluye información del sistema (plataforma, modelo, versión de OS) en la ficha pública.
- El correo de contacto y la URL de Ko-fi son constantes idénticas en todos los proyectos; al
  cambiarlos hay que hacerlo en todos a la vez.

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
