# TypeScript Monorepo

- Use a root workspace for apps and shared packages.
- Relative imports: always extensionless — web, server, packages; never `.js`, `.jsx`, `.ts`, or `.tsx` in import paths.
- Greenfield: `moduleResolution: "Bundler"` — not `"NodeNext"`.
- Put browser UI in `apps/web`.
- Put API/backend service code in `apps/server`.
- Put shared libraries under root `packages/`, not inside an app.
- Source export is preferred for `packages/*`.
- Add `packages/ui` only when multiple web frontends must share one UI layer.
- Keep `crates/`, `zig/`, and `python/` as optional root namespaces when cross-language work is expected.
- Configure workspaces in the root `package.json` (`"workspaces": ["apps/*", "packages/*"]`).
- Commit `bun.lock` at the repo root.
- Put shared TypeScript defaults in root `tsconfig.base.json`; apps extend it, packages extend it with emit settings.
- Use root `tsconfig.json` with project references for `tsc -b` typechecking.
- Install shadcn primitives into `apps/web/src/components/ui` unless the app folder differs.
- Enable React Compiler auto memoization in `apps/web` — add `babel-plugin-react-compiler` and wire it in `vite.config.ts`.
- Enforce no manual memo at the repo root — add `.oxlintrc.json` with `react-hooks-js/use-memo` and `react-hooks-js/void-use-memo` via `eslint-plugin-react-hooks` as an oxlint JS plugin.

```text
{repo-root}/
├─ package.json
├─ bun.lock
├─ .oxlintrc.json
├─ tsconfig.base.json
├─ tsconfig.json
├─ apps/
│  ├─ web/
│  │  ├─ package.json
│  │  ├─ tsconfig.json
│  │  ├─ vite.config.ts
│  │  └─ src/components/ui/
│  └─ server/
│     ├─ package.json
│     ├─ tsconfig.json
│     └─ src/
├─ packages/
│  ├─ contracts/
│  │  ├─ package.json
│  │  └─ tsconfig.json
│  └─ utils/
│     ├─ package.json
│     └─ tsconfig.json
├─ crates/
├─ zig/
└─ python/
```

`tsconfig.base.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "skipLibCheck": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "moduleDetection": "force"
  }
}
```

Root `tsconfig.json`:

```json
{
  "files": [],
  "references": [
    { "path": "./apps/web" },
    { "path": "./apps/server" },
    { "path": "./packages/contracts" },
    { "path": "./packages/utils" }
  ]
}
```

Root `package.json` (add to devDependencies):

```json
{
  "devDependencies": {
    "eslint-plugin-react-hooks": "latest",
    "oxlint": "latest"
  }
}
```

`.oxlintrc.json`:

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "jsPlugins": [
    {
      "name": "react-hooks-js",
      "specifier": "eslint-plugin-react-hooks"
    }
  ],
  "rules": {
    "react-hooks-js/use-memo": "error",
    "react-hooks-js/void-use-memo": "error"
  }
}
```

`apps/web/package.json` (add to devDependencies):

```json
{
  "devDependencies": {
    "babel-plugin-react-compiler": "latest"
  }
}
```

`apps/web/vite.config.ts`:

```typescript
import react from "@vitejs/plugin-react";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [
    react({
      babel: {
        plugins: ["babel-plugin-react-compiler"],
      },
    }),
  ],
});
```

`apps/web/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",
    "noEmit": true
  },
  "include": ["src/**/*"]
}
```

`apps/server/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "lib": ["ES2022"],
    "types": ["bun"],
    "noEmit": true
  },
  "include": ["src/**/*"]
}
```

`packages/*/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "composite": true,
    "declaration": true,
    "declarationMap": true,
    "rootDir": "src",
    "outDir": "dist"
  },
  "include": ["src/**/*"]
}
```
