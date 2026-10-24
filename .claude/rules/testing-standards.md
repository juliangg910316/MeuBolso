---
description: Estándares de pruebas (tests primero).
globs: ["test/**", "integration_test/**"]
paths:
  - "test/**"
  - "integration_test/**"
---

# Pruebas

## Objetivo

- **Tests primero** (constitución, principio 6): cada tarea empieza con sus tests en rojo y termina con `flutter test` en verde.
- Prioridad: la lógica del dinero (`lib/domain/`), después repositorios y Cubits, y por último widgets.

## Reglas

- **Estructura**: `test/` replica `lib/` y cada archivo termina en `_test.dart`. Fakes compartidos en `test/fakes/`.
- **Dominio**: casos mínimos para cualquier cálculo de dinero: mes vacío, un solo movimiento, solo gastos, solo ingresos, saldo negativo, movimientos del último día del mes y del primero del siguiente, importes grandes (sin desbordar `int`), conversión de texto a centavos (`"12,5"` → `1250`, `"0,01"` → `1`, valores no válidos).
- **Repositorios**: con un fake del servicio de Supabase escrito a mano; se comprueba la conversión DTO ↔ dominio, que se añade `usuarioId` y que los errores se convierten en `Result.error`.
- **Cubits**: con un repositorio fake; se comprueba la secuencia de estados (`loading` → `ready`/`error`).
- **Widgets**: pantallas con un Cubit o repositorio fake, envueltas con los `localizationsDelegates` de la app; probar al menos en `pt` y en `es` los textos que dependan del idioma.
- **Fakes antes que mocks**: implementaciones falsas escritas a mano; no añadir `mockito`, `mocktail` ni `bloc_test` sin aprobación del autor.
- **Tiempo**: fijar "hoy" inyectando el reloj, nunca depender de `DateTime.now()`.
- **Nunca** el Supabase real ni la red en un test. Nada de `Supabase.initialize` en `test/`.
- **Ejecución**: `flutter test`, o `flutter test <archivo>` / `--plain-name "<nombre>"` para uno solo.

## Anti-patrones

- Tests que dependen de la fecha real, de la red o del orden de ejecución.
- Usar `get_it` en tests en lugar de pasar dependencias por constructor (si un test de widget lo necesita, `getIt.reset()` en `tearDown`).
- Comparar importes formateados (`'R$ 12,50'`) para probar un cálculo: se comparan centavos.
- Golden tests de pantallas que aún cambian.

## Checklist

- [ ] Los tests se escribieron antes que el código y fallaron primero.
- [ ] Reloj fijado y sin red.
- [ ] `flutter test` y `flutter analyze` en verde.
