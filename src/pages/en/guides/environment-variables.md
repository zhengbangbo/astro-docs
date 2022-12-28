---
layout: ~/layouts/MainLayout.astro
title: Using environment variables
description: Learn how to use environment variables in an Astro project.
setup: import PackageManagerTabs from '~/components/tabs/PackageManagerTabs.astro'
i18nReady: true
---
Astro uses Vite's built-in support for environment variables, and lets you [use any of its methods](https://vitejs.dev/guide/env-and-mode.html) to work with them.

Note that while _all_ environment variables are available in server-side code, only environment variables prefixed with `PUBLIC_` are available in client-side code for security purposes.

```ini title=".env"
SECRET_PASSWORD=password123
PUBLIC_ANYBODY=there
```

In this example, `PUBLIC_ANYBODY` (accessible via `import.meta.env.PUBLIC_ANYBODY`) will be available in server or client code, while `SECRET_PASSWORD` (accessible via `import.meta.env.SECRET_PASSWORD`) will be server-side only.

:::caution
`import.meta.env` and `.env` files are not available inside [configuration files](/en/guides/configuring-astro/#environment-variables). 
:::

## Default environment variables

Astro includes a few environment variables out-of-the-box:

- `import.meta.env.MODE`: The mode your site is running in. This is `development` when running `astro dev` and `production` when running `astro build`.
- `import.meta.env.PROD`: `true` if your site is running in production; `false` otherwise.
- `import.meta.env.DEV`: `true` if your site is running in development; `false` otherwise. Always the opposite of `import.meta.env.PROD`.
- `import.meta.env.BASE_URL`: The base url your site is being served from. This is determined by the [`base` config option](/en/reference/configuration-reference/#base).
- `import.meta.env.SITE`: This is set to the [the `site` option](/en/reference/configuration-reference/#site) specified in your project's `astro.config`.

## Setting environment variables

### `.env` files
Environment variables can be loaded from `.env` files in your project directory.

You can also attach a mode (either `production` or `development`) to the filename, like `.env.production` or `.env.development`, which makes the environment variables only take effect in that mode.

Just create a `.env` file in the project directory and add some variables to it.

```ini title=".env"
# This will only be available when run on the server!
DB_PASSWORD="foobar"
# This will be available everywhere!
PUBLIC_POKEAPI="https://pokeapi.co/api/v2"
```

For more on `.env` files, [see the Vite documentation](https://vitejs.dev/guide/env-and-mode.html#env-files).

### Using the CLI
You can also add environment variables as you run your project:

<PackageManagerTabs>
 <Fragment slot="yarn">
    ```shell
    POKEAPI=https://pokeapi.co/api/v2 yarn run dev
    ```
 </Fragment>
 <Fragment slot="npm">
    ```shell
    POKEAPI=https://pokeapi.co/api/v2 npm run dev
    ```
 </Fragment>
 <Fragment slot="pnpm">
    ```shell
    POKEAPI=https://pokeapi.co/api/v2 pnpm run dev
    ```
 </Fragment>
</PackageManagerTabs>

:::caution
Variables set this way will be available everywhere within your project, including on the client.
:::
## Getting environment variables

Instead of using `process.env`, with Vite you use `import.meta.env`, which uses the `import.meta` feature added in ES2020.

For example, use `import.meta.env.PUBLIC_POKEAPI` to get the `PUBLIC_POKEAPI` environment variable.

```js /(?<!//.*)import.meta.env.[A-Z_]+/
// When import.meta.env.SSR === true
const data = await db(import.meta.env.DB_PASSWORD);

// When import.meta.env.SSR === false
const data = fetch(`${import.meta.env.PUBLIC_POKEAPI}/pokemon/squirtle`);
```

:::caution
Because Vite statically replaces `import.meta.env`, you cannot access it with dynamic keys like `import.meta.env[key]`.
:::

## IntelliSense for TypeScript

By default, Astro provides type definition for `import.meta.env` in `astro/client.d.ts`. 

While you can define more custom env variables in `.env.[mode]` files, you may want to get TypeScript IntelliSense for user-defined env variables which are prefixed with `PUBLIC_`.

To achieve this, you can create an `env.d.ts` in `src/` and configure `ImportMetaEnv` like this:

```ts title="src/env.d.ts"
interface ImportMetaEnv {
  readonly DB_PASSWORD: string;
  readonly PUBLIC_POKEAPI: string;
  // more env variables...
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```