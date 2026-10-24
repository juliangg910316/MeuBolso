---
description: Estructura de carpetas, nombres de archivo y dónde crear código nuevo.
globs: ["lib/**", "test/**", "supabase/**"]
paths:
  - "lib/**"
  - "test/**"
  - "supabase/**"
---

# Estructura del proyecto

## Objetivo

- Mantener la estructura descrita aquí (arquitectura oficial de Flutter).
- Que cualquier archivo nuevo tenga un sitio obvio.

## Reglas

- Árbol de `lib/`:
  - `main.dart`: arranque (`Supabase.initialize`, DI, `runApp`) y `MeuBolsoApp`.
  - `config/`: `dependencies.dart` (get_it) y `env.dart` (lee `SUPABASE_URL`, `SUPABASE_PUBLISHABLE_KEY`, `GOOGLE_WEB_CLIENT_ID` y `GOOGLE_IOS_CLIENT_ID` con `String.fromEnvironment`).
  - `routing/`: `route_names.dart`, `app_router.dart`.
  - `l10n/`: `app_pt.arb` (plantilla) y `app_es.arb`.
  - `ui/core/themes/`: tema; `ui/core/ui/`: widgets compartidos entre features.
  - `ui/<feature>/view_model/` y `ui/<feature>/widgets/`.
  - `domain/models/`: modelos; `domain/`: lógica pura.
  - `data/repositories/`, `data/services/` (Supabase y Google), `data/model/` (DTO).
  - `utils/`: utilidades genéricas sin dependencias de feature (`result.dart`, `formatters.dart`).
- Fuera de `lib/`: `supabase/migrations/` (SQL versionado), `env.example.json` (plantilla sin secretos; `env.json` está en `.gitignore`).
- No hay carpetas `core/` ni `shared/` en la raíz de `lib/`: lo compartido de UI va en `ui/core/`.
- **Nombres de archivo** (`snake_case`):
  - Pantalla: `<feature>_screen.dart` (no `_page`).
  - View model: `<feature>_cubit.dart` + `<feature>_state.dart`.
  - Repositorio: `<entidad>_repository.dart` (contrato) + `<entidad>_repository_supabase.dart`; servicio: `<nombre>_service.dart`; DTO: `<entidad>_dto.dart`.
  - Modelo: `<entidad>.dart` en singular (`movimiento.dart`, `categoria.dart`).
  - Migración: `supabase/migrations/NNNN_descripcion.sql` (`0001_movimientos.sql`).
- **Features**: una carpeta por pantalla o flujo de la especificación (`login`, `home`, `movimiento_form`, `categorias`).
- **Imports**: relativos dentro de `lib/`; `package:meu_bolso/...` en `test/`.
- **Código generado**: `*.freezed.dart`, `*.g.dart` y `app_localizations*.dart`, generados con `build_runner` o `flutter gen-l10n`; excluidos del análisis.
- **Tests**: `test/` replica la ruta de `lib/` (`test/domain/resumen_mensual_test.dart`); fakes compartidos en `test/fakes/`.

## Ejemplos cortos

- Nueva pantalla de categorías:
  - `lib/ui/categorias/view_model/categorias_cubit.dart` + `categorias_state.dart`
  - `lib/ui/categorias/widgets/categorias_screen.dart`
  - `RouteNames.categorias` + caso en `AppRouter.onGenerateRoute` + `registerFactory<CategoriasCubit>`.
- Nueva regla de negocio: `lib/domain/totales_por_categoria.dart` + `test/domain/totales_por_categoria_test.dart`.

## Checklist

- [ ] Archivo en la capa y carpeta correctas.
- [ ] Nombre con el sufijo de su tipo (`_screen`, `_cubit`, `_state`, `_repository`, `_dto`).
- [ ] Ruta, Cubit y DI registrados si es una pantalla nueva.
- [ ] Test en la ruta espejo de `test/`.
