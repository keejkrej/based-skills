# TypeScript Bun App

- Use ESM.
- Run with Bun.
- Relative imports: always extensionless — `from './module'`, never `from './module.js'` or `from './module.ts'`.
- Put the entrypoint under `src/index.ts`.
- Keep runtime wiring in `src/app.ts`.
- Keep config loading in `src/config.ts`.
- Enforce extensionless imports in `.oxlintrc.json` — oxlint `import/extensions`.
- Seven-day registry release age in `bunfig.toml` — see [../domains/security.md](../domains/security.md).

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
    "dev": "bun --watch src/index.ts",
    "start": "bun src/index.ts",
    "typecheck": "tsc -p tsconfig.json",
    "lint": "oxlint",
    "test": "echo No tests configured"
  },
  "dependencies": {},
  "devDependencies": {
    "typescript": "^5.7.0",
    "@types/bun": "latest",
    "oxlint": "latest"
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
