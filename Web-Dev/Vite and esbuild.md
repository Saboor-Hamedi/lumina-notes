---
id: a1b2c3d4-1169-4000-8000-000000000169
title: Vite and esbuild
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001169
---
# Vite and esbuild

Vite is a modern build tool that leverages esbuild for fast development and Rollup for production builds. It provides instant server start and fast HMR.

## Vite Architecture

```
Dev mode:
  Native ESM (no bundling)
  ─────────────────────────────────────────
  Browser requests module → Vite serves
  Transforms on-the-fly (esbuild)
  HMR via WebSocket

  Vite Dev Server:
    - esbuild: TS → JS, JSX → JS (pre-bundling deps)
    - Dev middleware: transforms on request
    - WebSocket: HMR updates

Production build:
  Rollup (full bundle, tree-shaking, code splitting)
  ─────────────────────────────────────────
  Build all files → Optimized output
  CSS inlining, asset hashing, minification
```

## Why Vite is Fast

| Aspect | Webpack | Vite | Speedup |
|--------|---------|------|---------|
| Cold start | Bundle everything | Serve native ESM | 10-100x |
| Hot update | Re-bundle module graph | HMR on changed module | <50ms |
| Dependency pre-bundling | Full webpack bundle | esbuild (Go-based) | 10-20x |
| CSS processing | webpack loaders | Lightning CSS | Fast |

## Configuration

```javascript
// vite.config.js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
    plugins: [react()],
    resolve: {
        alias: {
            "@": path.resolve(__dirname, "./src"),
        },
    },
    server: {
        port: 3000,
        proxy: {
            "/api": {
                target: "http://localhost:8080",
                changeOrigin: true,
            },
        },
    },
    build: {
        outDir: "dist",
        sourcemap: true,
        minify: "esbuild",      // or "terser"
        target: "es2020",
        rollupOptions: {
            output: {
                manualChunks: {
                    vendor: ["react", "react-dom"],
                    ui: ["@mui/material", "@emotion/react"],
                },
            },
        },
    },
    css: {
        modules: {
            localsConvention: "camelCaseOnly",
        },
        preprocessorOptions: {
            scss: {
                additionalData: `@import "@/styles/variables.scss";`,
            },
        },
    },
});
```

## Development Server

```bash
# Start dev server (instant)
npx vite

# Dev server options
npx vite --port 3000 --host 0.0.0.0 --open

# HMR via WebSocket
# Changes to .tsx/.jsx → update in <50ms
# CSS changes → update without page reload
```

## esbuild

esbuild is a JavaScript bundler written in Go, used internally by Vite for transformation and dependency pre-bundling.

```javascript
// Standalone esbuild usage
import * as esbuild from "esbuild";

// Transform
const result = await esbuild.transform("const x: number = 1", {
    loader: "ts",
    target: "es2020",
});
console.log(result.code);  // "const x = 1;\n"

// Bundle
await esbuild.build({
    entryPoints: ["src/index.ts"],
    bundle: true,
    outfile: "dist/bundle.js",
    minify: true,
    sourcemap: true,
    target: ["es2020", "chrome80", "firefox80"],
    loader: {
        ".png": "dataurl",
        ".svg": "text",
    },
});
```

## Plugin Example

```javascript
// Vite plugin to inject version
function versionPlugin() {
    return {
        name: "version-plugin",
        enforce: "pre",
        resolveId(id) {
            if (id === "virtual:version") {
                return "\0virtual:version";
            }
        },
        load(id) {
            if (id === "\0virtual:version") {
                return `export const version = "${new Date().toISOString()}";`;
            }
        },
        transform(code, id) {
            if (id.endsWith(".ts")) {
                // Log transformed files
                console.log(`Transformed: ${id}`);
            }
        },
        configureServer(server) {
            server.middlewares.use((req, res, next) => {
                console.log(`Request: ${req.url}`);
                next();
            });
        },
    };
}
```

## Environment Variables

```javascript
// .env
VITE_API_URL=http://localhost:8080
VITE_ENVIRONMENT=development

// .env.production
VITE_API_URL=https://api.example.com

// Usage in code
const apiUrl = import.meta.env.VITE_API_URL;
const isDev = import.meta.env.DEV;
const isProd = import.meta.env.PROD;
const mode = import.meta.env.MODE;  // "development" or "production"
```

## Production Build

```bash
npx vite build

# Output (dist/):
#   index.html
#   assets/
#     index-Bx2x2f2g.js       (hashed, minified)
#     index-Dx3x3g3h.css      (hashed, minified)
#     vendor-Cx4x4h4i.js      (code-split vendor chunk)
#     logo-Bx2x2f2g.svg       (hashed asset)
```

## Migration from Webpack

```javascript
// webpack.config.js → vite.config.js

// Webpack:
module.exports = {
    entry: "./src/index.js",
    output: { filename: "bundle.js" },
    module: {
        rules: [
            { test: /\.jsx?$/, use: "babel-loader" },
            { test: /\.css$/, use: ["style-loader", "css-loader"] },
        ],
    },
    plugins: [new HtmlWebpackPlugin({ template: "./public/index.html" })],
};

// Vite:
export default defineConfig({
    plugins: [react()],  // Handles JSX, Fast Refresh
});
```

## Key Replacements

| Webpack Feature | Vite Equivalent |
|----------------|----------------|
| babel-loader | @vitejs/plugin-react (Babel) or esbuild (default) |
| css-loader | Built-in CSS support |
| style-loader | Built-in CSS injection |
| file-loader | Built-in asset handling |
| url-loader | Built-in (base64 inline for small assets) |
| DefinePlugin | import.meta.env |
| ProvidePlugin | Use ES imports directly |
| HtmlWebpackPlugin | Built-in HTML generation |
| MiniCssExtractPlugin | Built-in CSS extraction |
| CopyWebpackPlugin | vite-plugin-static-copy |
