# CLAUDE.md

Guía para Claude Code (claude.ai/code) al trabajar con el código de este repositorio.

Las instrucciones principales del proyecto (stack, comandos, convenciones, reglas de dominio, límites, verificación y memoria) están en `AGENTS.md`, la fuente única compartida con cualquier agente:

@AGENTS.md

Aquí solo va el detalle extra para Claude Code. Si cambian las reglas generales, editar `AGENTS.md`, no este archivo.

## Contexto adicional

- Reglas detalladas por tema en `.claude/rules/` (se cargan automáticamente).
- SDK Dart `^3.13.5`: dot shorthands y parámetros con nombre privados (`this._campo`).
- Lints: `flutter_lints` + reglas extra en `analysis_options.yaml` (comillas simples, `const`, trailing commas, `final` locales…). Se excluyen `build/`, `android/`, `ios/`, `**/*.g.dart` y `**/*.freezed.dart`.
- Estado del código (2026-10-24): solo la plantilla de `flutter create`. El esqueleto de arquitectura (DI, router, `Result<T>`, cliente Supabase, l10n y tema base) se crea con el plan de la spec 001.

## Flujo de datos (UI → datos)

- `lib/ui/<feature>/widgets/` pinta el estado con `BlocBuilder`; `lib/ui/<feature>/view_model/` tiene el Cubit y su estado `@freezed`. El Cubit carga con `cargar()` (`Future<Result<T>>`, sin streams), recarga tras cada acción y no contiene reglas de negocio.
- `lib/domain/`: modelos `@freezed` y lógica pura (totales, saldo, agrupados, validaciones). Sin Flutter ni Supabase.
- `lib/data/repositories/`: convierte filas de Supabase (`Map<String, dynamic>` → DTO en `data/model/`) en modelos de dominio y devuelve `Result<T>` (`lib/utils/result.dart`) en vez de lanzar excepciones.
- `lib/data/services/`: envoltorios finos de `SupabaseClient` y `GoogleSignIn`, recibidos por constructor para poder usar fakes en los tests.
- `lib/config/dependencies.dart`: registro en `get_it`. `getIt<...>()` solo ahí y en el `create:` de los `BlocProvider` de `lib/routing/app_router.dart`.
- `main.dart`: `Supabase.initialize` con `String.fromEnvironment('SUPABASE_URL')` / `('SUPABASE_PUBLISHABLE_KEY')` → `setupDependencies()` → `runApp`.

## Documentación del proyecto (vault de Obsidian)

Nota del proyecto: `00 - Inbox/Proyecto - MeuBolso.md` dentro del vault (Windows: `D:\Obsidian Vault\`; Mac: `~/Documents/DriveSalva/Obsidian Vault/`). Se moverá a `01 - Projects/MeuBolso/` al organizar el Inbox.
