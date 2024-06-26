# TSConfig Paths Webpack Plugin

Use `paths` in tsconfig.json at each directory to let Webpack resolve files as expected. It's like a plugin for aliases, but it's more powerful in the monorepo scenario.

## Quick Start

Install the plugin using the package manager.

```bash
npm i -D multi-tsconfig-paths-webpack-plugin
yarn add -D multi-tsconfig-paths-webpack-plugin
pnpm i -D multi-tsconfig-paths-webpack-plugin
```

Add the plugin to your Webpack configuration.

```javascript
const { MultiTsconfigPathsWebpackPlugin } = require('multi-tsconfig-paths-webpack-plugin');

module.exports = {
  resolve: {
    plugins: [
      new MultiTsconfigPathsWebpackPlugin({
        glob: '../../**/tsconfig.json', // depends on your project structure
      }),
    ],
  },
};
```

## Why Use This Plugin?

In the monorepo scenario, you may have multiple `tsconfig.json` files in different directories. Each `tsconfig.json` file may have its own `paths` configuration. This plugin will help you resolve the files as expected.

For example, here is the project structure:

```
project
├── packages
│   ├── sub
│   │   ├── src
│   │   │   ├── bar.ts
│   │   │   └── index.ts
│   │   └── tsconfig.json
│   └── main
│       ├── src
│       │   ├── foo.ts
│       │   └── index.ts
│       ├── webpack.config.js
│       └── tsconfig.json
└── tsconfig.json
```

All `tsconfig.json`s have the same `baseUrl` and `paths` configuration:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

TypeScript respects the `tsconfig.json` in all directories. It will resolve the files as expected:

- In the file `/packages/main/src/index.ts`, if you write `import ... from '@/foo'`, TypeScript resolves the file to `/packages/main/src/foo.ts`.
- In the file `/packages/sub/src/index.ts`, if you write `import ... from '@/bar'`, TypeScript resolves the file to `/packages/sub/src/bar.ts`.

But Webpack aliases are globally set. If you set `@` to `/packages/main/src`, it will resolve all `@`s to `/packages/main/src`. The `@/bar` will be resolved to `/packages/main/src/bar.ts`, not `/packages/sub/src/bar.ts`. That means even if you can `Ctrl/Cmd + Click` to jump to the file in your IDE, there are still errors in Webpack.

This plugin will help you resolve the files as expected. If you don't use monorepo, you can still use this plugin as a replacement for setting `config.resolve.alias` in Webpack config using `tsconfig.json` manually.

Don't worry, if your import path is not in the `paths` configuration, it will not be resolved by this plugin.

## Development

```bash
pnpm i
pnpm start # go to http://localhost:3000 and see the console
```

## License

MIT
