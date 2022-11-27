# @eloqjs/nuxt-next-auth

[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![Github Actions CI][github-actions-ci-src]][github-actions-ci-href]
[![Codecov][codecov-src]][codecov-href]
[![Contributions][contributions-src]][contributions-href]
[![License][license-src]][license-href]

## Quick Start

1. Install the package:

```bash
# Using yarn
yarn add --dev @eloqjs/nuxt-next-auth

# Using npm
npm install --save-dev @eloqjs/nuxt-next-auth
```

2. Add the package to your `nuxt.config.ts`:

```ts
export default defineNuxtConfig({
  modules: ['@eloqjs/nuxt-next-auth'],
})
```

3. Create the authentication handler (`NuxtAuthHandler`) and add at least
   one [authentication provider](https://next-auth.js.org/providers/):
```ts
// file: nuxt.config.ts
export default defineNuxtConfig({
  auth: {
    providers: ['github']
  }
})

// file: ~/server/api/auth/[...].ts
import { NuxtAuthHandler } from '#auth'
import GithubProvider from 'next-auth/providers/github'

export const authOptions = {
  secret: 'enter-your-secret-here',
  providers: [
    GithubProvider({ clientId: 'enter-your-client-id-here', clientSecret: 'enter-your-client-secret-here' })
  ]
}
export default NuxtAuthHandler(authOptions)
```
- `[..].ts` is a catch-all route, see the [nuxt server docs](https://v3.nuxtjs.org/guide/directory-structure/server#catch-all-route)

4. Done! You can now use all user-related functionality, for example:
- application-side (e.g., from `.vue` files):

```ts
const { status, data } = useSession()

status.value // Session status: `unauthenticated`, `loading`, `authenticated`
data.value // Session data, e.g., expiration, user.email, ...

await signIn() // Sign-in the user
await signOut() // Sign-out the user
```
  
- server-side (e.g., from `~/server/api/session.get.ts`):
```ts
import { getServerSession } from '#auth'
import { authOptions } from '~/server/api/auth/[...]'

export default eventHandler(async (event) => {
  const session = await getServerSession(event, authOptions)

  if (!session) {
    return { status: 'unauthenticated!' }
  }

  return { status: 'authenticated!', text: 'im protected by an in-endpoint check', session }
})
```

## Development

- Run `yarn dev:prepare` to generate type stubs.
- Use `yarn dev` to start [playground](./playground) in development mode.

## Why another package?

Although there are similarities with [@sidebase/nuxt-auth][sidebase-nuxt-auth-src],
different design choices have been made, and therefore their usage are also different.

The most important differences are the usage of `useSession` and how we fetch the session.

In [@sidebase/nuxt-auth][sidebase-nuxt-auth-src], each time `useSession` is used, it makes a request to the api to get
the session, which means we have to `await` for the request and this makes our component asynchronous.

```vue

<script setup lang="ts">

const { data } = await useSession({ required: false })

</script>
```

In `@eloqjs/nuxt-next-auth`, the session is fetched on load the auth plugin, which means it's fetched before you page is loaded.
So we don't need to `await` the `useSession` as there are no requests being made. You can use `useSession`
anywhere you want, it's just accessing data stored
with [useState](https://nuxt.com/docs/getting-started/state-management#state-management).

```vue

<script setup lang="ts">

const { data } = useSession()

</script>
```

## Credits

Whoever contributes to this project! ❤️

This project was based and inspired by some amazing packages!

- [NextAuth.js][next-auth-src] by [NextAuth.js][next-auth-author]
- [NextAuth.js Playground Nuxt][next-auth-playground-nuxt-src] by [Robert Soriano][next-auth-playground-nuxt-author]
- [@sidebase/nuxt-auth][sidebase-nuxt-auth-src] by [Sidebase][sidebase-nuxt-auth-author]
- [@nuxtjs/auth-module][nuxtjs-auth-module-src] by [Nuxt Community][nuxtjs-auth-module-author]

## 📑 License

[MIT License][license-href]

<!-- Badges -->

[npm-version-src]: https://img.shields.io/npm/v/@eloqjs/nuxt-next-auth?logo=npm&style=flat-square
[npm-version-href]: https://npmjs.com/package/@eloqjs/nuxt-next-auth/

[npm-downloads-src]: https://img.shields.io/npm/dt/@eloqjs/nuxt-next-auth.svg?logo=npm&style=flat-square
[npm-downloads-href]: https://npmjs.com/package/@eloqjs/nuxt-next-auth

[github-actions-ci-src]: https://img.shields.io/github/workflow/status/eloqjs/nuxt-next-auth/ci?label=actions&logo=github&style=flat-square
[github-actions-ci-href]: https://github.com/eloqjs/nuxt-next-auth/actions/workflows/ci.yml

[codecov-src]: https://img.shields.io/codecov/c/github/eloqjs/nuxt-next-auth.svg?logo=codecov&style=flat-square
[codecov-href]: https://codecov.io/gh/eloqjs/nuxt-next-auth

[contributions-src]: https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat-square
[contributions-href]: https://github.com/eloqjs/nuxt-next-auth/issues

[license-src]: https://img.shields.io/github/license/eloqjs/nuxt-next-auth?style=flat-square
[license-href]: https://github.com/eloqjs/nuxt-next-auth/blob/master/LICENSE

<!-- Credits -->

[next-auth-src]: https://github.com/nextauthjs/next-auth

[next-auth-author]: https://github.com/nextauthjs

[next-auth-playground-nuxt-src]: https://github.com/nextauthjs/next-auth/tree/main/apps/playground-nuxt

[next-auth-playground-nuxt-author]: https://github.com/wobsoriano

[sidebase-nuxt-auth-src]: https://github.com/sidebase/nuxt-auth

[sidebase-nuxt-auth-author]: https://github.com/sidebase

[nuxtjs-auth-module-src]: https://github.com/nuxt-community/auth-module

[nuxtjs-auth-module-author]: https://github.com/nuxt-community
