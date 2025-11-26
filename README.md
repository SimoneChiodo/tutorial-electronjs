# ✅ INSTALLARE ELECTRON IN UN PROGETTO VITE (React)

## 1. Installa Electron

Nel tuo progetto Vite, installare i pacchetti electron con:  
`npm install electron --save-dev`  
`npm install electron-builder --save-dev`

## 2. Installazione manuale delle build Electron (.zip) (per aggirare errori di symlink)

Serve solo se electron-builder fallisce perché Electron non si scarica automaticamente:

Vai alla pagina ufficiale delle release:
https://github.com/electron/electron/releases/latest

Scarica electron-vXX.X.X-win32-x64.zip  
(*la versione esatta è quella citata nell'errore di build*)

Apri 7zip come amministratore (clic destro → Esegui come amministratore)

Estrai la ZIP in una cartella senza spazi nella cache:  
`C:\Users\<utente>\AppData\Local\electron-builder\Cache\winCodeSign\winCodeSign-x.x.x`

electron-builder userà automaticamente questi file.

## 3. Crea il file principale di Electron
Crea main.js (o main.cjs) nella root del progetto:

``` javascript,
import { app, BrowserWindow } from 'electron'

const createWindow = () => {
  const win = new BrowserWindow({
    width: 1200,
    height: 800,
    webPreferences: {
      nodeIntegration: true,
      contextIsolation: false
    }
  })

  if (process.env.NODE_ENV === 'production') {
    win.loadFile('dist/index.html')
  } else {
    win.loadURL('http://localhost:5173')
  }
}

app.whenReady().then(createWindow)

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})
```

⚠️ IMPORTANTE:

Se nel package.json hai "type": "module" → usa main.js con import.  
Se vuoi usare require() → usa main.cjs.

## 4. Aggiungi queste parti al package.json
```json,
"main": "main.js",
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "electron:start": "electron .",
  "electron:build": "npm run build && electron-builder"
}
```

# 🚀 COME AVVIARE ELECTRON IN SVILUPPO
Apri due terminali:
- Terminale 1 – Vite
  ``` bash,
  npm run dev
  ```
- Terminale 2 – Electron
  ``` bash,
  npm run electron:start
  ```

Electron mostrerà la tua app React in tempo reale.

# 📦 COME FARE LA BUILD PER WINDOWS 10 (EXE o installer)
## 1. Configura electron-builder
Aggiungi al package.json:

``` json,
"build": {
  "appId": "com.tuapackage.app",
  "files": [
    "dist/**/*",
    "main.js",
    "package.json"
  ],
  "win": {
    "target": "nsis",
    "sign": false
  }
}
sign: false // → evita problemi con certificati e firma digitale. (non mettere questo commento)
```

## 2. Modifica main.js per distinguere dev e build
Aggiungi al main.js:
``` javascript,
if (process.env.NODE_ENV === 'production') {
  win.loadFile('dist/index.html')
} else {
  win.loadURL('http://localhost:5173')
}
```

## 3. Esegui la build
Scrivi nella console:  
`npm run electron:build`

Ora si possono trovare le build ai percorsi:
- `📁 dist/win-unpacked/` → versione portabile dell’app
- `📁 dist/*.exe` → installer NSIS per Windows 10
