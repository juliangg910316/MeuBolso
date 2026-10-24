# MEMORY.md -- MeuBolso

## Estado actual (2026-10-24)
- Proyecto creado con `flutter create` (Android e iOS, `com.juliangg.meu_bolso`). Solo la plantilla: sin dependencias ni esqueleto todavía.
- Documentación SDD lista: `docs/constitution.md` y `docs/ESPECIFICACION.md` en **borrador** (pendientes de aprobación), skill `sdd`, comandos `/sdd-*` y reglas en `.claude/rules/`.
- No hay specs creadas. Siguiente paso: aprobar la constitución y la especificación, y después `/sdd-spec 001-acceso-google …`.
- Supabase: **el proyecto y el cliente OAuth de Google aún no existen**. `env.json` (ignorado por git) tendrá `SUPABASE_URL` y `SUPABASE_PUBLISHABLE_KEY`; plantilla en `env.example.json`.
- Git inicializado, sin commits.

## Decisiones (y por qué)
- Encargo de un cliente de Brasil: interfaz en pt-BR y es (ARB), moneda solo R$. Docs y commits en español.
- Supabase (Auth con Google + Postgres) en lugar de Firebase: decisión del autor. Datos en la nube para no perderlos al cambiar de móvil.
- Solo Android e iOS (sin web).
- Arquitectura de Rueda Viva (flutter_bloc + get_it + freezed, `Future<Result<T>>` sin streams), pero **con tests primero** como DShaddai: el cálculo de saldos es dinero de un cliente.
- Importes en centavos enteros: evita errores de redondeo de `double`.
- Preguntas abiertas resueltas (2026-10-24): fecha elegible (por defecto hoy); sin conexión solo se avisa (sin caché); gráfico = donut de gastos por categoría; "Otros" en gastos e ingresos; nombre definitivo MeuBolso; el autor es siempre el titular de Supabase, Google Cloud y tiendas (asume costes y LGPD).
- Categorías predefinidas + propias. Gastos repetidos a mano en el MVP (recurrentes y cuotas, v2). Vista por mes con ‹ ›.

## Errores a evitar
- (Heredado de Rueda Viva) RC-3 incluye los textos propios de Flutter/Material: hace falta `flutter_localizations`, no solo `intl`.
- (Heredado de Rueda Viva) No controlar el móvil con `adb shell input`; las comprobaciones interactivas las hace el autor.

## Próximos pasos
- [ ] Aprobar `docs/constitution.md` y `docs/ESPECIFICACION.md`.
- [ ] Crear el proyecto de Supabase y los clientes OAuth de Google siguiendo `docs/setup/supabase-y-google.md` (publishable key, no la anon key).
- [ ] Spec 001 — acceso con Google (incluye el esqueleto de arquitectura en su plan).
- [ ] Specs 002–006 (ver `docs/ESPECIFICACION.md`, sección 7).
