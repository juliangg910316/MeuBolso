---
description: Capas, direcciones de dependencia y responsabilidades según la arquitectura oficial de Flutter.
globs: ["lib/**"]
paths:
  - "lib/**"
---

# Arquitectura por capas

## Objetivo

- Seguir la [guía oficial de arquitectura de Flutter](https://docs.flutter.dev/app-architecture/guide): `ui` → `domain` ← `data`.
- Mantener la lógica de negocio (totales, saldo, validaciones) pura y testeable, aislada de Flutter y de Supabase.

## Estructura por feature

- **UI (presentation)**: `lib/ui/<feature>/widgets/` (pantallas) + `lib/ui/<feature>/view_model/` (Cubit + estado).
- **Domain**: `lib/domain/models/` (modelos `@freezed`) y lógica pura en `lib/domain/` (p. ej. `resumen_mensual.dart`).
- **Data**: `lib/data/repositories/` (única puerta de la UI a los datos), `lib/data/services/` (envoltorios de `SupabaseClient` y `GoogleSignIn`) y `lib/data/model/` (DTO con `fromJson`/`toJson` de las filas de Supabase).
- **Transversal**: `lib/config/` (DI y entorno), `lib/routing/`, `lib/utils/` (`Result<T>`, formatos), `lib/l10n/` (ARB), `lib/ui/core/` (tema y widgets compartidos).

## Direcciones de dependencia

- `ui` importa `domain` y `data/repositories`; **nunca** `data/services`, `data/model` ni `package:supabase_flutter`.
- `data/repositories` importa `domain`, `data/services`, `data/model` y `utils`.
- `domain` no importa nada de `ui`, `data`, Flutter ni paquetes externos: solo `dart:` y `package:freezed_annotation`.
- Los DTO y los `Map<String, dynamic>` de Supabase **solo existen en `data/`**: el repositorio los convierte a modelos de dominio.
- Los repositorios devuelven `Future<Result<T>>` en lecturas y escrituras; capturan `PostgrestException`, `AuthException` y los errores de red, y no lanzan excepciones hacia la UI. **Sin streams ni realtime**: las vistas se recargan después de cada acción.
- Repositorio con contrato abstracto + implementación con Supabase (hace falta el fake en los tests de Cubits y widgets).
- Casos de uso: no hay capa `use_cases`. La lógica va como funciones puras en `domain/`; crear `domain/use_cases/` solo si un flujo combina varios repositorios.

## Reglas de negocio que viven en capas concretas

- `usuarioId` lo pone el repositorio a partir de la sesión actual; la UI nunca lo envía. Además, RLS en Postgres (`auth.uid() = usuario_id`) en todas las tablas.
- **Importes en centavos enteros** (`int importeCentavos > 0`) en dominio, DTO y base de datos (`bigint`). La conversión desde el texto que escribe el usuario es una función pura de `domain/`; el formato a R$ vive en `utils/formatters.dart`.
- La `fecha` de un movimiento es un día (columna `date`, sin hora ni zona). El mes se calcula a partir de ella.
- Totales, saldo y agrupados por categoría se calculan en `domain/`, no en SQL ni en el Cubit (salvo que un plan lo justifique por rendimiento).
- Las validaciones de negocio (importe, categoría del mismo tipo, fecha) son funciones puras en `domain/`. El Cubit las llama y convierte el resultado en estado; el formulario solo pinta los errores.
- Esquema y políticas: solo en `supabase/migrations/NNNN_descripcion.sql`. Nunca se edita una migración ya aplicada.

## Anti-patrones

- Widgets o Cubits que usan `Supabase.instance.client` directamente.
- `double` para dinero o sumas con `double`.
- Validaciones de negocio dentro del formulario (`validator:` con reglas propias) o del Cubit.
- Modelos de dominio con `fromJson` o nombres de columnas (`usuario_id`).
- Usar `DateTime.now()` dentro de lógica de dominio en vez de recibir "hoy" como parámetro.
- Tablas sin RLS o políticas que dependan de datos enviados por el cliente.

## Checklist

- [ ] Ningún import de `data/services`, `data/model` o `supabase_flutter` desde `lib/ui/`.
- [ ] `lib/domain/` sin imports de Flutter ni paquetes (salvo `freezed_annotation`).
- [ ] Repositorio convierte DTO → modelos y envuelve errores en `Result`.
- [ ] Nueva regla de negocio con su función pura en `domain/` y sus tests.
- [ ] Cambio de esquema = migración nueva con RLS.
