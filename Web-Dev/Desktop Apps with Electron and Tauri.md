---
id: a1b2c3d4-1198-4000-8000-000000000198
title: Desktop Apps with Electron and Tauri
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001198
---
# Desktop Apps with Electron and Tauri

Electron and Tauri build cross-platform desktop applications using web technologies. Tauri is newer, smaller, and more secure.

## Architecture Comparison

```
Electron:
  ┌──────────────────────┐
  │  Node.js + Chromium   │  ≈ 120-200 MB per app
  │  (Full browser engine)│
  │                       │
  │  Main Process         │
  │  Renderer Process     │
  │  (One per window)     │
  └──────────────────────┘

Tauri:
  ┌──────────────────────┐
  │  OS WebView (system)  │  ≈ 3-10 MB per app
  │  Rust Backend         │
  │                       │
  │  WebView (renderer)   │
  │  Rust Core (backend)  │
  └──────────────────────┘
```

## Electron App

```javascript
// main.js (Main Process)
const { app, BrowserWindow, ipcMain, dialog, Menu } = require('electron');
const path = require('path');

let mainWindow;

function createWindow() {
    mainWindow = new BrowserWindow({
        width: 1200,
        height: 800,
        webPreferences: {
            preload: path.join(__dirname, 'preload.js'),
            contextIsolation: true,    // Security: isolate renderer
            nodeIntegration: false,    // Security: no node in renderer
        },
    });

    mainWindow.loadFile('index.html');

    // Open DevTools in development
    if (process.env.NODE_ENV === 'development') {
        mainWindow.webContents.openDevTools();
    }
}

app.whenReady().then(createWindow);

app.on('window-all-closed', () => {
    if (process.platform !== 'darwin') app.quit();
});

// IPC handlers
ipcMain.handle('select-file', async () => {
    const result = await dialog.showOpenDialog(mainWindow, {
        properties: ['openFile'],
        filters: [{ name: 'Images', extensions: ['jpg', 'png', 'gif'] }]
    });
    return result.filePaths[0];
});

ipcMain.handle('read-file', async (event, filePath) => {
    const fs = require('fs');
    return fs.readFileSync(filePath, 'utf-8');
});

// Application menu
const menu = Menu.buildFromTemplate([
    {
        label: 'File',
        submenu: [
            { label: 'Open', accelerator: 'CmdOrCtrl+O', click: () => mainWindow.webContents.send('menu-open') },
            { type: 'separator' },
            { label: 'Quit', accelerator: 'CmdOrCtrl+Q', click: () => app.quit() },
        ]
    },
    {
        label: 'Edit',
        submenu: [
            { role: 'undo' }, { role: 'redo' }, { type: 'separator' },
            { role: 'cut' }, { role: 'copy' }, { role: 'paste' },
        ]
    },
]);
Menu.setApplicationMenu(menu);
```

```javascript
// preload.js (Bridge between main and renderer)
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
    selectFile: () => ipcRenderer.invoke('select-file'),
    readFile: (path) => ipcRenderer.invoke('read-file', path),
    onMenuOpen: (callback) => ipcRenderer.on('menu-open', callback),
});
```

```html
<!-- renderer.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>My App</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <button id="openBtn">Open File</button>
    <div id="content"></div>

    <script>
        document.getElementById('openBtn').addEventListener('click', async () => {
            const filePath = await window.electronAPI.selectFile();
            if (filePath) {
                const content = await window.electronAPI.readFile(filePath);
                document.getElementById('content').textContent = content;
            }
        });
    </script>
</body>
</html>
```

## Tauri App

```rust
// src-tauri/src/main.rs
#![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]

use std::fs;
use tauri::Manager;

#[tauri::command]
fn read_file(path: String) -> Result<String, String> {
    fs::read_to_string(&path).map_err(|e| e.to_string())
}

#[tauri::command]
fn greet(name: &str) -> String {
    format!("Hello, {}! You've been greeted from Rust!", name)
}

fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![read_file, greet])
        .setup(|app| {
            let window = app.get_window("main").unwrap();
            window.set_title("My Tauri App").unwrap();
            Ok(())
        })
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

```json
// src-tauri/tauri.conf.json
{
    "build": {
        "beforeBuildCommand": "npm run build",
        "devPath": "http://localhost:1420",
        "distDir": "../dist"
    },
    "package": {
        "productName": "MyApp",
        "version": "0.1.0"
    },
    "tauri": {
        "allowlist": {
            "all": false,
            "shell": { "open": true },
            "dialog": { "open": true, "save": true },
            "fs": { "readFile": true, "writeFile": true, "scope": ["$APP/**", "$DOCUMENT/**"] },
            "notification": { "all": true }
        },
        "bundle": {
            "identifier": "com.example.myapp",
            "icon": ["icons/icon.png"],
            "windows": { "wix": null },
            "macOS": { "dmg": null },
            "linux": { "deb": null }
        },
        "security": {
            "csp": "default-src 'self'; style-src 'self' 'unsafe-inline'"
        }
    }
}
```

```javascript
// Frontend (Vanilla JS)
import { invoke } from '@tauri-apps/api/tauri';

async function openFile() {
    const { open } = await import('@tauri-apps/api/dialog');
    const selected = await open({
        filters: [{ name: 'Text', extensions: ['txt', 'md'] }]
    });
    if (selected) {
        const content = await invoke('read_file', { path: selected });
        document.getElementById('content').textContent = content;
    }
}
```

## Security Comparison

| Aspect | Electron | Tauri |
|--------|----------|-------|
| Renderer | Chromium (full browser) | System WebView |
| Node access | Optional (via preload) | None (Rust backend) |
| CSP support | Yes | Yes |
| Auto-updates | electron-updater | Built-in |
| Code signing | Required for distribution | Required |

## Distribution

```bash
# Electron
npx electron-builder --win --mac --linux

# Tauri
cargo tauri build
# Outputs: .msi, .dmg, .deb, .AppImage depending on platform
```
