# TypeScript Monorepo

- Use a root workspace for apps and shared packages.
- Relative imports: always extensionless — web, server, packages; never `.js`, `.jsx`, `.ts`, or `.tsx` in import paths.
- Greenfield: `moduleResolution: "Bundler"` — not `"NodeNext"`.
- Put thin browser UI in `apps/web` — routes, app atoms, port wiring only.
- Put API/backend service code in `apps/server` (TS) or `crates/` (Rust) — pick one primary server stack per service.
- Put shared libraries under root `packages/`, not inside an app.
- Source export is preferred for `packages/*` (`"exports": { ".": "./src/index.ts" }`).
- Pin shared dependency versions with a Bun workspace **catalog** in root `package.json`.
- Keep `crates/`, `zig/`, and `python/` as optional root namespaces when cross-language work is expected.
- Configure workspaces in the root `package.json` (`"workspaces": ["apps/*", "packages/*"]` or `["apps/*/*", "packages/*"]` for multi-product).
- Commit `bun.lock` at the repo root.
- Use Vite+ (`vp`) as the only CLI entry point — `vp install`, `vp dev`, `vp build`, `vp test`, `vp check`, `vp run`, `vp pack`; do not run npm/pnpm/Yarn/Bun directly for toolchain or package-management tasks.
- Configure Vite, Vitest, Oxlint, Oxfmt, and Vite Task in a single root `vite.config.ts`.
- Keep a root `vp` install (via `curl -fsSL https://vite.plus | bash`) documented in README; CI uses `voidzero-dev/setup-vp@v1`.
- Put shared TypeScript defaults in root `tsconfig.base.json`; apps extend it, packages extend it with emit settings.
- Use root `tsconfig.json` with project references for `tsc -b` typechecking.
- Install coss primitives into `packages/ui/src/components/ui/` — not in apps.
- Centralize Vite+ config in `packages/web-app` (wrapping `@vitejs/plugin-react`, TanStack Router, React Compiler); apps import `createWebViteConfig` from there and re-export it in their own `vite.config.ts`.
- Enforce stack rules via `vite.config.ts` `lint` (Oxlint) section: `import/extensions` error; manual memo discouraged on TSX/JSX (React Compiler handles memoization). Keep a minimal `.oxlintrc.json` only if a tool outside `vp` needs it.
- Seven-day registry release age in root `bunfig.toml` — see [../../techstack/domains/security.md](../../techstack/domains/security.md).
- Wire `build`, `test`, `lint`, `typecheck` through Turborepo → [../domains/dev.md](../domains/dev.md).
- Multi-product variant: `apps/<product>/web` + `apps/<product>/server` — same `packages/*`, product-specific routes/atoms only.

```text
{repo-root}/
├─ package.json
├─ bun.lock
├─ bunfig.toml
├─ turbo.json
├─ .oxlintrc.json
├─ tsconfig.base.json
├─ tsconfig.json
├─ apps/
│  ├─ web/
│  │  ├─ package.json
│  │  ├─ tsconfig.json
│  │  ├─ vite.config.ts
│  │  └─ src/
│  │     ├─ routes/
│  │     ├─ atoms/          # app-local writable UI atoms
│  │     └─ api/            # port wiring from env
│  └─ server/
│     ├─ package.json
│     ├─ tsconfig.json
│     └─ src/
├─ packages/
│  ├─ contracts/            # Schema + HttpApi + decode
│  ├─ client/               # runtime, ports, shared atoms
│  ├─ utils/                # pure helpers
│  ├─ storage/              # localStorage / sessionStorage adapters
│  ├─ ui-headless/          # hooks + render-prop presentation logic
│  ├─ ui/                   # shell + features + components/ui
│  └─ web-app/              # shared Vite config + app bootstrap
├─ crates/
├─ zig/
└─ python/
```

### Package responsibilities

| Package | Owns | Must not own |
| ------- | ---- | ------------ |
| `contracts` | Wire schemas, `HttpApi`, decode, generated OpenAPI | React, ports, UI |
| `client` | Runtime, `HttpApiClient`, ports, query/mutation atoms | DOM, route files |
| `utils` | Pure functions | React, storage, HTTP |
| `storage` | Sync storage adapters | Business logic |
| `ui-headless` | Presentation hooks, render-prop state | DOM, Tailwind |
| `ui` | coss primitives, shell, feature components | App routes, port env wiring |
| `web-app` | Vite/Router/Compiler config, provider stack, port factory | Feature UI |

Add optional `packages/<feature>/` when a domain outgrows `ui/features/` (parsers, catalogs, feature atoms).

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
    { "path": "./packages/client" },
    { "path": "./packages/utils" },
    { "path": "./packages/storage" },
    { "path": "./packages/ui-headless" },
    { "path": "./packages/ui" },
    { "path": "./packages/web-app" }
  ]
}
```

Root `package.json` (workspaces + catalog + devDependencies):

```json
{
  "workspaces": {
    "packages": ["apps/*", "packages/*"],
    "catalog": {
      "effect": "latest",
      "@effect-atom/atom-react": "latest",
      "@effect/platform": "latest",
      "@tanstack/react-router": "latest",
      "@tanstack/router-plugin": "latest",
      "react": "latest",
      "react-dom": "latest",
      "typescript": "latest",
      "vite": "latest",
      "vitest": "latest"
    }
  },
  "scripts": {
    "lint": "vp lint",
    "check": "vp check",
    "dev": "vp dev",
    "typecheck": "turbo run typecheck",
    "test": "vp test",
    "build": "vp build"
  },
  "overrides": {
    "vite": "npm:@voidzero-dev/vite-plus-core@latest",
    "vitest": "latest"
  },
  "devDependencies": {
    "vite-plus": "latest",
    "@voidzero-dev/vite-plus-core": "latest",
    "turbo": "latest",
    "typescript": "catalog:"
  }
}
```

`turbo.json` tasks:

```json
{
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**"] },
    "typecheck": { "dependsOn": ["^typecheck"] },
    "test": { "dependsOn": ["^build"] },
    "lint": { "cache": false }
  }
}
```

`.oxlintrc.json`:

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "rules": {
    "import/extensions": [
      "error",
      {
        "js": "never",
        "jsx": "never",
        "ts": "never",
        "tsx": "never",
        "ignorePackages": true
      }
    ],
    "no-restricted-imports": [
      "error",
      {
        "paths": [
          {
            "name": "react",
            "importNames": ["useMemo", "useCallback", "memo"],
            "allowTypeImports": true,
            "message": "React Compiler handles memoization."
          }
        ]
      }
    ]
  }
}
```

`bunfig.toml`:

```toml
[install]
minimumReleaseAge = 604800
```

`apps/web/vite.config.ts`:

```typescript
import { defineConfig } from "vite-plus";
import { createWebViteConfig } from "@repo/web-app/vite";

export default defineConfig(createWebViteConfig({
  appDir: import.meta.dirname,
  port: 5173,
}));
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

`packages/ui` exports (stable subpaths for apps):

```json
{
  "exports": {
    ".": "./src/index.tsx",
    "./shell": "./src/shell/index.ts",
    "./features": "./src/features/index.ts",
    "./components": "./src/components/ui/index.ts",
    "./hooks": "./src/hooks/index.ts"
  }
}
```

`packages/web-app` exports:

```json
{
  "exports": {
    ".": "./src/index.ts",
    "./vite": "./vite.ts",
    "./app.css": "./app.css"
  }
}
```
