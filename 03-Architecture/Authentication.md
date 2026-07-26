---
tags: [architecture, auth, security]
updated: 2026-07-26
status: draft
---

# Authentication

> No escribo auth a mano. Nunca. Ver [[Decision Making]] → qué no construyo.

---

## Qué uso

| Caso | Elección |
|---|---|
| App [[NextJS]] | Auth.js / Clerk / Supabase Auth |
| Backend [[ASP.NET]] | ASP.NET Identity + JWT |
| API entre servicios | API key o client credentials |
| Producto SaaS con equipos | Provider gestionado (SSO/SAML no se improvisa) |

Criterio: cuánto cuesta a escala 0 y si puedo migrar los usuarios después.

---

## Reglas duras

1. **Passwords:** bcrypt o argon2. Nunca MD5, SHA, ni "encriptado" reversible.
2. **Tokens en cookies** `httpOnly` + `secure` + `sameSite`. **Nunca en localStorage** — XSS lee localStorage.
3. **Access token corto** (15 min) + refresh token rotativo.
4. **Autorización en el servidor.** Esconder un botón en el front no es seguridad.
5. **HTTPS obligatorio.** Sin excepción, ni en staging.
6. **Rate limit** en login, registro y reset de password.
7. **Mensajes de error genéricos** en login: "credenciales inválidas", nunca "ese email no existe" (enumeración de usuarios).
8. **Verificación de email** antes de dar acceso a features que cuestan plata.

---

## Autenticación ≠ autorización

- **AuthN:** ¿quién sos? → una vez, en middleware.
- **AuthZ:** ¿podés hacer esto? → en cada operación, en la capa de servicio.

```ts
// ❌ el permiso se chequea en la ruta: el service queda desprotegido
if (user.role === "admin") await deletePost(id);

// ✅ el service recibe quién actúa y decide él
await deletePost(id, actor);
```

La regla: **la operación recibe el actor y verifica el permiso ella misma.** Si el chequeo
vive afuera, el día que llame a esa función desde un cron, un webhook o una acción nueva,
se ejecuta sin permiso y sin que nada avise.

**Verificar ownership siempre.** El bug más común: `/orders/:id` que devuelve la orden
de cualquiera porque solo se chequeó que hubiera sesión (IDOR). Sesión iniciada no es
autorización: hay que comparar el dueño del recurso contra el actor.

---

## Sesión en Next.js

- Leer la sesión en Server Components / middleware, no en el cliente.
- Middleware protege rutas, pero **no confío solo en él** — la validación real está en la capa de datos.
- Nada de datos sensibles en el JWT: es firmado, no encriptado. Cualquiera lo lee.

---

## Checklist antes de lanzar

- [ ] Cookies `httpOnly`, `secure`, `sameSite=lax`
- [ ] Rate limit en login y reset
- [ ] Reset de password: token de un solo uso, con expiración
- [ ] Logout invalida el refresh token del lado del servidor
- [ ] Ownership verificado en cada endpoint con `:id`
- [ ] Secrets fuera del repo (y rotados si alguna vez se commitearon)

---

## Enlaces

- [[API Design]] · [[Error Handling]] · [[NextJS]] · [[ASP.NET]]
