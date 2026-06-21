# TypeScript Bun App

- Use Vite+ (`vp`) as the unified entry point — `vp install/dev/build/test/check/run/pack`; avoid direct npm/pnpm/Yarn/Bun commands. For pure server scripts with no web toolchain, a plain Bun setup is fine.
- Use ESM.
- Run with Bun.
- Relative imports: always extensionless — `from './module'`, never `from './module.js'` or `from './module.ts'`.
- Put the entrypoint under `src/index.ts`.
- Keep runtime wiring in `src/app.ts`.
- Keep config loading in `src/config.ts`.
- Enforce extensionless imports in `.oxlintrc.json` — oxlint `import/extensions`.
- Seven-day registry release age in `bunfig.toml` — see [../../techstack/domains/security.md](../../techstack/domains/security.md).

```text
{project-root}/
├─ package.json
├─ bunfig.toml
├─ .oxlintrc.json
├─ tsconfig.json
└─ src/
   ├─ index.ts
   ├─ app.ts
   └─ config.ts
```

```json
{
  "name": "{name}",
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "vp dev",
    "build": "vp build",
    "check": "vp check",
    "start": "vp node src/index.ts",
    "typecheck": "tsc -p tsconfig.json",
    "lint": "vp lint",
    "test": "echo No tests configured"
  },
  "overrides": {
    "vite": "npm:@voidzero-dev/vite-plus-core@latest",
    "vitest": "latest"
  },
  "dependencies": {},
  "devDependencies": {
    "typescript": "^5.7.0",
    "@types/bun": "latest",
    "vite-plus": "latest",
    "@voidzero-dev/vite-plus-core": "latest"
  }
}
```

`bunfig.toml`:

```toml
[install]
minimumReleaseAge = 604800
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
    ]
  }
}
```

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "lib": ["ES2022"],
    "types": ["bun"],
    "strict": true,
    "skipLibCheck": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "moduleDetection": "force",
    "noEmit": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```
