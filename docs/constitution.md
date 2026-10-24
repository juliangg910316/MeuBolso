# Constitución -- MeuBolso
Estado: borrador (pendiente de aprobación del autor)

Principios innegociables. Si una spec, un plan o una tarea los contradice, gana la constitución. Cambiarla requiere la aprobación del autor.

1. **Stack mínimo.** Flutter + flutter_bloc + get_it + freezed + supabase_flutter + google_sign_in + intl/flutter_localizations. Ninguna dependencia nueva (gráficos, testing, etc.) sin aprobación del autor.
2. **La spec manda.** No se implementa comportamiento nuevo sin una spec aprobada (las correcciones y refactors que no cambian el comportamiento no la necesitan). Todo cambio va primero a la spec, luego al plan y al final al código. Cada RF se puede rastrear hasta el código y el test que lo cumplen.
3. **Lógica separada de la interfaz y del backend.** Las reglas de negocio (totales, saldo, agrupado por categoría y mes, validaciones) son funciones puras en `lib/domain/`, sin imports de Flutter ni de Supabase, y reciben "hoy" como parámetro. Solo `lib/data/` habla con Supabase. Los widgets y los Cubits no calculan reglas.
4. **El dinero y los datos del usuario son sagrados.** Los importes se guardan como **centavos enteros** (nunca `double`). Cada usuario solo puede leer y escribir sus propios datos (RLS activado en todas las tablas). Nunca se borra nada sin confirmación. Todo cambio de esquema es una migración SQL versionada en `supabase/migrations/` que conserva los datos. La clave secreta (`sb_secret_…` o la antigua `service_role`) nunca está en la app.
5. **Dos idiomas para el usuario, español para el equipo.** La interfaz está en portugués de Brasil y español (ARB, sin textos escritos en los widgets) y la moneda es siempre el real (R$). Documentación, comentarios y commits en español; nombres del dominio en español (`movimiento`, `importeCentavos`) y términos técnicos en inglés (`repository`, `cubit`).
6. **Tests y verificación como puerta.** Tests primero: cada tarea empieza con sus tests en rojo. Nunca se usa el Supabase real en los tests. No se da una tarea por terminada si `dart format .` deja cambios, si `flutter analyze` da errores o avisos, o si `flutter test` no está en verde.
