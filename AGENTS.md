# AGENTS.md -- MeuBolso
**MeuBolso** (paquete `meu_bolso`) es una app Flutter para Android e iOS de organización financiera, encargo de un cliente de Brasil: login con Google, gastos e ingresos por categoría, y tabla y gráfico del mes. Principios innegociables en `docs/constitution.md`; visión en `docs/ESPECIFICACION.md`; requisitos en `specs/`.

## Stack y estructura
- Flutter (Dart `^3.13.5`) · `flutter_bloc` (un Cubit por pantalla) · `get_it` · `freezed` (modelos de dominio y estados) · `supabase_flutter` (Auth + Postgres) · `google_sign_in` · `intl` + `flutter_localizations` con ARB (pt-BR y es) · rutas nombradas nativas.
- Capas: `lib/ui/<feature>/{view_model,widgets}` → `lib/domain/` (modelos, lógica pura y validaciones) → `lib/data/{repositories,services}`. DI en `lib/config/`, rutas en `lib/routing/`, ARB en `lib/l10n/`. SQL en `supabase/migrations/`. Detalle por tema en `.claude/rules/`.

## Comandos
```bash
flutter pub get
dart run build_runner build   # tras cambiar clases @freezed
flutter gen-l10n              # tras cambiar los .arb
dart format .
flutter analyze
flutter test
flutter run --dart-define-from-file=env.json   # Supabase y client ID de Google (ver env.example.json)
```

## Convenciones
- Commits semánticos en español: `feat(movimientos): registrar un gasto con su categoría`.

## Reglas de dominio / trampas conocidas
- **Dinero en centavos enteros** (`importeCentavos: int`, siempre > 0; el signo lo da `tipo`). Nunca `double` para importes; se formatea a R$ solo en la UI.
- El mes de un movimiento sale de su `fecha` local (sin hora), nunca de UTC.
- `usuarioId` lo pone el repositorio a partir de la sesión; la UI nunca lo envía. RLS en todas las tablas.
- Los nombres de las categorías predefinidas se traducen con su clave en los ARB; los de las propias se muestran tal cual.

## Forma de trabajar
- **Spec-Driven Development** (skill `sdd`, comandos `/sdd-*`): spec → clarificación → plan → tareas → implementación (una tarea cada vez, tests primero) → validación. No se pasa de fase sin aprobación. Las correcciones sin cambio de comportamiento se hacen directamente.
- Al terminar: resumen breve, cómo probarlo y decisiones que deba revisar el autor.

## Límites
- **Siempre:** respetar las capas; `getIt` solo en `config/` y en el router; el repositorio devuelve `Future<Result<T>>` (sin streams ni realtime); las vistas se recargan tras cada acción; textos de UI en los ARB.
- **Pregunta antes:** añadir dependencias, cambiar la arquitectura, cambiar el esquema de Supabase o sus políticas RLS.
- **Nunca:** editar `*.g.dart`, `*.freezed.dart` ni `app_localizations*.dart` a mano; subir `env.json` o cualquier clave; usar la clave secreta (`sb_secret_…` o la antigua `service_role`) en la app; usar el Supabase real en tests; hacer commits sin que se pida.

## Verificación
- `dart format .` sin cambios, `flutter analyze` sin errores ni avisos y `flutter test` en verde.
- Recorrido manual en un emulador, simulador o dispositivo (Android o iOS), en portugués y en español cuando la spec lo afecte.

## Memoria
- Al empezar, lee `MEMORY.md`. Al terminar una tarea, actualízalo: estado, decisiones (con su porqué) y errores a evitar.
- Máximo ~50 líneas. Si algo se convierte en regla permanente, propón moverlo a `AGENTS.md`. Nunca datos sensibles.
