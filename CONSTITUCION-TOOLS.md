# Constitución — Tools

> Amplía la [constitución general](CONSTITUCION-GENERAL.md). El detalle técnico está en
> [constitucion.md](constitucion.md), **Anexo B** (escritorio) y **Anexo D** (código nativo).
>
> Alcance: `Tools/RemoteSoc` y las utilidades internas y de escritorio que vengan después.
>
> **Última actualización: 2026-09-01**

---

## 1. Qué es una herramienta

Software de apoyo: scripts de automatización, utilidades de escritorio, generadores. **No** se
publica en tiendas y **no** tiene usuarios finales fuera del proyecto. Eso relaja los requisitos de
ficha y clasificación, pero **no** los de licencia, seguridad ni privacidad.

## 2. Reglas

1. **Una herramienta hace una cosa.** Si crece hasta necesitar su propia interfaz completa y
   distribución, deja de ser herramienta y pasa a `Mobile/` o se trata como aplicación de escritorio.
2. **Reproducible.** Se ejecuta desde una única orden documentada en su `README.md`, con las
   dependencias declaradas.
3. **Sin efectos destructivos por defecto.** Cualquier operación que borre, sobrescriba o publique
   pide confirmación o exige un flag explícito. Modo *dry-run* siempre que tenga sentido.
4. **Idempotente** cuando sea posible: ejecutarla dos veces no debe empeorar el estado.
5. **Salida legible.** Que se entienda qué hizo, qué omitió y por qué.
6. **Si tiene ventana, los iconos son planos**: dibujo de línea de un solo color, nunca emoji
   (ver [General §6.2](CONSTITUCION-GENERAL.md)).

## 3. Secretos

- Nunca incrustados. Se leen de `.env` o de fichero local, ambos en `.gitignore`.
- Las herramientas que tocan Play Console, cuentas o dispositivos **enumeran en su README a qué
  acceden**.
- Cualquier herramienta expuesta a la red exige autenticación robusta: token, firma por petición,
  rate-limiting y lista de permitidos. Sin token, 401.
- **Una herramienta que toque la base de datos de una aplicación** (consultas de mantenimiento,
  migraciones, volcados) se atiene a la [sección 5 de la general](CONSTITUCION-GENERAL.md): el texto de
  ahí está cifrado y **se queda cifrado**. Nada de descifrarlo para verlo cómodo en un volcado, en
  un log o en una captura. Si hace falta mirarlo, se mira desde la aplicación, que es de quien es la
  clave.

## 4. Automatización peligrosa

Las herramientas que ejecutan órdenes en el equipo o publican en tiendas se tratan como superficie
de riesgo:

- Permisos acotados al mínimo.
- Registro de lo que ejecutan.
- Nada de `--dangerously-skip-permissions` ni equivalentes salvo decisión explícita y consciente.

## 5. Documentación

Cada herramienta tiene `README.md` con: qué hace, cómo se ejecuta, qué necesita instalado, a qué
accede y qué puede romper.
