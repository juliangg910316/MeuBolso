# Configurar Supabase y el inicio de sesión con Google

*2026-10-24 · Titular de todas las cuentas: el autor (decisión del 2026-10-24).*

Flujo elegido: **inicio de sesión nativo** con `google_sign_in`. El móvil obtiene un `idToken` de Google y lo envía a Supabase con `signInWithIdToken`, sin abrir el navegador. Por eso hacen falta **tres clientes OAuth** en Google (web, Android e iOS) y el de tipo web se registra en Supabase.

Datos de la app (ya fijados en el repo):

| Dato | Valor |
| --- | --- |
| Paquete de Android | `com.juliangg.meu_bolso` |
| Bundle ID de iOS | `com.juliangg.meuBolso` |
| SHA-1 de **depuración** (keystore `~/.android/debug.keystore` de este Mac) | `51:F5:77:9D:F4:6D:2E:C1:78:99:36:FD:72:4B:80:6C:5A:78:BA:5F` |

> [!warning] Qué no se comparte nunca
> Ni la contraseña de la base de datos, ni la clave secreta de Supabase (`sb_secret_…` / `service_role`), ni el **client secret** de Google. Este último solo se pega en el panel de Supabase. Guárdalos en tu gestor de contraseñas.

---

## Parte A — Crear el proyecto de Supabase

1. Entra en [supabase.com/dashboard](https://supabase.com/dashboard) → **New project**.
   - **Name:** `meu-bolso`
   - **Database password:** genera una y guárdala en el gestor de contraseñas.
   - **Region:** **South America (São Paulo)**, la más cercana a los usuarios de Brasil.
   - **Plan:** Free para el desarrollo. Ojo: en el plan gratuito el proyecto **se pausa tras 7 días sin actividad**. Antes de publicar, valorar el plan Pro.
2. Cuando termine de crearse, abre **Connect** (arriba) o **Settings → API Keys** y copia:
   - **Project URL** (`https://<ref>.supabase.co`)
   - **Publishable key** (`sb_publishable_…`). Es la que va en la app. La `anon` key es la versión antigua y Supabase la retira a finales de 2026.
3. **Authentication → Sign In / Providers**:
   - **Email:** desactívalo para que solo se pueda entrar con Google (recomendado).
   - Abre **Google** (todavía sin activar) y copia la **Callback URL** (`https://<ref>.supabase.co/auth/v1/callback`). Se usa en la parte B.
4. **No crees tablas a mano.** El esquema y la RLS llegan como migraciones de `supabase/migrations/` con el plan de cada spec.

## Parte B — Google Cloud: proyecto, pantalla de consentimiento y clientes OAuth

1. Entra en [console.cloud.google.com](https://console.cloud.google.com) → selector de proyectos → **New project** → nombre `MeuBolso`.
2. Menú → **Google Auth Platform** → **Get started** y rellena:
   - **Branding:** nombre de la app `MeuBolso`, correo de soporte y correo de contacto del desarrollador (el tuyo). Por ahora sin logo: subirlo obliga a verificar la marca.
   - **Audience:** **External**. Estado **Testing**. En **Test users**, añade tu Gmail y el del cliente: mientras esté en Testing, solo ellos pueden entrar (hasta 100).
   - **Data Access → Add or remove scopes:** `openid`, `.../auth/userinfo.email` y `.../auth/userinfo.profile`. No añadas más: con estos tres no hace falta la verificación de Google para publicar.
3. **Google Auth Platform → Clients → Create client**, tres veces:

   | # | Tipo | Nombre | Datos | Qué guardar |
   | --- | --- | --- | --- | --- |
   | a | **Web application** | `MeuBolso Web (Supabase)` | **Authorized redirect URIs:** la Callback URL de la parte A.3 | **Client ID** y **Client secret** |
   | b | **Android** | `MeuBolso Android debug` | Paquete `com.juliangg.meu_bolso` + SHA-1 de depuración (tabla de arriba) | Client ID |
   | c | **iOS** | `MeuBolso iOS` | Bundle ID `com.juliangg.meuBolso` (sin App Store ID ni Team ID por ahora) | Client ID e **iOS URL scheme** (`com.googleusercontent.apps.…`) |

   El cliente Android no aparece en el código: Google lo usa para reconocer la app por su paquete y su SHA-1. El cliente **web** es el `serverClientId` en Android e iOS.

## Parte C — Conectar Google con Supabase

1. Supabase → **Authentication → Sign In / Providers → Google** → **Enable**.
2. **Client IDs:** los tres, separados por comas y **con el web primero**: `<web>,<android>,<ios>`.
3. **Client Secret (for OAuth):** el secret del cliente **web**.
4. **Skip nonce check:** **activado** (necesario para el inicio de sesión nativo en iOS).
5. **Save**.

## Parte D — `env.json` en el repo (local, nunca en git)

```bash
cd ~/Documents/GitHub/MeuBolso
cp env.example.json env.json
```

Rellena los cuatro valores. Ninguno es secreto: los cuatro acaban dentro de la app.

```json
{
  "SUPABASE_URL": "https://<ref>.supabase.co",
  "SUPABASE_PUBLISHABLE_KEY": "sb_publishable_…",
  "GOOGLE_WEB_CLIENT_ID": "….apps.googleusercontent.com",
  "GOOGLE_IOS_CLIENT_ID": "….apps.googleusercontent.com"
}
```

`env.json` está en `.gitignore`. Guarda una copia en el gestor de contraseñas para Windows u otro Mac.

## Parte E — Lo que NO se hace ahora (va en el plan de la spec 001)

- Añadir `supabase_flutter` y `google_sign_in` al `pubspec.yaml` (requiere aprobación, constitución 1).
- iOS: añadir el **iOS URL scheme** (`CFBundleURLTypes`) y los client ID en `ios/Runner/Info.plist`.
- Código: `GoogleSignIn.instance.initialize(serverClientId: <web>, clientId: <ios>)` → `authenticate()` → `idToken` → `supabase.auth.signInWithIdToken(provider: OAuthProvider.google, idToken: …, accessToken: …)`.
- Primera migración con las tablas y la RLS.

## Más adelante (antes de publicar)

- **SHA-1 de release:** al crear la clave de subida (*upload key*) y activar Play App Signing, crea **otro cliente Android** por cada SHA-1 (la clave de subida y la de firma de Google Play) y añade sus Client ID en Supabase (parte C.2).
- **iOS en la App Store:** completa el App Store ID y el Team ID en el cliente iOS.
- **Audience → Publish app** (de Testing a In production) para que entre cualquier usuario.
- **Política de privacidad** (LGPD) y su URL en Branding; dominio propio si se sube el logo.
- Valorar el **plan Pro** de Supabase (evita la pausa por inactividad y añade copias de seguridad diarias).

## Comprobación final

- [ ] Proyecto de Supabase en São Paulo; URL y publishable key copiadas.
- [ ] Proveedor Email desactivado.
- [ ] Pantalla de consentimiento con los 3 scopes y los usuarios de prueba.
- [ ] Clientes OAuth web, Android (debug) e iOS creados.
- [ ] Proveedor Google activado en Supabase: 3 Client IDs (web primero), secret del web y *Skip nonce check*.
- [ ] `env.json` relleno y fuera de git.

Fuentes: [Supabase — Login with Google (Flutter)](https://supabase.com/docs/guides/auth/social-login/auth-google?platform=flutter) · [Supabase — API keys](https://supabase.com/docs/guides/api/api-keys)
