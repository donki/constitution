# Constitucion de Proyectos Android en .NET MAUI

## 1. Proposito
Documento de referencia para la arquitectura, gobernanza, publicación y mantenimiento de aplicaciones Android desarrolladas en .NET MAUI. Define estándares operacionales aplicables a cualquier proyecto con este stack.

## 2. Alcance
- Plataforma objetivo principal: Android.
- Ciclo de desarrollo: desde arquitectura hasta publicación en Google Play.
- Áreas cubiertas: estructura del proyecto, seguridad, versionado, publicación, iconografía, idiomas, calidad y colaboración.
- Fuera del alcance: decisiones de producto específicas, requisitos funcionales particulares, integraciones de terceros.

## 3. Principios no negociables
- Privacidad primero: datos de usuario se mantienen en el dispositivo.
- Minimo privilegio: solo permisos estrictamente necesarios.
- Confiabilidad operativa: prevenir fallos silenciosos y errores no documentados.
- Trazabilidad: toda publicacion debe ser reproducible y verificable.
- Cambios pequenos y seguros: evitar refactorizaciones masivas sin necesidad.
- Reproducibilidad: cualquier version debe poder regenerarse desde el codigo fuente.

## 4. Estructura del Proyecto
Carpetas y proposito estandar:
- Pages/ o Views/: interfaces de usuario en XAML/C# (elegir una convencion y mantenerla; no duplicar la misma pagina en ambas).
- ViewModels/: logica de presentacion (estado, comandos) en el patron MVVM.
- Services/: logica de negocio e integraciones.
- Models/: clases de datos y entidades.
- Converters/: convertidores de valor para enlaces de datos (IValueConverter).
- Helpers/: utilidades transversales (por ejemplo, acceso a servicios, formateo).
- Platforms/Android/: codigo Android nativo, manifiestos, recursos especificos.
- Resources/: iconos, imagenes, estilos, fuentes.
- Properties/: configuracion del proyecto (launchSettings.json).

Reglas de dependencia obligatorias:
- La logica de negocio reside en Services, no en interfaz.
- La logica de presentacion reside en ViewModels, no en el code-behind de la vista (ver seccion 14).
- Codigo Android especifico (permisos, integraciones nativas) debe estar encapsulado en Platforms/Android.
- La interfaz MAUI no debe contener logica de plataforma o referencias directas a APIs Android.
- Inyeccion de dependencias para todos los servicios y ViewModels.
- No debe quedar codigo muerto ni paginas/ViewModels duplicados (mantener un unico origen por componente).

Convención de identificador de paquete (Package Name):
- Formato obligatorio: `com.socratic.[nombre-aplicacion]` (en minusculas, sin espacios).
- Ejemplos: `com.socratic.smsforwarder`, `com.socratic.notasapp`, `com.socratic.calculadora`.
- Configurar en CSPROJ: ApplicationId = "com.socratic.[nombre-aplicacion]".
- Este identificador es permanente y publicamente visible en Google Play; no puede cambiar una vez publicado.

## 5. Seguridad, Secretos y Cumplimiento
- Politica de secretos:
  - Nunca subir archivos con credenciales (json de servicio, contrasenas, almacenes de claves) a repositorio.
  - Usar variables de entorno o archivos locales gitignored para credenciales.
  - Tratar credenciales como material sensible incluso en máquinas locales.
- Permisos Android:
  - Revisar AndroidManifest.xml antes de cada publicacion.
  - Justificar cada permiso solicitado en documentacion.
  - Aplicar principio de minimo privilegio.
- Metadata de Play:
  - Mantener descripciones, titulos y notas de lanzamiento precisos y veridicos.
  - Evitar promesas no cumplidas o contenido enganoso.
  - Actualizar descripciones en todos los idiomas soportados antes de publicar.

## 6. Versionado y Publicacion
Esquema de versionado recomendado:
- ApplicationDisplayVersion: cadena legible para usuario (ejemplo: 2026.05.20.0 o 1.10.0).
- ApplicationVersion: entero incremental requerido por Play Store (ejemplo: 202605200).
- Ambos valores deben actualizarse en sincronía antes de cada publicacion.
- Registrar todos los cambios de version en CHANGELOG.

Artefactos de compilacion y publicacion:
- Proyecto CSPROJ: configura version, metadatos, dependencias.
- Script de compilacion: automatiza generacion de APK y AAB firmados (debe ser reproducible).
- Script de publicacion: subida a Google Play automatizando AAB, ficha, icono, canal de distribucion.

## 7. Flujo de Publicacion Estandar
1. Actualizar ApplicationDisplayVersion y ApplicationVersion en el archivo CSPROJ.
2. Actualizar CHANGELOG con los cambios de esta version.
3. Ejecutar script de compilacion y firmado (debe producir APK y AAB).
4. Validar localmente el artefacto AAB firmado.
5. Ejecutar script de publicacion con parametros de Play Console (cuenta de servicio, pista, metadata).
6. Confirmar en Play Console:
   - versionCode es incremental y correcto
   - pista/canal es la deseada
   - icono de aplicacion se ve correctamente
   - idioma por defecto es valido
   - descripcion y notas de lanzamiento estan en idiomas soportados
7. Registrar la publicacion en repositorio (tag de version, commit de merge).

## 8. Politica de Google Play Store
Canales de distribucion:
- Pruebas internas: primer canal para validacion, sin revisor de Play.
- Pruebas cerradas: para grupos limitados de testers.
- Produccion: distribucion publica a todos los usuarios.

Reglas obligatorias:
- Publicar siempre primero en pruebas internas para validacion.
- No pasar a produccion sin cumplir lista de verificacion completa de metadata y permisos.
- Mantener metadatos (titulo, descripcion corta, descripcion larga, notas de lanzamiento, capturas) consistentes en todos los idiomas.
- Fijar idioma por defecto antes de eliminar cualquier traduccion.
- El icono de aplicacion debe ser visible, legible y cumplir guias de Google Play.
- No mezclar cambios funcionales con cambios de metadata en misma publicacion.

## 9. Estandares de Calidad
- Compilacion: debe finalizar sin errores. Las advertencias deben documentarse y reducirse progresivamente.
- Pruebas: incluir pruebas unitarias e integracion cuando sea viable.
- Permisos: revisar AndroidManifest.xml y justificar cada permiso.
- Validacion manual: cada cambio funcional debe verificarse en dispositivo Android real o emulador antes de publicar.
- Documentacion: mantener README y CHANGELOG actualizados con cambios relevantes.

## 10. Convenciones de Colaboracion en Repositorio
- Confirmaciones (commits) atomicas: un cambio logico por commit.
- Mensajes de commit descriptivos: explicar el "que" y el "por que", no solo el "que".
- No mezclar cambios funcionales con cambios cosmeticos o refactorizacion.
- Ramas de trabajo: crear rama descriptiva para cada feature o fix.
- Pull requests: incluir descripcion del cambio, impacto y verificacion realizada.
- Revisor: al menos un revisor antes de merge a rama principal.
- Documentacion: actualizar README y CHANGELOG si aplica.
- Publicacion: si el cambio afecta publicacion o scripts de build, registrar procedimiento.

## 11. Plan de Contingencia
Errores comunes y procedimientos de recuperacion:
- Compilacion bloqueada por permisos: cerrar procesos de compilacion, limpiar bin/obj, reintentar.
- versionCode rechazado por Play: regenerar AAB con entero incremental valido que no haya sido usado antes.
- Play bloquea publicacion por idioma por defecto: cambiar idioma por defecto a uno soportado via API, reintentar.
- Problemas de firma: validar que el keystore sea accesible, contrasena correcta, alias correcto.
- Fallo de autenticacion Play: verificar cuenta de servicio JSON, permisos en Play Console, token expirado.

## 12. Criterio de Entrega (Definition of Done)
Una version se considera lista para produccion cuando:
- Compila sin errores y genera AAB/APK firmado correctamente.
- Subida a Play Console sin fallos.
- Icono de aplicacion visible y legible en Play Store.
- Metadata (titulo, descripcion, notas) esta en todos los idiomas soportados.
- Permisos solicitados estan justificados y documentados.
- Cambios principales estan registrados en CHANGELOG.
- Version (versionCode y versionName) esta incrementada y registrada.
- Se ha realizado validacion manual en dispositivo Android real o emulador.

## 13. Revision Continua y Mejora Incremental
Politica de revision y optimizacion:
- Revisar periodicamente el codigo, arquitectura y dependencias para identificar areas de mejora.
- Aplicar mejoras pequeñas e incrementales: refactorizacion, actualizacion de dependencias, optimizacion de rendimiento, claridad de codigo.
- **Principio fundamental: nunca sacrificar estabilidad por mejora.** Todo cambio debe dejar la aplicacion en estado funcional y verificado.
- Cambios de mejora deben:
  - Aislarse en rama separada y commit independiente.
  - No alterar la funcionalidad existente o la experiencia del usuario.
  - Incluir validacion manual en dispositivo antes de merge.
  - Documentarse en CHANGELOG como "mejora tecnica" o "refactorizacion".
- Si una mejora requiere cambios mayores, planificar como feature independiente en version futura.

## 14. Arquitectura de Presentacion (MVVM)
- Patron obligatorio: Model-View-ViewModel (MVVM) para separar interfaz de logica.
- Usar una libreria estandar de MVVM (por ejemplo, CommunityToolkit.Mvvm) para comandos y notificacion de cambios.
- La vista (XAML + code-behind) solo contiene logica de presentacion estricta; el estado y los comandos viven en el ViewModel.
- El code-behind no debe acceder a Services directamente saltandose el ViewModel salvo casos justificados y documentados.
- Los ViewModels se registran e inyectan por dependencias; no se instancian manualmente en la vista.
- No mezclar dos estilos (code-behind con logica y MVVM) para el mismo tipo de pagina sin justificacion.

## 15. Internacionalizacion y Localizacion (i18n)
- Todo texto visible al usuario debe ser localizable; no incrustar cadenas fijas en la interfaz cuando exista soporte multiidioma.
- Centralizar las traducciones (servicio de localizacion o ficheros de recursos .resx) y acceder a ellas de forma uniforme.
- Definir un idioma por defecto valido y fijarlo antes de eliminar cualquier traduccion (coherente con seccion 8).
- Formatos sensibles a la cultura (fechas, numeros, divisas) deben usar la cultura del usuario, no valores fijos.
- Mantener las traducciones de la interfaz sincronizadas con la metadata de Play en todos los idiomas soportados.

## 16. Persistencia de Datos Local
- Coherente con "Privacidad primero" (seccion 3): los datos del usuario se almacenan en el dispositivo.
- Usar el mecanismo adecuado segun el dato: preferencias/clave-valor para configuracion ligera; almacenamiento estructurado (archivo o base de datos local) para historicos y conjuntos de datos.
- No almacenar secretos ni credenciales en almacenamiento en claro; aplicar la politica de la seccion 5.
- Validar y manejar datos ausentes o corruptos sin provocar fallos silenciosos (seccion 3).
- Considerar la migracion de esquema cuando cambie el formato de los datos persistidos entre versiones.

## 17. Manejo de Errores y Registro (Logging)
- Prohibido el fallo silencioso (seccion 3): toda excepcion esperada se gestiona y se informa de forma adecuada.
- Operaciones que puedan fallar (E/S, red, plataforma) se envuelven con manejo de errores y mensajes claros al usuario cuando proceda.
- Usar el sistema de logging del framework; habilitar trazas de depuracion solo en configuracion Debug.
- No registrar datos personales ni secretos en los logs.
- Antes de publicar, revisar que no queden excepciones no controladas en flujos principales.

## 18. Convenciones de Nombres y Estilo de Codigo
- C#: PascalCase para tipos, metodos y propiedades; camelCase para variables locales y parametros; prefijo `_` para campos privados.
- Interfaces con prefijo `I` (por ejemplo, `INotificationService`).
- Nombres de paginas/vistas terminados en `Page`; ViewModels terminados en `ViewModel`; servicios en `Service`.
- Habilitar `Nullable` e `ImplicitUsings` y tratar las advertencias del compilador como deuda a reducir (seccion 9).
- XAML: nombrar recursos y estilos de forma consistente; preferir estilos y recursos compartidos sobre valores repetidos en linea.
- Un idioma para el codigo y los comentarios por proyecto, de forma consistente.
